# git基本命令

### （1）初始化

初始化设置用户名和邮箱

```shell
git config --global user.name "Your Name"
git config --global user.email email@email.com
git config --global credential.helper store
```

### （2）创建仓库

创建一个新的本地仓库（省略<project-name>将在当前目录下创建仓库）

```shell
git init <project-name>
```

下载一个远程仓库

```shell
git clone <url>
```

### （3）Git的四个区域

工作区 (working Directory)：就是你在电脑⾥能看到的目录

暂存区 (Stage/Index）：⼀般存放在 .git ⽬录下的index ⽂件，所以我们把暂存区有时也叫作索引 (index)

本地仓库 (Repository）：⼯作区有⼀个隐藏⽬录git，这个不算⼯作区，⽽是Git的版本库

远程仓库 (Renote）：托管在远程服务器上的仓库

### （4）Git的三种状态

已修改(nodified)：修改了⽂件，但没保存到暂存区

已暂存 (staged）：把修改后的⽂件放到暂存区

已提交 (Committed)：把暂存区的⽂件提交到本地仓库

### （5）基本概念

main：默认主分⽀

origin：默认远程仓库

HEAD ：指向当前分⽀的指针

HEAD~：上⼀个版本

HEAD~4：上4个版本

### （6）特殊文件

.git： Git仓库的元数据和对象数据库

.gitignore：忽略⽂件，不需要提交到仓库的⽂件

.gitattributes：指定⽂件的属性，⽐如换⾏符

.gitkeep：使空⽬录被提交到仓库

.gitmodules：记录⼦模块的信息

.gitconfig：记录仓库的配置信息

### （7）添加和提交

添加一个文件到仓库

```shell
git add <file>
```

添加所有文件到仓库

```shell
git add .
```

提交所有暂存区的文件到仓库

```shell
git commit -m "message"
```

提交已修改的文件到仓库

```shell
git commit -am "message"
```

### （8）分支

看所有本地分⽀，当前分⽀前⾯会有⼀个`*`，`-r`查看远程分⽀，`-a`查看所有分⽀

```shell
git branch
```

创建一个新分支

```shell
git branch <branch-name>
```

切换到制定分支，并更新工作区

```shell
git checkout <branch-name>
```

创建一个新分支，并切换到该分支

```shell
git checkout -b <branch-name>
```

删除一个已经合并的分支

```shell
git branch -d <branch-name>
```

删除一个分支，不管是否合并

```shell
git branch -D <branch-name>
```

给当前的提交打上标签，通常用于版本发布

```shell
git tag <tag-name>
```

### （9）合并分支

合并分支a到分支b，`-no-ff` 参数表示禁用Fast forward模式，合并后的历史有分支，能看出曾经做过合并，而`-ff`参数表示使用Fast forward模式，合井后的历史会变成一条直线

```shell
git merge --no-ff -m "message" <branch-name>
```

```shell
git merge --ff -m "message" <branch-name>
```

合并&squash所有提交到⼀个提交

```shell
git merge --squash <branch-name>
```

ebase不会产⽣新的提交，⽽是把当前分⽀的每⼀个提交都 • 复制"到⽬标分⽀上，然后再把当前分⽀指向⽬标分⽀，⽽merge会产生一个新的提交，这个提交有两个分支的所有修改。

### （10）Rebase

Rebase操作可以把本地未push的分 叉提交历史整理成直线,看起来更直观。但是，如果多⼈协作时，不要对已经推送到 远程的分⽀执⾏Rebase操作。

```shell
git checkout <dev>
git rebase <main>
```

### （11）移动

移动一个文件到新的位置

```shell
git mv <file> <new-file>
```

### （12）删除

从工作区和暂存区中删除一个文件，然后暂存删除操作

```shell
git rm <file>
```

只从暂存区中还是拿出一个文件，工作区中的文件没有变化

```shell
git rm --cached <file>
```

### （13）撤销

恢复一个文件到之前的版本

```shell
git checkout <file> <commit-id>
```

创建一个新的提交用来撤销指定的提交，后者的所有变化都将被前者抵消，并且应用到当前分支

```shell
git revert <commit-id>
```

重置当前分⽀的HEAD为之前的某个提交，并且删除所有之后的提交。`--hard`参数表示重置⼯作区和暂存区，`--soft`参数表示重置暂存区，`--mixed`参数表示重置⼯作区（一般使用之前都先去查询操作查询到需要的`commit-id`才回去进行插销等操作，例如查询操作日志`git reflog`）

```shell
git reset --mixed <commit-id>
```

```shell
git reset --hard <commit-id>
```

```shell
git restore --stanged <file>
```

### （14）查看

列出还未提交的新的或修改的文件

```shell
git status
```

查看提交历史，`--oneline`可省略

```shell
git log --oneline
```

查看为暂存的文件更新了哪些部分

```shell
git diff
```

查看两个提交之间的差异

```shell
git diff <commit-id> <commit-id>
```

### （15）Stash

stash操作可以把当前⼯作现场“储藏"起来，等以后恢复现 场后继续⼯作。`-u`参数表⽰把所有未跟踪的⽂件也⼀并存 ，`-a`参数表示把所有未跟踪的⽂件和忽略的⽂件也⼀并存储，save参数表示存储的信息，可以不写。

```shell
git stash save "message"
```

查看所有stash

```shell
git stash list
```

恢复最近一次的stash

```shell
git stash pop
```

恢复指定的stash，`stash@{2}`表示第三个stash，`stash@{0}`表示最近的stash

```shell
git stash pop stash@{2}
```

重新接受最近一次stash

```shell
git stash apply
```

`pop`和`apply`的区别是，`pop`会把`stash`内容删除，⽽`apply`不会。可以⽤ `git stash drop`来删除 `stash`

```shell
git stash drop stash@{2}
```

删除所有stash

```shell
git stash clear
```

### （16）远程仓库

添加远程仓库

```shell
git remote add <remote-name> <remote-url>
```

查看远程仓库

```shell
git remote -v
```

删除远程仓库

```shell
git remote rm <remote-name>
```

重命名远程仓库

```shell
git remote rename <old-name> <new-name>
```

从远程仓库拉取代码

```shell
git pull <remote-name> <branch-name>
```

fetch默认远程仓库（origin）当前分支的代码，然后合并到本地分支

```shell
git pull
```

将本地改动的代码Rebase到远程长裤最新的代码上（为了有一个干净的、线性的提交历史）

```shell
git pull -rebase
```

推送到远程仓库（然后再发起`pull request`）

```shell
git push <remote-name> <branch-name>
```

获取所有远程分支

```shell
git fetch <remote-name>
```

查看远程分支

```shell
git branch -r
```

fetch某一个特定的远程分支

```shell
git fetch <remote-name> <branch-name>
```

### （17）Git Flow

GitFlow 是⼀种流程模型，⽤于在 Git 上管理软件开发项⽬。

主分支 (master)：代表了项目的稳定版本，每个提交到主分支的代码都应该是经过测试和审核的。

开发分支 (develop)：用于日常开发。所有功能分支、发布分支和修补分支都应该从 develop 分支派生。

功能分支 (feature)：用于开发单独的功能或特性。每个功能分支应该从 develop 分支派生，并在开发完成后合并回 develop 分支。

发布分支 (release)：用于准备项目发布。发布分支应该从 develop 分支派生，井在准备好发布版本后合井回master 和develop分支。

修补分支（hotfix）：用于修复主分支上的紧急问题。修补分支应该从 master 分支派生，并在修复完成后合并回 master 和 develop 分支。
