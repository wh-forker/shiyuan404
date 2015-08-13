---
layout: page
title: c++_libxml2
permalink: /c++_libxml2/
tags: c++
---
###Preface:

XML(Extensible Markup Language)��һ�ֽṹ�Խ�ǿ�ı�����ԣ���������һЩ��Ŀ�Ĺ��������ļ���ͬJSON��ȣ�XML�Եø�Ϊӷ�ף��ҽ��������ϸ�Ϊ���ӣ����ǿ��ǵ�XML��������չ��ֱ�ۣ�������Ϊ���������ļ����ڴ��ʱ���Ҫ�󲢲�̫�ߣ�XML����ʧΪһ�ֺõĹ��������ļ���ʽ�������ѧϰc++������б�Ҫ����ѧϰһ��Libxml2(����c�Ŀ�ԴXML������)��   

###Install��   
1.`wget http://ftp.gnome.org/pub/GNOME/sources/libxml2/2.6/libxml2-2.6.30.tar.gz`   
2.`tar vxf libxml2-2.6.30.tar.gz`   
3.`./configure -prefix /home/xxx/xxx/libxml2` ���ð�װ·��(�����Ǿ���·��)   
4.`make && make install`

###Basic:
���������Ѿ�д����ĳ�� xxx.cc ��һ��ִ��`g++ -g -I../../thirdparty/libxml2/include/libxml2 -L../../thirdparty/libxml2/lib -lxml2 xxx.cc -o xxx` ���б��룬Ȼ������ `./xxx` ������һ���ļ������Ǿ�Ҫ������˸��ӵĲ����������Ҫ��Makefile����������Ŀ¼�ṹ���£�  
 
```
���� .
���� thirdparty
��      ���� libxml2
����  common
��      ���� libxml2_test
��             ���� Makefile 
��             ���� produceXML.cc
��             ���� parseXML.cc
```

������c�򵥵�ʵ����XML�ļ������ͽ���������ӦMakefile�ļ��������£�   

```
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
```

���ھ���ʵ�ֵ��ļ��������Ҫ����ͷ�ļ���`#include <libxml/tree.h>` �� `#include <libxml/parser.h>`   
��������
��������

```
// produceXML.cc
#include <stdio.h>
#include <libxml/tree.h>
#include <libxml/parser.h>

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
```

```
#include <stdio.h>
#include <libxml/parser.h>
#include <libxml/tree.h>
#include <string.h>
#include <iostream>
#include <cstdio>
using namespace std;
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
				printf("\n����schedule=[taxi]\n");
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
			
			printf("\n����type=[Proxy] name=[%s] so=[%s]\n",proxy_name.c_str(),proxy_so.c_str());
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

				printf("��������type=[resource] name=[%s] class=[%s]\n",resource_name.c_str(),resource_name.c_str());

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
					
					printf("������������type=[config] name=[%s] value=[%s]\n",config_name.c_str(),config_value.c_str());
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
		printf("��������proxy=[%s]\n",proxy.c_str());
	}
	return 0;
}
```

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

```

����type=[Proxy] name=[filterProxy] so=[../plugins/filterProxy.so]
��������type=[resource] name=[filterResource] class=[filterResource]
������������type=[config] name=[THRESHOLD] value=[0.0001]

����type=[Proxy] name=[strModelProxy] so=[../plugins/strModelProxy.so]
��������type=[resource] name=[strModelResource] class=[strModelResource]

����schedule=[taxi]
��������proxy=[filterProxy]
��������proxy=[strModelProxy]

```