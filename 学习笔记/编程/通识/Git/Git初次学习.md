# Git概念


## Git和Github、Gitee的关系？

Git ：一个**分布式版本控制系统**，实际上是一个**命令行工具**，建立本地的Git仓库实现**存档**与**回档**

Github / Gitee：二者都是一个**在线代码托管服务平台**，相当于**远程仓库**

关系：通过Github / Gitee的平台，通过Git的方式实现代码更优雅的管理



## Commit是什么？

Git仓库中的一个**存档点**，即**你对项目进度的一次有意义的、被记录的保存**



## HEAD 是什么？

HEAD指向当前提交的存档点Commit

效果：**HEAD指向哪个存档点，你的工作目录（你在电脑上看到的文件）就呈现出哪个存档点的状态**



## 分支、本地分支、远程分支？

分支：是 Git 中**一条独立的开发线**，基于某个特定的提交（Commit）创建，并拥有自己的提交历史。

本地分支：本地仓库中的分支

远程分支：远程仓库中的分支



## 工作区、暂存区、仓库

工作区：电脑上能看到的项目文件夹，你在这里新增、修改、删除文件，但尚未被 Git 正式管理

暂存区：一个 **“准备区域”** 或 **“缓存区域”**，存放着你**打算下次提交**的所有更改

仓库：Git 用来保存项目**所有历史版本和元数据**的数据库，每一次提交的存储位置



## 远程仓库与上游仓库的关系

![image-20251108203349864](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108203349864.png)

==上游仓库是远程仓库的子集==



# Git命令简化版

- `git init`  ==初始化==

- `git remote add <name> <URP>`    ==由URP关联远程仓库并命别名name==
- `git remote -v`  ==验证远程仓库是否连接==
- `git pull origin main`  ==拉取远程仓库origin的main分支中的文件==
- `git add .`  ==将本地所有文件放入暂存区==
- `git commit -m "提交描述"`  ==将暂存区所有文件放入本地仓库==
- `git remote`    ==获取当前已关联到的远程仓库==
- `git remote rename <oldname> <newname>`    ==为远程仓库重命名==
- `git push <remotename> <localname>`    ==将本地localname分支推送到远程仓库remotename上==
- `git branch --list`    ==查看本地仓库当前分支列表==
- `git branch <newbranchname>`    ==本地仓库创建一个新的分支newbranchname==
- `git checkout <targetbranch>`    ==切换到目标分支targetbranch==
- 



# Git命令

## **1.给本地仓库添加一个远程仓库引用**

`git remote add <远程仓库的本地别名（自定义名称）> <远程仓库的实际URL地址>`

**例图：**

![image-20251108195103091](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108195103091.png)



## **2.检查是否绑定成功**

`git remote -v`

**例图：**

![image-20251108195539113](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108195539113.png)



## **3.在本地仓库中创建分支+切换到该分支**

`git checkout -b <要创建的分支名>`

**例图：**

![image-20251108200142093](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108200142093.png)

创建了新的分支+切换到该分支



## **4.查看当前分支情况**

`git branch`

**例图：**

![image-20251108192625427](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108192625427.png)

这里的feature/test为刚刚创建的分支

当前所在的分支前会有“*”标记



### 4.1.将当前所在的本地分支重命名

`git branch -M <当前分支的名称>`



### 4.2.查看当前提交状态

`git status`



### 4.3.给给终端清屏

`cls`



## **5.切换分支**

` git checkout  <对应分支名>`

**例图：**

![image-20251108192847879](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108192847879.png)



## **6.合并分支**

`git merge <要合并的分支>`

**例图：**

![image-20251108193348543](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108193348543.png)

==该步的操作对象为本地仓库，即把本地仓库中的feature/test分支合并到trunk上==

==也就是说，远程仓库即Github上的trunk分支不会显示修改==

**如下：**

![image-20251108193735553](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108193735553.png)

![image-20251108201319166](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108201319166.png)

**注意到：**在**远程仓库**中，**feature/test分支**有**aabbcc.txt**这个文件而**trunk分支**中没有



## **7.本地仓库合并的修改推送到远程仓库中**

`git push <远程仓库的别名> <要推送的本地分支名>`

**例图：**

![image-20251108201030387](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108201030387.png)

此时在Github的远程仓库中：

![image-20251108201223774](C:\Users\29182\AppData\Roaming\Typora\typora-user-images\image-20251108201223774.png)

可以看到：**aabbcc.txt**这个文件出现在**trunk分支**中了



# 写代码之前

- **1.切换到本地主分支**

  `git checkout <主分支>`

- **2.从上游仓库拉取最新代码**

  `git fetch <上游仓库的别名>`

- **3.把上游的某一分支合并到本地的当前分支**

  `git merge <上游仓库的别名>/<上游仓库的一个分支名>`

- **4.把更新后的当前分支推送到自己的远程仓库**

  `git push <远程仓库的别名> <要推送的本地分支名>`

- **5.把远程仓库的分支合并到本地当前分支**

  `git merge <远程>`