---
title: "Ubuntu 下配置 SVN"
subtitle: ""
date: 2020-07-10T13:03:07+08:00
lastmod: 2019-07-09T13:03:07+08:00
draft: false
author: "Liusoon"
authorLink: "liusoonfang.space"
description: "井字型少年"

tags: [Ubuntu,SVN]
categories: [学个技术]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc: true
  
math:
  enable: false
lightgallery: true
license: ""
---

<!--more-->

## 什么是SVN  

&emsp;Subversion简称SVN，是一个开放源代码的版本控制系统，相对于的RCS、CVS，采用了分支管理系统，它的设计目标就是取代CVS。在Subversion管理下，文件和目录可以超越时空。Subversion将文件存放在中心版本库里。这个版本库很像一个普通的文件服务器，不同的是，它可以记录每一次文件和目录的修改情况。这样就可以籍此将数据恢复到以前的版本，并可以查看数据的更改细节。互联网上越来越多的控制服务从CVS转移到Subversion。

&emsp;Subversion的设计包括了一个抽象的网络层，这意味着版本库是可以通过各种服务器进程访问的。理论上来会说，Subversion可以使用无限数量的网络协议来实现，甚至可以直接用perl利用相关的接口来编写自定义协议。但日常中，用得最多的就是svn://协议和用mod_dav_svn模块通过Apache服务进行访问。

## Subversion的结构

&emsp;Subversion使用服务端—客户端的结构，当然服务端与客户端可以都运行在同一台服务器上。在服务端是存放着所有受控制数据的Subversion仓库，另一端是Subversion的客户端程序，管理着受控数据的一部分在本地的映射(称为工作副本)。在这两端之间，是通过各种仓库存取层(Repository Access，简称RA)的多条通道进行访问的。这些通道中，可以通过不同的网络协议，例如HTTP、SSH等，或本地文件的方式来对仓库进行操作。

&emsp;Subversion是一种开放源码的全新版本控制系统，支持可在本地访问或通过网络访问的数据库和文件系统存储库。不但提供了常见的比较、修补、标记、提交、回复和分支功能性，Subversion还增加了追踪移动和删除的能力。此外，它支持非ASCII文本和二进制数据，所有这一切都使Subversion不仅对传统的编程任务非常有用，同时也适于 Web 开发、图书创作和其他在传统方式下未采纳版本控制功能的领域。

## 安装Subversion  

&emsp;大部分的发行版都已经提供Subversion套件，Ubuntu可以通过下面的命令进行安装：

&emsp;`apt-get install subversion`

### Subversion套件常用工具  

> - svn 一个命令行式的客户端程序
> - svnversion 报告本地工作副本状态(即当前文档的修订版本号)的程序
> - svnversion 报告本地工作副本状态(即当前文档的修订版本号)的程序
> - svndumpfilter 用于过滤仓库中的数据
> - snvserve 一个定制的、独立的Subversion服务程序，可以作为一个驻留进程或通过ssh调用，以便他人可以通过其访问仓库

## 使用Subversion

&emsp;Subversion一般使用两种后台服务模式：一种是其自己实现的svnserve，可以通过svn协议访问，另一种是借助mod_dav_svn模块，由Apache http服务器来实现，然后通过HTTP或HTTPS协议来访问。

### 通过svn协议访问

#### 1.svn协议

&emsp;客户端若要通过svn协议访问仓库，必须在存放仓库的机器上运行svnserve服务程序。启动该程序后，会监听在3690端口，以响应客户端的访问工作。

```bash  
mkdir /var/local/svndata
svnserve -d -r /var/local/svndata
```

{{< admonition >}}

**-d 表示以后台模式运行，-r用于指定服务的根目录。其余参数，请用svnserve -h查看。**

{{< /admonition >}}

#### 2.创建仓库

&emsp;开始必须先用svnadmin在存放仓库的目录中创建必须的文件，以形成仓库。

&emsp;`svnadmin create /var/local/svndata/dev1`

#### 3. 设置授权  

