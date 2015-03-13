## 多github账号的SSH key切换 ##
原文链接[http://www.cnblogs.com/BeginMan/p/3548139.html](http://www.cnblogs.com/BeginMan/p/3548139.html)
### 1.新建user2de SSH key ###

	#新建SSH key
	$ cd ~/.ssh     # 切换到C:\Users\Administrator\.ssh
	ssh-keygen -t rsa -C "mywork@email.com"  # 新建工作的SSH key
	# 设置名称为id_rsa2
	Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa): id_rsa2

### 2.新密钥添加到SSH agent中 ###
因为默认只读取id_rsa，为了让SSH识别新的私钥，需将其添加到SSH agent中：

	ssh-add ~/.ssh/id_rsa2

如果出现Could not open a connection to your authentication agent的错误，就试着用以下命令：

	eval `ssh-agent`  （是～键上的那个`）
	ssh-add ~/.ssh/id_rsa2

### 3.修改config文件 ###
在~/.ssh目录下找到config文件，如果没有就创建：

	touch ~/.ssh/config        # 创建config

然后修改如下：
我的config配置如下：
	# 该文件用于配置私钥对应的服务器
	# Default github user(first@mail.com)
	Host github.com
	 HostName github.com
	 User git
	 IdentityFile C:/Users/Administrator/.ssh/id_rsa
	
	 # second user(second@mail.com)
	 # 建一个github别名，新建的帐号使用这个别名做克隆和更新
	Host github2
	 HostName github.com
	 User git
	 IdentityFile C:/Users/Administrator/.ssh/id_rsa2

其规则就是：从上至下读取config的内容，在每个Host下寻找对应的私钥。这里将GitHub SSH仓库地址中的git@github.com替换成新建的Host别名如：github2，那么原地址是：git@github.com:funpeng/Mywork.git，替换后应该是：github2:funpeng/Mywork.git.

### 4.打开新生成的~/.ssh/id_rsa2.pub文件，将里面的内容添加到GitHub后台。 ###

可不要忘了添加到你的另一个github帐号下的SSH Key中。

### 5.测试： ###

	Administrator@FANGPENG /e/work
	$ ssh -T git@github.com
	Hi BeginMan! You've successfully authenticated, but GitHub does not provide shel
	l access.
	
	Administrator@FANGPENG /e/work
	$ ssh -T github2
	Hi funpeng! You've successfully authenticated, but GitHub does not provide shell
	 access.

### 6.应用 ###

测试成功，那么我尝试在我的work目录下克隆我@126.com账号下的远程仓库。如下：

	Administrator@FANGPENG /e/work
	$ git clone github2:funpeng/Mywork.git
	Cloning into 'Mywork'...
	remote: Counting objects: 3, done.
	remote: Total 3 (delta 0), reused 0 (delta 0)
	Receiving objects: 100% (3/3), done.
	Checking connectivity... done

克隆成功，大功告成了！
