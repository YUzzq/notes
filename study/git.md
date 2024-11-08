# 1.git目录结构

<img src="https://p.ipic.vip/hk1ijz.png" alt="image-20241008170007147" style="zoom:150%;" />

## 1.1配置文件

- 查看：git config -l（局部）
- 查看：git config ---global -l（全局）
  - 编辑：git config key:value

## 1.2objects

- 用于储存文件的信息
- 通过计算文件内容的哈希值来实现**两个内容一致的文件只储存一次**



# 2常用命令

## 2.1git add

- 在执行`git add`之后，git会将工作区的文件的哈希值作为文件名储存在objects目录下

- 工作区发生新的变化时，objects中就会创建新的记录文件

## 2.2git commit

- 执行git commit之后，objects中会多出以下这些文件，其中：

  - commit文件记录本次提交的一些信息，包括：
    1. 具体文件变动的tree文件
    2. 提交者信息与提交时间

  - tree文件记录发生变化的文件

![image-20241009111303732](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241009111303732.png)

- tree是记录每层文件夹的文件变动的文件，当有新的层级的文件发生变动后，执行commit就会生成新的tree

![image-20241009115405873](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241009115405873.png)

# 3文件状态

![image-20241009120140310](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241009120140310.png)

# 4分支

- 分支本质上是指向某一次commit的指针，而HEAD是指向当前commit的指针



## 4.1分支操作

- git branch
  - 查看当前所有的分支

- git branch <branchName>
  - 新建分支
  - 新建分支的指向为工作分支的最新提交

- git branch -d <branchName>
  - 删除一个分支
  - 如果该分支没有被merge到其他分支，则无法删除
  - 删除分支只是删除了指针，对被删除分支上的commit不会造成任何影响

- git branch -D <branchName>
  - 强制删除一个分支，不管有没有被merge

- git checkout <branchName>｜<commitHash>
  - 切换到指定的分支
  - 也可以切换到某一次commit，可以用于恢复误删的分支







## 4.2文件结构

HEAD指针永远指向当前分支的最新提交

- .git/HEAD
  - 保存了当前分支的信息

- .git/refs/heads
  - 保存所有分支的信息

# 5git的压缩

- 工作区的文件被添加到暂存区时，git会自动将文件进行一次压缩
- 但是文件发生微小变动时，git也会在projects中保存整个文件

**使用git gc命令压缩**

- 调用git gc，git会将projects中的文件进行压缩，形成如下结构

  ![image-20241009171610766](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241009171610766.png)

  - idx文件保存了要访问文件的索引信息
  - pack文件保存了文件的主体内容

**git gc压缩后pack详细信息**

![image-20241009172723674](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241009172723674.png)

## 5.1git解压

- git unpack-objects < <packFilePath>
  - 解压.pack文件，一般需要提前将其放到objects中

# 6git垃圾清理

- git prune
  - 清除不可达的对象，比如多次git add中被覆盖的对象
  - 使用git prune -n可以看到即将被删除的对象，但不会直接删除

- git fsck
  - 查看垃圾对象

**情况2：当删除了一个分支之后，不想再使用这个分支了，这个分支中已经暂存的blob也是垃圾对象**

- `git -c gc.reflogExpire=0 -c gc.reflogExpireUnreachable=0 \
  -c gc.rerereresolved=0 -c gc.rerereunresolved=0 \
  -c gc.pruneExpire=now gc "$@"`

# 7合并

## 7.1fast-forward

- 如图，当在master上签出一个新分支并提交了新东西，master未做修改的情况下直接合并
  - git会直接将master指针指向c3，称为快速合并

![image-20241010115750277](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241010115750277.png)

![image-20241010120203402](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241010120203402.png)

- 合并完成之后会多出来一个文件：.git/ORIG_HEAD
  - 记录了**合并之前的HEAD指向**

## 7.2-3 way merge

- 当master上有新的提交时（下图C4），调用git merge bugfix合并会产生一个新的merge commit（下图C5） 

