---
layout: page
title: c++_libxml2
permalink: /c++_libxml2/
tags: c++
---
###Preface:

XML(Extensible Markup Language)是一种结构性较强的标记语言，可以用于一些项目的管理配置文件。同JSON相比，XML显得更为臃肿，且解析难易上更为复杂，但是考虑到XML更易于扩展更直观，而且作为管理配置文件对内存和时间的要求并不太高，XML并不失为一种好的管理配置文件格式。最近在学习c++，因此有必要学习一下Libxml2(基于c的开源XML解析包)。   

###Install：   
1.`wget http://ftp.gnome.org/pub/GNOME/sources/libxml2/2.6/libxml2-2.6.30.tar.gz`   
2.`tar vxf libxml2-2.6.30.tar.gz`   
3.`./configure -prefix /home/xxx/xxx/libxml2` 设置安装路径(必须是绝对路径)   
4.`make && make install`

###Basic:
假设我们已经写好了某个 xxx.cc ，一般执行   

```
g++ -g -I../../thirdparty/libxml2/include/libxml2 \
-L../../thirdparty/libxml2/lib -lxml2 xxx.cc -o xxx 
```

进行编译，然后运行 `./xxx` ，仅对一个文件，我们就要进行如此复杂的操作，因此需要用Makefile来管理，定义目录结构如下：  
 
```
┌─ .
├─ thirdparty
│      └─ libxml2
├─  common
│      └─ libxml2_test
│             ├─ Makefile 
│             ├─ produceXML.cc
└             └─ parseXML.cc
```

这里简单地用c++实现了XML文件创建和解析，所对应Makefile文件内容如下：   

{% highlight make %}
# Makefile
GCC = /usr/bin/g++
CPPFLAGS = -g -O3

INCLUDE = -I../../thirdparty/libxml2/include/libxml2

LIBS = -L../../thirdparty/libxml2/lib -lxml2

all:
	$(GCC) $(CPPFLAGS) produceXML.cc -o produceXML $(INCLUDE) $(LIBS)
	$(GCC) $(CPPFLAGS) parseXML.cc -o parseXML $(INCLUDE) $(LIBS)
	./produceXML > test.xml
clean:
	rm -rf produceXML
	rm -rf parseXML
	rm -rf test.xml
{% endhighlight %}

这里列出一些基本的函数:   

{% highlight c %}
typedef unsigned char xmlChar;
#define BAD_CAST (xmlChar *)

xmlDocPtr doc = xmlNewDoc(BAD_CAST "1.0"); // 创建xml
xmlDocPtr doc = xmlReadFile(file, "UTF-8", XML_PARSE_NOBLANKS); // 读取xml文件
xmlSaveFormatFileEnc(file, doc, "UTF-8", 1); // 保存xml到文件
xmlNodePtr node = xmlNewNode(NULL, BAD_CAST "XXX") // 创建一个节点
xmlDocSetRootElement(doc, root_node); // 每个文档需要有一个根节点
xmlNodePtr root = xmlDocGetRootElement(doc); // 读取文档时需要获取根节点
xmlNewChild(father, NULL, BAD_CAST "XXX", BAD_CAST "XXX_content"); // 创建一个子节点
xmlAddChild(father, son); // 创建一个子节点
xmlNewProp(node, BAD_CAST "XXX", BAD_CAST "XXX_value"); // 添加节点属性
xmlStrcmp(node->name , BAD_CAST "XXX"); // 判断节点是否为 XXX
xmlGetProp(node, BAD_CAST "XXX"); // 获取节点的XXX属，返回类型为 unsigned char*
xmlFreeDoc(doc); // 释放文档指针
xmlMemoryDump(); // 释放所有内存资源
{% endhighlight %}

下面是一个产生XML文件的c代码：   
{% highlight c++ %}
// produceXML.cc
#include <stdio.h>
#include <libxml/tree.h>

