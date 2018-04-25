---
title: Rest-Framework源码解析和自定义组件
date: 2018-05-01 21:53:25
tags:
---



## 分页

- 分页，看第n页，每页显示n条数据

```
from rest_framework.pagination import PageNumberPagination

class MyPageNumberPagination(PageNumberPagination):
    # 每页显示的个数
  page_size = 2
  # 每页现实的个数可以自定制
  page_size_query_param = 'size'
  # 每页最多可以显示5个
  max_page_size = 5

  page_query_param = 'page'

class Page1View(APIView):
    def get(self,request,*args,**kwargs):
        # 获取所有数据
  roles = models.Role.objects.all()
        # 创建分页对象
  pg = MyPageNumberPagination()
        # pg = PageNumberPagination()  # 默认的不可以自己设置每页显示的大小
 # 在数据库中获取分页的数据  page_roles = pg.paginate_queryset(queryset=roles,request=request,view=self)
        # 对数据进行序列化
  ser  =  PagerSerializer(instance=page_roles,many=True)
        print(page_roles)

        # 渲染
 # return Response(ser.data) # 默认给我们写好了上一页下一页，多少个  return pg.get_paginated_response(ser.data)
```

- 分页，在n个位置，向后查看n条数据

```
from rest_framework.pagination import PageNumberPagination,LimitOffsetPagination


# offset指的是索引
class MyLimitOffsetPagination(LimitOffsetPagination):
    # 每页显示的个数
    default_limit = 2
    # 每页现实的个数可以自定制
    limit_query_param = 'size'
    # 每页最多可以显示5个
    max_limit = 5

class Page1View(APIView):
    def get(self,request,*args,**kwargs):
        # 获取所有数据
        roles = models.Role.objects.all()
        # 创建分页对象
        pg = MyLimitOffsetPagination()
        # pg = PageNumberPagination()  # 默认的不可以自己设置每页显示的大小
        # 在数据库中获取分页的数据
        page_roles = pg.paginate_queryset(queryset=roles,request=request,view=self)
        # 对数据进行序列化
        ser  =  PagerSerializer(instance=page_roles,many=True)
        print(page_roles)

        # 渲染
        # return Response(ser.data)
        # 默认给我们写好了上一页下一页，多少个
        return pg.get_paginated_response(ser.data)

```

- 加密分页，上一页和下一页

```
from rest_framework.pagination import CursorPagination

class MyCursorPagination(CursorPagination):
    cursor_query_param = 'cursor'
    # 每页显示的个数
    page_size = 2
    ordering = 'id'
    # 每页现实的个数可以自定制
    page_size_query_param = None
    # 每页最多可以显示5个
    max_page_size = None


# 加密之后记住了当前id的最大值和最小值，第二次执行的时候，id就不扫描，直接跳到指定的位置
class Page1View(APIView):
    def get(self,request,*args,**kwargs):
        # 获取所有数据
        roles = models.Role.objects.all()
        # 创建分页对象
        pg = MyCursorPagination()
        # pg = PageNumberPagination()  # 默认的不可以自己设置每页显示的大小
        # 在数据库中获取分页的数据
        page_roles = pg.paginate_queryset(queryset=roles,request=request,view=self)
        # 对数据进行序列化
        ser  =  PagerSerializer(instance=page_roles,many=True)
        print(page_roles)

        # 默认给我们写好了上一页下一页，多少个,这里就必须使用这个，因为页码是加密的，我们不知道页码是多少没法通过页码自己获取
        return pg.get_paginated_response(ser.data)

```


## 视图

- 之前，View

```
class PagerView(View):
    pass
```
- 现在，APIView

```
class PagerView(APIView): # 继承自View
    pass
```
- 无用  GenericAPIView

```
from rest_framework.generics import GenericAPIView
class View1View(GenericAPIView):  # 继承自APIView
    queryset = models.Role.objects.all()
    serializer_class = PagerSerializer
    pagination_class = PageNumberPagination
    def get(self,request,*args,**kwargs):
        # 获取数据
        roles = self.get_queryset()
        # 分页
        page_roles = self.paginate_queryset(roles)
        # 序列化
        ser = self.get_serializer(instance=page_roles,many=True)

        return Response(ser.data)
```

- 多继承`class GenericViewSet(ViewSetMixin, generics.GenericAPIView):`  GenericViewSet重写了`as_view（）`方法

```
路由
 # 执行了内部的对应关系，发送get请求的时候，执行对应的list方法，发送post请求的时候执行对应的xxx方法
 url(r'^(?P<version>[v1|v2]+)/view1/$',views.View1View.as_view({'get':'list'})),


视图
from rest_framework.viewsets import GenericViewSet

class View1View(GenericViewSet):
    queryset = models.Role.objects.all()
    serializer_class = PagerSerializer
    pagination_class = PageNumberPagination
    def list(self,request,*args,**kwargs):
        # 获取数据
        roles = self.get_queryset()
        # 分页
        page_roles = self.paginate_queryset(roles)
        # 序列化
        ser = self.get_serializer(instance=page_roles,many=True)
        return Response(ser.data)
```

