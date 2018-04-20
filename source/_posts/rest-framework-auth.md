---
title: Rest Framework的源码分析----用户登录认证
date: 2017-03-20 11:36:09
tags:
---





### 认证
- 问题，有些API需要用户登录成功之后，才能访问，有些无需登录就能访问。
- 基本使用认证组件
  解决：
    - 创建两张表
    - 用户登录认证（返回token并保存到数据库）

views.py
```
from django.http import JsonResponse, HttpResponse
from django.shortcuts import render

# Create your views here.
from rest_framework import exceptions
from rest_framework.views import APIView

from myappp import models


ORDER_DICT = {
    1:{
        'name': "媳妇",
        'age':18,
        'gender':'男',
        'content':'...'
    },
    2:{
        'name': "老狗",
        'age':19,
        'gender':'男',
        'content':'...。。'
    },
}


# 对token使用md5摘要
def md5(user):
    import hashlib
    import time
    ctime = str(time.time())
    m = hashlib.md5(bytes(user,encoding='utf-8'))
    m.update(bytes(ctime,encoding='utf-8'))
    return m.hexdigest()


class AuthView(APIView):
    """
    用于用户认证
    """
    def post(self,request,*args,**kwargs):
        self.dispatch()
        ret= {'code':1000,'msg':None}
        try:
            # 获取到原生的那个request，拿到用户名和密码
            user = request._request.POST.get('username')
            pwd = request._request.POST.get('password')
            obj = models.UserInfo.objects.filter(username=user, password=pwd).first()
            #如果没有这个，返回错误码
            if not obj:
                ret['code'] = 1001
                ret['msg'] = "用户名或密码错误"
            # 为登录用户创建token
            token = md5(user)
            # 存在就更新，不存在就创建
            models.UserToken.objects.update_or_create(user=obj, defaults={'token': token})
            ret['token'] = token
        except Exception as e:
            ret['code'] = 1002
            ret['msg'] = '请求异常'
        return JsonResponse(ret)



class FirstAuthtication(object):
    """
    用户认证,返回None的情况，默认是匿名用户
    """
    def authenticate(self,request):
       pass

    def authenticate_header(self,request):
        pass



class Authtication(object):
    """
    用户认证，一般都是使用一个对象认证，很少有多个认证的情况
    """
    def authenticate(self,request):
        # 获取到请求中的token
        token = request._request.GET.get('token')
        token_obj = models.UserToken.objects.filter(token=token).first()
        if not token_obj:
            raise exceptions.AuthenticationFailed('用户认证失败')
        # 在rest  framework内部会将这两个字段赋值给request，以供后续使用
        return (token_obj.user,token_obj)

    def authenticate_header(self,request):
        pass

class OrderView(APIView):
    """
    订单相关信息
    """
    # 只需要应用上这个列表就可以实现认证了
    authentication_classes = [FirstAuthtication,Authtication]
    def get(self,request,*args,**kwargs):
        # request.user
        # request.auth
        ret = {'code':1000,'msg':None,'data':None}
        try:
            ret['data'] = ORDER_DICT
        except Exception as e:
            pass
        return JsonResponse(ret)

class UserInfoView(APIView):
    authentication_classes = [Authtication, ]
    def get(self,request,*args,**kwargs):
        return HttpResponse('用户信息')

```
models.py
```
from django.db import models

# Create your models here.

class UserInfo(models.Model):
    user_type_choices = (
        (1,'普通用户'),
        (2,'VIP'),
        (3,'SVIP'),
    )
    user_type = models.IntegerField(choices=user_type_choices)
    username = models.CharField(max_length=32,unique=True)
    password = models.CharField(max_length=64)

class UserToken(models.Model):
    user = models.OneToOneField(to='UserInfo')
    token = models.CharField(max_length=64)
```

- 认证流程原理：

