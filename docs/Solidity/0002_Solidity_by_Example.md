# Solidity by Example

## Voting

```
pragma solidity ^0.4.16;                                        // 指定程序运行版本

/// @title Voting with delegation.
contract Ballot {
    // This declares a new complex type which will
    // be used for variables later.
    // It will represent a single voter.
    struct Voter {                                              // 每一个参与投票的人
        uint weight; // weight is accumulated by delegation     // 投票的人的权重
        bool voted;  // if true, that person already voted      // 这个人有没有参与投票
        address delegate; // person delegated to                // 投票人的地址
        uint vote;   // index of the voted proposal             // 投票人投的提案
    }

    // This is a type for a single proposal.
    struct Proposal {                                           // 提案
        bytes32 name;   // short name (up to 32 bytes)          // 提案的名字
        uint voteCount; // number of accumulated votes          // 提案获取的投票数
    }

    address public chairperson;                                 // 主持人，创建投票的人

    // This declares a state variable that
    // stores a `Voter` struct for each possible address.
    mapping(address => Voter) public voters;                    // 投票参与人的集合，地址映射到选举人的结构体

    // A dynamically-sized array of `Proposal` structs.
    Proposal[] public proposals;                                // 当前所有的提案

    /// Create a new ballot to choose one of `proposalNames`.
    function Ballot(bytes32[] proposalNames) public {           // 构造函数
        chairperson = msg.sender;                               // 获取创建投票人（主持人）的地址信息
        voters[chairperson].weight = 1;                         // 设置主持人的权重，并加入投票人集合中

        // For each of the provided proposal names,
        // create a new proposal object and add it
        // to the end of the array.
        for (uint i = 0; i < proposalNames.length; i++) {       // for循环迭代提案数组
            // `Proposal({...})` creates a temporary
            // Proposal object and `proposals.push(...)`
            // appends it to the end of `proposals`.
            proposals.push(Proposal({                           // 从这里可以看出，数组也是具有push功能的
                name: proposalNames[i],
                voteCount: 0
            }));
        }
    }

    // Give `voter` the right to vote on this ballot.
    // May only be called by `chairperson`.
    function giveRightToVote(address voter) public {            // 给予投票者权限
        // If the argument of `require` evaluates to `false`,   // 如果require参数执行是false，就会全部回退之前的操作
        // it terminates and reverts all changes to
        // the state and to Ether balances. It is often
        // a good idea to use this if functions are
        // called incorrectly. But watch out, this
        // will currently also consume all provided gas
        // (this is planned to change in the future).
        require((msg.sender == chairperson) && !voters[voter].voted && (voters[voter].weight == 0));
        voters[voter].weight = 1;
    }

    /// Delegate your vote to the voter `to`.
    function delegate(address to) public {                      // 转移投票权限
        // assigns reference
        Voter storage sender = voters[msg.sender];              // 获取执行函数的人
        require(!sender.voted);

        // Self-delegation is not allowed.
        require(to != msg.sender);                              // 不允许发给自己

        // Forward the delegation as long as
        // `to` also delegated.
        // In general, such loops are very dangerous,
        // because if they run too long, they might
        // need more gas than is available in a block.
        // In this case, the delegation will not be executed,
        // but in other situations, such loops might
        // cause a contract to get "stuck" completely.
        while (voters[to].delegate != address(0)) {             // 防止出现循环代理的死环
            to = voters[to].delegate;

            // We found a loop in the delegation, not allowed.
            require(to != msg.sender);
        }

        // Since `sender` is a reference, this
        // modifies `voters[msg.sender].voted`
        sender.voted = true;
        sender.delegate = to;
        Voter storage delegate = voters[to];
        if (delegate.voted) {                                   // 如果代理人已经代理了，那就直接投票了
            // If the delegate already voted,
            // directly add to the number of votes
            proposals[delegate.vote].voteCount += sender.weight;
        } else {
            // If the delegate did not vote yet,
            // add to her weight.
            delegate.weight += sender.weight;
        }
    }

    /// Give your vote (including votes delegated to you)
    /// to proposal `proposals[proposal].name`.
    function vote(uint proposal) public {                       // 选票
        Voter storage sender = voters[msg.sender];
        require(!sender.voted);
        sender.voted = true;                                    // 标记已选票
        sender.vote = proposal;

        // If `proposal` is out of the range of the array,
        // this will throw automatically and revert all
        // changes.
        proposals[proposal].voteCount += sender.weight;         // 给目标加上权重
    }

    /// @dev Computes the winning proposal taking all
    /// previous votes into account.
    function winningProposal() public view
            returns (uint winningProposal)
    {
        uint winningVoteCount = 0;
        for (uint p = 0; p < proposals.length; p++) {           // 一次for循环找出最大值
            if (proposals[p].voteCount > winningVoteCount) {
                winningVoteCount = proposals[p].voteCount;
                winningProposal = p;
            }
        }
    }

    // Calls winningProposal() function to get the index
    // of the winner contained in the proposals array and then
    // returns the name of the winner
    function winnerName() public view                           // 获取最大值的名称
            returns (bytes32 winnerName)
    {
        winnerName = proposals[winningProposal()].name;
    }
}
```

## Blind Auction

### Simple Open Auction

