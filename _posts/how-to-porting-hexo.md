title: "Hexo移植"
date: 2015-06-07 23:02:58
tags: hexo
---

&nbsp; &nbsp; &nbsp; &nbsp; 今天讨论话题是如何在两台电脑间进行[Hexo](hexo.io)博客的移植，在开始之前，确认两台设备上的Hexo环境配置ok。顺便提一下，在Git配置完成后，Gihub的SSK-KEY列表中应该有两个KEY。为了很方便的区分两台电脑，我们暂且称之为旧设备和新设备，在旧设备上已经更新博客有一段时间了，而新机器只是配置好环境，还没有写博客。  
<!-- more -->  

![](http://7xjk0e.com1.z0.glb.clouddn.com/github-ssh-keys.png)


##**关于移植**

&nbsp; &nbsp; &nbsp; &nbsp; 在Hexo中，有三类资源：源文件(文章数据)、主题资源和配置文件，分别对应Hexo初始化目录中 `../source` 、`../themes` 和 `../_config.yml` 。他们是构成博客站点的核心内容，当两个站点的源文件、主题资源和配置文件一致时，可以把他们看作是相同的。对于博客的移植，关键就是保持这三类资源在两台设备上的一致性。其实具体操作也很简单，配置文件和主题资源在站点配置完成后变化不大，拷贝旧设备中的到新设备并替换原有文件就可以了。而源文件属于频繁更新，对它的处理有所不同，具体方法后面会提到。

注意：如果主题文件和配置文件经常改动的同学，可以参照下面的方法，将主题资源和配置文件维护在Github。

##**处理写作**

&nbsp; &nbsp; &nbsp; &nbsp; 大家都知道，Hexo的博客站点文件是维护在Github上的，在发布新文章，修改或删除已有文章之后，hexo都是调用Git将更新后的站点文件上传至Github，这样就保证在网页中显示的内容永远是最新的。我们借鉴这一思路将源文件保存到云端(Github)并进行同步更新，从而替代直接拷贝。具体处理流程为：首先上传旧设备中的源文件到Github，然后抓取并替换新设备中的源文件。接下来，不论是在新设备还是旧设备中，写博客之前更新一下源文件，然后在更新后的基础上写文章，待发布完成之后，将新添加的内容上传到Github。如此往复，就可以轻松而且准确地在两台设备上更新博客了。对应的具体操作步骤如下： 

###**添加源文件**
首先是在已经更新博客的设备上进行如下操作：  
  
1. 新建远程仓库(远程仓库是Git中得一个概念，这里简单地理解为Github中的一个文件夹)，Github->Repositories，点击new， 如下图：  
![](http://7xjk0e.com1.z0.glb.clouddn.com/github-new-repo.png)  
输入仓库名称 `source`，点击 create repostry。
![](http://7xjk0e.com1.z0.glb.clouddn.com/github-repo.png)  
远程仓库 `source` 创建完成。接下来回到Hexo初始化文件夹。   
2. 进入source文件夹，右键，选择 Git bash。    
3. 输入命令 `git init` 初始化本地仓库。  
4. 输入命令 `git add .` 添加当前目录文件到暂存区域。   
5. 输入命令 `git commit -m "<备注信息>"` 提交至本地仓库。  
6. 输入命令 `git push -u origin master` 提交至远程仓库。
出现下面的提示信息，表示提交成功。现在回到Github，检查远程仓库文件
![](http://7xjk0e.com1.z0.glb.clouddn.com/git-push.png)  

查看source远程仓库，多出_posts文件夹，现在本地的源文件已经提交至远程仓库。
![](http://7xjk0e.com1.z0.glb.clouddn.com/github-source.png)  

###**获取源文件**

接下来是将Github中的源文件抓取到新设备的Hexo目录中，具体操作如下： 
 
1. 进入hexo初始化目录，删除source文件夹(如果新设备中已经有文章数据，建议先备份，等抓取完成后，再拷贝到source文件夹中)，避免和远程仓库名字冲突。      
2. 右键，选择 git bash。  
3. 输入命令 `git clone http://github.com/<username>/source.git`克隆Github上的源文件到当前目录。  
现在博客的源文件已经更新到新设备上了，我们测试一下，重新生成(`hexo generate`)、发布(`hexo deploy`),然后在网页端检查是否发布成功。

###**更新源文件**

最后是将添加的博客同步到Github，具体操作如下：  

1. 输入命令 `hexo new post "nodlee"`创建文章，录入内容并保存。  
2. 进入 `source` 文件夹，右键，选择 Git bash.  
3. 输入命令 `git status`，查看本地仓库状态。如图：  
![](http://7xjk0e.com1.z0.glb.clouddn.com/git-untrackfile.png)  
在状态报告中可以看到新建的 `nodlee.md` 文件出现在“Untracked files”下面。未跟踪的文件意味着Git在之前的提交中没有这些文件。  
3. 输入命令 `git add "nodlee.md"`。  添加新文章到暂存区域。  
4. 输入命令 `git commit -m "<备注信息>"` 提交新添加的文章至本地仓库。    
5. 输入命令 `git push -u origin master` 提交至远程仓库。   
 
回到Github检查新添加的文章是否添加成功。到目前为止，博客移植的主要三个操作已经介绍完毕。简单回顾下整个流程：第一步是上传旧设备中的源文件到Github中。第二步是获取Github上源文件。第三步开始写作，撰写文章，生成站点文件然后发布，更新源文件到Github。  

不论是在旧设备还是新设备，每次在写作前记得先抓取远程数据更新到本地，写作完成后更新新内容到远程，慢慢的养成更新习惯。  

##**遇到的问题**  
1、`hexo deploy` 出现 `deployer not found:github`     
在hexo更新到3.0之后，`deploy` 的 `type` 需要改成 `git`。如果没有安装Git发布工具，使用 `npm` 命令安装：
`npm install hexo-deployer-git --save` 。
最后清除工程(`hexo clean`)，重新生成(`hexo generate`)、发布文件(`hexo deploy`)，检查是否发布成功。  

2、`hexo deploy` 出现 `SSH：connect to host github.com port 22 timed out`    
检查 `_config.yml` 中 `repo`，如果是 `git@github.com:<username>/vernonlee.github.io.git` 则修改为 `http://<username>/vernonlee.github.io.git`。  


