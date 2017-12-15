# Introduction to Smart Constracts

## A Simple Smart Constract

### Storage

```
pragma solidity ^0.4.0;     // 指定程序运行版本

contract SimpleStorage {    // 合同，相当于类
    uint storedData;        // 变量

    function set(uint x) public {                           // 函数，public类型的函数
        storedData = x;
    }

    function get() public constant returns (uint) {         // public类型的函数，返回值是常量型的uint
        return storedData;
    }
}
```

### Subcurrency Example

```
pragma solidity ^0.4.0;                                         // 指定程序的版本

contract Coin {                                                 // 名称是Coin的合同
    // The keyword "public" makes those variables
    // readable from outside.
    address public minter;                                      // Public访问类型的minter变量，变量类型address
    mapping (address => uint) public balances;                  // Public访问类型的balances变量，变量类型是mapping，泛型指定为address作为key，uint作为value

    // Events allow light clients to react on
    // changes efficiently.
    event Sent(address from, address to, uint amount);          // 注册事件函数，和Qt里面的事件函数类似

    // This is the constructor whose code is
    // run only when the contract is created.
    function Coin() public {                                    // 构造函数
        minter = msg.sender;                                    // 记录合同生成者的address
    }

    function mint(address receiver, uint amount) public {       // Public类型的函数
        if (msg.sender != minter) return;                       
        balances[receiver] += amount;
    }

    function send(address receiver, uint amount) public {
        if (balances[msg.sender] < amount) return;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        Sent(msg.sender, receiver, amount);                     // 执行注册了event类型的函数，会触发Client端的事件
    }
}
```


