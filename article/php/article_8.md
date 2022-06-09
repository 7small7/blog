> 专注于PHP、MySQL、Linux和前端开发，感兴趣的感谢点个关注哟！！！文章整理在[GitHub](https://github.com/7small7),[Gitee](https://gitee.com/bruce_qiq)主要包含的技术有PHP、Redis、MySQL、JavaScript、HTML&CSS、Linux、Java、Golang、Linux和工具资源等相关理论知识、面试题和实战内容。

@author:[7small7](https://github.com/7small7)。

@source:[公众号-菜鸟成长学习笔记](/site/)。

@project:[微信小程序 程序员面试题大全](/site/)。

## trait是什么

trait是为了在PHP实现多继承的一种实现机制。PHP使用`extends`实现继承，本身是不能继承多个类的。同一类可以实现多个trait类。

## 优先级

1. 如果当前类和trait存在同名方法，当前类的优先级是高于trait类中同名方法。

```php
trait A
{
    public function show()
    {
        "traitA-show"
    }
}

class B {
    use A;
    public function show()
    {
        "B-show"
    }
}
// output
// B-show
```

2. 如果当前类继承了一个基类，并且实现了一个trait类。三则中存在同名方法，其优先级为：当前类->trait类->基类。
3. 如果两个 trait 都插入了一个同名的方法，如果没有明确解决冲突将会产生一个致命错误。需要使用指定别名来全部实现trait类中的方法。

```php
class A
{
    // 该方法只会实现指定的方法，trait类中的其他方法将会被排除。
    use TraitA, TraitB {
        A:function1 insteadof a;
        B:function1 insteadof b;
    }
    
    // 给实现的trait类中，方法冲突的都指定一个别名。
    use TraitA, TraitB {
        A:function1 as af1;
        A:function2 as af2;
        B:function1 as bf1;
        B:function2 as bf2;
    }
}
```

4. 指定实现trait类中的方法权限。

```php
class A
{
    use TraitA {
        A:function1 as private af1;
    }
}
```

6. trait类本身也可以集成其他的trait类。

```php
trait A {

}

trait B {

}

trait C {
    use A, B;
}
```

7. 为了对使用的类施加强制要求，trait 支持抽象方法的使用。 支持 public 、protected 和 private 方法。PHP 8.0.0 之前， 仅支持 public 和 protected 抽象方法。

```php
trait Hello {
    public function sayHelloWorld() {
        echo 'Hello'.$this->getWorld();
    }
    abstract public function getWorld();
}

class MyHelloWorld {
    private $world;
    use Hello;
    public function getWorld() {
        return $this->world;
    }
    public function setWorld($val) {
        $this->world = $val;
    }
}
```

8. Traits 可以定义静态变量、静态方法和静态属性。自 PHP 8.1.0 起，弃用直接在 trait 上调用静态方法或者访问静态属性。 静态方法和属性应该仅在使用了 trait 的 class 中访问。

```php
trait StaticExample {
    public static $name = "john";
    public static function doSomething() {
        return 'Doing something';
    }
}

class Example {
    use StaticExample;
}
Example::name;
Example::doSomething();
```

9. Trait 同样可以定义属性。Trait 定义了一个属性后，类就不能定义同样名称的属性，否则会产生 fatal error。 有种情况例外：属性是兼容的（同样的访问可见度、初始默认值）。

```php
trait PropertiesTrait {
    public $same = true;
    public $different = false;
}

class PropertiesExample {
    use PropertiesTrait;
    public $same = true;
    public $different = true; // 致命错误
}
```