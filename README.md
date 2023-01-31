<!---
 Licensed to the Apache Software Foundation (ASF) under one or more
 contributor license agreements.  See the NOTICE file distributed with
 this work for additional information regarding copyright ownership.
 The ASF licenses this file to You under the Apache License, Version 2.0
 (the "License"); you may not use this file except in compliance with
 the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.
-->

# [Apache Log4j 2](https://logging.apache.org/log4j/2.x/)
[![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/{owner}/{repo}/badge)](https://api.securityscorecards.dev/projects/github.com/{owner}/{repo})

Apache Log4j 2 is an upgrade to Log4j that provides significant improvements over its predecessor, Log4j 1.x,
and provides many of the improvements available in Logback while fixing some inherent problems in Logback's architecture.

## 背景与软件简介
Apache Log4j是一个基于Java的日志记录工具。它是由瑞士程序员Ceki Gülcü于2001年开发的，现在是Apache软件基金会的一个项目。 Log4j是几种Java日志框架之一。

Apache Log4j 2是Log4j 1的继任者，2014年7月正式发布版发布。该框架被重写，该版本与Log4j 1的主要差异是：

● 改进的配置语法

● 支持XML和JSON配置

● 改进的过滤器

● 属性（Property）支持

● 标记

● 提高速度

● 模块化：Log4j 2支持插件系统

● 提高了可靠性

● 配置自动重装

## 软件架构
● 组件结构
![image](https://user-images.githubusercontent.com/65814333/215372906-fb47738c-5de6-403b-9725-fc9daca7b68b.png)
使用 Log4j 2 API 的应用程序将从 LogManager 请求具有特定名称的 Logger。LogManager 会定位到相应的 LoggerContext，然后从中获取 Logger。 如果必须创建Logger对象，则
a) 相同的 LoggerConfig 关联名称为 Logger

b) 父包的名称

c) 根 LoggerConfig。 

记录器配置对象是从配置中的 Logger 声明创建的。 LoggerConfig 是关联的使用实际传递 LogEvents 的 Appender。

● LoggerContext对象
LoggerContext 充当 Logging 系统的锚点。 根据具体情况，一个应用程序中可能有多个活动的 LoggerContext

● Configuration对象
每个 LoggerContext 都有一个活动的Configuration。 Configuration包含所有 Appenders、上下文范围的过滤器、LoggerConfigs 并包含对 StrSubstitutor 的引用。 在重新配置期间，将存在两个配置对象。 一旦所有 Logger 都被重定向到新的配置，旧的配置将被停止并丢弃

支持以 XML、JSON、YAML 或属性格式编写的配置文件。
以代码的方式，创建 ConfigurationFactory 和 Configuration 实现。
以代码的方式，调用 Configuration 接口中公开的 API 来添加组件到默认配置。
以代码的方式，调用内部 Logger 类的方法。

● Logger对象
如前所述，Logger 是通过调用 LogManager.getLogger 创建的。 Logger 本身不执行任何直接操作。 它只有一个名称并与 LoggerConfig 相关联。 它扩展了 AbstractLogger 并实现了所需的方法。 随着配置的修改，Logger 可能会与不同的 LoggerConfig 相关联，从而导致它们的行为被修改。

调用具有相同名称的 LogManager.getLogger 方法将始终返回对完全相同的 Logger 对象的引用
Logger x = LogManager.getLogger("wombat"); 
Logger y = LogManager.getLogger("wombat");
x 和 y 指向的是完全相同的 Logger 对象。

● LoggerConfig对象
LoggerConfig 对象是在日志配置中声明 Logger对象 时创建的。 LoggerConfig 包含一组过滤器，它们允许 LogEvent对象 在传递给任何 Appender对象 之前传递。 它包含对应该用于处理事件的 Appender 对象集的引用。

LoggerConfig对象 将被分配一个日志级别。 内置级别集包括 ALL、TRACE、DEBUG、INFO、WARN、ERROR、FATAL 和 OFF。 Log4j 2 还支持自定义日志级别。获得更多粒度的另一种机制是使用标记。 OFF 和 ALL 级别一般不会用于调用日志记录 API。 在配置中指定 OFF 意味着不应该匹配任何日志事件，而指定 ALL 意味着所有事件都匹配，包括自定义事件。 但是，在特殊情况下，无论配置如何，都应始终记录事件，因此可以在记录 API 调用时使用 OFF。 但是，一般建议使用使用具有相应全局标记过滤器的标记来代替。

