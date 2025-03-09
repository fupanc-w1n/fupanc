# Company Website CMS

Company Website CMS在创建/dashboard/Services页面，后端代码无限制，导致XSS漏洞。



后端代码为：

```php
if(ISSET($_POST['save'])){
$service_title = mysqli_real_escape_string($con,$_POST['service_title']);
$service_desc = mysqli_real_escape_string($con,$_POST['service_desc']);
$service_detail = mysqli_real_escape_string($con,$_POST['service_detail']);

 if ( strlen($service_title) < 5 ){
$msg=$msg."Service Title Must Be More Than 5 Char Length.<BR>";
$status= "NOTOK";}
 if ( strlen($service_desc) > 150 ){
$msg=$msg."Short description Must Be Less Than 150 Char Length.<BR>";
$status= "NOTOK";}

if ( strlen($service_detail) < 15 ){
  $msg=$msg."Service Detail Must Be More Than 15 Char Length.<BR>";
  $status= "NOTOK";}
//这里是去掉了文件上传逻辑

if($status=="OK")
{
$qb=mysqli_query($con,"INSERT INTO service (service_title, service_desc, service_detail,ufile) VALUES ('$service_title', '$service_desc', '$service_detail', '$new_file_name')");

```

可以看到这里是有一些要求的，比如长度大于150呀等的，但是这里最关键的是是否有对XSS的过滤，可以看到这里是使用的`mysqli_real_escape_string()`来防止sql注入的，从代码逻辑上看，最后是将我传入的内容插入进了数据库的，所以这里很合理，但是这里又是否有效防止了XSS呢？

这里的`mysqli_real_escape_string`函数主要是转义如下几个字符：`NUL (ASCII 0)`、 `\n`、`\r`、`\`、 `'`、`"` 和`CTRL+Z`。

不包含XSS的字符，那么直接弹窗试试：

```javascript
<script>alert(1)</script>
```

在后台的Services直接编辑就行了：

![image-20250309182831175](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092202495.png)

然后更改内容：

![image-20250309182845066](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092202505.png)

前台页面为：

![image-20250309220413269](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092204316.png)

最后点开这个内容就触发XSS：

![image-20250309182943208](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092202609.png)

查看源代码可以看到是成功解析了的：

![image-20250309183123063](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092202510.png)

成功！存在xss漏洞。

——————