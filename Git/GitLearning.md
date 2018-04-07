# Git 学习笔记

本文介绍了学习 Git 的路线，参考文献可从官网此处下载：[ProGit](https://git-scm.com/book/zh/v2)．

## 目录

1. [Git 学习笔记](#Git-学习笔记)
    1. [目录](#目录)
    2. [Git 简介](#Git-简介)
        1. [版本控制](#版本控制)
        2. [Git 简史](#Git-简史)
        3. [Git 仓库、工作目录与暂存区](#Git-仓库、工作目录与暂存区)
    3. [Git 准备工作](#Git-准备工作)
        1. [初次配置](#初次配置)
        2. [帮助](#帮助)
    4. [Git 基础命令](#Git-基础命令)
        1. [仓库操作](#仓库操作)
            1. [初始化仓库（init）](#初始化仓库（init）)
            2. [克隆现有仓库（clone）](#克隆现有仓库（clone）)
        2. [提交文件](#提交文件)
            1. [检查文件状态（status）](#检查文件状态（status）)
            2. [暂存文件（add）](#暂存文件（add）)
            3. [忽略文件（.gitignore）](#忽略文件（gitignore）)
            4. [内容比对（diff）](#内容比对（diff）)
            5. [提交更新（commit）](#提交更新（commit）)

## Git 简介

### 版本控制

版本控制（Version Control System, VCS）记录了文件的变化，便于查阅或恢复到某个时刻的文件状态．

版本控制系统主要有三种：
- **本地版本控制（Local Version Control System）**：  
所有修订版本的信息都存储在本地．磁盘损坏可能损坏记录．
- **中心式版本控制（Centralized Version Control System, CVCS）**：  
所有修订版本的信息都存储在中心服务器上，协同工作的用户们通过客户端连接服务器，以此取出文件或提交更新．优点是便于管理员管理，缺点是中心服务器宕机会导致无法进行任何工作．
- **分布式版本控制（Distributed Version Control System, DVCS）**：  
客户端取出文件时，会克隆整个仓库．任一协同服务器的故障都可以从克隆仓库中恢复．

Git 属于分布式版本控制系统．

### Git 简史

2005 年，分布式版本控制系统 BitKeeper 所在公司与 Linux 内核开源社区合作结束；后者无法继续使用 BitKeeper 进行版本控制．因此 Linux 开源社区开发了自己的版本系统，称为 Git．Git 仍遵循着其设计初衷：
- 速度
- 简洁的设计
- 支持数量众多的并行开发分支
- 完全分布式
- 能够高效管理具有 Linux 或更大规模的大型项目

基本的 Git 工作特点：
- Git 记录每个版本的快照，而不是相对上个版本的变更．这使得 Git 与其他大部分版本控制系统都不相同．
- Git 使用 SHA-1 散列建立内部的索引，显示为 40 位 16 进制字符．
- Git 很少执行删除指令，因此信息误丢失的可能极小．

### Git 仓库、工作目录与暂存区

Git 仓库是保存对象数据库的地方．工作目录是从仓库中提取的某版本的文件内容，可能包含你的修改．暂存区保存了你下次要提交到仓库的内容．

在 Git 中托管的文件有三种状态：
- **已提交（commited）**：文件更改已记录到仓库中．
- **已修改（modified）**：文件被修改了（与最后一次提交的文件不同），但修改尚未记录．
- **已暂存（staged）**：文件已被修改且被标记，将在下一次提交中记录到仓库．

## Git 准备工作

### 初次配置

设置用户名和地址：
```sh
$ git config --global user.name "wklchris"
$ git config --global user.email wklchris@example.com
```

选项 `--global` 表示这些设定会应用于每个项目的 Git 仓库．

通过 `git config --list` 来罗列所有的配置参数，或 `git config <key>` 来查看某项配置的值：
```sh
$ git config user.name
wklchris
```

### 帮助

使用 `git help <command>` 来显示帮助内容．这些帮助可以离线访问．

## Git 基础命令

### 仓库操作
#### 初始化仓库（init）

进入你的项目目录，输入以下命令来初始化：
```sh
$ git init
```

初始化时，你的项目目录可以为空，也可以包含文件．

#### 克隆现有仓库（clone）

从指定的仓库网址（可能是 https, git, 或者 SSH 协议）取回整个仓库，便于在本地展开工作：
```sh
$ git clone <repo-url> [<folder-name>]
```
默认会在当前目录新建一个与仓库同名的文件夹，并将仓库内容放入；`<folder-name>` 参数是可省略的，如果指定将以该值作为仓库文件夹的名称．

### 提交文件
#### 检查文件状态（status）
在初始化仓库后，立刻使用 `git status` 命令会返回以下结果：
```sh
$ git status
On branch master
nothing to commit, working directory clean
```
这表示自从上次提交以来，Git 追踪的文件都没有发生变化;同时，也没有任何新的文件被检测到．

如果新建一个 `README` 文档，再运行此命令：
```sh
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
    README
nothing added to commit but untracked files present (use "git add" to track)
```
这告诉我们发现了一个新的文件（untracked file）,它还从没被 git 版本仓库记录过．该命令还可以使用 `-s` 参数，生成一个简略列表：
```sh
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```
上例中，偏右的 `M` 表示修改了尚未暂存，偏左的 `M` 表示修改并已暂存．`A` 表示一个新加入追踪的文件，最后 `??` 表示新检测到的未追踪的文件．

#### 暂存文件（add）
```sh
$ git add README
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file: README
    modified: test.py
```

你也可以通过 `git add .` 来暂存所有文件：
```sh
$ git add .
```

通常版本控制只针对文本文件；例如 `.pdf` 或 `.jpg` 这类文件一般不加入暂存．

#### 忽略文件（.gitignore）

当目录中有许多文件或者子目录无须交付 Git 进行版本控制时，新建一个 `.gitignore` 文件:
```sh
$ touch .gitignore
```

向其中添加内容来忽略匹配的文件：
- `.gitignore` 文件特性：
    - 空行或以 '#' 开头的行会被忽略
    - 使用 glob 模式进行匹配
    - 以 `/` 开头防止匹配时递归
    - 以 `/` 结尾确保匹配目录
    - 以 `!` 开头表示取反
- glob 模式特性：glob 模式是 shell 使用的简化后的正则表达式．
    - 用 `*` 表示匹配字符 0 到无穷次
    - 用 `?` 表示匹配单个任意字符
    - 用 `[...]` 匹配任意一个方括号内的字符(例如 `[acd]` 可以是 `a`, `c` 或 `d`)，用 `[x-y]` 匹配任意一个字符 `x` 与 `y` 之间的字符（例如 `[0-9]` 匹配任意一个阿拉伯数字）
    - 用 `**` 匹配任意中间目录，例如 `a/**/b` 可以匹配 `a/c/b` 与 `a/c/d/b`．

一个简单的例子：
```sh
*.a         # 忽略所有扩展名为 .a 的文件
/A          # 忽略当前目录下名为 A 的文件
A/          # 忽略文件夹 A 内的所有内容
B/*.pdf     # 忽略文件夹 B 下的（不包括子文件夹） pdf 文件
B/**/*.pdf  # 忽略文件夹 B 及其子文件夹中的 pdf 文件
```

这里有一个 [Github 仓库](https://github.com/github/gitignore)，收录了许多编程语言的 `.gitignore` 文件样式，可以参考．

#### 内容比对（diff）

如果你有修改了但尚未暂存的文件，使用 `git diff` 来查看**尚未暂存的改动**：
```sh
$ git diff [<filename>]
```
如果不指定文件名，那么会查看两次版本快照的差异．

如果加入 `--staged` 选项，则可以查看暂存区与版本库中最新版本之间的差异：
```sh
$ git diff --staged [<filename>]
```

#### 提交更新（commit）
