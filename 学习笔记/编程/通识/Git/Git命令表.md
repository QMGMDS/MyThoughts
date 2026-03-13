# 最常用的指令

- 克隆远程代码下来本地(自带一个文件夹)
	`git clone xxxx`
	`xxxx为web URL`

- 给本地仓库添加一个远程仓库引用
	`git remote add origin https://github.com/QMGMDS/Test-Repository`
	`origin为远程仓库的本地别名(自定义名称)`
	`https://github.com/QMGMDS/Test-Repository为远程仓库的实际URL地址`

- 检查是否成功添加引用
	`git remote -v`

- 在本地仓库中创建并切换到该分支
	`git checkout -b main`
	`main为被创建的本地分支名`

- 查看对应远程分支的本地分支的对应关系
	`git branch -vv`

- 查看本地和远程所有分支(远程新建的未修改过的不算)
	`git branch -a`

- 以当前本地分支作为基础新建一个xxx分支
	`git checkout -b xxx`
	`xxx分支默认提交时提交到本地建立时的当前分支`

- 当前分支状态(暂存区的改动总览)
	`git status`

- 在当前文件夹目录中创建一个新的、空的Git仓库
	`git init`

- 暂存当前文件夹目录下所有变更
	`git add .`

- 提交暂存区内容，并直接在命令行中指定提交消息
	`git commit -m "提交消息"`

- 推送当前分支到指定远程分支（省略本地分支名时默认为当前分支）
	`git push origin main`
	`origin为远程分支名`
	`main为本地分支名`

- 拉取远程仓库origin中main分支的文件
	`git pull origin main`
	`origin为远程仓库名`
	`main为远程仓库中的分支名`

- 删除分支
	`git branch -d xxx`
	`xxx为要删除的本地分支名`
	`不能直接删除当前所在的分支，要先切换其他分支再删除该分支`

- 分支的快进式合并
	`git merge xxx`
	`将名为xxx的分支合并到当前分支上`


# 实用小指令

- 查看分支情况
	`git branch 查看本地分支情况，带*为当前所处分支`
	`git branch -a 查看所有修改过的分支情况，包括远程仓库的分支`

- 切换本地分支(两个命令都行)
	`git switch xxx`
	`git checkout xxx`
	`xxx为对应本地分支名，切换后的工作目录的文件也会相应切换哦`

- 删除分支
	`git branch -d xxx`
	`xxx为要删除的本地分支名`
	`不能直接删除当前所在的分支，要先切换其他分支再删除该分支`

- 更改分支名
	`git branch -m aaa bbb`
	`这一句的意思是：将分支aaa名字改成bbb`

- 更新最新信息
	`git fetch xxx`
	`xxx为远程仓库名(origin)`
	`从远程仓库 origin 获取最新信息，但不合并到当前分支`
	`执行后：本地知道了远程仓库当前的所有分支、标签和提交`

# 开工一条龙服务

1. 首先是初始化本地仓库
	- 在当前文件夹目录中创建一个新的、空的Git仓库
		`git init`

2. 然后是关联远程仓库，这里有两种办法
	- 克隆远程代码下来到本地(拉取时外面会包一层文件夹)
		`git clone xxxx`
		`xxxx为远程仓库的实际URL地址`
	- 给本地仓库添加一个远程仓库引用
		`git remote add origin xxxx`
		`origin为远程仓库的本地别名(自定义名称)`
		`xxxx为远程仓库的实际URL地址`

3. 上一步选了方法2的话，推荐检查一下是否连接上了
	- 检查是否成功添加引用
		`git remote -v`

4. 接下来就是在本地开一个分支力
	- 在本地仓库中创建并切换到该分支
		`git checkout -b main`
		`main为被创建的本地分支名`

5. 给本地main拉取一下远程main的文件
	- 拉取远程仓库origin中main分支的文件
		`git pull origin main`
		`origin为远程仓库名`
		`main为远程仓库中的分支名`

6. 搞定
	好了到这里一个项目最开始的准备工作完成力，你就可以正式开工了
	等你写完了一次修改后再来看这个[[#提交一条龙服务]]叭



# 提交一条龙服务

1. 首先是将工作目录中的修改提交到暂存区
	- 无脑将本次修改的所有内容全部交上去
		`git add .`

2. 你可以用这个看看暂存区里现在提交了哪些内容
	- 当前分支状态(暂存区的改动总览)
		`git status`

3. 然后是将暂存区的修改提交到版本库
	- 提交暂存区内容，并直接在命令行中指定当次的提交消息
		`git commit -m "提交消息"`

4. 最后是将版本库的修改提交到远程仓库
	- 更新目标分支对应的远程分支进度
		`git push origin main`
		`origin为远程仓库名`
		`main为本地分支名`
		`例如main对应的远程分支名字是master，而远程仓库名字叫做origin`
		`使用上述指令则让master更新至本地main分支所对应的进度`


# 分支的合并

1. 首先是切换到主分支
	- 切换本地分支(两个命令都行)
		`git switch xxx`
		`git checkout xxx`
		`xxx为对应本地分支名，切换后的工作目录的文件也会相应切换哦`

2. 然后是把要合并的分支合并到当前分支上
	- 分支的快进式合并
		`git merge xxx`
		`将名为xxx的分支合并到当前分支上`

随后就是提交本次修改，推送到远程仓库
但是但是...[[#如果不幸遇到冲突]]




# 如果不幸遇到冲突

你合并的时候应该是这样的
![[Pasted image 20260114231326.png]]

1. 在工作目录中找到冲突文件(比如这里的就是eeeee.txt)并打开
	![[Pasted image 20260114231701.png]]
	*例子中是将xiaoming分支合并到main分支上*
	其中：
	HEAD为Current Change即当前所在分支上的修改，即主分支main
	修改内容为：
	1
	xiaoming为Incoming Change即被合并的分支上的修改，即被合并分支xiaoming
	修改内容为：
	1
	2

2. 处理冲突
	- 如果你想保留主分支main上的更改，这样改
		![[Pasted image 20260114232329.png]]
	- 如果你想保留合并分支xiaoming上的更改，这样改
		![[Pasted image 20260114232445.png]]
	- 如果你都不想要，瞎几把改也行
		![[Pasted image 20260114232543.png]]

3. 改完后记得保存，然后回终端看看
	- 查看当前状态
		`git status`
		![[Pasted image 20260114232802.png]]
	- 添加冲突的处理更改
		`git add eeeee.txt`
		![[Pasted image 20260114233114.png]]
		查看状态后可见冲突已经被我们处理掉了

随后就是提交本次修改，推送到远程仓库










