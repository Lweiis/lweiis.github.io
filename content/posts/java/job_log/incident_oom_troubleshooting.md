---
title: 一次内存溢出问题排查
date: 2023-12-11
categories:
  - Java
tags:
  - Java
  - Troubleshooting
summary: 分析问题不能草率，应有理有据
series:
  - 开发记录
draft: false
---
## 某周四下午
MQ 堆积，偶发性接口响应慢  

## 第一次排查
发现下午 CPU 飙高时间，与 MQ 堆积时间点吻合  
同事：”近日新增了下午开始的定时任务，会发出大量 MQ“。  
我们很快达成一致，将锅甩给了定时任务引起了资源占用  
向老板反馈：**定时任务时间需要挪到半夜闲时，并按此方案解决**

## 错误发酵
由于未复现问题和精准定位，并草率给出结论，问题再次浮现

**第二周的周五下午**：偶发性接口响应慢，1/4服务器CPU飙高，发现内存溢出异常

## 第二次排查  
1. 判断可能是由于某些**异常代码的执行或调用引起**的
	1. 查询问题前后时间范围内的 **API 网关记录**
	2. 从调用次数、响应时间上，**过滤出接口 A 和接口 B**
	3. 其中 A 为数据查询，外部调用次数非常频繁，且有少量明显超时
	4. B 为数据导出，响应时长正常可达分钟级
2. 内存快照分析
	1. 尝试导出异常服务器上的**内存快照**，**失败**，因为16gb 的文件，从跳板机导出受云桌面和网络限制，几乎无法正常导出（必中断）
	2. 从公司内部云服务找到**线上 JVM 分析工具**，尝试使用 **Arthas 分析内存快照和GC**  
	   能发现 full GC 次数明显大于其他正常服务器  
	   但**内存快照分析失败**（文件体积和线上工具性能制约）
	4. 此时发现线上 JVM 分析工具能提供出内存快照下载
	5. 开始下载内存快照至云桌面，企图于本地使用 VisualVM 进行分析
	6. VisualVM 加载内存快照预估 1.5 小时，时间也来到晚 8 点，俩人再次达成一致：先回家，到家再看
	7. Jconsole 终于加载完了，可能云桌面性能问题，堆内存分析和类列表加载仍然卡在 loading 无法分析（云桌面内存占用已经 98%）
3. VisualVM 分析失败后，我尝试将**内存快照导入至 IDEA Profiler** 中
	1. 加载成功（给 IDEA 点赞!!!），发现了海量的 C 类变量，明显与正常服务器的类变量不同
	2. 经过对 C 类的调用查询，定位到了接口 B、C、D 等，但按时间上看，接口 B 的嫌疑最大
	3. 于本地环境调用接口 B，成功引起本地服务 OOM 功能（我太傻了，我一开始就这么做不就好了）
	4. 时间 24 点，暂时定论为接口 B 引起，问题延迟至周一解决  

**第三周的周一早上**：于测试环境调用接口 B，成功复现问题：内存快速溢出、CPU 占用高、服务器逐渐不可用  
## 代码分析、解决
1. 代码逻辑：查询并构建出树状数据，导出为 Excel，最差查询场景单次查询会达到10W+数据量，且有循环子查询场景，即：多个大数据量查询。  
2. 问题点：
	1. 同事之前对该功能做了查询优化，限制了查询字段，但优化中丢失了某个过滤条件，导致最差查询场景下会进行**千万级数据的全表查询**
	2. 代码对大批量数据的查询处理不太正确，不应一次查询数万的数据
3. 解决方案：
	1. **熔断**：网关层面禁止了该接口的调用，返回空结果  
	2. **重启**：重启内存溢出的服务器节点，使其恢复正常运行
	3. **代码修复**
		1. 补全丢失的查询逻辑
		2. 优化大批量数据查询：
			1. 原始方式：find(...)
			2. 优化后：先获取 MongoCursor，[参考[1]](https://mongodb.github.io/mongo-java-driver/4.4/apidocs/mongodb-driver-sync/com/mongodb/client/MongoCursor.html)，调用 MongoCursor 的 foreachRemaing 方法进行迭代处理，[参考[2]](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Iterator.html?is-external=true#forEachRemaining(java.util.function.Consumer))
4. 漏了什么没做？
	1. 对于要查询返回给用户的数据，应该先对查询条件和查询量做校验
	2. 查询条件为空的，即获取全量数据，要与需求业务方讨论是否合理
	3. 数据查出前应先查询数据量，对数据量做**保险丝校验**，超过上限值应直接报错，禁止数据查出和返回，防止服务雪崩。
	4. 预想方案：代理模式，拦截 SQL ，预先执行 count 查询和判断
   
## 总结
1. 分析问题不能草率，应有理有据。
2. 写代码时，对于可能引起的异常场景要心里有数，应有最基本的压力测试，不能写过且过。

## 引用：
[[参考1] MongoCursor](https://mongodb.github.io/mongo-java-driver/4.4/apidocs/mongodb-driver-sync/com/mongodb/client/MongoCursor.html)  
[[参考2] Iterator](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Iterator.html?is-external=true#forEachRemaining(java.util.function.Consumer))