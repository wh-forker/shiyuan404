---
layout: post
tags: fun
---

Description:

通过实现基于邻域的算法，对韩剧进行推荐    
tags: sklearn Scrapy Django flup nginx mysql      

you can see my codes [here](http://github.com/shiyuan/recommender_demo)


### 1.数据

Scrapy爬虫，阿里的服务器，2M宽带，爬了18.5个小时，获取豆瓣迄今为止489部韩剧下用户的评分。一共是131033条用户信息，每个用户对评分节目的量分布如下：    
{% highlight python %}
{1: 68857, 2: 22067, 3: 11376, 4: 6864, 5: 4572, 6: 3239, 7: 2375, 8: 1828,
9: 1 477, 10: 1179, 11: 922, 12: 799, 13: 683, 14: 586, 15: 456, 16: 386, 
17: 355, 18: 314, 19: 281, 20: 231, 21: 223, 22: 171, 23: 159, 24: 158, 
25: 132, 26: 105, 27: 117, 28: 103, 29: 82, 30: 82, 31: 71, 32: 61, 33: 52,
34: 43, 35: 52, 36: 47, 37: 43, 38: 39, 39: 36, 40: 16, 41: 31, 42: 19,
43: 21, 44: 20, 45: 16, 46: 16, 47: 9, 48: 12, 49: 12, 50: 12, 51: 13, 
52: 8, 53: 12, 54: 12, 55: 7, 56: 5, 57: 15, 58: 8, 59: 6, 60: 4, 61: 9, 
62: 5, 63: 6, 64: 5, 65: 6, 66: 5, 67: 6, 68: 5, 69: 4, 70: 4, 71: 3, 72: 5,
73: 1, 74: 5, 75: 3, 76: 2, 77: 4, 79: 5, 80: 2, 81: 5, 82: 4, 83: 2, 84: 1, 
85: 1, 87: 3, 88: 2, 89: 2, 90: 2, 91: 3, 92: 1, 93: 1, 95: 1, 96: 2, 97: 2, 
101: 1, 103: 1, 104: 1, 105: 2, 106: 1, 109: 1, 110: 2, 111: 1, 112: 2, 
113: 1, 120: 1, 125: 1, 137: 1, 138: 2, 178: 1}
{% endhighlight %}
考虑到评分量过低的用户信息对推荐没有太大帮助，以及推荐算法的时间复杂度，我们筛掉评分不超过10次的用户，剩下4209个用户    
基于邻域的算法按实现方式又分为基于用户和基于内容，侧重点在于前者是挖掘一类人群，后者是挖掘一类兴趣。    

### 2.基于用户的推荐

实现基于用户的推荐，我们要做的是，给你一个用户，我们找到跟它最相似的(空间距离最近的)K个用户，将他们喜欢，但是该用户没看过的节目推荐给它。我们可以用k-nn算法实现kd-tree来寻找这K临近点，那么我们要怎样去取这个K值呢，我们可以先离线处理一下，将数据集按99:1分成训练数据集和测试数据集，通过对测试数据集的预测，我们用准确率，召回率来衡量我们K取值的好坏(相似的可靠性)，用覆盖率来衡量推荐的范围(推荐的覆盖性)：(以下数据的情况为仅删除评分四次以下的用户)    
<table border="1px" align="center">
<tr>
<th>K</th>
<th>Recall</th>
<th>Percision</th>
<th>Coverage</th>
</tr>
<tr>
<td>5</td>
<td>0.699930</td>
<td>0.636306</td>
<td>0.040900</td>
</tr>
<tr>
<td>10</td>
<td>0.899910</td>
<td>0.818107</td>
<td>0.061350</td>
</tr>
<tr>
<td>20</td>
<td>0.999900</td>
<td>0.909008</td>
<td>0.100204</td>
</tr>
<tr>
<td>40</td>
<td>0.899910</td>
<td>0.818107</td>
<td>0.137014</td>
</tr>
<tr>
<td>80</td>
<td>0.899910</td>
<td>0.818107</td>
<td>0.202454</td>
</tr>
</table>

由此可知，K取20是比较理想的。kd树的实现即在K个维度上对点进行分治，时间复杂度按实现方式可以分为O(nklogn)(按每个维度方差切分)和O(nlogn)(随机选择维度切分)。k-nn具体实现参照Udacity上Intro to ML中涉及到的sklearn    

### 3.基于内容的推荐

实现基于内容的推荐，一开始我以为是要具体看每个物品有哪些属性，要从这些属性的相似及权重的角度来给出推荐，后来发现其实不是。用比较通俗的话讲就是，看过这部剧的人很有可能也会看某部剧，同基于用户的推荐，我们要做的也是从用户的评分记录中去挖掘，得到物品之间的相似度。离线计算N个人对M个物品的喜好(建立一个M*M的矩阵，遍历每个人喜欢的物品(i,j),i!=j,分别在M[i][j]和M[j][i]加1)，时间O(NMM)归一化可以得到余弦相似矩阵(这里我采用的做法是每行除以最大值，以此惩罚某些影响太大的物品，这有助于推荐走向多样性)，推荐时需要计算用户对M个物品的兴趣，对每个物品找到最相似的K个累加相似度得到兴趣指数，时间O(MKMlogM)，考虑到服务器相应时间（尽量在2~3s给出推荐结果），我们需要先离线处理相似度矩阵，对于出现在历史中的物品，我们遍历跟它们有关系的物品加上权重，排序后返回推荐列表。    

### 4.韩剧推荐系统

目前挂在阿里云服务器上，截止到3.31号都还可以[访问](http://ccnu.xyz)   
 
主页截图：

<img align="center" src="http://ww1.sinaimg.cn/large/005X3qakjw1ep96cp3euij30mx0e2js8.jpg">

选择偏好（基于用户和基于内容界面一样）：

<img align="center" src="http://ww2.sinaimg.cn/large/005X3nOQjw1ep96g3df4qj30r90hfdp6.jpg">

<img align="center" src="http://ww2.sinaimg.cn/large/005X3pWTjw1ep98unldkhj30ou0gyahp.jpg">

推荐结果页面(两者均有)：

<img align="center" src="http://ww1.sinaimg.cn/large/005X4kGljw1ep96pxqu3wj30o20jmahz.jpg">

基于内容的附了一张依赖表：

<img align="center" src="http://ww2.sinaimg.cn/large/005X3q9sjw1ep96mp9vi2j30q70h2aec.jpg">

### 5.具体实现

配置python，选用高集成的Anaconda2.1.0 32bit(python2.7)，这样我们可以避免一些繁琐的包的安装    
配置sql，选用mysql server 5.5 和 Navicat for Mysql 11.0.10    
配置scrapy，`pip install scrapy`    
配置MySQL-python，按理来说`pip install MySQL-python`可以搞定的，失败后用了`easy_install MySQL-python`    
配置django，`pip install Django`    
配置nginx，官网上下载，用于服务器代理，从而外网访问    
配置flup，用于衔接django和nginx，`easy_install flup`    

安装好以上依赖之后，我们可以先熟悉一下Scrapy和Django这两个python框架：    

Scrapy Part:    

1.`scrapy startproject drate` 自动生成框架代码:    
--items.py    
--piplines.py    
--settings.py    
--__init__.py    
--/spiders/__init__.py    

2.items.py用于定义存放对象，比如我们要爬取豆瓣评分，那么每一个对象包含至少三个属性id，aid，score，分别表示评分节目，用户id以及评分：
{% highlight python %}
from scrapy.item import Item, Field

class DrateItem(Item):
    id = Field()
    aid = Field()
    score = Field()
{% endhighlight %}

3./spiders/目录下新建自己的爬虫rate_spider.py    
代码意思很简单，从本地json文件(我从豆瓣前台抠下来的json包含了489部电影的id,url等)获取id，从而凑出每部韩剧短评的url，start_urls为初始爬虫列表，由于该爬虫是多线程实现，初始会从多个url分别进行。rule定义为爬虫满足的一系列规则，每个Rule规定了爬虫解析页面的url正则式，以及callback对应解析方式。下面的parse方法则是通过xpath进行页面抽离，分别得到若干item对象，这个在前面定义过，直接返回就行了
{% highlight python %}
__author__ = 'shiyuan'
 # -*- coding: UTF-8 -*-
from scrapy.selector import Selector
from scrapy.contrib.spiders import CrawlSpider,Rule
from scrapy.contrib.linkextractors.sgml import SgmlLinkExtractor
from drate.items import *
import json
import sys
reload(sys)
sys.setdefaultencoding('utf8')

class RateSpider(CrawlSpider):
    name = "douban_rate"
    allowed_domains = ["movie.douban.com"]
    start_urls = []
    f = file("drama.json")
    jobj = json.load(f)
    pre_url = u'http://movie.douban.com/subject/'
    end_url = u'/comments?start=0&limit=20&sort=time'
    for data in jobj:
        start_urls.append(pre_url+data['id']+end_url)
    rules = [
        Rule(SgmlLinkExtractor(allow=(r'http://movie.douban.com/subject/\d+/comments\?start=\d+&limit=20&sort=time')),follow=True,callback="parse_item"),
    ]

    def parse_item(self,response):
        item = DrateItem()
        sel = Selector(response)
        id = (response.url).split('/')[-2:-1]
        user = sel.xpath('//*[@id="comments"]//span[@class="comment-info"]/a/@href').extract()
        rate = sel.xpath('//*[@id="comments"]//span[@class="comment-info"]/span/@title').extract()
        score = []
        mp = {
            u'力荐': '5',
            u'推荐': '4',
            u'还行': '3',
            u'较差': '2',
            u'很差': '1'}
        for i in rate:
            score.append(mp[i])
        aid=[]
        for i in user:
            aid.append(i.split('/')[-2:-1][0])
        item['id']=id
        item['score']=score
        item['aid']=aid
        return item
{% endhighlight %}

4.piplines.py 对应为管道控制，配置好本地数据库以及python的MySQLdb，我们只需要在下面的方法中根据上面item传进来的值，对数据库进行增删改查的操作。下面我的代码实现的是，对应一个用户表，每个人都有一个aid和score，对于每个item(id,aid,score)，我先select该用户是否存在，不存在的话新建一个长度为500的0串，将对应第x位置改为score后插入该数据，若该用户存在，只需update一下对应第x位置的score，由于评分为5分制，所以用一个长度500的串就可以记录所有的评分信息了
{% highlight python %}
from scrapy import log
from twisted.enterprise import adbapi
from scrapy.http import Request
import MySQLdb
import MySQLdb.cursors

class DratePipeline(object):
    def __init__(self):
        self.dbpool = adbapi.ConnectionPool('MySQLdb',
        		host='127.0.0.1',
                db = 'douban',
                user = 'root',
                passwd = 'pikachu',
                cursorclass = MySQLdb.cursors.DictCursor,
                charset = 'utf8',
                use_unicode = False
        )
    def process_item(self, item, spider):
        query = self.dbpool.runInteraction(self._conditional_insert, item)
        query.addErrback(self.handle_error)
        return item

    def _conditional_insert(self,tx,item):
        tx.execute("select no from contents where id= %s",(item['id'][0],))
        no = tx.fetchone()
        no = no['no']
        l = min(len(item['aid']),len(item['score']))
        for i in xrange(l):
            tx.execute('select * from rate where aid=%s',(item['aid'][i],))
            result = tx.fetchone()
            if result:
                pre_score = result['score']
                now_score = pre_score[0:(no-1)]+item['score'][i]+pre_score[(no+1):]
                tx.execute('update rate set score=%s where aid=%s',(now_score,item['aid'][i]))
            else:
                now_score = (no-1)*'0'+item['score'][i]+(500-no)*'0'
                tx.execute('insert into rate (aid,score) values (%s,%s)',(item['aid'][i],now_score))
        log.msg("Item stored in db : %s" % item['id'],level=log.DEBUG)

    def handle_error(self, e):
        log.err(e)
{% endhighlight %}

5.做到这一步，爬虫基本完成，但是需要伪装一下，否则会一下子被豆瓣发现，出现403的页面，并在两三个小时内，该ip都不得爬取该站页面，配置一下settings.py，DOWNLOAD_DELAY为爬取间隔时间    
{% highlight python %}
BOT_NAME = 'drate'

SPIDER_MODULES = ['drate.spiders']
NEWSPIDER_MODULE = 'drate.spiders'

ITEM_PIPELINES={
    'drate.pipelines.DratePipeline':400,
}
LOG_LEVEL='DEBUG'

DOWNLOAD_DELAY = 2
RANDOMIZE_DOWNLOAD_DELAY = True
USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.5 (KHTML, like Gecko) Chrome/19.0.1084.54 Safari/536.5'
COOKIES_ENABLED = True
{% endhighlight %}

6.开心地运行爬虫吧，在根目录下运行`Scrapy crawl douban_rate`，后面具体根据spiders里面name的定义，这个爬虫会爬上18个小时，可以先懒懒地睡上一觉。    

<img align="center" src="http://ww1.sinaimg.cn/large/005X4kGljw1ep9ygkodm8j30hs0d3mzg.jpg">

<br>

Django Part    

1.`django-admin startproject mysite` 自动生成框架代码：    
--manage.py    
--mysite/__init__.py    
--mysite/urls.py    
--mysite/settings.py    
--mysite/wsgi.py    
我们需要在mysite下添加views.py，models.py，admin.py，并新建两个folder命名为templates和static。其中static放置静态的css和js文件，templates存放需要预留python对象的html文件。models，views，urls三者组成经典的MVC模型      

2.settings.py 定义数据库配置，static和templates路径，以及django中某些特定的板块以及包，如果调用了邮件功能还需进行相应配置，以下代码仅供参考：
{% highlight python %}
import os
import os.path
BASE_DIR = os.path.dirname(os.path.dirname(__file__))

STATIC_ROOT = 'C:\Users\shiyuan\Desktop\mysite\static'

SECRET_KEY = '@ity1&w5l2s%j&0&gb072ox5vsp)0qbb+vkl*=f%f^=u@vjs-w'

DEBUG = True

TEMPLATE_DEBUG = True

ALLOWED_HOSTS = []
  
TEMPLATE_DIRS = [os.path.join(BASE_DIR, 'templates')]

INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'mysite',
)

MIDDLEWARE_CLASSES = (
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
)

ROOT_URLCONF = 'mysite.urls'

WSGI_APPLICATION = 'mysite.wsgi.application'

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'douban',
        'USER': 'root',
        'PASSWORD': 'pikachu',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}

