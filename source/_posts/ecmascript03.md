title: JS中的 call与apply方法 以及 bind方法
date: 2016-01-30 14:28:25
tags: JavaScript
original: false
categories: 前端
---

call 与apply 类似 都是与函数相关的方法，
每个函数都包含这两个特殊的方法 apply( ) 和 call( )  这两个方法都是在特定的作用域中调用函数，即将函数作为对象的方法来调用。
调用格式:
function.apply(obj,args);
<!-- more -->
1. apply方法有两个 参数 其中 obj 代表调用function的对象，在函数体中，obj是设置函数体内this对象的值。
args代表的参数是一个数组,数组中的值时传递给function的参数值。
function.call(obj,args. . .);
2. call方法的参数obj与apply中的类似，args. . . 则表示要绑定扫函数上的0-n个参数值,call与apply的区别就在于传递给函数的参数必须逐个列举出来
如果obj参数为null，则使用全局对象。
使用call or apply方改变函数的作用域的好处 就是对象不需要与方法有任何的耦合关系, 参数obj为不同的对象 返回值可能就随对象obj的而定

        var a=10,b=11;
        function add(x,y){
            if(x && y){
                return x+y;
              }
            return this.a + this.b;
        }
        var obj={a:1,b:2}
        add();
        //21
        add(2,3);
        //5
        add.call(obj);
        //3
        add.call(obj,2,4);
        //6
        add.apply(obj);
        //3
        add.apply(obj,[3,4,5]);
        //7
3. bind( )也是函数的一个方法,这个方法会返回一个作为方法调用的函数，其this值会被绑定到传给bind( )函数的值;
 
        var name = 'Jaak';
        var word = 'Hello';
        function say(){
            alert(this.name+" say "+this.word);
         }
         say();
         var p = {name:'Tom',word:'Hi'};
         var sayp = say.bind(p);
         sayp(); //chrome浏览器中运行 查看结果