---
title: Django的两种实现方式----CBV,FBV
date: 2017-09-17 19:11:26
tags:
---




## CBV，FBV

- 开发模式
    - 普通开发方式(前后端放在一起写)
    - 前后端分离
        - 所有的交互都是ajax实现的
        - 为前端提供URL（api/接口的开发）
          注意：永远返回HttpResponse
    - Django FBV  CBV
        - FBV  function base  view
            ```
            def users(request):
            user_list = ['alex','oldboy']
            return HttpResponse(json.dumps(user_list))
            ```
            ```
            url(r'^users/', views.users),
            ```
        - CBV  class  base view
            - 只要是基于CBV的框架几乎都是使用反射实现的，也就是根据method不同，执行getattr获取到，然后执行相应的方法

视图

```
 class StudentsView(View):
    
    #继承自Django的view视图，在url里调用as_view()方法，这个固定搭配
    #以不同的方式请求的时候，Django内部就会自动的选择调用不同的函数
    
    def get(self,request,*args,**kwargs):
        return HttpResponse('GET')
    def post(self,request,*args,**kwargs):
        return HttpResponse('POST')
    def put(self,request,*args,**kwargs):
        return HttpResponse('PUT')
    def delete(self,request,*args,**kwargs):
        return HttpResponse('DELETE')
```

路由
``` 
url(r'^students/',views.StudentsView.as_view())
```

- 列表生成式

```
 class Foo:
 pass
 class Bar:
 pass

# 对象列表
v = [item() for item in [Foo,Bar]]

# 上面的对象列表就相当于下面代码的简写
v = []
for i in [Foo,Bar]:
obj = i()
v.append(obj)
```
- 面向对象
    - 封装
        - 类可以对同一类的方法进行封装
        - 将数据封装到对象中

```
# -*- coding:UTF-8 -*-

class Request(object):

    def __init__(self,obj):
        self.obj = obj

    @property
    def user(self):
        return self.obj.authticate()

class Auth(object):
    def __init__(self,name,age):
        self.name = name
        self.age = age

    def authticate(self):
        return self.name


class APIView(object):

    def dispatch(self):
        self.f2()

    def f2(self):
        a = Auth('alex',18)
        b = Auth('oldboy',18)
        # b是一个Auth对象，将这个Auth对象封装到Request对象里
        req = Request(b)
        # 调用Request类的user方法，但是因为user方法使用了property装饰器，所以
        # 调用的时候不需要加括号
        # 电泳Request的user（）方法，req对象里的obj变量是一个Auth对象，调用req对象里的
        # Auth对象中的authticate（）方法，返回该Auth对象的name属性，也就是oldboy
        print(req.user)

#对象通过点能够调用出来就说明这个对象里有什么，
obj = APIView()
obj.dispatch()
```


## 内容详细
   ### 0.FBV,CBV 
   - 表现： CBV 基于反射实现根据请求方式不同，执行不同方法
   - 原理:
       - 在URl里调用的永远是方法，
            ``` 
            url(r'^students/',views.StudentsView.as_view())
            ```
        - 我们这里调用的是StudentsView类的父类View类的as_view()方法，

        ```
            @classonlymethod
        ```
    def as_view(cls, **initkwargs):
        """
        Main entry point for a request-response process.
        """
        for key in initkwargs:
            if key in cls.http_method_names:
                raise TypeError("You tried to pass in the %s method name as a "
                                "keyword argument to %s(). Don't do that."
                                % (key, cls.__name__))
            if not hasattr(cls, key):
                raise TypeError("%s() received an invalid keyword %r. as_view "
                                "only accepts arguments that are already "
                                "attributes of the class." % (cls.__name__, key))
    
        def view(request, *args, **kwargs):
        #创建了一个当前对象
            self = cls(**initkwargs)
            if hasattr(self, 'get') and not hasattr(self, 'head'):
                self.head = self.get
            self.request = request
            self.args = args
            self.kwargs = kwargs
            # 调用了dispath方法
            return self.dispatch(request, *args, **kwargs)
        view.view_class = cls
        view.view_initkwargs = initkwargs
    
        # take name and docstring from class
        update_wrapper(view, cls, updated=())
    
        # and possible attributes set by decorators
        # like csrf_exempt from dispatch
        update_wrapper(view, cls.dispatch, assigned=())
        # 返回view这个方法
        return view
        
            def dispatch(self, request, *args, **kwargs):
        # Try to dispatch to the right method; if a method doesn't exist,
        # defer to the error handler. Also defer to the error handler if the
        # request method isn't on the approved list.
        if request.method.lower() in self.http_method_names:
        # 获取到method的方式，调用相对应的方法
            handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
        else:
            handler = self.http_method_not_allowed
        return handler(request, *args, **kwargs)
        ```
    
    - CBV流程，就是url中调用该视图类的as_views()方法，这个是固定的写法，as_views()方法在父类View类也就是Django封装好的View类中，它返回一个函数，在这个函数中创建了一个对象，并且调用了该对象的dispatch（）方法，在dispath()方法中，使用getattr（）获取到method的方式，然后调用的视图类的相关的方法，进行执行，然后返回


多继承
```
class MyBaseView(object):
    def dispatch(self, request, *args, **kwargs):
        print('before')
        # super执行的是当前对象的继承关系
        # super里的self是StudentsView对象，super首先找MyBaseView里有没有dispatch方法
        # 如果没有就会去View类中找
        ret = super(StudentsView,self.dispatch(request,*args,**kwargs))
        print('after')
        return ret