&emsp;通过svn协议访问远端的仓库，必须建立授权关系。svnserve对于每个仓库，都有一个独立的配置文件和独立的用户、权限管理。权限文件存放在：repository-path/conf/svnserve.conf

&emsp;我这里的位置就是/var/local/svndata/dev1/conf/svnserve.conf，缺省的示例配置文件中和选项都是被注释的。

&emsp;以下是一个简单的例子：

```bash
vi /var/local/svndata/dev1/conf/svnserve.conf

[general]
anon-access = read # 允许匿名用户只读访问，若设置为none表示不允许匿名访问
auth-access = write # 通过验证的用户可以读和写
password-db = passwd # 用户保存文件，也可以使用绝对路径来指定位置
authz-db = authz # 权限管理文件
realm = My First Repository # 仓库名称
```

{{< admonition >}}

**配置项前面不能有前置空格，否则后续执行svn命令时，会报Option expected错误。**

{{< /admonition >}}

#### 4.配置用户和权限

&emsp;在svnserve.conf文件中定义的用户和权限配置文件的中加入相关信息：

- 配置用户

```bash
vi  /var/local/svndata/dev1/conf/passwd

#定义用户
[users]
#以用户名＝密码对方式进行定义
mike = 000000
easylife = 000000
```



- 配置用户权限

``` bash
vi /var/local/svndata/dev1/conf/authz
#定义组
[groups]
#定义属于组的用户
group1 = mike,easylife 

#可定义仓库中的某个相对路径，如[/]，或[/project1]
#若是多个仓库共享一个权限文件，可以用仓库名加“:”开头来定义，如：[project2:/]
#这个project2必须是已经由svnadmin create /path/project2创建好的仓库

[/]
#定义某个用户的权限，r是只读、w是可写、留空是不允许访问
mike = rw
#定义某个组的权限
@group1 = rw
#用“*”号表示其他用户
* = r``
```

{{< admonition >}}

**用户和权限配置文件必须与svnserve.conf中设置的路径相同。若把这两个配置文件放在仓库外，例如：/var/local/svndata/目录，则允许多个仓库共享同样的配置文件。用户和权限配置文件保存后即可生效，不需重启svnserve服务。**

{{< /admonition >}}

#### 5.访问仓库

- 使用svn客户端
- 进行首次导入

```bash
svn import /data0/software/memadmin  svn://192.168.1.109/dev1 -m "initialization"
认证领域: <svn://192.168.1.109:3690> My First Repository“root”的密码: 
认证领域: <svn://192.168.1.109:3690> My First Repository用户名: mike“mike”的密码: 
-----------------------------------------------------------------------
注意!  你的密码，对于认证域:   
<svn://192.168.1.109:3690> My First Repository
只能明文保存在磁盘上!  如果可能的话，请考虑配置你的系统，让 Subversion可以保存加密后的密码。
请参阅文档以获得详细信息。
你可以通过在“/root/.subversion/servers”中设置选项“store-plaintext-passwords”为“yes”或“no”，
来避免再次出现此警告。
-----------------------------------------------------------------------
保存未加密的密码(yes/no)? yes
增加           /data0/software/memadmin/LICENSE.txt
增加           /data0/software/memadmin/apps
增加           /data0/software/memadmin/apps/SaveList.php
......
增加           /data0/software/memadmin/views/memadmin.php
增加           /data0/software/memadmin/index.php
提交后的版本为 1。
```

{{< admonition >}}

**Linux下，默认会先以当前用户去验证，若用户名不匹配，可在后续的提示中输入指定的用户名。输入一次用户名后，svn会把验证结果保留在本地的~/.subversion/auth/svn.simple目录中，下次使用svn时，就不需要再做验证工作了。(除非修改了远端的验证用户名和密码)**

{{< /admonition >}}

- 从SVN仓库签出

```bash
svn co svn://192.168.1.109/dev1/

A    dev1/LICENSE.txt
A    dev1/apps
A    dev1/apps/GetListTime.php
......
A    dev1/views/mem_count.php
A    dev1/views/memadmin.php
取出版本 1。
```

### 通过HTTP协议访问版本库

