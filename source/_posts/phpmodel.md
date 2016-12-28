title: PHP中的设计模式学习（A）
date: 2016-12-28 09:44:48
tags: PHP
original: false
comments: true
categories: 后端
thumbnail: http://7xqeyw.com1.z0.glb.clouddn.com/898935221173953867.jpg
---
#### 工厂模式
简单来说工厂模式就是讲new操作封装在了工厂方法里，避免到处new某个对象。
工厂模式是一种类，它具有为你创建对象的一些方法，你可以使用工厂类创建对象，而不直接使用new的方式创建对象。这样，当您想更改所创建的对象类型时，只需要更改工厂即可。使用该工厂的所有代码会自动更改。

```
class User
{
    private $id;
    private $name;

    public function __construct($id, $name)
    {
        $this->id = $id;
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }
}
// 工厂类
class Factory
{
    public static function Create($id, $name)
    {
        return new User($id, $name);
    }
}
```
<!-- more -->
#### 单例模式
单例模式保证一个类仅有一个实例，并提供一个访问它的全局访问点。在它的核心结构中只包含一个被称为单例的特殊类。通过单例模式可以保证系统中一个类只有一个实例。php单例代码：

```
class  Singleton
{
    // 静态变量 跟类绑定，保存全局实例；私有化变量 避免通过类名::@instance 直接调用，防止为空
    private static $instance;

    /* 私有化构造函数 ，防止外界实例化对象*/
    private function __construct()
    {
    }

    // 私有化克隆函数，防止外界克隆对象
    private function __clone()
    {

    }

    // 静态方法，单例访问的统一入口，返回唯一的对象实例
    public static function getInstance()
    {
        if (!(self::$instance instanceof self)) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}
```
#### 注册树模式
注册树模式又叫注册模式/注册器模式，但是注册树模式更容易让人理解。注册树模式通过将对象实例注册到一棵全局的对象树上，需要的时候从对象树上采摘的模式设计方法。类似于小时候卖糖葫芦的将糖葫芦插在一个大的杆子上面，人们买的时候就取下来。不同的是，注册树模式摘下来还会有，能摘多次，糖葫芦摘一次就没有了 ……
##### 注册树模式想解决什么问题
单例模式解决的是如何在整个项目中创建唯一对象实例的问题，工厂模式解决的是如何不通过new建立实例对象的方法。首先，单例模式创建唯一对象的过程本身还有一种判断，即判断对象是否存在。存在则返回对象，不存在则创建对象并返回。 每次创建实例对象都要存在这么一层判断。 工厂模式更多考虑的是扩展维护的问题。 总的来说，单例模式和工厂模式可以产生更加合理的对象。怎么方便调用这些对象呢？而且在项目内如此建立的对象好像散兵游勇一样，不便统筹管理安排啊。因而，注册树模式应运而生。不管你是通过单例模式还是工厂模式还是二者结合生成的对象，都统统给我“插到”注册树上。我用某个对象的时候，直接从注册树上取一下就好。这和我们使用全局变量一样的方便实用。而且注册树模式还为其他模式提供了一种非常好的想法。
**三种模式的小结合**
```
// 创建单例
class  Singleton
{
    private static $instance;
    private function __construct() {}
    private function __clone() {}
    
    public static function getInstance()
    {
        if (!(self::$instance instanceof self)) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}

// 工厂模式
class Factory{
 public static function factory(){
  return Single::getInstance();
 }
}

//注册树
class Register
{
    protected static $objects;
    // 注册到全局树上
    static function set($alias, $object)
    {
        self::$objects[$alias] = $object;
    }
    // 获取树上的对象
    static function get($alias)
    {
       return self::$objects[$alias] ;
    }
    // 释放树上的对象
    static function _unset($alias)
    {
        unset(self::$objects[$alias]);
    }
}
// 注册
Register::set('db',Factory::factory());
// 其他地方使用注册的对象
$DB = Register::get('db');
```




