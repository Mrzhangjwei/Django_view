## 运行本地服务器，用来测试Django页面的命令是：
 ``` python manage.py runserver```
 
  运行成功如下图所示：(这个时候你在网站上输入http://127.0.0.1:8000/或者http://localhost:8000/就可进入到Django编译显示结果的界面了。)
  
 ![这里写图片描述](http://img.blog.csdn.net/20161031161139440)
# 视图和URL配置
 使用Django，你会用不同的方法来说明这两件事 页面的内容是靠view function（视图函数） 来产生，URL定义在 URLconf 中。首先，我们先写一个Hello World视图函数。
### 第一份视图：
 在上一章使用django-admin.py startproject制作的mysite文件夹中，创建一个叫做views.py的空文件。这个Python模块将包含这一章的视图。
 完整的函数和导入声明，你需要输入到views.py文件：
 ```
 from django.http import HttpResponse
 def hello(request):
 return HttpResponse("Hello world")
 ```
 逐行分析一遍代码：
 
- 首先，我们从 django.http 模块导入（import） HttpResponse 类;
- 接下来，我们定义一个叫做hello 的视图函数;
- 每个视图函数至少要有一个参数，通常被叫作request。 这是一个触发这个视图、包含当前Web请求信息的对象，是类django.http.HttpRequest的一个实例。在这个示例中，我们虽然不用request做任何事情，然而它仍必须是这个视图的第一个参数;
- 注意视图函数的名称并不重要；并不一定非得以某种特定的方式命名才能让 Django 识别它。 在这里我们把它命名为：hello，是因为这个名称清晰的显示了视图的用意;
- 这个函数只有简单的一行代码： 它仅仅返回一个HttpResponse对象，这个对象包含了文本“Hello world”。
 
*这里主要讲的是： 一个视图就是Python的一个函数。这个函数第一个参数的类型是HttpRequest；它返回一个HttpResponse实例。为了使一个Python的函数成为一个Django可识别的视图，它必须满足这两个条件。*

### 你的第一个URLconf
 现在，如果你再运行：python manage.py runserver，你还将看到Django的欢迎页面，而看不到我们刚才写的Hello world显示页面。 那是因为我们的mysite项目还对hello视图一无所知。我们需要通过一个详细描述的URL来显式的告诉它并且激活这个视图。为了绑定视图函数和URL，我们使用URLconf。
当用户访问/foo/时，调用视图函数foo_view()，这个视图函数存在于Python模块文件view.py中。

前一章中执行 django-admin.py startproject 时，该脚本会自动为你建了一份 URLconf（即 urls.py 文件）。 默认的urls.py会像下面这个样子：

```
from django.conf.urls.defaults import *

# Uncomment the next two lines to enable the admin:
# from django.contrib import admin
# admin.autodiscover()

urlpatterns = patterns('',
    # Example:
    # (r'^mysite/', include('mysite.foo.urls')),

    # Uncomment the admin/doc line below and add 'django.contrib.admindocs'
    # to INSTALLED_APPS to enable admin documentation:
    # (r'^admin/doc/', include('django.contrib.admindocs.urls')),

    # Uncomment the next line to enable the admin:
    # (r'^admin/', include(admin.site.urls)),
)
```
默认的URLconf包含了一些被注释起来的Django中常用的功能，仅仅只需去掉这些注释就可以开启这些功能. 下面是URLconf中忽略被注释的行后的实际内容
```
from django.conf.urls.defaults import *

urlpatterns = patterns('',
)
```
让我们逐行解释一下代码：

- 第一行导入django.conf.urls.defaults下的所有模块，它们是Django URLconf的基本构造。 这包含了一个patterns函数。

- 第二行调用 patterns() 函数并将返回结果保存到 urlpatterns 变量。patterns函数当前只有一个参数—一个空的字符串。(这个字符串可以被用来表示一个视图函数的通用前缀)

自己进行了简单的练习和操作：

 如果要调用templates方法，一定要记住把settings.py文件中‘DIRS’项加上：['templates']，如下图：
 ![TEMPLATES的设置](http://img.blog.csdn.net/20161031170303776)

```
from django.conf.urls import *
from django.contrib import admin
from website.views import *

urlpatterns = patterns('website.views',
    url(r'^admin/', admin.site.urls),
    (r'^$','my_homepage_view'),
    (r'^hello/$','hello'),
    (r'^time/$','current_datetime'),#显示当前时间
    (r'^time/plus/(\d{1,2})/$','hour_ahead'),#显示几小时后的时间
    (r'xiaomi/$','xiaomi'),
    (r'name/$','name'),
    )
```
 然后在views.py的文件中定义相关函数：
```
from django.http import HttpResponse
import datetime

from django.template import loader
from django.shortcuts import render

def hello(request):
    return HttpResponse("Hello World")

def my_homepage_view(request):
    return HttpResponse("你好")

def current_datetime(request):
    now = datetime.datetime.now()
    html = "<html><body>It is now %s.</body></html>"%now
    return HttpResponse(html)

def hour_ahead(request,offset): #定义函数，这个函数名在urls.py文件中必须存在
    offset = int(offset)
    dt = datetime.datetime.now() + datetime.timedelta(hours = offset)
    html = "<html><body>In %s hour(s),it will be %s.</body></html>"%(offset,dt)
    return HttpResponse(html)

def xiaomi(request):
    t = loader.get_template("xiaomi.html")
    html = t.render({})
    return HttpResponse(html)

d = {'name':'zhangsan','age':'14'}  #可以引入字典
l = ['zhangsan','14']  #引入列表

#定义变量
class A:
    age = 11
a = A()

#定义函数
def fun():
    return "hello"

def name(request):

    today = 1 #1代表判断正确，输出结果；0代表错误，不输出结果
    #name_list = ['zhangsan','lisi','wangwu','zhanba']
    name_list = []

    t = loader.get_template("name.html")
    #html = t.render({'name':'zhangjianwei'}) #直接加入值
    #html = t.render({'name':fun})  #引用函数
    html = t.render({'name':fun,'today_is_weekend':today,'athlete_list':name_list})
    return HttpResponse(html)
```
** 启动Django开发服务器来测试修改好的 URLconf, 运行命令行 python manage.py runserver 。 开发服务器的地址是 http://127.0.0.1:8000/ ，打开你的浏览器访问 http://127.0.0.1:8000/hello/ ，（hello是你定义的函数名）你就可以看到输出结果了。 开发服务器将自动检测Python代码的更改来做必要的重新加载， 所以在代码更改之后你不需要重启Server。**

 **注意：引入或者创建html文件，应该在/django/website目录下创建一个文件夹templates，然后在这个文件中建立你的html页面，目录如下图：**

 ![文件目录](http://img.blog.csdn.net/20161031163940064)

### 关于网站根目录的快速参考
 尽管匹配网站根目录的URL模式不能想象，但是还是值得提一下的. 当为网站根目录实现一个视图，你需要使用URL模式`` ‘^$’`` , 它代表一个空字符串。 例如:
```
from mysite.views import hello, my_homepage_view

urlpatterns = patterns('',
    ('^$', my_homepage_view),
    # ...
)
```


