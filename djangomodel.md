## django中的model

### model的创建
- 我们在models.py这个文件中进行模型的创建
```python
from django.db import models


class Student(models.Model):
	id = models.CharField(max_length=2,primary_key=True)
	s_name = models.CharField(max_length=5,unique=True)
	s_sex = models.BooleanField(default=1)
	s_birth = models.DateTimeField(auto_now_add=True)
	password = models.CharField(max_length=200)

	class Meta:
		db_table = 'stu'
```  
这样我们就成功的创建了一个学生模型,需要注意的是,`auto_now_add`的作用是在第一次保存时用当前时间进行保存,之后可以进行修改;`auto_now`则是每次修改时都存入当前时间,我们不能输入其他时间来修改它。  

- 我们在实际业务中往往需要多个关联性的表来存放相应的数据,而表的关联关系有三种
	- OneToOneField 一对一联系
	- ForeignKey 一对多联系
	- ManyToMany 多对多联系  
现在我们就创建这三种类型的关联表来实践一下吧
```python
class StuInfo(models.Model):
	id = models.AutoField(primary_key=True)
	s = modles.OneToOneField(Student)
```

```python
class Parents(models.Model):
	id = models.AutoField(primary_key=True)
	s = models.ForeignKey(Student)
```

```python
class Course(models.Model):
	id = models.AutoField(primary_key=True)
	s = models.ManyToManyField(Student)
```

### 数据的读取
- 获取原表中的数据
```python
stu = Student.objects.get(id=1)
info = StuInfo.objects.get(id=1)
parent = Parents.objects.get(id=1)
cou = Course.objects.get(id=1)
```
`stu`是一个实例对象,我们可以通过`.`它的相应字段获取相应的值,例如`stu.id`
- 获取一对一表中的值
`stu.stuinfo`通过`.`关联表的类名全小写就可以获得一个关联的`object`实例对象,之后就跟获取表中的值一样,通过`.`属性来获取  
`info.s`在建立了关联字段的表中通过`.`关联的字段名就可以获得关联表的实例,而且建立的关联字段名在表中生成时会添加一个`_id`,所以如果想要查看该字段数据的话,需要`.s_id`。
- 获取一对多的值
`stu.course_set`首先我们这样获取到的是一个`RelatedManager`,而且因为是一对多关系,所以这里的值可能是多个的,我们可以通过筛选条件来得到我们想要的数据。我就假设使用了`first()`只获取一条关联数据,那么我们可以通过`stu.course_set.first().id`来获取相应的id值;如果你想获取所有的关联数据,我们可以使用`all()`,但是这样我们取得的是一个`queryset`,而对应`queryset`的我们可以通过`stu.course_set.all().values('id')`来获取所有的值,需要注意的是获取的值仍然是个`queryset`。  
跟一对一关联的表读取数据一样,只需要通过`.`关联字段名就可以得到关联的表的数据,因为这是一对多,所以获取的仍然只有一条数据,是一个`object`
- 获取多对多的值
我们在建立多对多的数据库时,会自动在数据库中建立一个第三个库,用来保存关联关系。没有定义关联字段的表获取关联表的数据的方法和一对多一样,但是在有定义关联字段获取关联表的数据时得到的是一个`queryset`,而获取它的值的方法我也在上文提过了。

### 数据库的增删改查
- 查
	- `model.objects.all()` 获取该表中的所有数据,注意取出的是一个`queryset`,里面是该表数据的实例化对象
	- `model.objects.filter(id=1)` 筛选出`id=i`的数据,取出的是一个`queryset`即便是只有一条数据。如果没有找到相应的数据,则会输出一个空的`queryset`
	- `model.objects.get(id=1)` 当且只当表中满足该条件的数据只有一条时才能正常运行,得到的是一个`object`,其他情况皆会报错
	- `model.objects.filter(s_sex=1).first()` 取出第一条匹配的数据,是一个`object`。如果没有匹配的数据,则输出为空
	- `model.objects.filter(s_sex=1).all()` 效果和 `filter()` 相同
	- 如果表中有建立关联的字段,例如 `ForeignKey、ManyToManyField、OneToOneField`,我们可以通过`__`两个下划线来进行关联表数据查询  
	`Course.objects.filter(s__id=1)` 我的 `course` 和 `student` 是关联的,这个语句的意思就是取出关联的 `student.id=1` 的 `course` 数据,输出是一个 `course queryset`。当然 `get` 也是可以使用双下划线的方法的
	- 取出的如果是一个`queryset`的话我们是不能通过`.`字段名这个方法来获取值的,但是我们可以通过`values`这个方法进行值得获取  
	`Student.objects.filter(s_sex=1).values('id')` 这样就可以输出,结果如下  
	>>> <QuerySet [{'id': '1'}, {'id': '3'}, {'id': '5'}, {'id': '7'}, {'id': '8'}]>  
	可以看出`queryset`里面是一个个字典,而我们还有一个`values_list`方法  
	`Student.objects.filter(s_sex=1).values_list('id','s_sex')`  
	>>> <QuerySet [('1', True), ('3', True), ('5', True), ('7', True), ('8', True)]>  
	可以看出`queryset`里面是一个个元组

- 增
	- `model.objects.create(id=1)`
	- ```python
	stu = model(id=1)
	stu.save()
	```
	- ```python
	dict = {'id':1}
	model.objects.create(**dict)	
	```
- 删
	- `model.objects.filter(id=1).delete()` 删除是不推荐使用的。如果确实需要删除,我们可以通过定义一个布尔类型字段,1为删除,0为不删除,通过逻辑判断达到软删除的效果。
- 改
	- `model.objects.filter(id=1).update(id=2)` 
	- ```python
	stu = model.objects.get(id=1)
	stu.id = 1
	stu.save()
	```