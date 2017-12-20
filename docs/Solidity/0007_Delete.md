# 特殊的运算符delete

delete运算符，用于将某个变量重置为初始值。对于整数，运算符的效果等同于a = 0。而对于定长数组，则是把数组中的每个元素置为初始值，变长数组则是将长度置为0。对于结构体，也是类似，是将所有的成员均重置为初始值。

delete对于映射类型几乎无影响，因为键可能是任意的，且往往不可知。所以如果你删除一个结构体，它会递归删除所有非mapping的成员。当然，你是可以单独删除映射里的某个键，以及这个键映射的某个值。

需要强调的是delete a的行为更像赋值，为a赋予一个新对象。我们来看看下文的示例：

```
pragma solidity ^0.4.0;

contract DeleteExample {
    uint data;
    uint[] dataArray;

    function f() {
        //值传递
        uint x = data;
        //删除x不会影响data
        delete x;

        //删除data，同样也不会影响x，因为是值传递，它存的是一份原值的拷贝。
        delete data; 

        //引用赋值
        uint[] y = dataArray;

        //删除dataArray会影响y，y也将被赋值为初值。
        delete dataArray;

        //下面的操作为报错，因为删除是一个赋值操作，不能向引用类型的storage直接赋值从而报错
        //delete y;
    }
}
```

通过上面的代码，我们可以看出，对于值类型，是值传递，删除x不会影响到data，同样的删除data也不会影响到x。因为他们都存了一份原值的拷贝。

而对于复杂类型略有不同，复杂类型在赋值时使用的是引用传递。删除会影响所有相关变量。比如上述代码中，删除dataArray同样会影响到y。

由于delete的行为更像是赋值操作，所以不能在上述代码中执行delete y，因为不能对一个storage的引用赋值
