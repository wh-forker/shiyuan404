---
layout: page
title: c++_libuuid
permalink: /c++_libuuid/
tags: c++
---

###Preface:

UUID(Universally Unique Identifier)ͨ��Ψһʶ���룬��һ�����������׼��Ŀ�����÷ֲ�ʽϵͳ���е�Ԫ�ض���Ψһ�ı�ʶ��UUID����32λ��16��������ɣ������ϻ��� \\(2^{128}\\)����   
���ó�����ĳЩ���������õ����ֶ���Ϊ��ʶ�û��ķ�ʽ���ǳ����ױ�������ͨ�������ֶλ�ȡ��ʶ���ɹ�����ʱ��Ҫʹ���㷨�����ɣ�û�й��ɡ����ظ����̶����ȵ��ַ�����   


###Install:

1.`wget http://sourceforge.net/projects/libuuid/files/libuuid-1.0.3.tar.gz/download`   
2.`tar xvf libuuid-1.0.3.tar.gz`   
3.`./configure  -prefix /home/xxx/xxx/thirdparty/libuuid`
4.`make && make install`

###Basic:
�㷨��ԭ���ž��Ǹ���ʱ�䣬������������������ɹ̶����ȵ��ַ�����libuuid���ʵ�ַ�ʽ��Ҫ�����֣������ؿ���������ʱ������� `uuid_generate_random(uuid)`���������ʱ�������� `uuid_generate_time(uuid)`   
linux���������� `/dev/random` �� `/dev/urandom` �в�����
�����г�һЩ�����ĺ�����

{% highlight c %}
typedef unsigned char uuid_t[16];

void uuid_clear(uuid_t uu);							  // ���uuid
int uuid_compare(const uuid_t uu1, const uuid_t uu2); // �Ƚ�uuid
void uuid_copy(uuid_t dst, const uuid_t src);         // uuid��ֵ

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

