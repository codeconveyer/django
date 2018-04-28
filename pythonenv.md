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
    auto_now_add:第一次创建的时候赋值
    auto_now:每次修改的时候赋值
AutoField:自动增长
DateTimeField:年月日,时分秒

DecimalField:
    model.DecimalField(max_digits=3,decimal_places=1)
    
TextField:存文本信息
IntegerField:整数
FloatField:浮点
FileField:文件上传字段
ImageField:上传图片
    upload_to=""指定上传图片的路径

### 模型参数
default:默认
null:设置是否为空,针对数据库中改字段是否可以为空
blank:设置是否为空,针对表单提交该字段是否可以为空
primary key:创建主键
unique:唯一

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
{% for i in stu}
{% endfor %}

{% if xxx %}
{% endif %}

{% ifequal xxx 1 %}
{% else %}
{% endifequal %}

计数
{{ forloop.counter0 }}从零开始计数
{{ forloop.revcounter0 }}倒着计数,到零结束