● Filter对象
除了上一节中描述的自动日志级别过滤之外，Log4j 提供的过滤器，可以在日志内容传递给任何 LoggerConfig 之前生效，可以在日志内容传递给 LoggerConfig 之后，调用任何 Appenders 之前生效、还可以在的日志内容传递给LoggerConfig之后， 调用特定 Appender或调用每个 Appender之前生效。

● Appender对象
根据Logger有选择地启用或禁用记录请求的能力只是图片的一部分。 Log4j 允许记录请求打印到多个目的地。 在 log4j 中，输出目的地称为 Appender。 目前，存在控制台、文件、远程套接字服务器、Apache Flume、JMS、远程 UNIX Syslog 守护进程和各种数据库 API 的附加程序。  一个 Logger 可以附加多个 Appender。

●Log4j 2的架构特性
Log4j 的 API 与实现分开，使应用程序开发人员清楚地知道他们可以使用哪些类和方法，同时确保向前兼容性。这允许 Log4j 团队以安全且兼容的方式改进实现。
Log4j API 是一个日志外观，当然可以与 Log4j 实现一起使用，但也可以在其他日志实现之前使用，例如 Logback。与 SLF4J 相比，Log4j API 有几个优点：
Log4j API 支持记录消息，而不仅仅是字符串。
Log4j API 支持 lambda 表达式。
Log4j API 提供了比 SLF4J 更多的日志记录方法。
除了 SLF4J 支持的“参数化日志”格式之外，Log4j API 还支持使用 java.text.MessageFormat 语法的事件以及 printf 样式的消息。
Log4j API 提供了一个 LogManager.shutdown() 方法。底层日志实现必须实现 Terminable 接口才能使方法生效。
完全支持其他结构，例如标记、日志级别和 ThreadContext（又名 MDC）。

## 历史漏洞
[CVE-2021-44228](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-44228)

Log4Shell漏洞：阿里巴巴于 2021 年 11 月 24 日发现并报告给 Apache ，并于 12 月 9 日在推文中发布了一个涉及 Log4j 2 中远程代码执行的零日漏洞，其描述符为“Log4Shell”（CVE -2021-44228）， 2021. 受影响的服务包括Cloudflare、iCloud、Minecraft：Java 版、 Steam、腾讯 QQ和Twitter。 Apache 软件基金会分配了最大CVSS Log4Shell 的严重等级为 10，因为数百万台服务器可能容易受到该漏洞的攻击。网络安全公司Tenable将该漏洞描述为“过去十年中最大、最关键的漏洞” ，Lunasec 的 Free Wortley 将其描述为“灾难性的设计失败”。在美国，网络安全和基础设施安全局(CISA)主任Jen Easterly称该漏洞利用“至关重要”，并建议供应商优先考虑软件更新，和德国机构联邦信息安全办公室(BSI)将该漏洞指定为处于最高威胁级别，称其为“极其严重的威胁情况”（翻译）。加拿大网络安全中心(CCCS) 呼吁各组织立即采取行动。

可以通过配置设置禁用导致漏洞的功能，该设置已在 Log4j 版本 2.15.0-rc1（2021 年 12 月 6 日正式发布，漏洞发布前三天）中删除 ，并替换为各种设置限制远程查找，从而减轻漏洞。为提高安全性，此漏洞所基于的所有使用JNDI的功能将默认禁用，并且从版本 2.16.0 开始删除对消息查找的支持。

[CVE-2021-44832](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-44832)	

Apache Log4j2 版本 2.0-beta7 到 2.17.0（不包括安全修复版本 2.3.2 和 2.12.4）容易受到远程代码执行 (RCE) 攻击，在这种攻击中，有权修改日志配置文件的攻击者可以构建恶意配置将 JDBC Appender 与引用 JNDI URI 的数据源一起使用，该 JNDI URI 可以执行远程代码。此问题已通过将 JNDI 数据源名称限制为 Log4j2 版本 2.17.1、2.12.4 和 2.3.2 中的 java 协议来解决。
