title: js 深浅拷贝 &&  js 函数参数传递
date: 2016-02-20 10:02:45
tags: JavaScript
original: false
categories: 前端
---
#### 浅拷贝
我所理解的 javascript中的数组或者对象的浅拷贝 是增加了一个指向同一个内存地址的变量;由于指向的内存地址相同,改变任一变量的值,会引起另一变量跟随变化。
<!-- more -->

```
var arr1 = [1,2,3];
var arr2 = arr1; // 浅拷贝 （arr2与arr1指向同一个内存地址）
var obj1 = {a:1};
var obj2 = obj1;// 浅拷贝（obj2与obj1指向同一个内存地址）
// 由于指向的内存地址相同 改变任一变量的值 会引起另一变量跟随变化
console.log(arr1);
console.log(arr2);
console.log(obj1);
console.log(obj2);
// 改变arr2 obj2 的属性 
arr2[1] = 100;
obj2.a=2;
console.log(arr1);
console.log(arr2);
console.log(obj1);
console.log(obj2);
```

#### 深拷贝
深拷贝一般都是开辟一块新的内存地址，将原数组或者对象的各个属性逐个复制出去,新对象拥有原对象的各个属性，是两个独立的对象 ，不会互相影响。

```
var obj1 = {a:1,b:2};
var obj2 = JSON.parse(JSON.stringify(obj1)); //深拷贝
console.log(obj1);
console.log(obj2);
//给obj2添加属性c
obj2.c = 3;
console.log(obj1);
console.log(obj2);
```

 JSON.parse(JSON.stringify( obj )) 进行深拷贝时 正确处理的对象只有 Number, String, Boolean, Array, 扁平对象，即那些能够被 json 直接表示的数据结构，但是坏处也显而易见，这会抛弃对象的constructor，也就是深复制之后，无论这个对象原本的构造函数是什么，在深复制之后都会变成Object

#### javascript中的函数参数传递
 函数的参数一般包括基本类型 和 引用类型 
对于基本类型 数字、字符串等是将它们的值传递给了函数参数，函数参数的改变不会影响函数外部的变量。
对于引用类型 数组和对象等是将对象(数组)的变量的值（内存地址）传递给了函数参。当**函数改变这个地址指向的对象(数组)的 内容** 时，同时也**改变了函数外部变量指向的对象(数组)的内容**；当**函数改变的是变量的地址**时，实际**就与函数外部的变量失去了联系**，变成了完全不同的对象了，**不会对函数外部对象造成改变**。
        
```
var a1=1;
var a2=[];
var a3={};
function fun(a1,a2,a3){
    a1=3;
    a2=[1,2,3];
    a3={a:3};
}
fun(a1,a2,a3)
a1  // 1
a2  // []
a3  // Object {}
```

a1、a2、a3 都没有被改变，a1 仍然是原来的数字，a2、a3 仍然是空白的数组、对象。
**js中的函数参数都是按值传递的 只是 数组、对象等按值传递，是指变量地址的值。** 

```
var a1=1;
var a2=[];
var a3={};
function fun(a1,a2,a3){
    a1 = 3;
    a2.push(2);
    a3.a = 3;
}
undefined
fun(a1,a2,a3)
undefined
a1 //  1
a2 //  [2]
a3 //  Object {a: 3}
```

外部a2、a3的值发生了改变
即如果不赋新值（不改变对象地址），而是在函数内部 直接对数组或者对象的内容进行操作  则会改变外部对象或数组的内容