&emsp;通过HTTP协议访问版本库是Subversion的亮点之一，这种方式具备许多svnserve服务器所没有的特性，使用上更加灵活。

#### 1.mod_dav_svn模块

&emsp;由于Subversion需要版本化的控制，因此标准的HTTP协议不能满足需求。要让Apache与Subversion协同工作，就要使用WebDAV(Web-based Distributed Authoring and Versiong)Web分布式创作和版本控制协议。WebDAV是HTTP 1.1的扩展，关于WebDAV的规范和工作原理，可以参考IETF RFC 2518。

&emsp;mod_dav_svn是Apache服务器的一个插件模块，作为Subversion与Apache之间的接口，通过它Apache就可以访问版本库，并且可以让客户端也使用HTTP的扩展协议WebDAV/DeltaV进行访问。

#### 2.安装和配置  

&emsp;与使用SVN协议的svnserve不同，要通过Apache访问Subversion，除了必须已经部署好Apache和Subversion的基本环境外，当然不可缺少的是Apache的mod_dav_svn模块。

```bash
#安装mod_dav_svn
apt-get install libapache2-svn libsvn1

#启用mod_dav_svn模块
a2enmod dav_svn
```

{{< admonition >}}

**Ubuntu缺省是启用mod_dav_svn模块的。**

{{< /admonition >}}

- 创建版本仓库

&emsp;这与使用SVN协议方式的工作是基本相同的。不同点在于需要让运行Apache的用户拥有SVN仓库根路径的所有权,这样做可以在SVN仓库根路径下创建多个版本库，并且所有版本库都使用同一个用户信息文件和访问权限文件来管理。

```bash
cd /var/local/svndata
svnadmin create dev2
chown -R www-data.www-data /var/local/svndata/
```

- 配置mod_dav_svn

```bash
vi /etc/apache2/mods-enabled/dav_svn.conf

#指定SVN仓库根路径
SVNParentPath /var/local/svndata
```

#### 3.访问仓库

- 访问SVN

- 重启Apache

```/etc/init.d/apache2 restart```

&emsp;通过浏览器访问http://ip/svn/repos，即可在浏览中浏览SVN库中的内容。

#### 4.加入认证信息

&emsp;上面定义的版本仓库，默认是任何人都可以匿名访问，并且拥有完全的写入、读取、修改、提交、删除版本库中信息的权限。因此，我们需要加入认证信息以做权限的管理。HTTP协议版本就提供了简单的客户端认证方式，这可通过Apache配置完成。

&emsp;Apache提供了一个htpasswd工具来管理，使用该工具可以创建一个文件，其中存放着用户名和加密后的密码信息。而这些就是Subversion可以引用的用户了，根据这些用户信息，配合mod_authz_svn模块即可进行目录的访问控制。

- 创建存放用户名信息的文件  

```bash
htpasswd -c /var/local/svndata/passwdfile mike
New password: 		<==输入密码
Re-type new password: 　<==再次输入密码
Adding password for user mike
```

- htpasswd其它用法  

```bash
#新增用户
htpasswd -m /var/local/svndata/passwdfile NewUserName
#修改用户密码
htpasswd -m /var/local/svndata/passwdfile oldUserName
#删除用户
htpasswd -D /var/local/svndata/passwdfile oldUserName
```

{{< admonition >}}

**-m是可选的参数，表示强制使用MD5算法加密，当用-c创建用户名信息文件后，即可修改或添加用户信息。**

{{< /admonition >}}

- 创建存放用户访问权限文件

&emsp;由于采用的是多个版本库使用同一个访问权限控制文件来管理权限，这里就需要使用[repository:/path]的方式定义权限。

&emsp;使用“认证用户(组)=权限”的方式描述每个用户(组)访问版本库的级别：r是只读，rw是可读写，留空是不允许访问。另外，*表示所有用户，可以用于控制匿名用户的访问权限；@表示已经被分组的组名。

```bash
vi /var/local/svndata/authz

[groups]
tester = easylife,test1
developers = dev1,dev2

[dev1:/]
* = r
mike =rw
@tester=rw

[dev2:/]
* = r
mike =rw
@developers=rw
```

