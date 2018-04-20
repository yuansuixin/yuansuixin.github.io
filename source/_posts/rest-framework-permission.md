---
title: Rest Framework框架源码分析----权限
date: 2017-09-20 11:29:28
tags:
---




## 权限
- 问题：不同的视图，不同的权限可以访问
- 基本使用（局部使用）

views.py
```
class OrderView(APIView):
    """
    订单相关信息，只有svip用户有权限
    """
    # 只需要应用上这个列表就可以实现认证了
    # authentication_classes = [FirstAuthtication,Authtication]
    permission_classes = [MyPermission,]
    def get(self,request,*args,**kwargs):
        # request.user
        # request.auth
        # 权限的访问
        # if request.user.user_type != 3:
        #     return HttpResponse('无权访问')
        ret = {'code':1000,'msg':None,'data':None}
        try:
            ret['data'] = ORDER_DICT
        except Exception as e:
            pass
        return JsonResponse(ret)

class UserInfoView(APIView):
    """
    订单相关业务（普通用户，VIP）
    """

    # authentication_classes = [Authtication, ]
    permission_classes = [MyPermission1, ]
    def get(self,request,*args,**kwargs):
        if request.user.user_type == 3:
            return HttpResponse('无权访问')
        return HttpResponse('用户信息')
```
permission.py
```
class MyPermission(object):
    def has_permission(self,request,view):
        if request.user.user_type != 3:
            return False
        return True
class MyPermission1(object):
    def has_permission(self,request,view):
        if request.user.user_type == 3:
            return False
        return True
```
- 源码流程，和认证的源码类似，
    - 匹配到url，然后到view视图里，执行父类的as_view()方法，该方法返回一个view函数，而在view（）函数里返回的是dispatch（）方法，我们就从dispatch()方法开始
    - 先封装好request，然后执行initial()实现一系列的认证，其中check_permissions()方法就是权限的判断
    - 通过get_permissions（）方法，执行一个列表生成式，拿到权限的对象列表，对象列表是从permission_classes中获取到的，默认是配置文件中拿到，当然也可以自己写到视图里
    - 遍历权限的对象列表，通过调用has_permission(),判断是否有权限，没有权限的抛出异常

- 可以使用全局设置，在settings里配置好，，如果某个视图不需要使用，可以使用`` permission_classes = [MyPermission1, ]``声明就可以了。
```
REST_FRAMEWORK = {
    # 全局使用的认证类
    'DEFAULT_AUTHENTICATION_CLASSES':['myappp.utils.auth.FirstAuthtication','myappp.utils.auth.Authtication'],
    # 值是一个函数，可以调用
    # 'UNAUTHENTICATED_USER':lambda :'匿名用户',
    'UNAUTHENTICATED_USER':None,# 匿名，request.user = None
    'UNAUTHENTICATED_TOKEN':None,# 匿名，request.auth = None
    'DEFAULT_PERMISSION_CLASSES':['myappp.utils.permission.SVIPPermission'],
}
```

- 自定义的权限类
    - 自己写权限类的时候应该继承自BasePermission类

###### 梳理
- 一般使用rest的时候，都会把认证和权限放到rest的组件里
- 基本使用
    - 类，必须继承BasePermission类，必须实现:has_permission（）方法
    - 返回值
        - True 有权访问
        - False 无权访问，返回的是None
    - 全局和局部

```
from rest_framework.permissions import BasePermission
class SVIPPermission(BasePermission):
    def has_permission(self,request,view):
        if request.user.user_type != 3:
            return False
        return True
```


```
class UserInfoView(APIView):
    """
    订单相关业务（普通用户，VIP）
    """

    # authentication_classes = [Authtication, ]
    permission_classes = [MyPermission1, ]
    def get(self,request,*args,**kwargs):
        if request.user.user_type == 3:
            return HttpResponse('无权访问')
        return HttpResponse('用户信息')
```
```
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES':['myappp.utils.permission.SVIPPermission'],
}
```

- 源码流程