![Untitled-1-2018418201518](http://p693ase25.bkt.clouddn.com/Untitled-1-2018418201518.png)

- 再看一遍源码（看配置）
    - 认证的配置可以局部视图使用还可以全局使用
    - 全局使用认证只需要在settings里配置好就可以，默认就是调用settings里的配置
    - 匿名时request.user = None
      `` authentication_classes = api_settings.DEFAULT_AUTHENTICATION_CLASSES``
    - 可以将认证相关的类抽出来放到一个文件中，更清晰

settings.py
```
REST_FRAMEWORK = {
    # 全局使用的认证类
    'DEFAULT_AUTHENTICATION_CLASSES':['myappp.utils.auth.FirstAuthtication','myappp.utils.auth.Authtication'],
    # 值是一个函数，可以调用
    # 'UNAUTHENTICATED_USER':lambda :'匿名用户',
    'UNAUTHENTICATED_USER':None,# 匿名，request.user = None
    'UNAUTHENTICATED_TOKEN':None,# 匿名，request.auth = None
}
```

views.py
```
from django.http import JsonResponse, HttpResponse
from rest_framework.views import APIView

from myappp import models


ORDER_DICT = {
    1:{
        'name': "媳妇",
        'age':18,
        'gender':'男',
        'content':'...'
    },
    2:{
        'name': "老狗",
        'age':19,
        'gender':'男',
        'content':'...。。'
    },
}


# 对token使用md5摘要
def md5(user):
    import hashlib
    import time
    ctime = str(time.time())
    m = hashlib.md5(bytes(user,encoding='utf-8'))
    m.update(bytes(ctime,encoding='utf-8'))
    return m.hexdigest()


class AuthView(APIView):
    """
    用于用户认证
    """
    # 如果不需要认证，只需要设置列表为空就可以了，在settings设置认证之后，如果不写，使用的是默认的认证，
     authentication_classes = []
    def post(self,request,*args,**kwargs):
        self.dispatch()
        ret= {'code':1000,'msg':None}
        try:
            # 获取到原生的那个request，拿到用户名和密码
            user = request._request.POST.get('username')
            pwd = request._request.POST.get('password')
            obj = models.UserInfo.objects.filter(username=user, password=pwd).first()
            #如果没有这个，返回错误码
            if not obj:
                ret['code'] = 1001
                ret['msg'] = "用户名或密码错误"
            # 为登录用户创建token
            token = md5(user)
            # 存在就更新，不存在就创建
            models.UserToken.objects.update_or_create(user=obj, defaults={'token': token})
            ret['token'] = token
        except Exception as e:
            ret['code'] = 1002
            ret['msg'] = '请求异常'
        return JsonResponse(ret)


class OrderView(APIView):
    """
    订单相关信息
    """
    # 只需要应用上这个列表就可以实现认证了
    def get(self,request,*args,**kwargs):
        # request.user
        # request.auth
        ret = {'code':1000,'msg':None,'data':None}
        try:
            ret['data'] = ORDER_DICT
        except Exception as e:
            pass
        return JsonResponse(ret)

class UserInfoView(APIView):
    # authentication_classes = [Authtication, ]
    def get(self,request,*args,**kwargs):
        return HttpResponse('用户信息')
```

- 内置的认证类
    - 基于Django做的，本质上都是把你的用户信息给我，我去数据库检测一下你是否认证成功，一般用不到，更趋向于使用自定义的
    - 认证类，必须继承，``from rest_framework.authentication import BaseAuthentication``
    - 其他认证类，BaseAuthentication，流程其实就是浏览器对你的用户名秘密（浏览器做的那个对话框里面的用户名密码）使用base64加密，加密之后放到请求头里面发过去![这里写图片描述](https://img-blog.csdn.net/20180420113856261?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ByYWlyaWU5Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


###### 梳理
1. 使用
    - 创建类，继承BaseAuthentication，实现authenticate()，authenticate_header()方法
    - 返回值
        - None，下一次认证来执行，
        - raise exceptions.AuthenticationFailed('用户认证失败')  # from rest_framework import exceptions
        - 返回元组，（元素1，元素2），元素1赋值给request.user 元素2赋值给request.auth
    - 局部使用
        - 在视图类中写个单独的字段 `` # authentication_classes = [Authtication, ]``
        - 全局使用,在配置文件settings中设置
```
REST_FRAMEWORK = {
    # 全局使用的认证类
    'DEFAULT_AUTHENTICATION_CLASSES':['myappp.utils.auth.FirstAuthtication','myappp.utils.auth.Authtication'],
    # 值是一个函数，可以调用
    # 'UNAUTHENTICATED_USER':lambda :'匿名用户',
    'UNAUTHENTICATED_USER':None,# 匿名，request.user = None
    'UNAUTHENTICATED_TOKEN':None,# 匿名，request.auth = None
}
```

2. 源码流程













