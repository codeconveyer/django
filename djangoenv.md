## 设计软件的典范
- MVC -- Model View Controller
- MVT -- Model View Template

## 网络结构模式
- B/S -- Browser/Server
- C/S -- Client/Server

## 虚拟环境的安装与使用
当我们在开发python应用程序的时候,往往会需要用到不同不同版本的python,这就使得我们需要让每个应用都拥有自己独立的开发环境，这就需要用到virtualenv。

- pip install virtualenv
- virtualenv --no-site-packages env  
env为安装的名称,也可以使用 virtualenv --no-site-packages -p xxxx env 指定安装版本
- 进入env,使用activate进入虚拟环境,deactivate退出虚拟环境
- pip install django  
安装django,还可以用==来指定安装的django版本
- django-admin startproject xxx
- 进入xxx数据库,python manage.py startapp xxx

### app中的一些模块
- __init__.py:初始化
- admin.py:管理后台注册模型
- apps.py:settings.py里面注册app时需要使用，一般不推荐这样使用
- models.py:写模型的地方
- urls.py:链接到处理业务逻辑
- views.py:写处理业务逻辑的地方

- 迁移数据库  
python manage.py makemigrations  
python manage.py migrate

- 创建超级管理员  
python manage.py createsuperuser

- 模型字段  
CharField:字符串  
BooleanField:布尔类型  
DateField:年月日,日期  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;auto_now_add:第一次创建的时候赋值  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;auto_now:每次修改的时候赋值  
AutoField:自动增长  
DateTimeField:年月日,时分秒  
DecimalField:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model.DecimalField(max_digits=3,decimal_places=1)  
TextField:存文本信息  
IntegerField:整数  
FloatField:浮点  
FileField:文件上传字段  
ImageField:上传图片  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;upload_to=""指定上传图片的路径  

### 模型参数
default:默认  
null:设置是否为空,针对数据库中改字段是否可以为空  
blank:设置是否为空,针对表单提交该字段是否可以为空  
primary key:创建主键  
unique:唯一  
auto_increment:自增

### 筛选、关联
filter,get,first,last,count,gt,gte,lt,lte  
F()Q() from django.db models import  
OneToOneField主键与外键是一对一关系,主表可直接.关联表的models名,关联表需要.外键  
ManyToMantField 主键可以.关联表的models_set来获取关联表信息  
on_delete:默认cascade，主表删除，从表也删除;set_null,主表删除,从表关联字段设为空;protect,不让删除;set_default,主表删除，从表关联字段设为默认值

### 静态资源加载
需要在setting中添加字段STATICFILES_DIRS=[os.path.join(BASE_DIR,'static']  
加载static中的方法:static/images/xxx.png或者{% load static %}  {% static 'images/xx.png' %}

### 循环、判断
{% for i in stu %}  
{% endfor %}

{% if xxx %}  
{% endif %}

{% ifequal xxx 1 %}  
{% else %}  
{% endifequal %}

计数  
{{ forloop.counter0 }}从零开始计数  
{{ forloop.revcounter0 }}倒着计数,到零结束

### 过滤器
{{ value | date:'`Y-m-d` `H:m:s`' }}通过|表示过滤  
y:两位年,m:月,d:日,Y:四位年  
h:12小时制,m:分,s:秒,H:24小时制  
add:进行加法计算  
floatformat:arg 四舍五入并保留arg位小数.若arg为负数,四舍五入，并保留有效小数，最多arg位;则若arg为null，则为arg=-1  
{% widthratio this_value max_value value %}:表示this_value/max_value`*`value  
{ value | divisibleby arg}:若value能被arg整除则输出True  

### 重命名
同过在工程url中定义namespace，在模块url中定义name，则可以通过{% url'namespace:name' %}进行方法的读取

### 请求
- post 提交数据隐藏了
- get 提交数据在url上,可以通过?xx=xx来获取,或者`?P<xx>\d+`来获取
- put 更新全部数据
- patch 更新局部信息
- delete 删除

### cookies  
我们在登陆网页时往往是会需要验证信息的，但验证一次之后如果在一定时间再登陆时还是需要验证信息的话对用户就非常的不友好，所以这就需要用到set_cookies，将验证信息cookies存入到页面响应中，使得用户在一定时间内不必再次验证。  

- set_cookies(key, value, max_age, exprie).其中的max_age和expire都表示了cookies的过期时间，但是man_age是相对时间，相对于第一次页面请求的时间，而且是以秒为单位的,HTTP/1.1中才有定义;expire是绝对时间,这样如果客户端和本地时间不一致时就会发生错误，expire在HTTP/1.0中已有定义，所以在低版本中可以使用.
- 在我们设置了cookies之后， 我们可以通过request.COOKIES.get(key)来获取这个cookies，从而来与本地数据库中存储的cookies进行验证,确保用户的正确

### 面向切面编码
正因为cookies是每一个用户都需要的，且会在许多url中需要使用，那么如果在如此多的url中进行重复的编写会显得臃肿且影响性能，所以我们可以使用中间键和面向切面编码AOP来进行处理:

- process_request:在处理url路由之前进行逻辑处理
- process_response:在响应返回浏览器之前调用
- process_view:调用视图之前执行
- process_templates_response:在视图刚好执行完的时候调用

中间键的使用一般是在utils文件夹中新建一个py文件
```
class UserMiddleware(MiddlewareMixin):
  def process_request(self, request)
```
通过这种方式来实现。

### 分页
- 导入Paginator对象
```
paginator = Paginator(queryset,num) 一页中显示num个queryset中的值
page = paginator.page(x) page是第x页的qureyset
for val in page 可以将这一页中的num个queryset遍历出来，val.xx来获取对应的值
共有page.paginator.num_pages页，共有page.paginator.count条数据(这里的数据是所有的并不是当页的)
page.has_previous有上一页则为true,page.has_next有下一页则为true
page.previous_page_number上一页页码，page.next_page_number下一页页码
page.number当前页数
page.paginator.page_range是一个页码range，可以通过for来遍历显示1.2.3所有页码
```