LANGUAGE_CODE = 'en-us'

TIME_ZONE = 'Asia/Shanghai'

USE_I18N = True

USE_L10N = True

USE_TZ = True

STATIC_URL = '/static/'

STATICFILES_DIRS = (
    os.path.join(BASE_DIR, "static"),
)

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_USE_TLS = True
EMAIL_HOST = 'smtp.126.com'  
EMAIL_PORT = 25 
EMAIL_HOST_USER='shiyuan404@126.com'  
EMAIL_HOST_PASSWORD='***********'
{% endhighlight %}

3.urls.py 定义controllers，自定义设置一定的url，返回一些调用方法     
{% highlight python %}
from django.conf.urls import patterns, include, url
from django.contrib import admin
from mysite.view import *
admin.autodiscover()
urlpatterns = patterns('',
    
    url(r'^$',homepage),
    url(r'^admin/', include(admin.site.urls)),
    url(r'^show/$',display_meta),
    url(r'^preference/(\d{1})/$',preference),
    url(r'^user_cf/$',user_CF),
    url(r'^item_cf/$',item_CF),
    url(r'^mails/$',SendEmail),
)
{% endhighlight %}

4.views.py 定义上述所谓的方法，比较通用一点做法是将获取到的数据经过某种计算后，以渲染的方式返回html。我的推荐算法的实践代码也定义在其中，还有邮件的操作，以下代码仅供参考：     
{% highlight python %}
# -*- coding: UTF-8 -*-
from django.http import HttpResponse,Http404,HttpResponseRedirect
import datetime
from django import template
from django.template.loader import get_template
from django.template import Context, loader
from django.shortcuts import render,render_to_response
from mysite.models import *
from django import forms
from django.template import RequestContext
from django.core.mail  import  send_mail
from django.core.mail import EmailMultiAlternatives
from sklearn.neighbors import NearestNeighbors
from sklearn import cross_validation
import math
import urllib
import random

