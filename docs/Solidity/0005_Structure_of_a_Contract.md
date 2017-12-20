# Structure of a Contract

每个合约中可包含状态变量(State Variables)，函数(Functions),函数修饰符（Function Modifiers）,事件（Events）,结构类型(Structs Types)和枚举类型(Enum Types)。

## 状态变量（State Variables）

变量值会永久存储在合约的存储空间

```
pragma solidity ^0.4.0;

// simple store example
contract simpleStorage{
    uint valueStore; //state variable
}
```

## 函数（Functions）

智能合约中的一个可执行单元。

```
pragma solidity ^0.4.0;

contract simpleMath{
    //Simple add function,try a divide action?
    function add(uint x, uint y) returns (uint z){
        z = x + y;
    }
}
```

上述示例展示了一个简单的加法函数。

## 函数修饰符(Function Modifiers)

函数修饰符用于增强语义。详情见函数修饰符相关章节。

## 事件（Events）

事件是以太坊虚拟机(EVM)日志基础设施提供的一个便利接口。用于获取当前发生的事件。

```
pragma solidity ^0.4.0;

contract SimpleAuction {
    event aNewHigherBid(address bidder, uint amount);
    
    function  bid(uint bidValue) external {
        aNewHigherBid(msg.sender, msg.value);
    }
}
```

## 结构体类型（Structs Types）

自定义的将几个变量组合在一起形成的类型。详见关于结构体相关章节。

```
pragma solidity ^0.4.0;

contract Company {
    //user defined `Employee` struct type
    //group with serveral variables
    struct employee{
        string name;
        uint age;
        uint salary;
    }
    
    //User defined `manager` struct type
    //group with serveral variables
    struct manager{
        employee employ;
        string title;
    }
}
```

## 枚举类型

特殊的自定义类型，类型的所有值可枚举的情况。详情见后续相关章节。

```
pragma solidity ^0.4.0;

contract Home {
    enum Switch{On,Off}
}
```