```
pragma solidity ^0.4.11;

contract SimpleAuction {
    // Parameters of the auction. Times are either
    // absolute unix timestamps (seconds since 1970-01-01)
    // or time periods in seconds.
    address public beneficiary;                             // 拍卖者
    uint public auctionEnd;                                 // 竞拍结束

    // Current state of the auction.                        
    address public highestBidder;                           // 最高出价地址
    uint public highestBid;                                 // 最高出价

    // Allowed withdrawals of previous bids
    mapping(address => uint) pendingReturns;                // 竞拍出局者

    // Set to true at the end, disallows any change
    bool ended;                                             // 竞拍结束

    // Events that will be fired on changes.
    event HighestBidIncreased(address bidder, uint amount); // 最高出价出现
    event AuctionEnded(address winner, uint amount);        // 竞拍结束事件

    // The following is a so-called natspec comment,
    // recognizable by the three slashes.
    // It will be shown when the user is asked to
    // confirm a transaction.

    /// Create a simple auction with `_biddingTime`
    /// seconds bidding time on behalf of the
    /// beneficiary address `_beneficiary`.
    function SimpleAuction(
        uint _biddingTime,
        address _beneficiary
    ) public {
        beneficiary = _beneficiary;                         // 获取竞拍地址
        auctionEnd = now + _biddingTime;                    // 竞拍结束时间
    }

    /// Bid on the auction with the value sent
    /// together with this transaction.
    /// The value will only be refunded if the
    /// auction is not won.
    function bid() public payable {
        // No arguments are necessary, all
        // information is already part of
        // the transaction. The keyword payable
        // is required for the function to
        // be able to receive Ether.

        // Revert the call if the bidding
        // period is over.
        require(now <= auctionEnd);                                 // 未出现超时

        // If the bid is not higher, send the
        // money back.
        require(msg.value > highestBid);                            // 金额大于当前最高竞价

        if (highestBidder != 0) {                                   // 最高竞价者地址不是空的
            // Sending back the money by simply using
            // highestBidder.send(highestBid) is a security risk
            // because it could execute an untrusted contract.
            // It is always safer to let the recipients
            // withdraw their money themselves.
            pendingReturns[highestBidder] += highestBid;            // 将当前最高竞价放入出局者名单，因为竞价者可以多次出价，所以会出现累加
        }
        highestBidder = msg.sender;                                 // 指明当前最高竞价者
        highestBid = msg.value;
        HighestBidIncreased(msg.sender, msg.value);                 // 发送公开最高竞价者信息
    }

    /// Withdraw a bid that was overbid.
    function withdraw() public returns (bool) {                     // 提取竞价金额
        uint amount = pendingReturns[msg.sender];
        if (amount > 0) {
            // It is important to set this to zero because the recipient
            // can call this function again as part of the receiving call
            // before `send` returns.
            pendingReturns[msg.sender] = 0;                         // 提出竞价的值

            if (!msg.sender.send(amount)) {                         // 最高竞价者付款
                // No need to call throw here, just reset the amount owing
                pendingReturns[msg.sender] = amount;
                return false;
            }
        }
        return true;
    }

    /// End the auction and send the highest bid
    /// to the beneficiary.
    function auctionEnd() public {
        // It is a good guideline to structure functions that interact
        // with other contracts (i.e. they call functions or send Ether)
        // into three phases:
        // 1. checking conditions
        // 2. performing actions (potentially changing conditions)
        // 3. interacting with other contracts
        // If these phases are mixed up, the other contract could call
        // back into the current contract and modify the state or cause
        // effects (ether payout) to be performed multiple times.
        // If functions called internally include interaction with external
        // contracts, they also have to be considered interaction with
        // external contracts.

        // 1. Conditions
        require(now >= auctionEnd); // auction did not yet end
        require(!ended); // this function has already been called

        // 2. Effects
        ended = true;
        AuctionEnded(highestBidder, highestBid);

        // 3. Interaction
        beneficiary.transfer(highestBid);
    }
}
```

### Safe Remote Purchase

```
pragma solidity ^0.4.11;

contract Purchase {
    uint public value;
    address public seller;
    address public buyer;
    enum State { Created, Locked, Inactive }                    // 枚举类型
    State public state;

    // Ensure that `msg.value` is an even number.
    // Division will truncate if it is an odd number.
    // Check via multiplication that it wasn't an odd number.
    function Purchase() public payable {                        // 检查数据有效性
        seller = msg.sender;
        value = msg.value / 2;
        require((2 * value) == msg.value);
    }

    modifier condition(bool _condition) {                       // 接下来是修士器，函数中的_;表示的调用的函数体
        require(_condition);
        _;
    }

    modifier onlyBuyer() {
        require(msg.sender == buyer);
        _;
    }

    modifier onlySeller() {
        require(msg.sender == seller);
        _;
    }

    modifier inState(State _state) {
        require(state == _state);
        _;
    }

    event Aborted();
    event PurchaseConfirmed();
    event ItemReceived();

    /// Abort the purchase and reclaim the ether.
    /// Can only be called by the seller before
    /// the contract is locked.
    function abort()
        public
        onlySeller
        inState(State.Created)
    {
        Aborted();
        state = State.Inactive;
        seller.transfer(this.balance);
    }

    /// Confirm the purchase as buyer.
    /// Transaction has to include `2 * value` ether.
    /// The ether will be locked until confirmReceived
    /// is called.
    function confirmPurchase()
        public
        inState(State.Created)
        condition(msg.value == (2 * value))
        payable
    {
        PurchaseConfirmed();
        buyer = msg.sender;
        state = State.Locked;
    }

    /// Confirm that you (the buyer) received the item.
    /// This will release the locked ether.
    function confirmReceived()
        public
        onlyBuyer
        inState(State.Locked)
    {
        ItemReceived();
        // It is important to change the state first because
        // otherwise, the contracts called using `send` below
        // can call in again here.
        state = State.Inactive;

        // NOTE: This actually allows both the buyer and the seller to
        // block the refund - the withdraw pattern should be used.

        buyer.transfer(value);
        seller.transfer(this.balance);
    }
}
```