def user_CF(req):
	l = req.GET.getlist('l')
	ans = user_recommendation(l)
	return render_to_response('user_CF.html',{'error':True,'data':ans})
def item_CF(req):
	l = req.GET.getlist('l')
	ans = item_recommendation(l)
	return render_to_response('item_CF.html',{'error':True,'data':ans})

def punish(x):
	if x<3:
		return 1
	if x>3:
		return 5
	return 3

K=20
def user_recommendation(l):
	s=500*'3'
	me=set()
	for i in l:
		no=int(i.encode())
		me.add(no)
		s=s[0:(no-1)]+'5'+s[no:]
	obj=Rates.objects.filter()
	con=Contents.objects.filter().order_by('-no')
	mp=dict()
	for i in con:
		mp[str(i.no)]=str(i.id)
	v=[]
	for i in obj:
		v.append([punish(int(j.encode())) for j in i.score])
	neigh = NearestNeighbors(n_neighbors=K,algorithm='kd_tree',metric='minkowski')
	neigh.fit(v)
	near=neigh.kneighbors([int(j) for j in s],return_distance=False)[0]
	r=dict()
	for i in near:
		for j in range(len(v[i])):
			if r.get(j+1)==None:
				r[j+1]=v[i][j]
			else:
				r[j+1]+=v[i][j]
	r=list(sorted(r,key=r.__getitem__,reverse=True))
	ans=[]
	for i in r:
		if not(i in me):
			ans.append({'id':mp[str(i)],'no':i})
		if len(ans)==10:
			break
	return ans

