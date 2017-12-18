# Units and Globally Available Variables

## 以太单位

数词后面可以有一个后缀, wei, finney, szabo 或 ether 和 ether 相关量词 之间的转换,在以太币数量后若没有跟后缀，则缺省单位是"wei", 如 2 ether == 2000 finney （这个表达式）计算结果为true。

## 特殊的变量和函数

有特殊的变量和函数总是存在于全局命名空间,主要用于提供关于blockchain的信息。

### 块属性

合同也是存在于区块链中的，所以它有区块链信息。

* block.coinbase (address): :当前块的矿工的地址
* block.difficulty (uint):当前块的难度系数
* block.gaslimit (uint):当前块汽油限量
* block.number (uint):当前块编号
* block.blockhash (function(uint) returns (bytes32)):指定块的哈希值——最新的256个块的哈希值
* block.timestamp (uint):当前块的时间戳

### 交易属性

* msg.data (bytes):完整的calldata
* msg.gas (uint):剩余的汽油
* msg.sender (address):消息的发送方(当前调用)
* msg.sig (bytes4):calldata的前四个字节(即函数标识符)
* msg.value (uint):所发送的消息中wei的数量
* now (uint):当前块时间戳(block.timestamp的别名)
* tx.gasprice (uint):交易的汽油价格
* tx.origin (address):交易发送方(完整的调用链)

### Address Related

* <address>.balance (uint256): balance of the Address in Wei  
  查询地址上剩余多少值，单位Wei
* <address>.transfer(uint256 amount): send given amount of Wei to Address, throws on failure  
  往这个地址里发送对应amount数量的Ether，单位Wei，如果出现错误，抛出异常
* <address>.send(uint256 amount) returns (bool): send given amount of Wei to Address, returns false on failure  
  和transfer一样，只是返回值取代了抛出异常
* <address>.call(...) returns (bool): issue low-level CALL, returns false on failure  
  合同本身也是地址，可以调用里面的函数
* <address>.callcode(...) returns (bool): issue low-level CALLCODE, returns false on failure  
  合同本身也是地址，可以调用里面的函数
* <address>.delegatecall(...) returns (bool): issue low-level DELEGATECALL, returns false on failure  
  合同本身也是地址，可以调用里面的函数

### Contract Related

* this (current contract’s type): the current contract, explicitly convertible to Address
* selfdestruct(address recipient): destroy the current contract, sending its funds to the given Address
* suicide(address recipient): alias to selfdestruct
