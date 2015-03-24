### WSGI

[WSGI](http://wsgi.readthedocs.org/en/latest/)(Web Server Gateway Interface)在python PEP333中定义的一种web服务器和web框架通信的接口协议。它隔离了python web服务中的服务器和应用程序，也就是说，如果一个应用程序支持wsgi协议，那么它可以在任何支持wsgi协议的服务器上运行。

#### 1. WSGI应用
WSGI应用程序是一个可调用的对象，代码如下：

```python
def application(environ, start_response):
    status = '200 OK'
    headers = [('Content-Type', 'text/html')]
    start_response(status, headers)
    body = "hello world"
    return body
```
* environ：保存了wsgi环境变量的字典。
* start_response：wsgi server hook抓取HTTP的状态和头部信息。
* body：http返回值的body。

#### 2. WSGI中间件
WSGI中间件是在web服务器和web框架之间的一类特殊程序，它可以在HTTP请求进入web服务器后，进入web框架前处理相关业务，比如：

* 修改环境变量
* 验证相关信息
* 记录请求耗时

下面是一个在有nginx做代理情况下，获取请求真实IP的中间件:

```python
class RealIpMiddleware(object): 

    def __init__(self, app):
        self.app = app 

    def __call__(self, environ, start_response):
        host = environ.get('HTTP_X_REAL_IP', '') 
        if host:
            environ['REMOTE_ADDR'] = host
        return self.app(environ, start_response)
```
仔细琢磨一下，其实WSGI也算是web服务和web框架之间的一个中间件，只不过这个中间件有相应的协议规范。

#### 3. 完整示例
```python
# -*- coding: utf-8 -*-

from wsgiref.simple_server import make_server

def application(environ, start_response):
    status = '200 OK'
    headers = [('Content-Type', 'text/html')]
    start_response(status, headers)
    body = "hello world"
    return body

class RealIpMiddleware(object): 

    def __init__(self, app):
        self.app = app 

    def __call__(self, environ, start_response):
        host = environ.get('HTTP_X_REAL_IP', '') 
        if host:
            environ['REMOTE_ADDR'] = host
        return self.app(environ, start_response)

httpd = make_server('localhost', 8051, RealIpMiddleware(application))
httpd.serve_forever()
```