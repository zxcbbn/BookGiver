#### SAE 和 微信 对接的要求
在微信公众平台后台填写相应参数

提交信息后，微信服务器将发送 GET请求 到填写的 服务器地址URL 上

这个服务器地址URL，就是 SAE 的应用中，负责处理该请求的代码函数绑定的静态路由

SAE 应用返回要求的信息给 微信服务器 ， 微信服务器 确认后，对接成功

#### SAE应用
想长治久安式的维护服务,代码的物理分布一定要合理/模块化/易管理!

目录结构:
```
/BookGiver/project-files/
  +- config.yaml    应用版本配置
  +- config.py      全局配置
  +- index.wsgi     应用根代码
  +- web/           应用代码
        +-  __init__.py
         -  mana4api.py
```

运行时调用关系:
```
index.wsgi
  -> import config.py
  -> from web import APP
    -> __init__.py mount api.py
```

SAE 中与微信后台对接 代码的配置
`index.wsgi`
```
import sae
import config               #调用config模块
from bottle import *
from web import APP

application = sae.create_wsgi_app(APP)      #创建 SAE APP
```
`config.py`
```
import sys
import os.path

app_root = os.path.dirname(__file__)
sys.path.insert(0, os.path.join(app_root, 'site-packages'))
sys.path.insert(0, os.path.join(app_root, "web"))

class Borg():
    '''base http://blog.youxu.info/2010/04/29/borg
        - 单例式配置收集类
    '''
    __collective_mind = {}
    def __init__(self):
        self.__dict__ = self.__collective_mind

    TPL_TEXT=''' <xml>
     <ToUserName><![CDATA[%(toUser)s]]></ToUserName>
     <FromUserName><![CDATA[%(fromUser)s]]></FromUserName>
     <CreateTime>%(tStamp)s</CreateTime>
     <MsgType><![CDATA[text]]></MsgType>
     <Content><![CDATA[%(content)s]]></Content>
     </xml>'''

CFG = Borg()
```
`web/_init__.py`
```
from bottle import *     #bottle 是一个单文件实现的框架，可以在官网下载`bottle.py`文件，放到应用目录导入使用

APP = Bottle()                #实例化
APP.mount('/api', __import__('mana4api').APP)      #关联URL 中`/api/`和应用下文件'mana4api'
```
`mana4api`
```
@APP.get('/echo/')                                         #配置静态路由/echo/，处理 微信服务器 发送来的 用于验证的 get请求
def checkSignature():                                    #配置负责处理验证的 get 请求的函数，和 静态路由/echo/ 绑定
#    print request.query.keys()
#    print request.query.echostr
    return request.query.echostr                  #和微信的对接有一个bug，虽然 微信 明确给出了 加密/校验流程 ，但完全可以忽略这个流程，直接从 get 请求中提取`echostr`并返回就行
```

服务器地址URL：`http://bookgiver.sinaapp.com/api/echo/`

#### 填写参数，由微信服务器发送消息验证
完成 SAE 代码部署后，接下来要填写微信后台的相应参数，提交并让微信服务器发送消息验证

- 登录微信公众平台官网
- 在公众平台后台管理页面 - 开发者中心页，点击“修改配置”按钮
- 填写服务器地址（URL）、Token和EncodingAESKey
    - URL是开发者用来接收微信消息和事件的接口URL
    - Token可由开发者可以任意填写
    - EncodingAESKey由开发者手动填写或随机生成，将用作消息体加解密密钥。
- 点击提交
- 在窗口页面上部分会显示验证成功或失败

>如果 SAE 实名认证没有通过，会导致验证失败

#### 参考资料
- (微信公众平台开发者文档：接入指南)[http://mp.weixin.qq.com/wiki/17/2d4265491f12608cd170a95559800f2d.html]
- (42分钟乱入 wechat 手册!)[https://chaos2wechat.readthedocs.org/en/latest/ch00/try.html]