int main(int argc,char** argv){

	xmlDocPtr doc = NULL;
	xmlNodePtr proxy = NULL;

	doc = xmlNewDoc(BAD_CAST "1.0");

	proxy = xmlNewNode(NULL, BAD_CAST "Proxys");

	xmlDocSetRootElement(doc,proxy);

	xmlNodePtr proxyObjects = xmlNewNode(NULL, BAD_CAST "ProxyObjects");
	
	xmlAddChild(proxy,proxyObjects);


	// Add filterProxy start
	xmlNodePtr filterProxy = xmlNewNode(NULL, BAD_CAST "Proxy");
	xmlAddChild(proxyObjects, filterProxy);

	xmlNewProp(filterProxy, BAD_CAST "name", BAD_CAST "filterProxy");
	xmlNewProp(filterProxy, BAD_CAST "so" , BAD_CAST "../plugins/filterProxy.so");

	xmlNodePtr filterResource = xmlNewNode(NULL, BAD_CAST "Resource");
	xmlAddChild(filterProxy, filterResource);
	
	xmlNewProp(filterResource, BAD_CAST "name", BAD_CAST "filterResource");
	xmlNewProp(filterResource, BAD_CAST "class", BAD_CAST "filterResource");

	xmlNodePtr filterConfig = xmlNewNode(NULL, BAD_CAST "Config");
	xmlAddChild(filterResource, filterConfig);
	xmlNewProp(filterConfig, BAD_CAST "name", BAD_CAST "THRESHOLD");
	xmlNewProp(filterConfig, BAD_CAST "value", BAD_CAST "0.0001");
	// Add filterProxy end

	// copy  start
	xmlNodePtr strModelProxy = xmlNewNode(NULL, BAD_CAST "Proxy");
	xmlAddChild(proxyObjects, strModelProxy);

	xmlNewProp(strModelProxy, BAD_CAST "name", BAD_CAST "strModelProxy");
	xmlNewProp(strModelProxy, BAD_CAST "so", BAD_CAST "../plugins/strModelProxy.so");

	xmlNodePtr strModelResource = xmlNewNode(NULL, BAD_CAST "Resource");
	xmlAddChild(strModelProxy, strModelResource);

	xmlNewProp(strModelResource, BAD_CAST "name", BAD_CAST "strModelResource");
	xmlNewProp(strModelResource, BAD_CAST "class", BAD_CAST "strModelResource");
	// copy end

	xmlNodePtr taxi = xmlNewNode(NULL,BAD_CAST "ProxySchedules");
	xmlAddChild(proxy, taxi);

	xmlNewProp(taxi, BAD_CAST "name", BAD_CAST "taxi");
	
	xmlNodePtr pFilterProxy = xmlNewNode(NULL, BAD_CAST "Phase");
	xmlNewProp(pFilterProxy, BAD_CAST "proxy", BAD_CAST "filterProxy");
	xmlAddChild(taxi, pFilterProxy);

	xmlNodePtr pStrModelProxy = xmlNewNode(NULL, BAD_CAST "Phase");
	xmlNewProp(pStrModelProxy, BAD_CAST "proxy", BAD_CAST "strModelProxy");
	xmlAddChild(taxi, pStrModelProxy);

	xmlSaveFormatFileEnc(argc>1?argv[1]:"-",doc,"UTF-8",1);
	xmlFreeDoc(doc);
	xmlCleanupParser();
	xmlMemoryDump();
	return 0;
}
{% endhighlight %}

产生结果如下：   

```
<?xml version="1.0" encoding="UTF-8"?>
<Proxys>
  <ProxyObjects>
    <Proxy name="filterProxy" so="../plugins/filterProxy.so">
      <Resource name="filterResource" class="filterResource">
        <Config name="THRESHOLD" value="0.0001"/>
      </Resource>
    </Proxy>
    <Proxy name="strModelProxy" so="../plugins/strModelProxy.so">
      <Resource name="strModelResource" class="strModelResource"/>
    </Proxy>
  </ProxyObjects>
  <ProxySchedules name="taxi">
    <Phase proxy="filterProxy"/>
    <Phase proxy="strModelProxy"/>
  </ProxySchedules>
</Proxys>
```

下面是一个解析XML文件的c代码：   
{% highlight c++ %}
// parseXML.cc
#include <stdio.h>
#include <libxml/parser.h>
#include <libxml/tree.h>
#include <string.h>
#include <iostream>

int parseProxyObjects(xmlNodePtr cur);
int parseProxySchedules(xmlNodePtr cur);

