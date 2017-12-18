# Function Modifiers

## 参考文档

* [函数修改器(Function Modifiers)](http://www.tryblockchain.org/Solidity-FunctionModifiers-%E5%87%BD%E6%95%B0%E4%BF%AE%E6%94%B9%E5%99%A8.html)

## 说明

修改器(Modifiers)可以用来轻易的改变一个函数的行为。比如用于在函数执行前检查某种前置条件。修改器是一种合约属性，可被继承，同时还可被派生的合约重写(override)。

* 修改器可以被继承，使用将modifier置于参数后，返回值前即可；
* 特殊_表示使用修改符的函数体的替换位置；
* 全约可以多继承，通过,号分隔两个被继承的对象；
* 修改器也是可以接收参数；