-  ModelViewSet

```
路由
 # 执行了内部的对应关系，发送get请求的时候，执行对应的list方法，发送post请求的时候执行对应的xxx方法
 url(r'^(?P<version>[v1|v2]+)/view1/$',views.View1View.as_view({'get':'list','post':'create'})),
 url(r'^(?P<version>[v1|v2]+)/view1/(?P<pk>\d+)$',views.View1View.as_view({'get':'retrieve','post':'create','delete':'destroy','put':'update','patch':'partial_update'})),

视图
from rest_framework.viewsets import GenericViewSet,ModelViewSet
from rest_framework.mixins import ListModelMixin,CreateModelMixin
# 可以完成增删改查了
class View1View(ModelViewSet):
    queryset = models.Role.objects.all()
    serializer_class = PagerSerializer
    pagination_class = PageNumberPagination
	

class View1View(CreateModelMixin,GenericViewSet)
```

##### 总结
- 增删改查  ModelViewSet
- 增删  CreateModelMixin,DestroyModelMixin,GenericViewSet
- 复杂的逻辑  GenericViewSet或APIView

- 权限
    - 使用到mixins.RetrieveModelMixin的时候，会执行源码中的retrieve（），在这里面调用了get_object（）
	- 根据层级找上去，我们会在GenericAPIView类中找到get_object（），在这个方法里调用check_object_permissions（）来判断这个对象是否有权限，照比我们之前对所有的权限来说，更细粒度了
	- 在这个方法里，调用get_permissions（）方法，循环拿到permission，调用has_object_permission（）
	- 只有调用get_object（）的时候has_object_permission（）才会被调用，不然永远不会调用
```
    GenericAPIView.get_object
       check_object_permissions
	       has_object_permission
```


## 路由

- ` url(r'^(?P<version>[v1|v2]+)/users/$',views.UsersView.as_view(),name='uuu')`
- ` url(r'^(?P<version>[v1|v2]+)/view1/$',views.View1View.as_view({'get':'list','post':'create'})),`

- 根据传入的url不同，渲染器渲染的也不同，一个url可以写成4个

```
# http://127.0.0.1:8000/myapp/v1/view1/?format=json  渲染器根据传的url不同，渲染不同
 url(r'^(?P<version>[v1|v2]+)/view1/$',views.View1View.as_view({'get':'list','post':'create'})),
# http://127.0.0.1:8000/myapp/v1/view1.json
 url(r'^(?P<version>[v1|v2]+)/view1\.(?P<format>\w+)$',views.View1View.as_view({'get':'list','post':'create'})),


 url(r'^(?P<version>[v1|v2]+)/view1/(?P<pk>\d+)$',views.View1View.as_view({'get':'retrieve','post':'create','delete':'destroy','put':'update','patch':'partial_update'})),
 url(r'^(?P<version>[v1|v2]+)/view1/(?P<pk>\d+)\.(?P<format>\w+)$',views.View1View.as_view({'get':'retrieve','post':'create','delete':'destroy','put':'update','patch':'partial_update'})),

```


- 自动生成路由

```
from django.conf.urls import url, include
from rest_framework import routers

from myapp import views


router = routers.DefaultRouter()
# 帮我们自动生成4个url
router.register(r'xxxx',views.View1View)
## 帮我们自动生成4个url
router.register(r'rt',views.View1View)

# 一般情况下，自动生成的情况不是特别多
urlpatterns = [
 url(r'^(?P<version>[v1|v2]+)/',include(router.urls)),
]
```

## 渲染器

- 视图里面可以添加renderer_classes,也可以在配置中添加
```
from rest_framework.renderers import JSONRenderer,BrowsableAPIRenderer,AdminRenderer,HTMLFormRenderer
class TestView(APIView):
    # renderer_classes = [JSONRenderer,BrowsableAPIRenderer]
    def get(self,request,*args,**kwargs):
        # 获取数据
        roles = self.get_queryset()
        # 分页
        page_roles = self.paginate_queryset(roles)
        # 序列化
        ser = self.get_serializer(instance=page_roles, many=True)

        return Response(ser.data)
```

```

REST_FRAMEWORK = {
    'PAGE_SIZE':2,
    'DEFAULT_RENDERER_CLASSES':[
        'rest_framework.renderers.JSONRenderer',
        'rest_framework.renderers.BrowsableAPIRenderer',
    ]
}

```
















