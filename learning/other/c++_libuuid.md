---
layout: page
title: c++_libuuid
permalink: /c++_libuuid/
tags: c++
---

###Preface:

UUID(Universally Unique Identifier)通用唯一识别码，是一种软件建构标准，目的是让分布式系统所有的元素都有唯一的标识。UUID是由32位的16进制数组成，理论上会有 \\(2^{128}\\)个。   
适用场景：某些工程里会采用递增字段作为标识用户的方式，非常容易被攻击者通过暴力手段获取标识生成规则，这时需要使用算法来生成，没有规律、不重复、固定长度的字符串。   


###Install:

1.`wget http://sourceforge.net/projects/libuuid/files/libuuid-1.0.3.tar.gz/download`   
2.`tar xvf libuuid-1.0.3.tar.gz`   
3.`./configure  -prefix /home/xxx/xxx/thirdparty/libuuid`
4.`make && make install`

###Basic:
算法的原理大概就是根据时间，机器网卡和随机数生成固定长度的字符串。libuuid库的实现方式主要有两种，当本地库存在随机数时，会调用 `uuid_generate_random(uuid)`，否则根据时间来产生 `uuid_generate_time(uuid)`   
linux的随机数会从 `/dev/random` 或 `/dev/urandom` 中产生，
这里列出一些基本的函数：

{% highlight c %}
typedef unsigned char uuid_t[16];

void uuid_clear(uuid_t uu);							  // 清除uuid
int uuid_compare(const uuid_t uu1, const uuid_t uu2); // 比较uuid
void uuid_copy(uuid_t dst, const uuid_t src);         // uuid赋值

void uuid_generate(uuid_t out);
void uuid_generate_random(uuid_t out);
void uuid_generate_time(uuid_t out);
int uuid_generate_time_safe(uuid_t out);

int uuid_is_null(const uuid_t uu);
int uuid_parse(const char *in, uuid_t uu);
void uuid_unparse(const uuid_t uu, char *out);
void uuid_unparse_lower(const uuid_t uu, char *out);
void uuid_unparse_upper(const uuid_t uu, char *out);

time_t uuid_time(const uuid_t uu, struct timeval *ret_tv);
int uuid_type(const uuid_t uu);
int uuid_variant(const uuid_t uu);

{% endhighlight %}

