title: PHP中的设计模式学习（C）
date: 2017-01-04 16:11:28
tags: PHP
original: false
comments: true
categories: 后端
thumbnail: http://7xqeyw.com1.z0.glb.clouddn.com/281539080765117436.jpg
---
#### 观察者模式
观察者模式定义了对象间的一种一对多的依赖关系。以便一个对象的状态发生变化后，所有依赖它的对象都得到通知并自动刷新。该模式必须包含两个角色：观察者和被观察对象。观察者和被观察者之间存在**观察**的逻辑关联，当被观察者发生改变的时候，观察者就会观察到这样的变化，并且做出相应的响应。

在PHP SPL标准库中已经提供[SplSubject(被观察对象)](http://php.net/manual/zh/class.splsubject.php)和[SqlOberver(观察者)](http://php.net/manual/zh/class.splobserver.php) 接口.

```
/* 被观察对象 SplSubject  接口摘要 */
SplSubject {
/* 方法 */
abstract public void attach ( SplObserver $observer )
abstract public void detach ( SplObserver $observer )
abstract public void notify ( void )
}

/* 观察者 SqlOberver 接口摘要 */
SplObserver {
/* 方法 */
abstract public void update ( SplSubject $subject )
}
```
具体实现代码示例：
<!-- more -->
```
// 被观察者类的实现
class Subject implements \SplSubject
{
    private $observers = array();

    /**
     * 添加观察者
     * @param SplObserver $observer
     * @return mixed
     */
    public function attach(SplObserver $observer)
    {
        if (!in_array($observer, $this->observers)) {
            $this->observers[] = $observer;
        }
    }

    /**
     * 移除观察者
     * @param SplObserver $observer
     * @return mixed
     */
    public function detach(SplObserver $observer)
    {
        if (false != ($index = array_search($observer, $this->observers))) {
            unset($this->observers[$index]);
        }
    }
    //完成自身功能后通知观察者
    public function post()
    {
        //post相关code
        //balabala ……
        $this->notify();  //完成自身功能后通知观察者
    }

    /**
     * 通知观察者
     */
    public function notify()
    {
        foreach ($this->observers as $observer) {
            $observer->update($this);
        }
    }

}
//观察者1
class Observer1 implements SplObserver
{
    /**
     * @param SplSubject $subject
     * @return mixed
     */
    public function update(SplSubject $subject)
    {
        echo "执行观察者1 相关逻辑";
         // balabla
    }

}
//观察者2
class Observer2 implements SplObserver
{
    /**
     * @param SplSubject $subject
     * @return mixed
     */
    public function update(SplSubject $subject)
    {
        echo "执行观察者2 相关逻辑";
        // balabla
    }

}

$subject = new Subject();  //一个被观察者的实例
$subject->attach(new Observer1());// 添加一个观察者
$subject->attach(new Observer2());// 再添加又一个观察者
$subject->post();// 被观察者执行自身任务，通知观察者事件触发执行
```
#### 原型模式
原型模式与工厂模式作用类似，也是用来创建对象的。不同的是，原型模式是先创建好一个原型对象，然后通过clone原型来创建新的对象，这样避免了类创建时的重复的初始化操作。原型模式主要适用于某些大型的结构复杂的对象的创建工作，创建一个对象需要大的开销，每次new消耗很大，使用原型模式只需内存拷贝即可。
实现代码示例：
```
// 声明一个克隆自身的接口 
interface Prototype
{
    function copy();
}
// 实现一个克隆自身的操作 
class ConcretePrototype implements Prototype
{
    private $name;

    function __construct($name)
    {
        $this->name = $name;

    }

    public function getName()
    {
        return $this->name;
    }

    public function setName($name)
    {
        $this->name = $name;
    }

    //克隆
    public function copy()
    {
        return clone $this;
    }
}
$prototype = new ConcretePrototype('32');
$pro1 = $prototype->copy(); //通过拷贝原型创建新的对象1
$pro2 = $prototype->copy(); //通过拷贝原型创建新的对象2
echo  $pro1->getName();
echo  $pro2->getName();
```

#### 装饰器模式
装饰器模式又叫装饰者模式。在不必改变原类文件和使用继承的情况下，动态地扩展一个对象的功能。它是通过创建一个包装对象，也就是装饰来包裹真实的对象。通常给对象添加功能，要么直接修改对象添加相应的功能，要么派生对应的子类来扩展，抑或是使用对象组合的方式。显然，直接修改对应的类这种方式并不可取。在面向对象的设计中，我们也应该尽量使用对象组合，而不是对象继承来扩展和复用功能。装饰器模式就是基于对象组合的方式，可以很灵活的给对象添加所需要的功能。
代码示例：
```
// 组件对象接口
interface  ICompoment
{
    function Display();
}
// 待装饰对象
class Service implements ICompoment
{
    private $data ;
    function __construct($data)
    {
        if (is_array($data)){
            $this->data = $data;
        }
    }
    function Display()
    {
        return $this->data;
    }
}
// 所有装饰器父类
abstract  class Decorator implements ICompoment
{
    protected $component;
    function __construct(ICompoment $component)
    {
        $this->component = $component;
    }

}
// 具体装饰器1
class RenderToJson extends Decorator {
    function Display()
    {
        $returnData = $this->component->Display();
        return json_encode($returnData);
    }
}
// 具体装饰器2
class RenderToXml extends Decorator {
    function Display()
    {
        $returnData = $this->component->Display();
        $doc = new \DOMDocument();
        foreach ($returnData as  $key => $value){
            $doc->appendChild($doc->createElement($key,$value));
        }
        return $doc->saveXML();
    }
}

$service = new Service(array('name'=>'Tom'));
$service = new RenderToJson($service);
var_dump($service->Display());
$service1 = new Service(['name'=>'Tom','age'=>24,'sex'=>'男']);
$service1 = new RenderToXml($service1);
var_dump($service1->Display());
```

#### 迭代器模式
迭代器模式提供一种方法顺序访问一个聚合对象中的各种元素，而又不暴露该对象的内部表示。为遍历不同的聚合结构提供一个统一的接口。
PHP SPL标准库中已经提供[ Iterator接口](http://php.net/manual/zh/class.iterator.php)（迭代器接口）
Iterator（迭代器）接口摘要

```
Iterator extends Traversable {
    /* 方法 */
    abstract public mixed current ( void )
    abstract public scalar key ( void )
    abstract public void next ( void )
    abstract public void rewind ( void )
    abstract public boolean valid ( void )
}
```
代码示例

```
// 实现Iterator的一个类
class MyIterator implements \Iterator {
    private $position = 0;
    private $array = array(
        "firstelement",
        "secondelement",
        "lastelement",
    );

    public function __construct() {
        $this->position = 0;
    }

    function rewind() {
    //        var_dump(__METHOD__);
        $this->position = 0;
    }

    function current() {
    //        var_dump(__METHOD__);
        return $this->array[$this->position];
    }

    function key() {
    //        var_dump(__METHOD__);
        return $this->position;
    }

    function next() {
    //        var_dump(__METHOD__);
        ++$this->position;
    }

    function valid() {
    //        var_dump(__METHOD__);
        return isset($this->array[$this->position]);
    }
}

$it = new MyIterator;
// 实现迭代器后就可以使用foreach循环迭代
foreach($it as $key => $value) {
    var_dump($key, $value);
    echo "\n";
}

//$it = new MyIterator;
//while ($it->valid()){
//    echo $it->current();
//    echo '<br>';
//    $it->next();
//}
```

#### 代理模式
代理模式为其他对象提供一种代理以控制这个对象的访问。在某些情况下，一个对象不适合或者不能直接引用另一个对象，而代理对象可以在客户端和目标对象之间起到中介的作用。
**代理模式组成**：
**抽象角色**：通过接口或抽象类声明真实角色实现的业务方法。
**代理角色**：实现抽象角色，是真实角色的代理，通过真实角色的业务逻辑方法来实现抽象方法，并可以附加自己的操作。
**真实角色**：实现抽象角色，定义真实角色所要实现的业务逻辑，供代理角色调用。
代码示例：

```
// 抽象角色
interface IRequest
{
    public function request($url);
}
// 真实角色
class RealObj implements IRequest
{
    function __construct()
    {
    }
    public function request($url)
    {
        echo '请求链接:' . $url;
        //balabala
    }
}
// 代理角色
class ProxyObj implements IRequest
{
    private $_client;
    private function client()
    {
        if (!$this->_client instanceof RealObj) {
            $this->_client = new RealObj();
        }
        return $this->_client;

    }
    public function request($url)
    {
        return $this->client()->request($url);
    }
}

$proxy = new ProxyObj();
$proxy->request('http://www.google.com');
```


