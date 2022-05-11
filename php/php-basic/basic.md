## 下列表达式运算的结果是?

```php
$a = "aabbzz"; 
$a++; 
echo $a; 
```
> 答案：aabcaa
解析：字符串字母相加其实就是在末尾字母加一 如：$a = "a"; $a++;答案结果就是 b,$a=''aa';结果就是ab 故$a = "aabb";打印结果就是 aabc ,如$a = "aabbz";结果就是 aabca,因为Z是末尾字母故加一变为a,向前一位进一,b就变为c,故结果为C；

变形
```php
$a = 'c';
$a++;
// c
```
## 下面表达式运算的结果是?

```php
if ('1e3' == '1000') 
echo 'LOL';
```
> 答案：LOL
解析:：1e3 是 科学计数法 实数的指数形式 为1乘以10的三次方，故‘1e3’=='1000'是成立的，输出echo ‘LOL’；

## 下列数组最终的值是?

```php
$data = ['a','b','c']; 
foreach($data as $k=>$v){
    $v = &$data[$k];
}
```
> 答案：['b','c',c]
解析：这里有个考点要记得 就是&是引用；修改引用变量的值，那么空间的值也会改变，第一次循环 得到$v=&$data[0]=>'a',第二次循环$v=&$data[1]=>'b',可见第一次引用的$data[0]的值已经被改变，所以此时的$data[0]=b,此时$v引用的$data[1],进入第三次循环 此时$v又变为 $v=&$data[2]=>'c',,$v又一次改变，引用的$data[1]的值也被改变为C，所以此时的$data[1]=c,这样循环结束 $data[0]=>'b'， $data[1]=>'c'， $data[2]=>'c'，

## 下列运算最终的结果是?

```php
$a= 0.1; 
$b = 0.7;
if($a+$b == 0.8){ 
	echo true; 
} else { 
	echo false; 
} 
```
> 答案：空
解析：这里的考点有两个：
1，echo false和true的值；
2、浮点类型不能用于精确计算；首先浮点类型的数据不能用于计算，他会将浮点类型转为二进制，所以有一定的损耗，故它无限接近于0.8，也就是0.79999999...，所以echo 应该是个false；echo false；结果是空；echo true；结果是1；

## 下列变量$a和$b输出的结果为?

```php
$a= 0;
$b= 0;
if($a = 3>0 || $b = 3>0){
	$a++;
	$b++;
}
echo $a,$b;
```
> 答案：1，1
解析：此题考查的是运算符的优先级问题，首先在此题中比较运算符>逻辑运算符>赋值，所以1，先看 3>0为true,2，因为是||运算所以后面的$b=3>0 ~~形成短路作用，当前者表达式运算结算，结果为true，则$b = 3> 是不会再参与运算~~，这时的$a=true,$b=0;故$a++;为1；$b++;为1这里解释下布尔类型运算不影响布尔类型结果；但是$b=0;$b++;就改变为1， echo true；结果为1，

