## 基于Ubunto的两种项目部署方式

#### 部署方式1：

1. 安装包

        sudo apt update
        apt install mysql-server mysql-client

2. 设置远程访问mysql

	    a) 查找 mysql.conf 
		    find / -name mysql.cnf
	    b）注释mysql.cof文件的bind_address
		    /etc/mysql/mysql.conf.d
        c）GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root@123' WITH GRANT OPTION;
            flush privileges; 

3. 修改django的配置文件

        a）修改settings.py文件中的DEBUG=FALSE，ALLOWED_HOST=['*']
        b）修改urls.py
         b1）from django.views.static import serve
		    url中加入以下配置
		    url(r'^static/(?P<path>.*)$', serve, {"document_root": settings.STATIC_ROOT}),
		    url(r'^media/(?P<path>.*)$', serve, {"document_root": settings.MEDIA_ROOT}),
		  b2）setting中
		    STATIC_ROOT = os.path.join(BASE_DIR, 'static')
		  b3）url中修改首页访问的地址
		    url(r'^$', views.home)

4. 修改首页的启动地址

        修改工程目录中的url ，并修改url(r'^$', views.home)

5. 安装pip3

        apt install python3-pip

6. 安装必备库
	
	    pip3 install django==1.11
	    pip3 install pymysql
	    pip3 install Pillow

7. 查看进程
 	
 	    netstat -lntp

5. 启动项目
	
	    python3 manage.py runserver

#### 部署方式2：

使用nginx+uwsgi配置django项目

1. 安装nginx

        sudo apt-get install nginx
	
2. 查看nginx的状态

        systemctl status nginx 查看nginx的状态
        systemctl start/stop/enable/disable nginx 启动/关闭/设置开机启动/禁止开机启动

        service nginx status/stop/restart/start

3. 安装uwsgi

        pip install uwsgi

4. nginx的配置文件中加载自定义的nginx的配置文件

	    vim /etc/nginx/nginx.conf
	    在server中加入以下配置：
	        include /home/app/conf/*.conf;


5. 配置自定义的nginx配置文件

```
server {
	listen       80;
	server_name 47.92.164.198 localhost;

	access_log /home/app/log/access.log;
	error_log /home/app/log/error.log;

	location / {
	    include uwsgi_params;
	    uwsgi_pass 127.0.0.1:8890;
	}
	location /static/ {
	    alias /home/app/day11axf0/static/;
	    expires 30d;
	}
}
```

6.配置uwsgi，名称为uwsgi.ini

        [uwsgi]
        # variables
        projectname = day11axf0
        newprojectname = day11axf
        base = /home/app
        # config
        #plugins = python
        master = true
        #protocol = uwsgi
        processes = 4
        #env = DJANGO_SETTINGS_MODULE=%(projectname).settings
        pythonpath = %(base)/%(projectname)
        module = %(newprojectname).wsgi
        socket = 127.0.0.1:8890
        logto = %(base)/log/uwsgi.log

7.启动方式： uwsgi --ini uwsgi.ini

### 服务器状态码及其含义
     100——客户必须继续发出请求
     101——客户要求服务器根据请求转换HTTP协议版本
     200——请求成功
     201——提示知道新文件的URL
     202——接受和处理、但处理未完成
     203——返回信息不确定或不完整
     204——请求收到，但返回信息为空
     205——服务器完成了请求，用户代理必须复位当前已经浏览过的文件
     206——服务器已经完成了部分用户的GET请求
     300——请求的资源可在多处得到
     301——删除请求数据
     302——在其他地址发现了请求数据
     303——建议客户访问其他URL或访问方式
     304——客户端已经执行了GET，但文件未变化
     305——请求的资源必须从服务器指定的地址得到
     306——前一版本HTTP中使用的代码，现行版本中不再使用
     307——申明请求的资源临时性删除
     400——错误请求，如语法错误
     401——请求授权失败
     402——保留有效ChargeTo头响应
     403——请求不允许
     404——没有发现文件、查询或URl
     405——用户在Request-Line字段定义的方法不允许
     406——根据用户发送的Accept拖，请求资源不可访问
     407——类似401，用户必须首先在代理服务器上得到授权
     408——客户端没有在用户指定的时间内完成请求
     409——对当前资源状态，请求不能完成
     410——服务器上不再有此资源且无进一步的参考地址
     411——服务器拒绝用户定义的Content-Length属性请求
     412——一个或多个请求头字段在当前请求中错误
     413——请求的资源大于服务器允许的大小
     414——请求的资源URL长于服务器允许的长度
     415——请求资源不支持请求项目格式
     416——请求中包含Range请求头字段，在当前请求资源范围内没有range指示值，请求也不包含If-Range请求头字段
     417——服务器不满足请求Expect头字段指定的期望值，如果是代理服务器，可能是下一级服务器不能满足请求
     500——服务器产生内部错误
     501——服务器不支持请求的函数
     502——服务器暂时不可用，有时是为了防止发生系统过载
     503——服务器过载或暂停维修
     504——关口过载，服务器使用另一个关口或服务来响应用户，等待时间设定值较
     505——服务器不支持或拒绝支请求头中指定的HTTP版本
