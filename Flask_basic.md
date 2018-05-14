## Flask基础

### 概念
    jango --> 完善完整高集成的框架
    flask --> '微'框架，不包括汉数据库抽象层框架，database，templates需要自己去组装

#### 安装
    创建虚拟环境virtualenv --no-site-packages flaskenv
    cd claskenv
    cd Scripts
    activate

    pip install flask

- 一个最小的Flask文件：
```
from flask import Flask
app = Flask(__name__)  # 初始化，__name__ 代表主模块名或则包

# 路由
@app.route('/')
def hello_world():
    return 'Hello World!'  # 视图函数

if __name__ == '__main__':
    app.run()  # 启动项目
```
**注：** 文件保存不能为flask.py，否则会与Flask本身冲突

#### 运行flask
     python xxxx.py --> 启动默认127.0.0.1:5000

#### 运行参数
     app.run(debug=True， port='8000'， host='0.0.0.0') 
        --- debug调试
            port指定端口为8000
            host主机ip地址0.0.0.0

#### 修改启动方式
    pip install flask-script
    python hello.py runserver -p 端口 -h ip地址 -d
    
#### 蓝图 -- 管理url，规划url
    pip install flask-blueprint
    a) 初始化
    b) 路由注册

#### route规则
    django中
      \(\d+)\
      \<?P(\d+)>\

    flask中
      <converter:name>

    strint: 默认的字符串
    int: 整型
    float: 浮点数
    path: '/'也是当错字符串返回
