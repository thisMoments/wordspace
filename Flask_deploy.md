Flask的部署方式与Django类似

1,更新ubuntu源

    sudo apt-get update

2,安装mysql

    sudo apt install mysql-server mysql-client

3, 修改mysql配置

	  cd进入 /etc/mysql/mysql.conf.d
	  注释 mysqld.conf 里的 bind_address

4, 修改配置
	
	mysql -u root -p

	use mysql；

	GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;

	flush privileges; 

5,重启mysql

	service mysql restart


6,安装Nginx：

	sudo apt-get install nginx

7.,安装pip3

	apt install python3-pip

	访问公网IP地址  welcome to nginx！ 则表示成功


8,安装uWSGI以及uWSGI对于Python的支持：

	pip3 install uwsgi

9,修改总的nginx的配置的文件

	vim  /etc/nginx/nginx.conf

10,配置nginx的文件
```python
server {
    listen       80;
    server_name 47.96.130.236 localhost;

	access_log /home/app/logs/access.log;
	error_log /home/app/logs/error.log;

	ocation / {
	    include uwsgi_params;
	    uwsgi_pass 127.0.0.1:8890;
	
	    uwsgi_param UWSGI_CHDIR /home/app/src/ihome;

	    # 启动flask的文件:Flask的实例
	    uwsgi_param UWSGI_SCRIPT manage:app;
	    }
	}
```

11,配置uwsgi的文件

	[uwsgi]

	socket=127.0.0.1:8890

	pythonpath=/home/app/src/ihome;  #项目所在目录

	callable=app;  # 回调的flask实例

	logto = /home/app/logs/uwsgi.log  # 存uwsgi日志的文件地址

12,启动方式：

     uwsgi –ini uwsgi.ini
     或则
     uwsgi  /home/app/src/ihome/uwsgi.ini
