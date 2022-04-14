## 版本控制

1. 版本控制的起源（diff和patch）

diff：用来比较两个文件或者目录之间的差异

<img src="D:\software\as494920005@163.com\7ec4660de6dd42de8369fe7891493ab1\clipboard.png" alt="img" style="zoom: 50%;" />

patch：diff的反向操作，用差异结果推算出源文件的内容

RSC：

<img src="D:\software\as494920005@163.com\f1bc5aa5d0d844b6b3b1c4a549ea6317\clipboard.png" alt="img" style="zoom:50%;" />

集中式VS分布式：

**脆弱的中央库**

* 备份的重要性

集中式CVS存在单点故障，备份极其重要

* 服务器压力

基本上所有的操作都需要与服务器进行交互，操作受限于带宽，不能移动办公

- 安全性

集中式CVS假定服务器是安全的，但排除不了单点故障、黑客攻击

- 不适合开源项目

强调集中管理，适合人数不多的项目



**强壮的分布库**

- 全是服务器

数据最安全，无带宽和性能瓶颈

- 提交为本地操作

快，全离线操作，编码不会被冲突打断，能够移动办公

- 数据的完整性

Git数据、提交全部使用SHA1哈希，以保证完整性，甚至提交可以使用PGP签名

- 工作模型

适合分布式开发，强调个体



Git:

- Git对于每次提交的更新，主要是对全部文件制作一个快照并保存这个快照的索引
- 当文件没有修改时，Git不再重新存储该文件，只是保留一个链接指向之前存储的文件，对待数据更像是一个快照流

SVN不适合的领域：

跨地域的协同开发

对代码的高质量追求和代码门禁

Git不适合的领域：

不适合Word等二进制文档的版本控制

因为：Git无锁定/解码模式，故不能排他式修改，整体的读授权，不能将读授权精细到目录级别

解决方案：版本库按照目录拆分



## Git 配置

**系统配置（对所有用户适用）**

存放在git的安装目录下：%Git%/etc/gitconfig；若使用git config时用--system选项，读写就是这个文件：git config --system core.autocrlf

**用户配置（只适用于该客户）**

存放在用户目录下。例如linux存放在：~/.gitconfig；若使用git config时用--global选项，读写的就是这个文件

**仓库配置（只对当前项目有效）**

当前仓库的配置文件（工作目录中的.git/config文件）；若使用--local选项，读写的就是该文件

PS：每一个级别的配置都会覆盖上层的相同配置，例如.git/config里的配置会覆盖%Git%/etc/gitconfig中的同名变量



**个人身份配置**

首次的Git设定

```
git config --global user.name "Zhang San"
git config --global user.email zhangsan123@huawei.com
```



**文本换行符配置**

在提交时自动把行结束符CRLF转换成LF，签出代码时把LF转换成CRLF，使用core.autocrlf来打开该功能

```$ git config --global core.autocrlf true
$ git config --global core.autocrlf true
```

Linux或Mac系统使用LF作为行结束符，可以使用input设置，使Git在提交时把CRLF转换成LF，代码签出时不进行转换

```
$ git config --global core.autocrlf input
```

只在Windows上开发，可以取消该功能，把回车符记录在库中

```
$ git config --global core.autocrlf false
```



**文本编码配置**

- i18n.commitEncoding选项：用来让git commit log存储时，采用的编码，默认UTF-8
- i18n.logOutputcoding选项：查看git log时，显示采用的编码，建议设置为UTF-8

```
#中文编码支持
git config --global gui.encoding utf-8
git config --global i18n.commitencoding utf-8
git config --global i18n.logoutputencoding utf-8

#显示路径中的中文
git config --global core.quotepath false
```



**与服务器的认证配置**

**1.两种常见的协议配置**

**http/https协议认证**

设置口令缓存：

```
git config --global credential.helper store
```

添加HTTPS证书信任

```
git config http.sslverify flase
```



