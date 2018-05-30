## 概念 ##
    与jango（完善完整高集成的框架）不同的是，
    flask是'微'框架（不包含数据库抽象层框架，database，templates需要自己去组装）
## 安装 ##
最简单的方式就是 virtualenv（需要一个Python2.6或更高的版本）
1. 创建虚拟环境

    virtualenv --no-site-packages flaskenv
    cd claskenv
    cd Scripts
    activate
2.安装flask

    pip install flask
3.运行flask

    python xxxx.py --> 启动默认127.0.0.1:5000

- 一个最小的Flask文件：
```
from flask import Flask  # 导入类Flask
app = Flask(__name__)  # 初始化，__name__ 代表主模块名或则包

# 路由/装饰器,把一个函数绑定到一个 URL 上
@app.route('/')
def hello_world():
    return 'Hello World!'  # 视图函数

if __name__ == '__main__':
    app.run()  # 启动项目
```
*注意：* 文件保存名不能为flask.py，否则会与Flask本身冲突

4.运行参数
```
    app.run(port='8000'， host='0.0.0.0') 
    # port指定端口为8000，host主机ip地址0.0.0.0
    # 实际项目中不要使用 debug=True
```
5.修改启动方式

    pip install flask-script
    python hello.py runserver -p 端口 -h ip地址 -d

- 调试模式

        run() 方法适用于启动一个本地开发服务器，但是需要在修改代码后手动重启服务器。
        如果启用了调试模式，在代码修改的时候服务器能够自动加载， 并且如果发生错误，它会提供一个有用的调试器。

        方式1：
          app.debug = True
          app.run()
        
        方式2：
          app.run(debug=True)
 *注意：* 在实际项目应用中不要使用调试模式！

## route规则 ##
```
@app.route('/stu/<int:id>/')
def show_stu(id):

    return 'stu %d' % id
```
    int      整型
    float    浮点数
    strint   默认的字符串
    
    path     '/'也是当成字符串返回
    uuid     uuid类型

- urls两种方式

```
# 第一种方式
@app.route('/stu/')
def stu():
    return 'hello, stu'

# 第二种方式
@app.route('/world')
def world():
    return 'hello, world'
```
    第一种方式，url 后面有一个斜线
    访问一个结尾不带斜线的 URL，Flask 会重定向到带斜线的 URL

    第二种方式的 URL 结尾不带斜线
    这时候访问结尾带斜线的 URL，会产生一个 404 “Not Found” 错误
    
    通常我们使用第一种方式！

- 重定向 
用 redirect（）函数重定向用户到其他地方
```
from flask import redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('index'))
```
## 模板 ##
```
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```
- 基础模板
```
{# base.html #}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
    <title>
        {% block title %} {% endblock %}
    </title>
    {% block extCSS %} {% endblock %}
</head>
<body>
{#  {% block %} 标签定义了四个子模板可以重载的块 #}
{% block nav %} {% endblock %}
{% block content %} {% endblock %}
{% block footer %} {% endblock %}
{% block extJS %} {% endblock %}
</body>
</html>
```
- 子模板1
```
{# base_main.html #}

{# {% extends %}标签必须是子模板中的第一个标签，这里表示子模板继承了'base.html'模板 #}
{% extends 'base.html' %}

{% block extCSS %}
    <link rel="stulesheet" href="{{ url_for('static', filename='css/home.css') }}">
{% endblock %}
```
- 子模板2
```
{# show_stu.html #}

{# 子模板2继承了子模板1，同时也继承了子模板1所继承的基础模板 #}
{% extends 'base_main.html' %}

{% block title %} 学生信息 {% endblock %}
{% block content %}
<ul>
    {% for stu in stus %}
        <li>
            id: {{ stu.s_id }}
            姓名: {{ stu.s_name }}
            年龄: {{ stu.s_age }}
        </li>
    {% endfor %}
</ul>
{% endblock %}
```

## HTTP方法 ##

默认情况下，路由只会响应 GET 请求， 但是能够通过给 route() 装饰器提供 methods 参数来改变，eg：
```
@app.route('/login', methods=['GET', 'POST'])
# 'POST'也可以换成'PUT'
# 同 POST 类似，但是服务器可能触发了多次存储过程，多次覆盖掉旧值
# 在传输过程中连接丢失的情况下，浏览器和服务器之间的系统可以安全地第二次接收请求，而不破坏其它东西
# POST 是不可能实现的，因为它只会被触发一次
def login():
    if request.method == 'POST':
        return render_template('create_stu.html')
    else:
        return render_template('index.html')
```


## 蓝图（blueprint） ##
-- 管理url，规划url
概念：在一个应用或者跨应用中构建应用组件以及支持通用模式

蓝图在Flask中的主要作用：

 - 把一个应用分解成一系列的蓝图。一个项目能实例化一个应用， 初始化一些扩展，以及注册一系列的蓝图
 - 以一个 URL 前缀和/或子域在一个应用上注册蓝图。 URL 前缀/子域名中的参数即成为这个蓝图下的所有视图函数的共同的视图参数（默认情况下）
 - 在一个应用中用不同的 URL 规则多次注册一个蓝图
 - 通过蓝图提供模板过滤器、静态文件、模板和其它功能。一个蓝图不一定要实现应用或视图函数
 - 初始化一个 Flask 扩展时，在这些情况中注册蓝图

1.安装

    pip install flask-blueprint
    
2.初始化
```
from flask import Blueprint

blue = Blueprint('first', __name__)
```
```
# 修改
@app.route('/')
def hello_world():
    return 'Hello World!'

# 为
@blue.route('/')
def hello_world():
    return 'Hello World!'
```
      
3.路由注册
```
from flask import Flask

def create_app():
    app = Flask(__name__)
    app.register_blueprint(blueprint=blue)

    return app
```

- 静态文件
一个蓝图可以通过 static_folder 关键字参数提供一个指向文件系统上文件夹的路 径，来公开一个带有静态文件的文件夹。这可以是一个绝对路径，也可以是相对于蓝图文件夹的路径：
```
admin = Blueprint('admin', __name__, static_folder='static')
# 蓝图为 /admin 把静态文件夹注册到 /admin/static
```
命名的 blueprint_name.static ，生成它的 URL 
```
url_for('admin.static', filename='style.css')
```
- 模板
用 Blueprint 构造函数中的 template_folder 参数来实现蓝图公开模板
```
admin = Blueprint('admin', __name__, template_folder='templates')
```

- 蓝图前缀

        url_prefix=‘hello’

阅读参考：[Flask官方教程中文翻译](http://www.pythondoc.com/flask/index.html)

