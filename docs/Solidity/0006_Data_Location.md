# Data location

## 参考文档

* [Solidity - default data location for local vars](https://ethereum.stackexchange.com/questions/9781/solidity-default-data-location-for-local-vars)
* [作用范围和声明(Scoping And Decarations)](http://www.tryblockchain.org/Solidity-ScopingAndDeclarations-%E4%BD%9C%E7%94%A8%E8%8C%83%E5%9B%B4%E5%92%8C%E5%A3%B0%E6%98%8E.html)

## 描述

复杂类型，如数组(arrays)和数据结构(struct)在Solidity中有一个额外的属性，数据的存储位置。可选为memory和storage。

注意这里是指复杂类型，基本类型除了contract全局变量以外，是memory类型的，复杂类型是storage。

基于程序的上下文，大多数时候这样的选择是默认的，我们可以通过指定关键字storage和memory修改它。

另外还有第三个存储位置calldata。它存储的是函数参数，是只读的，不会永久存储的一个数据位置。外部函数的参数（不包括返回参数）被强制指定为calldata。效果与memory差不多。

## 强制的数据位置(Forced data location)

* 外部函数(External function)的参数(不包括返回参数)强制为：calldata
* 状态变量(State variables)强制为: storage

## 默认数据位置（Default data location）

* 函数参数包括返回参数：memory
* 所有其它的局部变量（从参考文档中可以查看出，并不是全部的样子）：storage

## 作用范围和声明 

函数内定义的变量，在整个函数中均可用，无论它在哪里定义）。因为Solidity使用了javascript的变量作用范围的规则（[深入理解JavaScript的变量作用域](http://www.cnblogs.com/rainman/archive/2009/04/28/1445687.html)）。与常规语言语法从定义处开始，到当前块结束为止不同。

```
pragma solidity ^0.4.0;

contract ScopingErrors {
    function scoping() {
        uint i = 0;

        while (i++ < 1) {
            uint same1 = 0;
        }

        while (i++ < 2) {
            uint same1 = 0;// Illegal, second declaration of same1
        }
    }

    function minimalScoping() {
        {
            uint same2 = 0;
        }

        {
            uint same2 = 0;// Illegal, second declaration of same2
        }
    }

    function forLoopScoping() {
        for (uint same3 = 0; same3 < 1; same3++) {
        }

        for (uint same3 = 0; same3 < 1; same3++) {// Illegal, second declaration of same3
        }
    }
    
    function crossFunction(){
       uint same1 = 0;//Illegal
    }

}
```
