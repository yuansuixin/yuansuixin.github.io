---
title: Restful API设计规范
date: 2017-11-19 22:10:52
tags:
---


## restful规范（建议）


### RESTful 
- REST与技术无关，代表的是一种软件架构风格，REST是Representational State Transfer的简称，中文翻译为“表征状态转移”

- REST从资源的角度类审视整个网络，它将分布在网络中某个节点的资源通过URL进行标识，客户端应用通过URL来获取资源的表征，获得这些表征致使这些应用转变状态

- REST与技术无关，代表的是一种软件架构风格，REST是Representational State Transfer的简称，中文翻译为“表征状态转移”

- 所有的数据，不过是通过网络获取的还是操作（增删改查）的数据，都是资源，将一切数据视为资源是REST区别与其他架构风格的最本质属性

- 对于REST这种面向资源的架构风格，有人提出一种全新的结构理念，即：面向资源架构（ROA：Resource Oriented Architecture）


#### 1、 接口开发

```
def get_order(request):
    return HttpResponse('')

def add_order(request):
    return HttpResponse('')

def update_order(request):
    return HttpResponse('')

def del_order(request):
    return HttpResponse('')
```
```
    url(r'^get_order/', views.get_order),
    url(r'^add_order/', views.add_order),
    url(r'^del_order/', views.del_order),
    url(r'^update_order/', views.update_order),
```

#### 2. restful规范（建议）
    1. 根据method不同做不同的操作
基于FBV
```
def order(request):
    if request.method=='GET':
        return HttpResponse('获取订单')
    elif request.method=="POST":
        return HttpResponse('创建订单')
    elif request.method=="PUT":
            return HttpResponse('更新订单')
    elif request.method=="DELETE":
            return HttpResponse('删除订单')
```

```
    url(r'^order/', views.order),
```
基于CBV
```
   url(r'^dog/', views.DogView.as_view()),
```
```
class OrderView(View):
    def get(self,request,*args,**kwargs):

        ret  = {
            'code':1000,
            'msg':'xxx'
        }

        return HttpResponse(json.dumps(ret),status=201)

    def post(self,request,*args,**kwargs):
        return HttpResponse('创建订单')

    def put(self,request,*args,**kwargs):
        return HttpResponse('更新订单')

    def delete(self,request,*args,**kwargs):
        return HttpResponse('删除订单')
```
#### 3. RESTful API设计规范
- API与用户的通信协议，总是使用HTTPs协议。
- 域名 
    - https://api.example.com                         尽量将API部署在专用域名（会存在跨域问题）
    - https://example.org/api/                        API很简单
- 版本
    - URL，如：https://api.example.com/v1/
    - 请求头                                                  跨域时，引发发送多次请求
- 路径，视网络上任何东西都是资源，均使用名词表示（可复数）
    - https://api.example.com/v1/zoos
    - https://api.example.com/v1/animals
    - https://api.example.com/v1/employees
- method
    - GET      ：从服务器取出资源（一项或多项）
    - POST    ：在服务器新建一个资源
    - PUT      ：在服务器更新资源（客户端提供改变后的完整资源）
    - PATCH  ：在服务器更新资源（客户端提供改变的属性）
    - DELETE ：从服务器删除资源
- 过滤，通过在url上传参的形式传递搜索条件
    - https://api.example.com/v1/zoos?limit=10：指定返回记录的数量
    - https://api.example.com/v1/zoos?offset=10：指定返回记录的开始位置
    - https://api.example.com/v1/zoos?page=2&per_page=100：指定第几页，以及每页的记录数
    - https://api.example.com/v1/zoos?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序
    - https://api.example.com/v1/zoos?animal_type_id=1：指定筛选条件
- 状态码和code结合使用
    - 200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
     - 201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
    - 202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
    - 204 NO CONTENT - [DELETE]：用户删除数据成功。
    - 400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
    - 401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
    - 403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
    - 404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
    - 406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
    - 410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
    - 422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
    - 500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。


- 错误处理，状态码是4xx时，应返回错误信息，error当做key。
    ```
     error: "Invalid API key"
    ```
- 返回结果，针对不同操作，服务器向用户返回的结果应该符合以下规范。
    - GET /collection：返回资源对象的列表（数组）
    - GET /collection/resource：返回单个资源对象
    - POST /collection：返回新生成的资源对象
    - PUT /collection/resource：返回完整的资源对象
    - PATCH /collection/resource：返回完整的资源对象
    - DELETE /collection/resource：返回一个空文档

- Hypermedia API，RESTful API最好做到Hypermedia，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。

```
{"link": {
  "rel":   "collection https://www.example.com/zoos",
  "href":  "https://api.example.com/zoos",
  "title": "List of zoos",
  "type":  "application/vnd.yourformat+json"
}}
```
- 注意，推荐使用CBV方式