> 专注于PHP、MySQL、Linux和前端开发，感兴趣的感谢点个关注哟！！！文章整理在[GitHub](https://github.com/7small7),[Gitee](https://gitee.com/bruce_qiq)主要包含的技术有PHP、Redis、MySQL、JavaScript、HTML&CSS、Linux、Java、Golang、Linux和工具资源等相关理论知识、面试题和实战内容。

@author:[7small7](https://github.com/7small7)。

@source:[公众号-菜鸟成长学习笔记](/site/)。

@project:[微信小程序 程序员面试题大全](/site/)。


## 文字说明
下面这一段代码是PHP官方文档对于yield的解释说明。

1. 生成器函数看起来像普通函数——不同的是普通函数返回一个值，而生成器可以 yield 生成多个想要的值。 任何包含 yield 的函数都是一个生成器函数。

2. 当一个生成器被调用的时候，它返回一个可以被遍历的对象.当你遍历这个对象的时候(例如通过一个foreach循环)，PHP 将会在每次需要值的时候调用对象的遍历方法，并在产生一个值之后保存生成器的状态，这样它就可以在需要产生下一个值的时候恢复调用状态。

3. 一旦不再需要产生更多的值，生成器可以简单退出，而调用生成器的代码还可以继续执行，就像一个数组已经被遍历完了。

4. 生成器函数的核心是yield关键字。它最简单的调用形式看起来像一个return申明，不同之处在于普通return会返回值并终止函数的执行，而yield会返回一个值给循环调用此生成器的代码并且只是暂停执行生成器函数。

总结：上面几段话的意思，就是说一个函数中包含了yield，则这个函数就是一个生成器函数。你可以去循环调用生成器函数。并且每一次的调用都会生成一个新的数据，直到数据处理结束完。
`其特点在于调用一次生成一次，而不像return这样的函数，等待数据全部生成才返回数据。`如下面的示意图。
![Snipaste_2022-02-08_23-37-00](http://qiniucloudtest.qqdeveloper.com/mweb/Snipaste_2022-02-08_23-37-00.png)

## 代码演示
1. 我们先用一个普通函数来生成数组，普通函数生成完之后，直接返回给调用函数。
```php
 public function createRange1($number)
    {
        $data = [];
        for ($i = 0; $i < $number; $i++) {
            $data[] = time();
        }
        return $data;
    }

    public function yieldFun1()
    {
        var_dump("start", memory_get_usage());
        $data = $this->createRange1(1000);
        foreach ($data as $value) {
            // 在循环之前，方法createRange1就
            //把需要的数据一下写在数组$data中， 
            //因此此时$data数组整个元素都在内存中。
            # 停顿1秒
            sleep(1);
            echo $value . PHP_EOL;
        }
        var_dump("end", memory_get_usage());
    }
```
下面是一个输出结果，在代码中，我们每隔1秒对数组进行输出。但是得到的结果都是相同的，是因为在调用函数`createRange1`时，该函数把所有循环的结果都存储到数组中。这些数据都是存放在内存中，因此我们可以看到内存的消耗也是蛮大的。
```php
// 使用的内存为36952
/**
 * 输出结果
 * 1644332832
 * 1644332832
 * ......
 */
```
1. 下面使用迭代器生成。
```php
 public function createRange2($number)
    {
        $data = [];
        for ($i = 0; $i < $number; $i++) {
            yield time();
        }
        return $data;
    }

    public function yieldFun2()
    {
        var_dump("start", memory_get_usage());
        $data = $this->createRange2(1000);
        foreach ($data as $value) {
            // 每一次循环都去方法createRange2()里的迭代器读取数据，此时迭代器就生成一条数据，放在内存中。
            // 因此只有循环一次，就生成一次数据，内存汇总只有一条数据存在。
            # 停顿1秒
            sleep(1);
            echo $value . PHP_EOL;
        }
        var_dump($data->getReturn());
        var_dump("end", memory_get_usage());
    }
```
下面是一个输出结果，在代码中，我们同样每隔1秒钟进行输出，但是得到的结果却是不同的并且都是差距1秒。这说明了迭代器函数在返回数据时并不是一下返回全部数据，而是`yieldFun2`函数调用一次生成一次并返回生成的结果。我们也可以看到下面的内存使用，相对上面的普通函数来说，完全减少了很多倍。这就是因为迭代器只有在调用时才使用内存保存生成的数据。
```php
// 使用的内存为352
/**
* 输出结果
* 1644333040
* 1644333041
* ......
*/
```
通过上面的例子，我们很直观的能看出迭代器的好处，内存暂用极低。这得益于迭代器使用保持只有一条数据在内存中。一般我们可以使用在生成量大的数据，例如Excel生成与读取、大数组处理等业务场景。

![](https://qiniucloud.qqdeveloper.com/public_image.png)
