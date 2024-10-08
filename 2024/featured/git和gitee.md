### 1. 什么是Git？

​	按照百科的说法Git是分布式版本控制系统。也可以理解为是一个代码托管平台。一开始对于这个概念也是不太理解，直到我看了廖雪峰老师的Git教程之后，他用了一个例子说明了Git的作用：比如说我们正在写一个word文档，写了几千字之后发现有一个段落不想要了，然后就把他删除了，后面写着写着又发现那一段还可以，但是又找不回来了，所以后来你在每一次写完之后都用一个副本来保存，慢慢的保存的副本越来越多以至于你都分不清哪个是你想要的了。这个时候如果你会使用Git的话，这个问题就很好解决了，Git就是管理你的代码或者文档，你写的东西上传到Git上面，你的每一次上传Git都会有时间线记录，你只需要在提交的时候添加一个说明做了哪些修改，后面你想要寻找哪个片段很容易就会找到。

### 2. Git的使用

​	提交文件示意图

![img](https://raw.githubusercontent.com/kurong99/plog/master/pic/03d050acd661fc087249e7671b9ad528.png)

#### 1. 使用git init命令创建Git仓库

![init](https://raw.githubusercontent.com/kurong99/plog/master/pic/init.png?token=AYJ7A5Z25UQALTCPOM3EXHLGU552I)

 

选择一个目录，输入git init命令，这个仓库就创建在你的当前目录下。比如我先mkdir sunshine 再通过cd 命令切换到sunshine目录下。我的仓库就建好了。

#### 2. 使用git add添加文件到仓库。

​	我先在仓库内创建了一个Example.txt文件。然后再到git bash 命令行窗口输入指令。

![img](https://raw.githubusercontent.com/kurong99/plog/master/pic/aa705e32214c66ba51caea01813cf858.png)

 

就如廖雪峰老师说的：Unix的哲学就是“没有消息就是最好的消息”，这样就成功了。

但是这个命令是把文件放到仓库，但是还没有完全放入。是把文件放在 版本库的暂存区，可以理解为中转站。

#### 3. 再使用git commit 提交修改的内容 

![img](https://raw.githubusercontent.com/kurong99/plog/master/pic/1a3189b6662d28b7e7f6318e5ad88501.png)

 ok，出现下面的这几行就表示成功了，git commit -m "XXX" 这个 -m后面的内容相当于注释，对你每一次的提交进行一个简单地描述，以便于以后能够更加清楚的找到所想找到的内容。

到这一步才算是真正把文件放进了Git里面。

那我们来验证一下。我现在对文档进行一下修改。

![img](https://raw.githubusercontent.com/kurong99/plog/master/pic/7cae6d3eca76038648daaf374f9467de.png)

回到git bash 输入命令 git status

 ![img](https://raw.githubusercontent.com/kurong99/plog/master/pic/609b8e78a10ff78f5e69ec93a5635007.png)

上面清楚的显示 modified : Example.txt。

然后 我们只需要再次提交就行了。

关于Git的使用就讲到这里了，在下才疏学浅，后面了解到更多了再来与大家分享。

### 3. 什么是Gitee？

​	gitee是中国社区在2013年推出的基于 Git 的代码托管服务，因为我们所访问的Git相当于是外网，所以我们在国内访问的时候，有可能会出现访问不了的情况，所以gitee应运而生，相比于Git来说，Gitee毕竟是我们国人自己开发的网站，所以我们使用起来不会那么难，至少不会因为语言问题苦恼。

Gitee - 基于 Git 的代码托管和研发协作平台

这是Gitee的官网，进去之后先注册一个账号。然后点击头像旁边的 + 进行操作

![img](https://raw.githubusercontent.com/kurong99/plog/master/pic/3fe70af0f4fa3520fa32b30d439a1c4c.png)

![img](https://raw.githubusercontent.com/kurong99/plog/master/pic/5b28df59afe63044363410b1c8f30125.png)



 我这里创建了两个仓库，里面放的是一些平时的作业代码。

![img](https://raw.githubusercontent.com/kurong99/plog/master/pic/968360045052e1806f56ddc5c62c5480.png)

点进去之后就是上图这个界面，这里面清楚的显示了我所做的一些修改，当然后面的修改备注是需要自己填写的，这个就相比于Git更加直观，更加方便了。

然后点进去就可以看到自己的代码内容，还可以直接下载到本地。不愧是国人自己的网站用着确实比外网好的多。当然，这两个代码托管平台都是目前最主流的平台，使用的人数很多，所以小伙伴们根据自己的喜好进行学习和使用。
————————————————

    版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。

原文链接：https://blog.csdn.net/weixin_47622761/article/details/123613307