# shiro 解决什么问题  
* 用户身份识别（登录）
* 授权
* 密码加密
* 会话管理  

# 为什么选择shiro
 * **易于使用** - 易用性是这个项目的最终目标。应用安全有可能会非常让人糊涂，令人沮丧，并被认为是“必要之恶”【译注：比喻应用安全方面的编程。】。若是能让它简化到新手都能很快上手，那它将不再是一种痛苦了。
 * **广泛性** - 没有其他安全框架可以达到Apache Shiro宣称的广度，它可以为你的安全需求提供“一站式”服务。
 * **灵活性** - Apache Shiro可以工作在任何应用环境中。虽然它工作在Web、EJB和IoC环境中，但它并不依赖这些环境。Shiro既不强加任何规范，也无需过多依赖。
 * **Web能力** - Apache Shiro对Web应用的支持很神奇，允许你基于应用URL和Web协议（如REST）创建灵活的安全策略，同时还提供了一套控制页面输出的JSP标签库。
 * **可插拔** - Shiro干净的API和设计模式使它可以方便地与许多的其他框架和应用进行集成。你将看到Shiro可以与诸如Spring、Grails、Wicket、Tapestry、Mule、Apache Camel、Vaadin这类第三方框架无缝集成。
 * **支持** - Apache Shiro是Apache软件基金会成员，这是一个公认为了社区利益最大化而行动的组织。项目开发和用户组都有随时愿意提供帮助的友善成员。像Katasoft这类商业公司，还可以给你提供需要的专业支持和服务。
 # 核心概念  
   Subject，SecurityManager和Realms。  