![image-20241010144226808](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241010144226808.png)

![image-20241010144153937](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241010144153937.png)

- merge commit会有两个commit

  - 分别是合并两个分支的最新提交

  ![image-20241010154952250](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241010154952250.png)





**带冲突的3 way merge**

- 当C3和C4对**同一个文件进行了修改**时，git就会返回一个冲突提示，需要人工进行解决。否则直接进行合并

- 带冲突的情况下，冲突的文件会被修改成待处理的格式
- 索引区的冲突文件会多出三个版本
  1. 文件原始版本
  2. 分支1的版本
  3. 分支2的版本

## 7.3git rebase

- 将dev分支合并到master分支上之后，如果是3way merge，则dev分支就获取不到最新的master上的提交

![image-20241010161154504](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241010161154504.png)

- 而git rebase就会将dev上的第一个提交指向master的最新提交，然后进行fast-forward合并，这样dev分支就能获取master的最新commit了

  ![image-20241010161316409](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241010161316409.png)

# 8tag

## 8.1tag的基本操作

- git tag <tagName>

  - 在当前HEAD指向的commit上添加tag（lightweight tag 轻质tag）

  - eg：git tag v1.0

- git tag -a <tagname> -m <tagmessage>

  - 为tag添加备注信息（annotated tag 注释tag）

  - eg：git tag -a v1.0 -m "version 1.0"

- git tag -a <tagName> <commit sha1 hash>

  - 指定某一次commit的tag

  - eg：git tag -a v1.0 ad94ee

- git tag
  - 查看tag列表

- git tag -d <tagName>
  - 删除tag
  - eg：git tag -d v1.0



## 8.2tag的作用

- 为某一次commit取一个别名，会在.git/refs/tags下新建一个文件，文件内容为指向commit的hash值

![image-20241010165110719](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241010165110719.png)

- 如果是annotated tag，还会在objects中创建一个tag类型的object

  - 并且，由于**每个tag都会记录时间戳**，所以当删除一个tag之后，再次创建完全一样的tag也会新建一个object，而不会复用

  ![image-20241010170409258](/Users/yuzzq/Library/Application Support/typora-user-images/image-20241010170409258.png)

# 9本地分支与远程分支

## 9.1基本操作

- git branch
  - 查看本地分支

- git branch -r
  - 查看远程分支













# sao操作

- git stash
  - 暂存当前分支修改的内容，而不需要提交

- git stash apply（hash）
  - 恢复修改的内容

- git stash list
  - 查看暂存的列表

- git config --global push.default current
  - 默认推送到当前的分支

- git config --global core.ignorecase false
  - 忽略大小写设置关闭

- git cat-file -x <hash>

  - 根据文件的hash值查看一些信息

    ![image-20241008172808772](https://p.ipic.vip/1rbeyh.png)

- git ls-files -s
  - 查看索引取文件列表

- git reflog
  - 查看所有的提交信息

- git diff

  - git diff --cached：查看索引区和代码仓库的区别

  - 查看文件的变化

    <img src="https://p.ipic.vip/39fbkj.png" alt="image-20241009154349307" style="zoom:67%;" />









# 命令行指令

- mkdir <fileName> 
  - 新建文件
- echo "<content>"><fileName>
  - 新建一个文件，写入一些内容

- echo <content> | shasum
  - 将指定内容通过shasum算法转为哈希值

- cat <fileName>
  - 查看文件内容
- ls
  - 当前目录下的文件列表
- tree <filePath>
  - 查看文件的结构
- watch -n <time(S)> -d find .
  - 监控当前目录的变化
- rm -rf <path>
  - 删除指定目录的内容

- touch <fileName>
  - 创建空文件

- ls -lh <filePath>
  - 查看文件的信息

- du -h <filePath>
  - 查看目录下的文件信息

- mv <fromPath> <toPath>
  - 将某个文件从一个目录移动到另一个目录

- lsof -i:<port>
  - 查看某个端口的占用情况

- kill -9 <PID>
  - 根据PID终止某个进程