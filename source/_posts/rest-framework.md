---
title: Django的Rest Framework框架源码分析
date: 2017-12-20 22:15:20
tags:
---


#### Django RESTful framework框架
- 使用

```
class MyAuthentication(object):
    def authenticate(self,request):
        token = request._request.GET.get('token')
        # 获取用户名和密码，去数据校验
        if not token:
            raise exceptions.AuthenticationFailed('用户认证失败')
        return ("alex",None)

    def authenticate_header(self,val):
        pass

class DogView(APIView):
    authentication_classes = [MyAuthentication,]

    def get(self,request,*args,**kwargs):
        print(request)
        print(request.user)
        self.dispatch
        ret  = {
            'code':1000,
            'msg':'xxx'
        }
        return HttpResponse(json.dumps(ret),status=201)

    def post(self,request,*args,**kwargs):
        return HttpResponse('创建Dog')

    def put(self,request,*args,**kwargs):
        return HttpResponse('更新Dog')

    def delete(self,request,*args,**kwargs):
        return HttpResponse('删除Dog')
```

- 源码流程
    - 基于Django的CBV实现，我们先看请求的流程，先从url开始匹配，到views里，执行的是dispatch（）方法，那我们就从dispatch（）方法开始
    - 先看当前类中有没有dispatch（）方法，如果没有就去父类中查找，restful中我们的views里继承的是APIView，其实APIView源码也是继承自Django的View的，在restful的views源码里我们可以看到dispatch（）方法，
    - 在rest的views里面的dispatch（）方法中我们可以看到，对原生的request进行了加工，我们可以跟到initialize_request（）方法中具体的看一下
    - initialize_request（）中对request进行了封装，除了原生的request外，又加上了一些其他的东西，通过get_authenticators（）方法获取到auth对象的列表赋值给authenticators，get_authenticators（）是从authentication_classes中获取的auth对象，authentication_classes在api_settings中默认给出了一个列表
    - 接着往下看，把加工好的request赋值给当前对象，然后执行了initial（）方法，我们去看一看，我们可以看到在initial（）方法里调用了perform_authentication（request），将加工好的request传进去，其实是认证是否登录
    - 在perform_authentication（）我们可以看到直接调用的request的user，其实我们查看request源码可以知道，user方法使用了property装饰器，所以调用的时候不用写括号
    - 然后调用request类的_authenticate（），循环了其实是BasicAuthentication对象的那个列表，该对象里一定有authenticate方法，通过调用这个方法，来认证是否登录





rest_framework/views.py
```
    def dispatch(self, request, *args, **kwargs):
        """
        `.dispatch()` is pretty much the same as Django's regular dispatch,
        but with extra hooks for startup, finalize, and exception handling.
        """
        self.args = args
        self.kwargs = kwargs
        # 对原生的request进行加工
        request = self.initialize_request(request, *args, **kwargs)
        # 获取原生的request，request._request
        # 获取认证类的对象 request.authenticators,对象列表
        # 将加工过的request赋给当前对象，（原生的request， [BasicAuthentication对象，...]）
        self.request = request
        self.headers = self.default_response_headers  # deprecate?

        try:
            # 传入的是加工过的request
            self.initial(request, *args, **kwargs)

            # Get the appropriate handler method通过反射执行相应的函数
            if request.method.lower() in self.http_method_names:
                handler = getattr(self, request.method.lower(),
                                  self.http_method_not_allowed)
            else:
                handler = self.http_method_not_allowed

            response = handler(request, *args, **kwargs)

        except Exception as exc:
            response = self.handle_exception(exc)

        self.response = self.finalize_response(request, response, *args, **kwargs)
        return self.response
```

```
 # Dispatch methods
    # 对request进行加工
    def initialize_request(self, request, *args, **kwargs):
        """
        Returns the initial request object.
        """
        # 取出原生的request的内容
        parser_context = self.get_parser_context(request)
        # 返回一个Request对象
        return Request(
            # 原生的request
            request,
            parsers=self.get_parsers(),
            # 获取到auth对象列表
            authenticators=self.get_authenticators(),
            negotiator=self.get_content_negotiator(),
            parser_context=parser_context
        )

#####################################################


     def initial(self, request, *args, **kwargs):
        """
        Runs anything that needs to occur prior to calling the method handler.
        """
        self.format_kwarg = self.get_format_suffix(**kwargs)

        # Perform content negotiation and store the accepted info on the request
        neg = self.perform_content_negotiation(request)
        request.accepted_renderer, request.accepted_media_type = neg

        # Determine the API version, if versioning is in use.
        version, scheme = self.determine_version(request, *args, **kwargs)
        request.version, request.versioning_scheme = version, scheme

        # Ensure that the incoming request is permitted
        # 认证
        self.perform_authentication(request)
        self.check_permissions(request)
        self.check_throttles(request)
#####################################################
    
    def get_authenticators(self):
        """
        Instantiates and returns the list of authenticators that this view can use.
        """
        # 返回的是一个对象的列表，self都是从当前的对象找，找不到再去父类中找
        return [auth() for auth in self.authentication_classes]


##################################################

    authentication_classes = api_settings.DEFAULT_AUTHENTICATION_CLASSES


#############################################

     def initial(self, request, *args, **kwargs):
        """
        Runs anything that needs to occur prior to calling the method handler.
        """
        self.format_kwarg = self.get_format_suffix(**kwargs)

        # Perform content negotiation and store the accepted info on the request
        neg = self.perform_content_negotiation(request)
        request.accepted_renderer, request.accepted_media_type = neg

        # Determine the API version, if versioning is in use.
        version, scheme = self.determine_version(request, *args, **kwargs)
        request.version, request.versioning_scheme = version, scheme

        # Ensure that the incoming request is permitted
        # 认证
        self.perform_authentication(request)
        self.check_permissions(request)
        self.check_throttles(request)
        
        


#######################################################
        def perform_authentication(self, request):
        """
        Perform authentication on the incoming request.

        Note that if you override this and simply 'pass', then authentication
        will instead be performed lazily, the first time either
        `request.user` or `request.auth` is accessed.
        """
        # 加工后的request
        request.user
```

request.py
```
 def _authenticate(self):
        """
        Attempt to authenticate the request using each authentication instance
        in turn.
        """
        #循环这个列表[BasicAuthentication对象,]
        for authenticator in self.authenticators:
            try:
                # 该对象里一定有authenticate方法
                # 认证是否登录
                user_auth_tuple = authenticator.authenticate(self)
            except exceptions.APIException:
                self._not_authenticated()
                raise

            if user_auth_tuple is not None:
                self._authenticator = authenticator
                # 如果登录的话，就会返回元组，否则抛出异常
                self.user, self.auth = user_auth_tuple
                return

        self._not_authenticated()
#################################################
    @property
    def user(self):
        """
        Returns the user associated with the current request, as authenticated
        by the authentication classes provided to the request.
        """
        if not hasattr(self, '_user'):
            with wrap_attributeerrors():
                self._authenticate()
        return self._user
```




