### 1. 本视频一开始介绍了哪两个工具，他们的作用分别是什么？为什么作者会推荐firefox，它的优点是什么？（5分）
> 工具一：Burpsuite

* 作用：可以对http和https进行监控修改(也就是进行代理抓包，改包)还可以爆破，编码，爬虫。。。

> 工具二：Firefox浏览器

* 作用：
推荐原因：在做请求分析处理时可以轻松实现浏览器代理设置(google也有吧)，而无需对系统做其他设置，直接配合bp捕获的事web应用流量而不是其他系统的一些流量，另外，他还包含一些开发调试工具，可以方便查看cookie和检查dom等

### 2.本视频中体现了哪些攻防上的哲学观点？作者希望你养成什么样的思维？这些思维在帮助你挖掘漏洞的时候有什么帮助？结合你的经历与视频内容谈谈你的看法。（10分）
#### 打破思维定势的一些相关思路：

* 对于一个陌生的应用程序，要敢于去直接点击他的任何按钮，以最快的速度熟悉掌握了解，以便于发现他的一些弱点

* 攻防双方在安全的职责上本就存在一种不平衡
    
    对于防御者，必须发现所有的系统漏洞
    
    对于攻击者，只需要找到一个或者几个就可以
    
* 因此，我们需要在发现漏洞尤其是一些高级漏洞这一方面投入大量的精力，最大程度降低攻击影响


> 重要方法：

1. 换位思考攻击者最想得到什么：
    * 要去发现目标应用的现行功能
    * 针对每一个功能考虑攻击者目标会具体对什么形成危险,因此对于每个功能一定的漏洞都有不同等级的区别
  例：
    如果要攻击目标是电子商务网站，那么攻击者目标是持卡人的信息
   
    * 如果遇到开发人员，可以尝试问他们最关心的问题是什么，以此开发自己的思路

2. 自己的想法：

    * 我觉得攻防就是一个时间竞争和技术比拼的问题，对于一个漏洞如果被遭到攻击，蓝队也会及时发现，这几乎就开始了技术以及时间的较量，当然也有可能，红队存储发现的漏洞，集中攻打，这就要求蓝队事先用大量的精力发现自己的所有漏洞，并且把自己当成一名红队成员，想方设法寻找未发现的漏洞
    
    * 面对新的软件，要想更多的了解，利用他们，就是各种的试探，各种的收集，找到个功能可能会存在的危险


### 3.漏洞报告的写法：
基本要素：
  标题
  严重程度：
      信息级：代表漏洞问题目前没有实质安全影响，但可能会慢慢加剧
      低危：对系统影响较小
      中危：可能对系统业务存在潜在的危险，但不会泄露关键数据
      高危：会对关键信息形成泄漏，或者是与其他漏洞综合利用
      严重：极可能泄漏个人隐私或者是机密信息
  漏洞描述
  漏洞复现步骤
  漏洞影响
  缓解措施
  受影响的网站
  
  
### 4.审计以下代码：
```
<?php
if(isset($_GET[ ' name ' ])){
echo "<h1>Hello {$_GET['name']} !</h1>";
}
?>
<form method="GET">
Enter your name: <input type="input" name="name"><br>
<input type=" submit">
```

本段代码涉及到客户端，服务端以及通信协议。

运行在客户端的代码主要有HTML以及javascript，由浏览器核心负责解释
 
通信协议为HTTP协议，有多种格式的请求包，常见的为POST与GET
 
运行在服务端的代码为php，由php核心负责解释。
 
用户端与服务端通过HTTP通信协议进行交互。
 
* 那么，以上代码中，哪些部分属于客户端的内容，哪些属于服务端的内容？（1分）
 ```
 <?php ?>包裹的代码输入服务端
 <form> 后面是我们要提交的表单
 ```
客户端是通过传递什么参数来控制服务端代码的？（1分）
 ```
  表单input标签中的name属性用来接受客户输入的值，并将其传给服务端
 ```
