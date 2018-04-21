---
title: rest-framework源码解析和自定义组件----版本
date: 2018-02-21 08:47:18
tags:
---



## 版本

- url中通过GET传参
  自定义的版本

```
from django.http import HttpResponse
from django.shortcuts import render

# Create your views here.
from rest_framework.views import APIView
from rest_framework.request import Request


class ParamVersion(object):
    def determin_version(self,request):
        version = request.query_params.get('version')
        return version

class UsersView(APIView):
    def get(self,request,*args,**kwargs):
        # request首先是先从自己的类中拿，如果自己的类中没有，就去原生的那个类中的去拿，
        # 否则就会抛出异常
        # version = request._request.GET.get('version')
        # print(version)
        # version = request.query_param.get('version') # 和request._request.GET.get('version')取值完全一样
        # print version
        return HttpResponse('用户列表')
```
- 使用内置的类

```
from rest_framework.versioning import QueryParameterVersioning
class UsersView(APIView):
    versioning_class = QueryParameterVersioning
    def get(self,request,*args,**kwargs):
        print(request.version)
        return HttpResponse('用户列表')
```
![Untitled-1-2018420213639](http://p693ase25.bkt.clouddn.com/Untitled-1-2018420213639.png)
- 这3个只需要在配置文件中设置就可以

- 在url路径中传参(推荐使用)

```
REST_FRAMEWORK = {
    'DEFAULT_VERSION_CLASS':'rest_framework.versioning.URLPathVersioning',
    'DEFAULT_VERSION':'v1',
    'ALLOWED_VERSIONS':['v1','v2'],
    'VERSION_PARAM':'version',
}
```
```
class UsersView(APIView):
    def get(self,request,*args,**kwargs):
        print(request.version)
        return HttpResponse('用户列表')

```

```
urlpatterns = [
 # url(r'^users/',views.UsersView.as_view()),
 url(r'^(?P<version>[v1|v2]+)/users/$',views.UsersView.as_view()),
]
```
- 源码流程
    - 返回版本是在封装request之后，认证和权限之前做的
    - BaseVersioning对象


![Untitled-1-2018420213814](http://p693ase25.bkt.clouddn.com/Untitled-1-2018420213814.png)

- 里面有个rest framework封装的reverse（），内反向生成url，不需要指定版本，会自动生成，其实就是当前url的版本，本质上底层是使用了Django自带的reverse()实现的。

```
class UsersView(APIView):
    def get(self,request,*args,**kwargs):
        self.dispatch()
        # 获取版本
        print(request.version)
        # 获取版本的对象
        print(request.versioning_scheme)
        # 内置的反向生成url，不需要指定版本，会自动生成，其实是当前url的版本
        u1 = request.versioning_scheme.reverse(viewname='uuu',request=request)
        print(u1)
        # 使用Django内置的反向生成url,必须要指定版本
        u2 = reverse(viewname='uuu',kwargs={'version':1})
        print(u2)
        return HttpResponse('用户列表')
```
- 除了这些，版本的实现还有很多
  ![Untitled-1-2018420214715](http://p693ase25.bkt.clouddn.com/Untitled-1-2018420214715.png)

##### 总结
- 使用   配置文件
```
REST_FRAMEWORK = {
    'DEFAULT_VERSION_CLASS':'rest_framework.versioning.URLPathVersioning',
    'DEFAULT_VERSION':'v1',
    'ALLOWED_VERSIONS':['v1','v2'],
    'VERSION_PARAM':'version',
}
```
- 路由系统

```
urlpatterns = [
 # url(r'^users/',views.UsersView.as_view()),
 url(r'^(?P<version>[v1|v2]+)/users/$',views.UsersView.as_view(),name='uuu'),
]

```
```
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^api/$',include('myapp.urls'))
]

```

- 视图

```
class UsersView(APIView):
    def get(self,request,*args,**kwargs):
        self.dispatch()
        # 1.获取版本
        print(request.version)
        # 2.获取版本的对象
        print(request.versioning_scheme)
        # 3. 内置的反向生成url，不需要指定版本，会自动生成，其实是当前url的版本
        u1 = request.versioning_scheme.reverse(viewname='uuu',request=request)
        print(u1)
        # 使用Django内置的反向生成url,必须要指定版本
        u2 = reverse(viewname='uuu',kwargs={'version':1})
        print(u2)
        return HttpResponse('用户列表')
```

- 源码流程，和认证的源码类似，
    - 匹配到url，然后到view视图里，执行父类的as_view()方法，该方法返回一个view函数，而在view（）函数里返回的是dispatch（）方法，我们就从dispatch()方法开始
    - 先封装好request，然后执行initial()实现版本然后才是认证，然后是权限的判断，后面就是访问频率了
    - 调用determine_version（）返回的是一个元组，有两个元素，分别是版本和处理版本的对象
    - 版本是在determine_version（）方法中拿到的，通过versioning_class（）方法，获取到URLPathVersioning对象，默认是从配置文件中获取到的，和权限，节流等流程类似，只不过那些都是拿到一个列表，而版本的只需要拿到一个versioning_class就可以了，也可以设置到视图中。


[源码下载](https://github.com/yuansuixin/Rest-Framework-Version "源码下载")