* Subject 
```
在考虑应用安全时，你最常问的问题可能是“当前用户是谁？”或“当前用户允许做X吗？”。当我们写代码或设计用户界面时，问自己这些问题很平常：应用通常都是基于用户故事构建的，并且你希望功能描述（和安全）是基于每个用户的。所以，对于我们而言，考虑应用安全的最自然方式就是基于当前用户。Shiro的API用它的Subject概念从根本上体现了这种思考方式。  

Subject一词是一个安全术语，其基本意思是“当前的操作用户”。称之为“用户”并不准确，因为“用户”一词通常跟人相关。在安全领域，术语“Subject”可以是人，也可以是第三方进程、后台帐户（Daemon Account）或其他类似事物。它仅仅意味着“当前跟软件交互的东西”。但考虑到大多数目的和用途，你可以把它认为是Shiro的“用户”概念。在代码的任何地方，你都能轻易的获得Shiro Subject，参见如下代码：  

清单1. 获得Subject

Subject currentUser = SecurityUtils.getSubject();
一旦获得Subject，你就可以立即获得你希望用Shiro为当前用户做的90%的事情，如登录、登出、访问会话、执行授权检查等 - 稍后还会看到更多。这里的关键点是Shiro的API非常直观，因为它反映了开发者以‘每个用户’思考安全控制的自然趋势。同时，在代码的任何地方都能很轻松地访问Subject，允许在任何需要的地方进行安全操作。
```
* SecurityManager  
```
Subject的“幕后”推手是SecurityManager。Subject代表了当前用户的安全操作，SecurityManager则管理所有用户的安全操作。它是Shiro框架的核心，充当“保护伞”，引用了多个内部嵌套安全组件，它们形成了对象图。但是，一旦SecurityManager及其内部对象图配置好，它就会退居幕后，应用开发人员几乎把他们的所有时间都花在Subject API调用上。  
那么，如何设置SecurityManager呢？嗯，这要看应用的环境。例如，Web应用通常会在Web.xml中指定一个Shiro Servlet Filter，这会创建SecurityManager实例，如果你运行的是一个独立应用，你需要用其他配置方式，但有很多配置选项。  
一个应用几乎总是只有一个SecurityManager实例。它实际是应用的Singleton（尽管不必是一个静态Singleton）。跟Shiro里的几乎所有组件一样，SecurityManager的缺省实现是POJO，而且可用POJO兼容的任何配置机制进行配置 - 普通的Java代码、Spring XML、YAML、.properties和.ini文件等。基本来讲，能够实例化类和调用JavaBean兼容方法的任何配置形式都可使用。
为此，Shiro借助基于文本的INI配置提供了一个缺省的“公共”解决方案。INI易于阅读、使用简单并且需要极少依赖。你还能看到，只要简单地理解对象导航，INI可被有效地用于配置像SecurityManager那样简单的对象图。注意，Shiro还支持Spring XML配置及其他方式，但这里只我们只讨论INI。

下列清单2列出了基于INI的Shiro最简配置：

清单2. 用INI配置Shiro

[main]
cm = org.apache.shiro.authc.credential.HashedCredentialsMatcher
cm.hashAlgorithm = SHA-512
cm.hashIterations = 1024
# Base64 encoding (less text):
cm.storedCredentialsHexEncoded = false

iniRealm.credentialsMatcher = $cm

[users] 
jdoe = TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJpcyByZWFzb2 
asmith = IHNpbmd1bGFyIHBhc3Npb24gZnJvbSBvdGhlciBhbXNoZWQsIG5vdCB
在清单2中，我们看到了用于配置SecurityManager实例的INI配置例子。有两个INI段落：[main]和[users].

[main]段落是配置SecurityManager对象及其使用的其他任何对象（如Realms）的地方。在示例中，我们看到配置了两个对象：

cm对象，是Shiro的HashedCredentialsMatcher类实例。如你所见，cm实例的各属性是通过“嵌套点”语法进行配置的 - 在清单3中可以看到IniSecurityManagerFactory使用的惯例，这种方法代表了对象图导航和属性设置。
iniRealm对象，它被SecurityManager用来表示以INI格式定义的用户帐户。
[users]段落是指定用户帐户静态列表的地方 - 为简单应用或测试提供了方便。

就介绍而言，详细了解每个段落的细节并不是重点。相反，看到INI配置是一种配置Shiro的简单方式才是关键。关于INI配置的更多细节，请参见Shiro文档。

清单3. 装入shiro.ini配置文件

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.util.Factory;



//1.装入INI配置 
Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");

//2. 创建SecurityManager 
SecurityManager securityManager = factory.getInstance();

//3. 使其可访问 
SecurityUtils.setSecurityManager(securityManager);
在清单3的示例中，我们看到有三步：

装入用来配置SecurityManager及其构成组件的INI配置文件；
根据配置创建SecurityManager实例（使用Shiro的工厂概念，它表述了工厂方法设计模式）；
使应用可访问SecurityManager Singleton。在这个简单示例中，我们将它设置为VM静态Singleton，但这通常不是必须的 - 你的应用配置机制可以决定你是否需要使用静态存储。
```
* Realms  
```
Shiro的第三个也是最后一个概念是Realm。Realm充当了Shiro与应用安全数据间的“桥梁”或者“连接器”。也就是说，当切实与像用户帐户这类安全相关数据进行交互，执行认证（登录）和授权（访问控制）时，Shiro会从应用配置的Realm中查找很多内容。

从这个意义上讲，Realm实质上是一个安全相关的DAO：它封装了数据源的连接细节，并在需要时将相关数据提供给Shiro。当配置Shiro时，你必须至少指定一个Realm，用于认证和（或）授权。配置多个Realm是可以的，但是至少需要一个。

Shiro内置了可以连接大量安全数据源（又名目录）的Realm，如LDAP、关系数据库（JDBC）、类似INI的文本配置资源以及属性文件等。如果缺省的Realm不能满足需求，你还可以插入代表自定义数据源的自己的Realm实现。下面的清单4是通过INI配置Shiro使用LDAP目录作为应用Realm的示例。

清单4. Realm配置示例片段：连接存储用户数据的LDAP

[main]
ldapRealm = org.apache.shiro.realm.ldap.JndiLdapRealm
ldapRealm.userDnTemplate = uid={0},ou=users,dc=mycompany,dc=com
ldapRealm.contextFactory.url = ldap://ldapHost:389
ldapRealm.contextFactory.authenticationMechanism = DIGEST-MD5 
既然已经了解如何建立一个基本的Shiro环境，下面让我们来讨论，作为一名开发者该如何使用这个框架。
```

# shiro 程序实现
1. JWTUtil 实现获取token 、校验token
2. 实现Realm 进行认证和授权
3. 实现AuthenticationToken 返回token值
4. 重新BasicHttpAuthenticationFilter ，实现自己的filter
5. 参考(https://juejin.im/post/59f1b2766fb9a0450e755993)