def item_recommendation(l):
	file = urllib.urlopen('http://localhost/static/items.txt')
	l=[int(i.encode()) for i in l]
	v=[]
	me=set()
	for i in l:
		me.add(i)
	con=Contents.objects.filter().order_by('-no')
	mp_title=dict()
	mp=dict()
	for i in con:
		mp[str(i.no)]=str(i.id)
		mp_title[str(i.no)]=str(i.title)
	while True:
		line = file.readline()
		if not line:
			break
		line=line.strip()
		tmp=line.split(' ')
		v.append([int(i.encode()) for i in tmp])
	item=[{} for i in range(500)]
	for i in l:
		for j in range(500):
			if v[i-1][j]>0:
				item[j][i-1]=v[i-1][j]
	re_item=[]
	val={}
	for i in range(500):
		tmp=sorted(item[i],key=item[i].__getitem__,reverse=True)
		val[i]=0
		for j in item[i]:
			val[i]+=item[i][j]
		re_item.append(tmp[:3])
	li = sorted(val,key=val.__getitem__,reverse=True)
	re=[]
	for i in li:
		if val[i]==0:
		 	break
		if (i+1) in me:
			continue
		re.append(i)
		if len(re)==10:
			break
	ans=[]
	why=[]
	for i in range(500):
		why_title=[mp_title[str(j+1)]for j in re_item[i]]
		why_id=[mp[str(j+1)]for j in re_item[i]]
		why.append([{'id':why_id[j],'title':why_title[j]}for j in range(len(why_id))])

	for i in re:
		ans.append({'id':mp[str(i+1)],'no':i+1,'title':mp_title[str(i+1)],
			'why':why[i]})
	return ans