客户端通过控制该参数会对服务端造成什么影响，继而使得客户端本身收到影响，从而造成了什么漏洞？如果是xss漏洞，具体又是什么类型的xss漏洞，为什么？（3分）
```
 1. 输入的name会被服务端解析，正如echo里面的<d>标签一样，html会被解析，那么javascript的标签也会被解析执行，这样客户就可以通过输入js代码让服务器执行一些意想不到的行为，造成了xss漏洞
 2. 由于只是简单地把用户输入的数据反射给浏览器,简单来说，黑客往往需要去诱使用户点击一个恶意链接，才能攻击成功。所以应该是反射性xss漏洞
```
* 思考：现实中如何利用xss漏洞实施攻击，我们应该如何预防？（1分）

反射性xss：不停地进行弹窗等操作，干扰客户，可以做钓鱼网站，诱使其点击链接

Dom型xss：修改页面节点，不过后端不会被后端过滤

存储型xss：提取用户数据，比如留言

预防：
* 对输入的特定字符进行编码

* 对敏感字过滤

* 制作白名单...


## php伪协议

常见的文件包含函数:include、require、include_once、require_once、highlight_file 、show_source 、readfile 、file_get_contents 、fopen 、file


协议概要：file:// php://filter php://input zip:// compress.bzip2://、compress.zlib://、data://


环境概要：

php.ini:

allow_url_fopen: on 默认开启，on的意思是激活url形式的fopen封装协议是的可以访问url对象文件等

allow_url_include: off 默认关闭，该选项on便是允许包含url 对象文件等。


> file://协议
PHP.ini:

file:/该协议在双off的情况下也可以使用

allow_url_fopen: on/off

allow_url_include: on/off

file://用于访问本地文件系统，在ctf中通常用来读取本地文件且不受allow_url_fopen与allow_url_include的限制

用法:

* /path/to/file.txt
* relative/path/to/file.txt
* fileInCwd.ext
* C:/path/to/winfile.ext
* C:\path\to\winfile.txt
* \\smbserver\share\path\to\winfile.ext
* file:///path/to/file.ext

