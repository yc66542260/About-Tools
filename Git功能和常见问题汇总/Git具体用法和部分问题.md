[TOC]

# Git具体用法和部分问题

## Git比较扯的特性

1.使用Git时，如果环境时Windows，那么所有的换行符都会自动转换为windows下面的换行符，即`\r\n`，而环境如果是Linux的话，那么将保持原有的换行符不变。如果在windows上面下载源码在linux下编译运行等，则在git中需要设置如下命令：

`git config –global core.autocrlf input`

这些在clone下来的代码就保持原仓库换行符了。

### 关于Git总连接补上的处理方法

可能遇到的报错：

`OpenSSL SSL_read:Connection was reset, errno 10054`

`Failed to connect to 127.0.0.1 port 1080: Connection refused`

此时说明Git连接远程仓库失败了。

- 首先要有一个代理

- 查看vpn的代理端口如：

  ![](./%5Cfigure%5C12.png)

- 在git bash里面运行如下命令：

  `git config --global http(s).proxy 127.0.0.1:端口号`

- 可能用到的命令：

   默认不设置代理：

  ```
  git config --global --unset http.proxy
  git config --global --unset https.proxy
  ```

   设置其他端口号

  ```
  git config –global http.proxy 127.0.0.1: 端口号
  git config –global https.proxy 127.0.0.1: 端口号
  ```

## Git具体用法

### 1. Git 本地init后通过push推到远程仓库