def preference(req,num):
	try:
		num=int(num)
	except ValueError:
		raise Http404
	if num%3==0:
		q=41
	elif num%3==1:
		q=151
	else:
		q=500
	obj=Contents.objects.filter(no__lt=q)
	num+=1
	if num==3:
		num=2
	if num==6:
		num=5
	return render_to_response('search_form.html', {'error': True,'data':{'data':obj,'tag':num}})


def homepage(req):
	msg=None
	return render_to_response('HomePage.html', {'error': True,'data':msg})


def display_meta(request):
    values = request.META.items()
    values.sort()
    html = []
    for k, v in values:
        html.append('<tr><td>%s</td><td>%s</td></tr>' % (k, v))
    return HttpResponse('<table>%s</table>' % '\n'.join(html))


def SendEmail(req):
	l = req.GET.getlist('l')
	ans=[]
	for i in l:
		con=Contents.objects.filter(no=i)[0]
		ans.append(con)
	title='shiyuan404的推荐'
	context={'error':True,'data':ans}
	sender = 'shiyuan404@126.com'
	if 'm' in req.GET and req.GET['m']:
		m = req.GET.getlist('m')
		mail_list=m
		t = loader.get_template("mailhtml.html")
		html_content = t.render(Context(context))
		msg = EmailMultiAlternatives(title, html_content, sender, mail_list)
		msg.attach_alternative(html_content, "text/html")
		msg.send()
		return render_to_response('successfully_mailed.html',{'error':True,'data':'Successfully mailed , now return to home page  ....'})
	return render_to_response('user_CF.html',{'error':True,'data':ans})