![image](https://user-images.githubusercontent.com/76896357/115543619-26a44b80-a2d4-11eb-90e6-ebefc2362e1c.png)

> php://协议
条件:
不需要开启allow_url_fopen,

php://访问各个输入输出流(I/O stream),在ctf尝试用的是php://filter和php://input,php://filter用于读取源码，php://input用于执行php代码

> php://filter协议

读取源代码并进行base64编码输出。不然会直接当作php代码执行就看不到源代码内容


PHP.ini：

php://filter在双off的情况下也可以正常使用；

allow_url_fopen ：off/on

allow_url_include：off/on

**对所要传输的数据进行一定规则的过滤**

#### 使用

```
<?php
    $file1=$_GET['file1'];
    $file2=$_GET['file2'];
    $txt = $_GET['txt'];
    echo file_get_contents($file1);
    file_put_contents($file2,$txt)
?>
```
* file_get_contents --将整个文件读入一个字符串
* file_put_contents --将字符串写入文件

* 读取文件

```
# 明文读取
index.php?file1=php://filter/resource=file.txt

# 编码读取
index.php?file=php://filter/read=covert.base64-encode/resource=file.txt
```

* 写入文件

```
# 明文写入
index.php?file2=php://filter/resource=test.txt

# 编码写入
index.php?file2=php://filter/write=convert.base64-encode/resource=test.txt&txt=helloworld
```

## 字符串过滤器

* **spring.rot13**

    str_rot13--对字符串执行rot13转换，ROT13编码简单滴使用字母表中后面第13个字母替换当前字母，同时忽略非字母表中的字符，编码和解码都是用相同的函数，传递一个编码过的字符串作为参数，得到原始的字符串。

* **string.toupper**

使用此过滤器等同于strtoupper()函数处理所有的数据流。

---将字符串转换为大写

* **string.tolower**

等同于strlower()函数处理流数据


* **string.strip_tags**
 
可以用两种格式接受参数，一种是和stip_tags()函数第二个参数相似的一个包含有标记列表的字符串，另一种是包含有标记名的数组。

- strip_tags--从字符中去除HTML和PHP标记，该函数尝试返回给定的字符串str去除空字符，html，php标记的结果使用与fgetss()一样的机制去除标记.
 
 
 注释：该函数始终会剥离 HTML 注释。这点无法通过 allow 参数改变
 
![image](https://user-images.githubusercontent.com/76896357/115666915-c1099b00-a377-11eb-8eb4-b63621306c21.png)

运行实例：

![image](https://user-images.githubusercontent.com/76896357/115666954-cff04d80-a377-11eb-9e91-db8aa4a54fa8.png)



### 转换过滤器

* **convert.base64**

convert.base64-encode和convert.base64-decode使用这两个过滤器等同于用base64_encode和base64_decode函数处理数据 convert.base64-encode支持以一个关
联数组给出的参数。如果给出了 line-length，base64 输出将被用 line-length个字符为长度而截成块。
如果给出了 line-break-chars，每块将被用给出的字符隔开。这些参数的效果和用 base64_encode()再
加上 chunk_split()相同。
 
 
 
* **convert.quoted**

convert.quoted-printable-encode 和 convert.quoted-printable-decode 使用此过滤器的 decode
版本等同于用 quoted_printable_decode()函数处理所有的流数据。没有和 convert.quoted-printableencode相对应的函数。 convert.quoted-printable-encode支持以一个关联数组给出的参数。除了支持和 convert.base64-encode一样的附加参数外,convert.quoted-printable-encode还支持布尔参数
binary和 force-encode-first。 convert.base64-decode只支持 line-break-chars参数作为从编码载荷中
剥离的类型提示。

* **convert.iconv**

这个过滤器需要php支持iconv，而iconv是默认编译的。使用convert.iconv.* 等同于使用iconv()函数处理流数据

- iconv--字符串按要求的字符编码来转换:

两种方法:

```
convert.iconv.<input-encoding>.<output-encoding>
or
convert.iconv.<input-encoding>/<output-encoding>
```


![image](https://user-images.githubusercontent.com/76896357/115669842-4f335080-a37b-11eb-8a4f-338a4e9ec9f2.png)

支持的字符编码
```
UCS-4* 
UCS-4BE 
UCS-4LE* * 
UCS-2 
UCS-2BE 
UCS-2LE 
UTF-32* 
UTF-32BE* 
UTF-32LE* 
UTF-16* 
UTF-16BE* 
UTF-16LE* 
UTF-7 
UTF7-IMAP 
UTF-8* 
ASCII*
```

### 压缩过滤器：

虽然 压缩封装协议 提供了在本地文件系统中 创建 gzip 和 bz2 兼容文件的方法，但不代表可以在网络的
流中提供通用压缩的意思，也不代表可以将一个非压缩的流转换成一个压缩流。对此，压缩过滤器可以
在任何时候应用于任何流资源。


```
Note: 压缩过滤器 不产生命令行工具如 gzip的头和尾信息。只是压缩和解压数据流中的有效载荷部 分。zlib.* 压缩过滤器自 PHP 版本 5.1.0起可用，在激活 zlib的前提下。也可以通过安装来自 » PECL的 » zlib_filter包作为一个后门在 5.0.x版中使用。此过滤器在 PHP 4 中 不可用。
```

![image](https://user-images.githubusercontent.com/76896357/115670866-776f7f00-a37c-11eb-9dce-8556e80c279e.png)

### 加密过滤器：

crypt.*和 mdecrypt.*使用 libmcrypt 提供了对称的加密和解密。这两组过滤器都支持 mcrypt 扩展库中相同的算法，格式为 mcrypt.ciphername，其中 ciphername是密码的名字，将被传递给 mcrypt_module_open()。有以下五个过滤器参数可用：

![image](https://user-images.githubusercontent.com/76896357/115671728-65daa700-a37d-11eb-880f-f334560979c8.png)

细则查看加密方式：

https://www.php.net/manual/zh/filters.encryption.php
