## 基于Python Django的博客管理系统的设计和实现

环境: python 3.6 venv(虚拟环境)

编译器: Pycharm 或者 Visul Studio Code (Pycharm使用官方版本,否则建不起django项目)

依赖的库: ...

##### 摘要:

> 目前，知识的学习十分的重要，我们要做到的不是固步自封，而是应该共享给所有的人，现在知识的学习还是很不方便。博客的开发与设计就能够实现该功能，用户发表博文后，其他用户可以看到，同时用户也可以记录一些重要的信息等。目前，知识的学习十分的重要，我们要做到的不是固步自封，而是应该共享给所有的人，现在知识的学习还是很不方便。博客的开发与设计就能够实现该功能，用户发表博文后，其他用户可以看到，同时用户也可以记录一些重要的信息等.
>
> 在软件开发活动中，无论何种服务，应用系统的开发仍然是开发的主题，博客就是这样一个MVC模式的系统。一个庞大的博客管理系统面向的用户很多，提供的功能也很多，如提供用户注册、用户管理、个人博客、发表博文、删除博文、留言、以及搜索博文等。本系统采用了Django框架进行开发。本文主要分析了该系统实现的相关技术原理与数据设计，给出了博客的系统架构，并对架构及管理模块结构作了划分及设计。本系统采用了当前流行的MVC结构，通过运用Python、Django+、TinyMCE、JQuery、Apache和Sqlite数据库等软件的强大功能完成了该系统的设计。在软件开发活动中，无论何种服务，应用系统的开发仍然是开发的主题，博客就是这样一个MVC模式的系统。一个庞大的博客管理系统面向的用户很多，提供的功能也很多，如提供用户注册、用户管理、个人博客、发表博文、删除博文、留言、用户评论、以及搜索博文等。本系统采用了Django框架进行开发。本文主要分析了该系统实现的相关技术原理与数据设计，给出了博客的系统架构，并对架构及管理模块结构作了划分及设计。本系统采用了当前流行的MVC结构，通过运用Python、Django、JQuery、Bootstrap和PostgreSQL数据库等软件的强大功能完成了该系统的设计。

关键词: Python Django MVC Bootstrap PostgreSQL

### get()和filter()方法的区别

get():只能查询出一个结果，如果查询的结果不止一个，name使用get()方法会报错

filter():可以根据条件，匹配一组结果，得到一个结果集

### 关键地方:

model 与 model之间的关系(一对一, 一对多[ForeignKey], 多对多[ManyToManyField])

一对一的关系去后台数据很方便

一对多的增删改查 :
代码:

models.py

```python
class WqCase(models.Model):
    title = models.CharField(max_length=255, default='', verbose_name='标题')
    content = models.TextField(null=True, blank=True, verbose_name='内容')
    dates = models.DateField( null=True, blank=True, verbose_name='日期')
    source = models.CharField(max_length=255, null=True, blank=True, verbose_name='来源')
    author = models.CharField(max_length=255, default='', verbose_name='作者')
    class Meta:
        verbose_name = '工程案例'
        verbose_name_plural = verbose_name
    def __str__(self):
        return self.title

class WqCaseImg(models.Model):
    case = models.ForeignKey(WqCase, on_delete=models.CASCADE, verbose_name='工程案例')
    image = models.ImageField(upload_to="case/", default='', max_length=255, verbose_name='图片')
    class Meta:
        verbose_name = '案例图片'
        verbose_name_plural = verbose_name
    def __str__(self):
        return str(self.case)
```

views.py

```python
# 当网页需要分页时,点击分页的链接跳到详情页面(是根据每增加一个对象有一个主键id,根据主键id跳到指定页面,在再前端显示出这个对象的详细信息)
def gcal(request):
    case = WqCase.objects.all().order_by('-dates')
    try:
        page = request.GET.get('page', 1)
    except:
        page = 1
    p = Paginator(case, 9, request=request)
    cases = p.page(page)
    return render(request, '工程案例.html', {'all_cases': cases})

def gcal_get_details(request, title_id):
    title = WqCase.objects.get(id = title_id) # 获取固定的title_id的WqCase对象；
    img = title.wqcaseimg_set.all()  
    # 获取id为title_id的所有图片 (用object.classname_set.all())
    ctx = {
        'blog': title,
        'image':img
    }
    return render(request, '工程案例details.html', ctx)
```

urls.py

```python
 from django.conf.urls import url
 url(r'^detail/(\d+)/$', get_details, name='blog_get_detail'),
```

多对多的增删改查

代码:

models.py

