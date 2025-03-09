# Company Website CMS

Company Website CMS creates /dashboard/Services pages with unlimited back-end code, resulting in XSS vulnerabilities.

The back-end code is:

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

You can see that there are some requirements, such as length greater than 150, but the most important thing here is whether there is a filter for XSS, you can see that here is the use of 'mysqli_real_escape_string()' to prevent sql injection, from the code logic point of view, I ended up inserting my input into the database, so it makes sense, but does it prevent XSS?

The `mysqli_real_escape_string` function here mainly escapes the following characters: `NUL (ASCII 0)`, `\n`, `\r`, `\`,`'`, `"`and `CTRL+Z `.

Do not contain XSS characters, then directly pop-up try:

```javascript
<script>alert(1)</script>
```

Edit directly in the background Services:

![image-20250309182831175](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092202495.png)

Then change the content:

![image-20250309182845066](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092202505.png)

The front page is:

![image-20250309220413269](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092204316.png)

Finally clicking on this content triggers XSS:

![image-20250309182943208](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092202609.png)

Looking at the source code, you can see that it was successfully parsed:

![image-20250309183123063](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092202510.png)

Success! xss vulnerability exists.

——————