**ssh协议认证**

SSH协议是一种非常常用的Git仓库访问协议，使用公钥认证、无需输入密码，加密传输，操作遍历又保证安全性

ssh认证配置过程

- 生成公钥

Git安装成功后运行GIt Bash，在弹出的客户端命令行界面中输入以下命令：

```
$ ssh-keygan -t rsa -C zhangsan1123@huawei.com
```

- 添加公钥到代码平台

1、登录代码平台

2、进入 Profile Settings

3、点击左侧懒得 SSH Keys

4、点击 ADD SSH Key ，将生成的公钥文件的内容，复制到 Public Key栏，保存即可



## **Git基本命令**

**Git版本控制下的工程区域只有三种：**

**1、版本库（Repository）**

在工作区中有一个隐藏目录.git，这个文件夹就是Git的版本库，里面存放了Git用来管理该工程的所有版本数据，也可以叫做本地仓库

**2、工作区（Working Directory）**

日常工作的代码文件或者文档所在的文件夹

**3、暂存区（stage）**

一般存放在工程根目录.git/index文件夹中，所以也可以称其为索引（index）

**Git版本控制下的文件状态只有三种：**

**1、已提交（committed）**

该文件已经被安全地保存在本地数据库中了

**2、已修改（modified）**

修改了某个文件，但还没有提交保存

**3、已暂存（staged）**

把已修改的文件放在下次提交时要保存的清单中



**常用命令**

**工程准备**

克隆——git clone

对二进制文件区别管理——git lfs clone

新建仓库——git init

**查看工作区**

比较差异——git diff（加--name-status参数，只看文件列表）

文件状态——git status

**文件修改后提交推送**

> 新增/删除/移动文件到暂存区——git add/rm/mv

> 提交更改的文件——git commit

PS：commit操作是本地操作，不影响远端服务器，一般要附带描述信息

常见用法为：git commit file_name -m "commit message"

要一次性提交所有暂存区改动的文件到版本库，可以执行：

git commit -am "commit message"

> 推送远端仓库——git push

再用commmit命令将自己的修改从暂存器提交到本地仓库后，使用push将本地版本库的分支推送到远程服务器对应的分支，推送格式如下：

git push origin branch_name

**查看日志**

> 查看当前分支上的提交日志——git log

**分支管理**

> 列出本地分支——git branch

- *代表当前工作区所在的分支
- 查看远端服务器上有哪些分支，执行git branch -r即可
- 查看本地和服务器的所有分支，执行git branch -a

> 新建分支——git branch/git checkout -b

git branch和git checkout -b的区别：
git branch新建分支后并不会切换到新分支
git checkout -b新建分支后会自动切换到新分支

常见用法：git branch new_branch_name / git checkout -b branch_name

> 删除分支——git branch -d

-D为强制删除
删除远端服务器上的分支可以使用：git branch -d -r branch_name
删除后还需要推送到服务器上才行，即git push origin：branch_name

> 切换分支——git checkout

使用git checkout -f 可以强制切换

> 更新分支——git pull/fetch

pull的作用是从远端服务器中获取某个分支的更新，再与本地指定高的分支进行自动合并
fetch和pull的不同在于fetch在获取到更新后，并不会进行合并操作（merge），可以给用户操作空间，确认fetch的内容达到预期后再手动合并节点

> 合并分支——git merge/rebase

merge是用于从指定的分支（节点）合并到当前分支的操作
rebase用于合并目标分支内容到当前分支


**撤销操作**

> 强制回退到历史节点——git reset

通常用于撤销当前工作区中的某些git add/commit操作，可将工作区内容回退到历史提交节点
常用格式：git reset commit_id

> 回退本地所有修改而未提交的——git checkout

git checkout . 用于回退本地所有修改而未提交的文件内容（谨慎使用）
只回退某个文件的未提交改动，可以使用git checkout -filename
回退到某个版本，可以使用git checkout commit_id