```python
class BlogInfo(models.Model):
    title = models.CharField(verbose_name='博客标题', max_length = 150)  
    author = models.CharField(verbose_name='作者', max_length=20, default='')
    content = models.TextField(verbose_name='博客正文')  
    visit = models.IntegerField(default=0, verbose_name='浏览量')
    category = models.ForeignKey(Category, verbose_name='分类', on_delete=models.CASCADE, null=True, blank=True) #多对一（博客--类别）
    tag = models.ManyToManyField(Tag, verbose_name='标签', default='') #(多对多）         
    add_time = models.DateTimeField(verbose_name='创建时间', default=datetime.now)
    class Meta:
        verbose_name = '博客'
        verbose_name_plural = verbose_name
    def __str__(self):
        return self.title
    
class Tag(models.Model):
    name = models.CharField(verbose_name='名称',max_length=30)
    class Meta:
        verbose_name = "标签"
        verbose_name_plural = verbose_name
    def __str__(self):
        return self.name
```

views.py

```python
from pure_pagination import Paginator 
def check(className, name=None):
    try:
        cate = className.objects.get(name=name)
        if cate:
            return cate
    except Exception as e:
        return None

class AddView(View):
    def get(self, request):
        return render(request, 'addblog.html', {})

    def post(self, request):
        add_form = AddForm(request.POST)
        if add_form.is_valid():
            title = request.POST.get('title', '')
            if BlogInfo.objects.filter(title=title):
                return render(request, 'addblog.html', {'msg': "标题已存在,请换一个"})
            content = request.POST.get('content', '')
            author = request.POST.get('author', '')
            category = request.POST.get('category', '')
            tags = request.POST.get('tag', '')
            # 标签有多个,当用户在前端页面填写标签时,用空格分开.后台会以空格分割来存入标签
            tag_list = tags.split(' ')
            cates = check(Category, name=category)
            # 次对象一对多的关系,我们在后台数据库中查找是否有相同字段,有就不增加,没就创建一个对象
            if cates is not None:
                blog = BlogInfo.objects.create(title=title, author=author, content=content, category=cates)
                for t in tag_list:
                    # 我们前端获取的标签分割为一个列表,所以for循环分别存储标签名
                    tags = check(Tag, name=t)
                    # 同理, 在数据库中查找是否相同标签
                    if tags is not None:
                        # 不存在就创建一个标签名
                        t = Tag.objects.get(name=tags.name)
                        # 然后将标签名添加到这个blog对象,使用1_objects.name,add(2_objects)
                        #                               对象.字段名.add(对象)
                        blog.tag.add(t)
                    else:
                        tag = Tag.objects.create(name=t)
                        blog.tag.add(tag)
            else:
                cates = Category.objects.create(name=category)
                blog = BlogInfo.objects.create(title=title, author=author, content=content, category=cates)
                for t in tag_list:
                    tags = check(Tag, name=t)
                    if tags is not None:
                        t = Tag.objects.get(name=tags.name)
                        blog.tag.add(t)
                    else:
                        tag = Tag.objects.create(name=t)
                        blog.tag.add(tag)
            blog_lists = BlogInfo.objects.all().order_by('-add_time')
            # 分页,获取对象中的所有元素并以字段add_time排序
            try:
                page = request.GET.get('page', 1)
            except:
                page = 1
            p = Paginator(blog_lists, 5, request=request)
            lists = p.page(page)
            return render(request, 'blog_list.html', {'blog_list':lists, 'page': p}) 
            # return render(request, 'addblog.html', {'msg': "添加成功,返回首页查看!"})
        else:
            return render(request, 'addblog.html', {'add_form': add_form})
```

登录用户后,在前端使用request.user显示登录用户的名字

在views.py里面使用request.user.username显示登录用户的名字

使用is_authenticated()判断用户是否登录成功



##### [Django中富文本编辑器总结](https://www.cnblogs.com/kayb/articles/7257270.html)

- 能够设置文字样式的页面成为文本编辑器(带有格式的文本信息)

## tinyce 配置：

#### 1.在虚拟环境中安装包。

```text
pip install django-tinymce
```

#### 2.在settings.py中为INSTALLED_APPS添加编辑器应用。 

```python
INSTALLED_APPS = (...,'tinymce',)
```

#### 3.在settings.py中添加编辑器配置。

```python
TINYMCE_DEFAULT_CONFIG = {
'theme': 'advanced',
'width': 600,
'height': 400,}
```

#### 4.在test6/urls.py中配置编辑器url。

```python
urlpatterns = [...,url(r'^tinymce/', include('tinymce.urls')),]       
```

## 在后台管理页面中使用(Admin)：

####  1）在booktest/models.py中，定义模型类，模型类的属性为HTMLField()类型。

```python
from django.db import models
from tinymce.models import HTMLField 
class GoodsInfo(models.Model):
	gcontent = HTMLField()
```

#### 2）生成迁移文件。

```python
python manage.py makemigrations
```

#### 3）执行迁移。

```python
python manage.py migrate
```

#### 4）在booktest/admin.py中注册模型类GoodsInfo

```python
from django.contrib import admin
from models import *
admin.site.register(GoodsInfo)
```