{% endhighlight %}

5.models.py 可以直接用 `python manage.py inspectdb > models.py` 生成，当然根据models.py 生成数据库里的表也是可行的 `python manege.py syncdb` django自带了一个admin管理系统，可以方便业余的人管理数据库，如果需要启用这个功能需要在settings里设置，并在admin.py里注册上系统能管理的数据库表有哪些。

6.运行测试，`python manage.py runserver` 则可以在localhost上看到相应的页面。

<br>

Nginx Part:    

我是部署在阿里云服务器上的，为了想让外网访问我的推荐系统，我选用nginx作为我的代理服务器，配制还是比较方便的，在下载好的nginx中找到nginx.conf，设置static文件路径，具体可以参照以下：
{% highlight python %}
worker_processes  1;

events {
  worker_connections  1024;
}

http {
  include	   mime.types;
  default_type  application/octet-stream;

  sendfile		on;
  
  keepalive_timeout  65;

  server {
    listen	   80;
    server_name  localhost;

  location /static {
    alias C:\Users\shiyuan\Desktop\mysite\static;
  }

    location / {
      fastcgi_pass 127.0.0.1:8801;
      fastcgi_param PATH_INFO $fastcgi_script_name;
      fastcgi_param REQUEST_METHOD $request_method;
      fastcgi_param QUERY_STRING $query_string;
      fastcgi_param CONTENT_TYPE $content_type;
      fastcgi_param CONTENT_LENGTH $content_length;
      fastcgi_param SERVER_PROTOCOL $server_protocol;
      fastcgi_param SERVER_PORT $server_port;
      fastcgi_param SERVER_NAME $server_name;
      fastcgi_pass_header Authorization;
      fastcgi_intercept_errors off;
    }
  }
}
{% endhighlight %}
然后把刚才运行的指令改成`python manage.py runfcgi method=threaded host=127.0.0.1 port=8801` 就可以愉快地玩耍了，记住端口要跟django中设置一致

至此，推荐系统就成型了，关于一些页面上的美化，像我这种审美还是不过多地进行修饰了吧，怎么简单怎么来。

