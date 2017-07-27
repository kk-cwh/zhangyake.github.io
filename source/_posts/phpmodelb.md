title: PHP中的设计模式学习（B）
date: 2016-12-29 16:14:18
tags: PHP
original: false
comments: true
categories: 后端
thumbnail: http://7xqeyw.com1.z0.glb.clouddn.com/281539080765117436.jpg
photo:
- http://7xqeyw.com1.z0.glb.clouddn.com/usess.png
---
#### 适配器模式
适配器模式就是定义一个适配器接口，符合这些适配器的类必须继承并实现适配器方法。适配器模式简单的说就是将不同的函数接口封装成统一的API。实际应用的例子比如,PHP的数据库操作有mysql,mysqli,pdo 三种,(mysql操作 PHP7已经废弃了推荐用mysqli,pdo 代替),可以用适配器模式统一成一致(见下面代码示例). 类似的场景还有cache适配器,将memcache,redis,file,apc等不同的缓存函数,统一成一致，比如目前比较优雅的一个PHP框架  [laravel](http://www.golaravel.com/laravel/) 中的缓存的使用就是这样，不论是使用redis还是用memcache作为缓存，使用的方法都是一致的。
<!-- more -->
```
//定义一个适配器接口
interface  IDatabase{
    function connect($host,$user,$pwd,$dbname);
    function query($sql);
    function close();
}

// 实现适配器1
class MySQLi implements IDatabase
{
    protected $conn;
    function connect($host, $user, $pwd, $dbname)
    {
     $conn = mysqli_connect($host,$user,$pwd,$dbname);
        $this->conn = $conn;
    }

    function query($sql)
    {
        return mysqli_query($this->conn,$sql);
    }

    function close()
    {
       mysqli_close($this->conn);
    }
    
}
// 实现适配器2
class PDO implements IDatabase
{
    protected $conn;

    function connect($host, $user, $pwd, $dbname)
    {
        $conn = new \PDO("mysql:host=$host;dbname=$dbname", $user, $pwd);
        $this->conn = $conn;
    }

    function query($sql)
    {
        return $this->conn->query($sql);
    }

    function close()
    {
        unset($this->conn);
    }
    
}

$db = new MySQLi(); // or  $db = new PDO(); 
// 这里无论你是使用 MySQLi 还是 PDO 下面调用都是一致的
$db->connect('192.168.10.14','root','root','atest');
$result = $db->query('SELECT * FROM tb_user');
$db->close();
```
#### 策略模式
策略模式定义了一系列的算法，并将每一个算法封装起来，而且使它们还可以相互转换。策略模式让算法独立于使用它的客户而独立变化。策略模式的组成：
- 抽象策略角色： 策略类，通常由一个接口或者抽象类实现。
- 具体策略角色：包装了相关的算法和行为。
-  环境角色：持有一个策略类的引用，最终给客户端调用。
PHP 代码实现：

```
//抽象策略角色
/**
 *定义策略基类 以及包含的策略方法 
 * 此处是一个出行旅游的方法
 */
interface TravelStrategy
{
    public function travelAlgorithm();
}
//具体策略类 实现策略
//1：乘坐飞机
class AirPlaneStrategy implements TravelStrategy
{
    public function travelAlgorithm()
    {
        echo 'travel by ariplane';
    }
}
//具体策略类 实现策略
//2：乘坐火车
class TrainStrategy implements TravelStrategy
{
    public function travelAlgorithm()
    {
        echo 'travel by train';
    }
}
//具体策略类 实现策略
//3：骑自行车
class BicycleStrategy implements TravelStrategy
{
    public function travelAlgorithm()
    {
        echo 'travel by bicycle';
    }
}
/**环境类(Context):
 *用一个ConcreteStrategy对象来配置。
 *维护一个对Strategy对象的引用。可定义一个接口来让Strategy访问它的数据。
 *算法解决类，以提供客户选择使用何种解决方案：
 */
class PersonContext
{
    private $_strategy = null;

    public function __construct(TravelStrategy $travel)
    {
        $this->_strategy = $travel;
    }

    public function setTravelStrategy(TravelStrategy $travel)
    {
        $this->_strategy = $travel;
    }

    public function travel()
    {
        return $this->_strategy->travelAlgorithm();
    }
}

//乘坐火车旅行
$person = new PersonContext(new TrainStrategy()); // 设置，使用策略
$person->travel();

//改骑自行车
$person->setTravelStrategy(new BicycleStrategy());// 设置策略，使用策略
$person->travel();
```
#### 数据映射模式
数据映射模式能更好的组织程序与数据库进行交互。它将对象和数据存储映射起来，对一个对象的操作会映射为数据库存储的操作。在代码中实现数据对象映射模式，实现一个ORM类，将复杂的sql语句映射成对象属性的操作。自己的代码示例：

```
// 被操作的对象类
class User
{
    public $id;
    public $name;

    function __construct(){}
    function __destruct(){}

    public function getId() { return $this->id;}

    public function setId($id) { $this->id = $id; }

    public function getName() { return $this->name; }

    public function setName($name) { $this->name = $name; }
}
 // 操作对象映射到数据库
class UserMapper
{
    protected $db;

    function __construct()
    {
        $this->db = new MySQLi(); // 见适配器模式的模式的MySQLi
        $this->db->connect('192.168.10.14', 'root', 'root', 'atest');
    }

    /**
     * @param User $user
     * @return bool
     * 保存user对象到数据库
     */
    function saveUser(User $user)
    {
        if ($user->getId() === null) {
            $sql = "INSERT INTO tb_user (name) VALUES ( '" . $user->getName() . "')";
            $res = $this->db->query($sql);
            return $res;
        } else {
            $this->db->query("update tb_user set name = " . $user->getName() . " where id = " . $user->getId());
            return true;
        }
    }

    /**
     * @param $id
     * @return User|null
     * 从数据库中查询返回一个user对象
     */
    function findByUserId($id)
    {
        $result = $this->db->query("select id , name from tb_user where id = " . $id);

        if ($result) {
            $result = $result->fetch_assoc();
            $entry = new User();
            $entry->setId($result['id']);
            $entry->setName($result['name']);
            return $entry;
        }
        return null;
    }

}
//简单调用
$userMapper = new UserMapper();
$user = new User();// 创建对象
$user->setName('jack chen');

$res = $userMapper->saveUser($user);// 保存对象到数据库中
var_dump($res);
$user2 = $userMapper->findByUserId(1);// 查数据库 返回user对象
var_dump($user2);
```