[参考文章,运算符优先级](https://www.php.net/manual/zh/language.operators.precedence.php)

## 列举任意几个获取当前请求和服务器信息的方法？

```php
// 获取客户端IP地址
$_SERVER['REMOTE_ADDR'];

// 服务端
gethostbyname(“www.baidu.com”);

// 获取客户端主机名
$_SERVER['REMOTE_HOST '];

// 客户端访问服务端应用端口号
$_SERVER['REMOTE_PORT '];
```

## $str是一段html文本，使用正则表达式去除其中的所有js脚本

```php
$pattern = ‘/<script.*>\.+<\/script>/’;
preg_replace($pattern,’’,$str);
```

## error_reporting() 的作用？

```php
error_reporting ([ int $level ] ) : int
```
error_reporting() 函数能够在运行时设置 error_reporting 指令。 PHP 有诸多错误级别，使用该函数可以设置在脚本运行时的级别。 如果没有设置可选参数 level， error_reporting() 仅会返回当前的错误报告级别。

[官方文档](https://www.php.net/manual/zh/function.error-reporting.php)

## 写出一个函数对文件目录做遍历

```php
function loopDir($dir){
    $handle = opendir($dir);
    while(false !==($file =readdir($handle))){
        if($file!='.'&&$file!='..'){
            echo $file."<br>";
            if(filetype($dir.'/'.$file)=='dir'){
                loopDir($dir.'/'.$file);
            }
        }
    }
}
$dir = '/';
loopDir($dir);
```
## 遍历某个目录下面的所有文件和文件夹(包含子文件夹的目录和文件也要依次读取出来)
```php
$dir = __DIR__;
function my_dir($dir) {
  $files = array();
  if(@$handle = opendir($dir)) {
      while(($file = readdir($handle)) !== false) {
          if($file != ".." && $file != ".") {
              if(is_dir($dir."/".$file)) { 
                  $files[$file] = my_dir($dir."/".$file);
              } else { 
                  $files[] = $file;
              }

          }
      }
      closedir($handle);
      return $files;
  }
}
print_r(my_dir($dir));
```

## 遍历某一个目录下面的文件和文件夹
```php
$dir = __DIR__;
if (is_dir($dir)) {
  $array = scandir($dir, 1);
  foreach($array as $key => $value) {
    if ($value == '.' || $value == '..') {
      unset($array[$key]);
      continue;
    }
  }
} else {
  echo '不是一个目录';
}

print_r($array);
```

## PHP中都有有哪些数据类型？

四种标量类型：boolean （布尔型）、integer （整型）、float （浮点型, 也称作 double)、string （字符串）。
两种复合类型：array （数组）、object （对象）。
最后是两种特殊类型：resource（资源）、NULL（NULL）。

## 请说明 PHP 中传值与传引用的区别，什么时候传值什么时候传引用?

按值传递：函数范围内对值的任何改变在函数外部都会被忽略
按引用传递：函数范围内对值的任何改变在函数外部也能反映出这些修改
优缺点：按值传递时，php必须复制值。特别是对于大型的字符串和对象来说，这将会是一个代价很大的操作。按引用传递则不需要复制值，对于性能提高很有好处。
使用场景：对于值的变动需要改变原值的情况，推荐使用引用传值，反之，则使用按值传递较为稳妥。
```php
$totalMoney = 0.00;// 用户余额

function addMoney($totalMoney)
{
	$totalMoney++;
    // 增加余额
   	echo $totalMoney.PHP_EOL;
}
addMoney($totalMoney);
echo $totalMoney;
// 1，0
```
常见真题:下列代码最终输出的结果是?
```php
// practice
$data = ['a', 'b', 'c'];

foreach ($data as $key => $val) {
	$val = &$data[$key];
}

var_dump($data);
// output
array(3) {
 [0]=>
 string(1) "b"
 [1]=>
 string(1) "c"
 [2]=>
 &string(1) "c"
}
```
![](/uploads/php-interview/images/m_3dab3914b4be7ccd848b6c6dd7e3deb7_r.png)
## 语句include和require的区别是什么？如果程序按需加载某个php文件你如何实现?

a. require
require是无条件包含，也就是如果一个流程里加入require，无论条件成立与否都会先执行require，当文件不存在或者无法打开的时候，会提示错误，并且会终止程序执行。

b. include
include有返回值，而require没有(可能因为如此require的速度比include快)，如果被包含的文件不存在的话，那么会提示一个错误，但是程序会继续执行下去。
[参考文章](https://www.laruence.com/2012/09/12/2765.html)
> get_included_files 和 get_require_files可以检测被加载的文件列表，返回的是一个数组。同时可以使用class_exists，function_exists来检测类或者函数是否存在，这样也可以检测文件是否被加载过。

## 静态化如何实现的？伪静态如何实现？

a. 静态化指的是页面静态化，也即生成实实在在的静态文件，也即不需要查询数据库就可以直接从文件中获取数据，指的是真静态。
**实现方式主要有两种：**
一种是我们在添加信息入库的时候就生成的静态文件，也称为模板替换技术。
一种是用户在访问我们的页面时先判断是否有对应的缓存文件存在，如果存在就读缓存，不存在就读数据库，同时生成缓存文件。

b. 伪静态不是真正意义上的静态化，之所以使用伪静态，主要是为了SEO推广，搜索引擎对动态的文件获取难度大，不利于网站的推广。实习原理是基于Apache或Nginx的rewrite机智。
**主要有两种方式：**
一种是直接在配置虚拟机的位置配置伪静态，这个每次修改完成后需要重启web服务器。
另一种采用分布式的，可以在网站的根目录上创建.htaccess的文件，在里面配置相应的重写规则来实现伪静态，这种每次重写时不需要重启web服务器，且结构上比较清晰。

## 简要单双引号的区别？

a. 单引号内部的变量不会执行， 双引号会执行。
b. 单引号解析速度比双引号快。
c. 单引号只能解析部分特殊字符，双引号可以解析所有特殊字符。

## 简单描述一下PHP7中的新特性有哪些？

a. 标量类型声明：PHP 7 中的函数的形参类型声明可以是标量了。在 PHP 5 中只能是类名、接口、array 或者 callable (PHP 5.4，即可以是函数，包括匿名函数)，现在也可以使用 string、int、float和 bool 了。

b. 返回值类型声明：增加了对返回类型声明的支持。 类似于参数类型声明，返回类型声明指明了函数返回值的类型。可用的类型与参数声明中可用的类型相同。

c. NULL 合并运算符：由于日常使用中存在大量同时使用三元表达式和 isset()的情况，NULL 合并运算符使得变量存在且值不为NULL， 它就会返回自身的值，否则返回它的第二个操作数。
```php
echo  $a ?? 1;
```

d. use 加强：从同一 namespace 导入的类、函数和常量现在可以通过单个 use 语句 一次性导入了。

e. 匿名类：现在支持通过new class 来实例化一个匿名类。

## 运行c.php文件，下列代码输出的结果是多少?
a,b,c 文件处于同级目录之下。
```php
// a.php
function demo () {
    echo 'a.php';
}
```

```php
// b.php
function demo () {
    echo 'b.php';
}
```

```php
// c.php
function demo () {
    echo 'c.php';
}
```
```php
// 输出结果
PHP Fatal error:  Cannot redeclare demo() (previously declared in /usr/local/var/www/demo/c.php:5) in /usr/local/var/www/demo/a.php on line 4
```
> 按照加载顺序，应该提示b和c文件中的方式属于redeclare，但是提示的是a。这里不难看出先解析c文件，在加载解析a和b文件，发现a中redeclare。

## 如何获取文件的后缀名称
```php
1.$file = 'x.y.z.png';
echo substr(strrchr($file, '.'), 1);
解析：strrchr($file, '.')   
 strrchr() 函数查找字符串在另一个字符串中最后一次出现的位置，
 并返回从该位置到字符串结尾的所有字符
2.$file = 'x.y.z.png';
echo substr($file, strrpos($file, '.')+1);
解析：strrpos($file, '.')   
查找 "." 在字符串中最后一次出现的位置,返回位置   substr()从该位置开始截取
3.$file = 'x.y.z.png';
$arr=explode('.', $file);
echo $arr[count($arr)-1];
4.$file = 'x.y.z.png';
$arr=explode('.', $file);
echo end($arr);  //end()返回数组的最后一个元素
5.$file = 'x.y.z.png';
echo strrev(explode('.', strrev($file))[0]);
6.$file = 'x.y.z.png';
echo pathinfo($file)['extension'];
解析：pathinfo() 函数以数组的形式返回文件路径的信息。包括以下的数组元素：
[dirname]
[basename]
[extension]
7.$file = 'x.y.z.png';
echo pathinfo($file, PATHINFO_EXTENSION);
总结：字符串截取2种，数组分割3种，路径函数2种
```

## PHP如何正确截取带有中文的字符串
```php
$str = 'a你好啊bcd';
mb_substr($str, 2);
输出结果：好啊bcd
```
> mb_substr是一个支持多字节安全的字符串截取函数，而substr则不是。

## PHP生成一个大数组，并且占用内存最少

```php
/**
 * 使用生成器构造生成对象，查看对内存的消耗
 * @param $start
 * @param $end
 * @param int $step
 * @return Generator
 */
function xrange($start, $end, $step=1)
{
    for($i=$start; $i<$end; $i+=$step)
    {
        yield $i;
    }
}
foreach (xrange(0, 1000000) as $item)
{
    echo $item . '<br />';
}
```

## 给你两个路径a和b,写一个算法或思路计算a和b差距几层并显示a和b的交集?

给定两个路径，首先我们能保证这两个路径的跟路径("/")是一致的，至于显示几层，我们就可以路径格式进行转换为数组，
方便后面比较交集。至于相差几层，对于两个数组直接比较长度即可。

```php
$a = "/Users/kert";
$b = "/usr/sbin";

$aArray = array_filter(explode("/", $a));
$bArray = array_filter(explode("/", $b));

//var_dump($aArray, $bArray);

// 计算相差的层级
$cDiffLenght = count($aArray) - count($bArray);
echo "a路径和b路径相差的层级是:" . abs($cDiffLenght) . PHP_EOL;

// 计算两个的交集
$dMerge = array_intersect($bArray, $aArray);
var_dump("a路径和b路径的交集是:", $dMerge);
```
```php
// output
a路径和b路径相差的层级是:3
string(30) "a路径和b路径的交集是:"
array(2) {
  [1]=>
  string(5) "Users"
  [2]=>
  string(4) "kert"
}
```

## 如何获取今天是本月所在的第几周

```php
echo ceil(date('d')/7);
```

## post与get的区别？
[参考文章](https://segmentfault.com/a/1190000018129846)

2.COOKIE和SESSION的区别和关系？

cookie和session都是属于一种会话机制，但两者存储的位置不同，因此在使用时，也会根据实际情况考虑。分别从如下几个方便考虑:

a.存储位置：COOKIE保存在客户端，而SESSION则保存在服务器端。

b.安全性：从安全性来讲，SESSION的安全性更高。

c.存储内容：从保存内容的类型的角度来讲，COOKIE只保存字符串（及能够自动转换成字符串）。

d.内容大小：浏览器存储的cookie数量一般都是有限制的，不同的浏览器存储的数量限制也不同。从保存内容的大小来看，COOKIE保存的内容是有限的，比较小，而SESSION基本上没有这个限制。

e.性能：从性能的角度来讲，用SESSION的话，对服务器的压力会更大一些。

f.生成关联：SEEION依赖于COOKIE，但如果禁用COOKIE，也可以通过url传递(该情况发生在客户端禁用cookie)。

g.shen生成方式：创建cookie的指令有服务端生成，创建由客户端执行。客户端(JavaScript)也可以操作cookie，当服务端生成cookie指令时，可以通过httponly参数禁止客户端操作cookie。

3.Cookie存在哪？

a. 如果设置了过期时间，Cookie存在硬盘里。

b.没有设置过期时间，Cookie存在内存里。

## HTTP 协议的工作特点和工作原理？

4.1 工作特点。

a. 基于 B/S 模式。
这里的是 B 是指浏览器(brow)，S 是指服务端(server)。浏览器作为客户端向服务端发送请求，服务端在接受请求之后响应给浏览器即可。

b. 通信开销小、简单快速和传输成本低。

c. 使用简单。
客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、HEAD、POST。每种方法规定了客户与服务器联系的类型不同。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。

d. 使用灵活、可使用超文本传输协议。
HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type（Content-Type是HTTP包中用来表示内容类型的标识）加以标记。

e. 无状态。
无状态是指协议对于事务处理没有记忆能力，服务器不知道客户端是什么状态。即我们给服务器发送 HTTP 请求之后，服务器根据请求，会给我们发送数据过来，但是，发送完，不会记录任何信息。
> HTTP 是一个无状态协议，这意味着每个请求都是独立的，Keep-Alive 没能改变这个结果。
缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。
HTTP 协议这种特性有优点也有缺点，优点在于解放了服务器，每一次请求“点到为止”不会造成不必要连接占用，缺点在于每次请求会传输大量重复的内容信息。
客户端与服务器进行动态交互的 Web 应用程序出现之后，HTTP 无状态的特性严重阻碍了这些应用程序的实现，毕竟交互是需要承前启后的，简单的购物车程序也要知道用户到底在之前选择了什么商品。于是，两种用于保持 HTTP 连接状态的技术就应运而生了，一个是 Cookie，而另一个则是 Session。
Cookie可以保持登录信息到用户下次与服务器的会话，换句话说，下次访问同一网站时，用户会发现不必输入用户名和密码就已经登录了（当然，不排除用户手工删除Cookie）。而还有一些Cookie在用户退出会话的时候就被删除了，这样可以有效保护个人隐私。
Cookies 最典型的应用是判定注册用户是否已经登录网站，用户可能会得到提示，是否在下一次进入此网站时保留用户信息以便简化登录手续，这些都是 Cookies 的功用。另一个重要应用场合是“购物车”之类处理。用户可能会在一段时间内在同一家网站的不同页面中选择不同的商品，这些信息都会写入 Cookies，以便在最后付款时提取信息。
与 Cookie 相对的一个解决方案是 Session，它是通过服务器来保持状态的。
当客户端访问服务器时，服务器根据需求设置 Session，将会话信息保存在服务器上，同时将标示 Session 的 SessionId 传递给客户端浏览器，浏览器将这个 SessionId 保存在内存中，我们称之为无过期时间的 Cookie。浏览器关闭后，这个 Cookie 就会被清掉，它不会存在于用户的 Cookie 临时文件。
以后浏览器每次请求都会额外加上这个参数值，服务器会根据这个 SessionId，就能取得客户端的数据信息。
如果客户端浏览器意外关闭，服务器保存的 Session 数据不是立即释放，此时数据还会存在，只要我们知道那个 SessionId，就可以继续通过请求获得此 Session 的信息，因为此时后台的 Session 还存在，当然我们可以设置一个 Session 超时时间，一旦超过规定时间没有客户端请求时，服务器就会清除对应 SessionId 的 Session 信息。

f. 无连接，节省传输时间。
无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。

> 早期这么做的原因是 HTTP 协议产生于互联网，因此服务器需要处理同时面向全世界数十万、上百万客户端的网页访问，但每个客户端（即浏览器）与服务器之间交换数据的间歇性较大（即传输具有突发性、瞬时性），并且网页浏览的联想性、发散性导致两次传送的数据关联性很低，大部分通道实际上会很空闲、无端占用资源。因此 HTTP 的设计者有意利用这种特点将协议设计为请求时建连接、请求完释放连接，以尽快将资源释放出来服务其他客户端。
随着时间的推移，网页变得越来越复杂，里面可能嵌入了很多图片，这时候每次访问图片都需要建立一次 TCP 连接就显得很低效。后来，Keep-Alive 被提出用来解决这效率低的问题。
Keep-Alive 功能使客户端到服务器端的连接持续有效，当出现对服务器的后继请求时，Keep-Alive 功能避免了建立或者重新建立连接。市场上的大部分 Web 服务器，包括 iPlanet、IIS 和 Apache，都支持 HTTP Keep-Alive。对于提供静态内容的网站来说，这个功能通常很有用。但是，对于负担较重的网站来说，这里存在另外一个问题：虽然为客户保留打开的连接有一定的好处，但它同样影响了性能，因为在处理暂停期间，本来可以释放的资源仍旧被占用。当Web服务器和应用服务器在同一台机器上运行时，Keep-Alive 功能对资源利用的影响尤其突出。 
这样一来，客户端和服务器之间的 HTTP 连接就会被保持，不会断开（超过 Keep-Alive 规定的时间，意外断电等情况除外），当客户端发送另外一个请求时，就使用这条已经建立的连接。

4.2 工作原理。
客户端向服务端发送请求时，先创建一个 tcp 连接，指定向某个端口发送请求。服务器网卡接受到对应的请求后，根据端口号将请求信息转发给对应的服务处理，服务在接收到请求信息后，根据请求信息进行处理，处理完毕之后将相应状态信息和数据信息返回给客户端，客户在就收信息之后在对数据进行展示活其他的业务处理。

4.3 HTTPS 是工作原理。
HTTPS 是一种基于SSL/TLS 的一种HTTP加密传输协议，所有的 HTTP 传输数据都是在 SSL/TLS 协议封装之上进行传输的。
HTTPS在 HTTP 协议基础之上，添加了 SSL 和 TLS 握手以及数据加密传输，同样也属于一种应用层传协议。

## HTTP 协议常见的请求方法以及含义？

GET:向服务端获取资源信息，进行展现。如文章列表拉取、商品信息展示等业务场景。

POST:向服务端发送创建性质，进行信息提交、数据创建。如用户注册、用户下订单等业务场景。

DELETE:向服务端发送删除某URL指定内容的请求。如订单历史记录的删除等业务场景。

PUT:向服务端发送一个写入类型的请求。在语义上，如果文档不存在则创建、存在则更新。如用户信息修改、购物车数量减少等业务场景。

HEAD:类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头，服务端需要将请求的报文头信息原样返回。例如判断某个资源是否存在，资源类型等业务场景。

OPTIONS:向服务端发送是否支持某种功能的请求，以验证服务器性能、请求要求、是否接受客户端请求等操作。如跨域请求等业务场景。

TRACE:回显服务器端接受到客户端的请求信息，以达到检测客户端在请求的过程中是否被中间的 HTTP 请求串改了请求信息。主要用于测试或诊断。