int main(int argc,char** argv){
	
	int ret = 0;
	char* file = (char*)"test.xml";
	xmlDocPtr doc = xmlReadFile(file, "UTF-8", XML_PARSE_NOBLANKS);
	if(doc == NULL){
		printf("read file failed\n");
		return 1;
	}

	xmlNodePtr cur = xmlDocGetRootElement(doc);
	if(cur == NULL){
		printf("empty file\n");
		return 1;
	}

	if(xmlStrcmp(cur->name , BAD_CAST "Proxys")){
		printf("wrong type of root, %s not equals PROXY\n",cur->name);
		return 1;
	}

	for(cur = cur->xmlChildrenNode; cur != NULL; cur = cur->next){
		if(xmlStrcmp(cur->name, BAD_CAST "ProxyObjects") == 0){
			ret |=  parseProxyObjects(cur);			
		}
		else if(xmlStrcmp(cur->name, BAD_CAST "ProxySchedules") == 0){
			xmlChar* tmp = xmlGetProp(cur, BAD_CAST "name");
			if(strcmp(reinterpret_cast<char*>(tmp),"taxi") == 0){
				printf("\n┌─schedule=[taxi]\n");
				ret |= parseProxySchedules(cur);
			}
		}
	}
	puts("");
	if(ret){
		printf("there is something wrong\n");
	}
	return ret;
	
}

int parseProxyObjects(xmlNodePtr cur){
	
	xmlNodePtr node = cur->xmlChildrenNode;
	xmlChar* tmp = NULL;
	std::string proxy_name,proxy_so,resource_name,resource_class,config_name,config_value;

	for(; node != NULL; node = node->next){
		if(xmlStrcmp(node->name, BAD_CAST "Proxy") == 0){
			
			tmp = xmlGetProp(node, BAD_CAST "name");
			proxy_name = reinterpret_cast<char*>(tmp);
			
			tmp = xmlGetProp(node, BAD_CAST "so");
			proxy_so = reinterpret_cast<char*>(tmp);
			
			printf("\n┌─type=[Proxy] name=[%s] so=[%s]\n",proxy_name.c_str(),proxy_so.c_str());
			xmlNodePtr resource_node = node->xmlChildrenNode;
			for(; resource_node != NULL; resource_node = resource_node->next){	
				if(xmlStrcmp(resource_node->name,BAD_CAST "Resource")){
					printf("wrong type of Resource\n");
					return 1;
				}

				tmp = xmlGetProp(resource_node, BAD_CAST "name");
				resource_name = reinterpret_cast<char*>(tmp);
				
				tmp = xmlGetProp(resource_node, BAD_CAST "class");
				resource_class = reinterpret_cast<char*>(tmp);

				printf("└───type=[resource] name=[%s] class=[%s]\n",resource_name.c_str(),resource_name.c_str());

				xmlNodePtr config_node = resource_node->xmlChildrenNode;
				for(; config_node != NULL; config_node = config_node->next){

					if(xmlStrcmp(config_node->name,BAD_CAST "Config")){
						printf("wrong type of Config\n");
						return 1;
					}
					tmp = xmlGetProp(config_node, BAD_CAST "name");
					config_name = reinterpret_cast<char*>(tmp);

					tmp = xmlGetProp(config_node, BAD_CAST "value");
					config_value = reinterpret_cast<char*>(tmp);
					
					printf("└─────type=[config] name=[%s] value=[%s]\n",config_name.c_str(),config_value.c_str());
				}
			}
		}
	}
	return 0;
}

int parseProxySchedules(xmlNodePtr cur){
	
	xmlNodePtr node = cur->xmlChildrenNode;
	xmlChar* tmp = NULL;
	std::string proxy;

	for(; node != NULL; node = node->next){
		if(xmlStrcmp(node->name, BAD_CAST "Phase")){
			printf("wrong type of Phase\n");
			return 1;
		}
		tmp = xmlGetProp(node, BAD_CAST "proxy");
		proxy = reinterpret_cast<char*>(tmp);
		printf("└───proxy=[%s]\n",proxy.c_str());
	}
	return 0;
}
{% endhighlight %}

解析结果如下：   

```

┌─type=[Proxy] name=[filterProxy] so=[../plugins/filterProxy.so]
└───type=[resource] name=[filterResource] class=[filterResource]
└─────type=[config] name=[THRESHOLD] value=[0.0001]

┌─type=[Proxy] name=[strModelProxy] so=[../plugins/strModelProxy.so]
└───type=[resource] name=[strModelResource] class=[strModelResource]

┌─schedule=[taxi]
└───proxy=[filterProxy]
└───proxy=[strModelProxy]

```