# 多继承，首先执行左边的继承
# 多各类公用，避免重复
class StudentsView(MyBaseView,View):
    '''
    继承自Django的view视图，在url里调用as_view()方法，这个固定搭配
    以不同的方式请求的时候，Django内部就会自动的选择调用不同的函数
    '''
    def get(self,request,*args,**kwargs):
        print('get方法')
        return HttpResponse('GET')
    def post(self,request,*args,**kwargs):
        return HttpResponse('POST')
    def put(self,request,*args,**kwargs):
        return HttpResponse('PUT')
    def delete(self,request,*args,**kwargs):
        return HttpResponse('DELETE')


class TeacherView(MyBaseView,View):
    '''
    继承自Django的view视图，在url里调用as_view()方法，这个固定搭配
    以不同的方式请求的时候，Django内部就会自动的选择调用不同的函数
    '''
    def get(self,request,*args,**kwargs):
        return HttpResponse('GET')
    def post(self,request,*args,**kwargs):
        return HttpResponse('POST')
    def put(self,request,*args,**kwargs):
        return HttpResponse('PUT')
    def delete(self,request,*args,**kwargs):
        return HttpResponse('DELETE')

```
路由

```
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^users/', views.users),
    url(r'^students/',views.StudentsView.as_view()),
    url(r'^teachers/',views.TeacherView.as_view()),
]

```


- 面试题
    1.Django的中间件
    - 最多写5个
    - process_request
    - process_view
    - process_response
    - process_exception
    - process_render_template

  2.使用中间件做过什么
  - 权限
  - 用户登录验证
  - Django的csrf token是怎么实现的？（基于FBV模式）
    > 情况一

      - csrf放到了process_view里面，一共做两件事
          - 检测视图是否加了csrf_exempt装饰器
          - 去请求体或cookie中获取token，校验
      - django的views里decorators里的csrf有个装饰器，csrd_exempt，使用该装饰器表示该方法免除中间件认证
      - 只要在settings里加了csrf就表示全站使用csrf，除非加上那个

    > 情况二

      - 在settings里面没有写csrf认证，只需要加上装饰器csrf_protect就表示该函数需要csrf认证了。


###### CBV小知识点，csrf时需要使用

> 方法一

- @method_decorator(csrf_exempt)
- 在dispatch方法中（单独的方法无效）

```
from django.views.decorators.csrf import csrf_exempt,csrf_protect
from django.utils.decorators import method_decorator
class TeacherView(View):
    @method_decorator(csrf_exempt)
    def dispatch(self, request, *args, **kwargs):
        ret = super(StudentsView,self.dispatch(request,*args,**kwargs))
        return ret

    def get(self,request,*args,**kwargs):
        return HttpResponse('GET')
    def post(self,request,*args,**kwargs):
        return HttpResponse('POST')
    def put(self,request,*args,**kwargs):
        return HttpResponse('PUT')
    def delete(self,request,*args,**kwargs):
        return HttpResponse('DELETE')
```

> 方法二

- 直接在类上加上装饰器

```
from django.views.decorators.csrf import csrf_exempt,csrf_protect
from django.utils.decorators import method_decorator
@method_decorator(csrf_exempt,name='dispatch')
class TeacherView(View):
    def get(self,request,*args,**kwargs):
        return HttpResponse('GET')
    def post(self,request,*args,**kwargs):
        return HttpResponse('POST')
    def put(self,request,*args,**kwargs):
        return HttpResponse('PUT')
    def delete(self,request,*args,**kwargs):
        return HttpResponse('DELETE')
```


#### CBV总结
- 本质：基于反射来实现
- 流程：路由，view，dispatch（反射）
- 取消csrf认证（装饰器要加在dispatch方法上且method_decorator装饰）

- 扩展
    - csrf
        - 基于中间件的process_view方法
        - 装饰器给单独函数进行设置（认证或无需认证）





