- 修改mod_dav_svn配置文件

&emsp;修改Location标签，加入认证部分配置。

```bash
vi /etc/apache2/mods-enabled/dav_svn.conf

#使用基本认证方式，即用户名、密码认证
AuthType Basic 
#在认证对话框中出现的提示信息
AuthName "Subversion Repository" 
#指定上面创建好的存放用户名信息的文件路径
AuthUserFile /var/local/svndata/passwdfile
#指定上面创建好的存放用户访问权限的文件路径
AuthzSVNAccessFile /var/local/svndata/authz
#限定只有用户输入正确的用户名和密码后才能访问该标签所指向的路径
Require valid-user
```

#### 5.访问SVN  

&emsp;使用mod_dav_svn模块，通过Apache访问版本仓库，是不需要启动svnserve服务的，一切都已经由dav模块做接口完成数据请求的工作，通过svn客户端以HTTP协议访问版本仓库即可。

```bash
#重启Apache
/etc/init.d/apache2 restart
```

&emsp;通过浏览器访问http://ip/svn/repos，输入对应的用户名和密码后，即可在浏览中浏览SVN库中的内容。

- 完整的mod_dav_svn配置示例文件：

```markdown
	DAV svn
	SVNParentPath /var/local/svndata
	AuthType Basic 
	AuthName "Subversion Repository"
	AuthUserFile /var/local/svndata/passwdfile
	AuthzSVNAccessFile /var/local/svndata/authz
	Require valid-user
	
	Require valid-user
	Allow from localhost
```

#### 6.mod_dav_svn模块中的其它语句  

&emsp;使用mod_dav_svn模块后，还可以使用或标签进行认证控制。表示除了下面的动作需要认证外，其他动作不需验证。表示需对特定动作进行控制.甚至完全不用或标签，表示对所有的动作进行控制。此外，定义“Require mike easylife”的写法，可实现将只有用户信息文件中特定的linuxing和hanry用户才能通过认证。

- 示例：

&emsp;除了下面的动作需要认证外，其他动作不需验证

```bash
 AuthType Basic
  AuthName "Subversion Repository"
  AuthUserFile /var/local/svndata/passwdfile
  Require valid-user
```

&emsp;该标签定义当进行除了指定的若干动作需要进行用户名和密码的认证后才能进行外，其他的动作是不做限制的。例如：只有认证用户可以写操作，同时也允许匿名的读取操作。

- 使用Satisfy允许匿名的读取操作  

&emsp;使用Satisfy语句定义后，就不需要加入或标签也可以实现允许匿名的读取操作。Satisfy Any的设置表示在同时启用了Allow(允许)和Require的情况下，指定相关策略。Satisfy策略一共有两种，All表示用户必须同时满足Allow和Require的条件，而Any则是满足其中之一即可。

- 示例：

```bash
DAV svn
	SVNParentPath /var/local/svndata
	Satisfy Any
	AuthType Basic 
	AuthName "Subversion Repository"
	AuthUserFile /var/local/svndata/passwdfile
	AuthzSVNAccessFile /var/local/svndata/authz
	Require valid-user
```

&emsp;这里Satisfy Any用于允许先用匿名方式尝试访问，并根据authz文件中对匿名用户的控制给予访问权限。若没有这句话，则无论authz文件中是否加入了“*=r”的写法，匿名用户都是无法访问的。

### SVN协议与mod_authz_svn模块的区别  

&emsp;SVN协议用于客户端使用svn://方式访问版本库，而mod_authz_svn模块让客户端可通过Apache访问版本。它们分别使用不同的服务：svnserve、httpd进行访问。

&emsp;由于用户、组权限不同，权限管理方式也不相同，因此，不建议同时启动两种访问方式。从易用性，管理方便的角度来分析，Apache以mod_authz_svn模块的方式访问版本库会更多人选择。mod_authz_svn方式，使用80端口访问，并且提供https等加密传输，用于用户信息验证的密码保存方式也不是明文的。