> 参考：[Git本地init与push到远程仓库](https://blog.csdn.net/Byd_chao/article/details/82821897)

1. git删除： `rm -rf .git`，作用是删除本地git文件夹。

2. 查看git config信息： `git config --list`，里面包括了user.name和user.email信息。

3. 修改信息，例如用户名与邮箱：

   a. `git config --global user.name "name"`

   b. `git config --global user.email "email"`

4. 修改账号和密码：

   a. `git config –system –unset credential.helper`  #重置认证信息

   b. `git config –global http.emptyAuth true`  #认证清除

5. （先进入项目文件夹）通过命令 gitinit 把这个目录变成git可以管理的仓库：

   `git init` 

6. 把文件添加到本地版本库中：

   `git add .`

7. 把文件提交到仓库：

   `git commit -a -m "版本说明/更新日期"`

8. 关联到远端地址：

   `git remote add origin 你的远程库地址`

   如：

   `git remote add origin git@192.168.31.130:/srv/sample.git`

   注意：如果这一步出现错误，那么使用如下命令修改：

   `git remote rm origin`   #删除origin

   `git remote add origin git@git.github.com:yourname/demo.git`  #重新添加origin

9. 远端仓库与本地仓库进行合并（必做）

   `git pull --rebase origin master`

10. 将最新的修改从本地仓库推送到远端仓库

    注意：注意:我们第一次push的时候,加上-u参数,Git就会把本地的master分支和远程的master分支进行关联起来,我们以后的push操作就不再需要加上-u参数了

    `git push -u origin master`

11. 额外内容：复制分支并推送到远程仓库

    ```bash
    # 从master切分出dev分支，并推送到远端
    git checkout -b develop    
     
    # 切换到功能开发分支
    git checkout -b feature-[name_of_feature]  
     
    # 进行功能开发，在阶段性完成之后，将代码合并回本地的dev分支
    git checkout develop
     
    # 确认分支代码为最新
    git pull origin develop
     
    # 注意，一定要添加--no-ff 标记
    git merge --no-ff feature-[name_of_feature]
     
    # 如果确认feature分支已经不需要了可以删除
    # git branch -d feature-[name_of_feature]
     
    # 代码提交到远端分支
    git push origin develop
    ```

12. 保存进度的列表

    ```bash
    git stash  # git stash save 'message...'可以添加一些注释
    git stash pop  #恢复最新的进度到工作区。git默认会把工作区和暂存区的改动都恢复到工作区，并且会删除当前进度。
    git stash pop --index  #恢复最新的进度到工作区和暂存区。（尝试将原来暂存区的改动还恢复到暂存区）
    git stash pop stash@{1} #恢复指定的进度到工作区。stash_id是通过git stash list命令得到的
    ```


### 2. Git删除远程文件夹或文件

> 参考：[git删除远程文件夹或文件](https://blog.csdn.net/bananachong/article/details/126698847)

1.操作

预览将要删除的文件（如果不清楚该目录下是否存在不应该删除的文件），

加上 -n 这个参数，执行命令时，是不会删除任何文件，而是展示此命令要删除的文件列表预览。

1. `git rm -r -n --cached` 文件/文件夹名称

2.确定无误后删除文件（本地的文件或文件夹不会被删除）

2. git rm -r --cached 文件/文件夹名称

3.提交到本地代码

3. git commit -m "提交说明"

4.推送到远程服务器

4. git push origin master

删除logs文件夹为例子：

![](./%5Cfigure%5C4.png)

### 3. Git stash命令

> 参考：[我们随手做的 git stash，究竟干了什么？](https://zhuanlan.zhihu.com/p/94216223)
>
> ​           [Git stash命令详解](https://blog.csdn.net/qq_39900031/article/details/125341514)
>
> ​           [git stash 用法总结和注意点](https://www.cnblogs.com/zndxall/archive/2018/09/04/9586088.html)

常用git stash命令：

（1）**git stash** save "save message" : 执行存储时，添加备注，方便查找，只有git stash 也要可以的，但查找时不方便识别。

（2）**git stash list** ：查看stash了哪些存储

（3）**git stash show** ：显示做了哪些改动，默认show第一个存储,如果要显示其他存贮，后面加stash@{$num}，比如第二个 git stash show stash@{1}

（4）**git stash show -p** : 显示第一个存储的改动，如果想显示其他存存储，命令：git stash show stash@{$num} -p ，比如第二个：git stash show stash@{1} -p

（5）**git stash apply** :应用某个存储,但不会把存储从存储列表中删除，默认使用第一个存储,即stash@{0}，如果要使用其他个，git stash apply stash@{$num} ， 比如第二个：git stash apply stash@{1} 

（6）**git stash pop** ：命令恢复之前缓存的工作目录，将缓存堆栈中的对应stash删除，并将对应修改应用到当前的工作目录下,默认为第一个stash,即stash@{0}，如果要应用并删除其他stash，命令：git stash pop stash@{$num} ，比如应用并删除第二个：git stash pop stash@{1}

（7）**git stash drop** stash@{$num} ：丢弃stash@{$num}存储，从列表中删除这个存储

（8）`**git stash clear** ：`删除所有缓存的stash

### 4. Git ssh key 作用与配置

> 重要参考：[Git ssh key 作用与配置](https://blog.csdn.net/qq_39366020/article/details/106431194)

#### **作用：**

**ssh指secure shell（一种安全的网络协议），git使用这种协议进行远程加密登录。**

**ssh登录安全性由非对称加密保证，产生密钥时，一次产生两个密钥，一个公钥，一个私钥，在git中一般分别命名为id_rsa.pub, id_rsa**

**ssh方式单独使用非对称的秘钥进行认证和加密传输，和账号密码分离开来，不需要账号也可以访问repo。**

**git基于多种传输协议，其中最常用的就是https和ssh。都是为了数据传输安全，那么设置ssh密钥的目的是为了节省输入用户名密码的过程，同时保证传输安全。并不是必须设置。**

#### 配置过程:

##### 1. git config 配置

终端执行下列命令：

```bash
git config --global  user.name "你的用户名"
git config --global user.email "你的邮箱"
```

这里的用户名和邮箱最后和你的github账户一致，下面是一个例子。

```bash
git config --global  user.name "Mike"
git config --global user.email "123@gmail.com"
```

这样你的用户名和邮箱将被设置为`Mike`和`123@gmail.com`

使用下列命令可以查看已配置信息。

```bash
git config --global --list
```

### 2. 秘钥生成

首先查看你的主目录`~`下是否含有`.ssh`文件路径，以及`.ssh`文件路径下是否含有以 id_dsa 或 id_rsa 命名的文件，其中一个带有 .pub 扩展名。.pub 文件是你的[公钥](https://so.csdn.net/so/search?q=公钥&spm=1001.2101.3001.7020)，另一个则是私钥。

可以在终端运行下列命令进行快速查看：

```bash
ls ~/.ssh
```

如果提示没有该目录，或者该目录下没有公[私钥](https://so.csdn.net/so/search?q=私钥&spm=1001.2101.3001.7020)文件，说明还未生成过密钥。

如果存在可以直接使用，跳到本步骤。但也可以选择重新生成[密钥](https://so.csdn.net/so/search?q=密钥&spm=1001.2101.3001.7020)，将之前的覆盖。

执行下列命令生成密钥：

```bash
ssh-keygen -t rsa -C "你的邮箱"
```

中途需要进行3次或4次确认：

- 秘钥的保存路径，不更改则直接回车
- 是否覆盖上一次生成的密钥（若之前已经生成过）
- 密码（若不设置则直接回车）
- 确认密码

建议路径使用默认配置，密码不设置。这样每次`git push`等操作都不需要输入密码，比较方便。

下面是生成过程的图片。

![](./%5Cfigure%5C10.png)

若是用默认路径则在`~/.ssh`路径下将看到两个新生成的文件。

![](./%5Cfigure%5C11.png)

至此已经生成好了ssh密匙，具体如何设置github账户上的密匙，参考5。

### 5.github端添加公钥

> 重要参考：[gitHub添加公钥](https://blog.csdn.net/qq_38819293/article/details/120677495)

1、删除所有原有的SSH Key**（可选）**

首先，清除所有的key-pair `ssh-add -D rm -r ~/.ssh` 删除你在github中的public-key

重新生成ssh密钥对 `ssh-keygen -t rsa -C "[xxx@xxx.com](mailto:xxx@xxx.com)"`

`chmod 0700 ~/.ssh` 和 `chmod 0600 ~/.ssh/id_rsa*` 接下来正常操作在github上添加公钥public-key:

**注意**

如果执行ssh-add时出现Could not open a connection to your authentication agent

在执行 `ssh-add ~/.ssh/id_ras` 时发生此错，

执行如下命令　`ssh-agent bash`

然后再执行 `ssh-add ~/.ssh/id_ras` 即可。

**2、创建SSH Key**
在windows下查看【C:\Users\Administrator.[ssh](https://so.csdn.net/so/search?q=ssh&spm=1001.2101.3001.7020)】当前.ssh文件夹里面是否有id_rsa、id_rsa.pub文件），若没有，就在当前目录打开git bash(需要安装Git)，输入
ssh-keygen -t [rsa](https://so.csdn.net/so/search?q=rsa&spm=1001.2101.3001.7020) -C “my-email.com”

![](./%5Cfigure%5C6.png)

其中，passphrase为密码，可输可不输（不输为空）
**3、登录github**

![](./%5Cfigure%5C7.png)

在C盘User找到`id_rsa.pub`

![](./%5Cfigure%5C8.png)

将id_rsa.pub输入到

![](./%5Cfigure%5C9.png)

## Git部分问题

### 1.Authetication failed for "https://........................"

![](./%5Cfigure%5C1.png)

参考：这一部分可能是由于token过期导致的，解决办法是重新生成新的token，可以参考下面链接[[关于Support for password authentication was removed 报错的解决方案](https://blog.51cto.com/u_13657808/5658164?u_atoken=a45684e9-77d4-4054-afb0-3a8c3f8466a2&u_asession=010wy8Ubf6eZ7BjXRyDJaN22WU7DNIe7ajY4aMwXg45QB6DXyp9nN0eu63MeXKW6B2X0KNBwm7Lovlpxjd_P_q4JsKWYrT3W_NKPr8w6oU7K-Af6RNkQpZJLc8jc8BaPREhVaMhQoQvxDmSarRnKyUVWBkFo3NEHBv0PZUm6pbxQU&u_asig=05qQYhRevD29F9Yl3kQGqbE__I86TXIE2N8vOvPQEySbU2B5RGCAHDtZU6SIcDlyGMfqVa0FAwl5mevb5WrV-vkw9jZ77EaGf3P1GxtGoVsCM9MQngXAafEmvaIwxjeeJkE6EX8na4df6XzeZHozUuYp7Rk1ze6aq6WNyzaNo8A-39JS7q8ZD7Xtz2Ly-b0kmuyAKRFSVJkkdwVUnyHAIJzV-LiEy8mTGwaE8JoxHl7taLztSBaNSwHP3nQ8utQlXiCNkNfzbmXGypMDlqpKRYAO3h9VXwMyh6PgyDIVSG1W_48ihTmF3uWhiYbJydy2oSNPiAmCOnVC6hPCK1rmxyar5DRbwl68DFzZK_2RItRKzMbAA4VR66HSlUVc2kX3IsmWspDxyAEEo4kbsryBKb9Q&u_aref=lNkCeCX4pINPMp5%2FgBOcjU4GGE0%3D)]，解决后，输入下面指令：

`git remote set-url origin https://<token>@github.com/name/repo`

![](./%5Cfigure%5C2.png)

### 2.fatal: The remote end hung up unexpectedly

参考：原因可能是因为项目推送的文件过大，此时需要修改git config文件中postBuffer文件的大小，使用下面命令：

`git config --local http.postBuffer 52428800`

具体看考下面链接[[Git：解决报错：fatal: The remote end hung up unexpectedly](https://blog.csdn.net/u013250071/article/details/81203900)]

### 3.关于推代码时出现下图错误，可能是由于token不对引起的

![](./%5Cfigure%5C3.png)

此时一个比较好的办法是：

a. 更新token，拷贝；

b. 当git需要输入密码的时候，直接把token拷贝上去就可以了。

参考：[Git操作相关问题（pull/push/clone/）](https://blog.csdn.net/qq_43665602/article/details/126045006)

### 4.下载好git却找不到id_rsa.pub

> 参考：[下载好git却找不到id_rsa.pub](https://blog.csdn.net/changyana/article/details/125567861)

由于在新电脑需要上传代码到github，新下载了git配置[ssh](https://so.csdn.net/so/search?q=ssh&spm=1001.2101.3001.7020)时却找不到id_rsa.pub，这是由于当前电脑还没有生成相关的密钥。

a. 首先需要在当前想要上传的文件目录下打开git，一般git下载好后就可以通过右键来打开

b. 输入命令`ssh-keygen -t rsa -b 4096 -C "邮箱"`，这里的邮箱替换成你自己的邮箱

c. 连按三次回车，然后就可以创建成功

### 5.HttpRequestException encountered解决方法

> 参考：[HttpRequestException encountered解决方法](https://www.codeprj.com/blog/b71ee71.html)

每次pull代码的时候，总是要输入账号，密码，百度了一下HttpRequestException encountered错误

发现是Github 禁用了TLS v1.0 and v1.1，必须更新Windows的git凭证管理器。

![](./%5Cfigure%5C5.png)

### 6.git pull 强制覆盖本地代码

`git pull` 强制覆盖本地的代码方式，下面是正确的方法：

`git fetch --all`

然后，你有两个选择：

`git reset --hard origin/master`

或者如果你在其他分支上：

`git reset --hard origin/<branch_name>`

说明：

`git fetch`从远程下载最新的，而不尝试合并或rebase任何东西。

然后`git reset`将主分支重置为您刚刚获取的内容。 --hard选项更改工作树中的所有文件以匹配origin/master中的文件。

## Git部分参考

[1] [Git教程](https://www.runoob.com/git/git-tutorial.html)

[2] [Git简明教程](https://www.runoob.com/w3cnote/git-guide.html)

[3] [Git 五分钟教程](https://www.runoob.com/w3cnote/git-five-minutes-tutorial.html)

[4] [Git简明指南](https://www.runoob.com/manual/git-guide/)

[5] [Git远程仓库][(https://www.runoob.com/git/git-remote-repo.html)]

[6] [gitee、github使用教程](https://blog.csdn.net/qq_56591814/article/details/122283459)

