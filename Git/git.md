### 1.安装git
Git Bash
PowerShell  git -v
## 配置git
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"

### 2.创建版本库
1. 选择一个文件夹创建版本库   
   命令：git init
   结果：多出.git初始化文件仓库成功
2. 在该文件夹添加文件
   命令：git add <file>
   结果: 将该文件夹中对应的文件添加到暂存区（Stage），可连续add多个文件等会一起提交
3. 将添加的文件改动提交
   命令：git commit -m <message>
   结果：提交暂存区到本地Git分支
4. 将提交的文件保存在远程仓库
   命令：git push origin master
   结果：提交到github上
5. 删除与远程仓库origin的关联
   命令：git remote rm origin
   结果：解除了本地仓库与远程仓库的绑定
疑惑：add是有新文件在该文件夹中创建使用 还是 已有文件但做了修改也要add吗
   都要add,add是将修改加入暂存区,然后才能commit
   
1. 查看文件状态
   命令：git status
2. 比对修改内容
   命令：git diff
3. 版本回退
   命令：git reset --hard HEAD^
   说明:
   前提：HEAD每指向的一个版本都有工作区和暂存区，以及已提交的状态 
   1.参数--hard 暂存区与工作区：都与 HEAD 完全一致（工作区的修改被删除）
   --soft 暂存区与工作区：保留回退前的修改，只是没提交  （一般用于合并提交或改写提交信息。）
   --mixed 暂存区：HEAD指向的内容（不在工作区你也看不到） 工作区：保留回退前的修改。
   2.HEAD^当前指针指向的上一个版本,HEAD^^当前指针指向的上两个版本,HEAD~100当前指针指向的上100个版本,或者可以写具体的commit_id，指定到哪个版本，比如git reset --hard e475a（哈希值前几位）
4. 修改撤销
   命令：git checkout -- file
   结果：情况1，文件修改后没add,这时checkout回文件修改前的状态；情况二，文件修改后add,然后又修改，这时checkout回文件add时的状态
5. 将暂存区的修改回退到工作区
   命令：git reset HEAD <file>
   说明：将add过的不想add了，但工作区的记录保留，要回退工作区可用 7.checkout
6. 删除文件
   命令：git rm file
   结果：删除file，并把删除file后的这个工作区版本放到暂存区

### 3.远程仓库
1. 创建SSH Key
命令：在git Bash下$ ssh-keygen -t rsa -C "youremail@example.com"
结果：在用户名.ssh目录下会生成id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。

2. 在 GitHub → Settings → SSH and GPG keys → New SSH key 添加 id_rsa.pub 文件

3. 通过gitBash关联现有本地仓库
命令：git remote add origin git@github.com:Roxy-Yuzuriha-Inori/learn_git.git
说明：origin是默认远程仓库的名称，一般采用ssh方式传输

4. 从远程仓库克隆到本地仓库
命令：git clone git@github.com:Roxy-Yuzuriha-Inori/learn_git.git
结果：在打开gitBash的路径下克隆的是个文件，里面包含.git等

### 4.分支管理
## 4.1创建与合并分支
HEAD指向的是当前的分支，Master指向的是最新提交的分支
我创建一个分支DEV，然后对代码进行修改，我的HEAD指向的是DEV，每次可以提交一部分，改完再和Master分支进行合并，不影响别人代码开发
对别人来说，他们的HEAD指向的是Master，看不到我的分支的代码
1. 创建并切换到分支dev
命令：git checkout -b dev   新版： git switch -c dev
说明：相当于git branch dev（创建分支）+ git checkout dev（切换分支）

2. 查看当前分支
命令：git branch
说明：带*的代表当前Head指向的分支

3. 切换分支
命令：git checkout master   新版：git switch master
说明：回到了主分支，看不到之前切换到dev分支时修改的内容

4. 合并分支
命令：git merge dev

5. 删除分支
命令：git branch -d dev 

6. 查看远程仓库信息
命令：git remote
结果：显示远程仓库的名字，详细信息$ git remote -v

faster-forward图示
                           HEAD
                             │
                             ▼
                          master
                             │
                             ▼
┌───┐    ┌───┐    ┌───┐    ┌───┐
│   │───▶│   │───▶│   │───▶│   │
└───┘    └───┘    └───┘    └───┘
                             ▲
                             │
## 4.2分支冲突
问题：分支和master都有修改，然后进行merge
结果：merge成功就自动提交；merge冲突就不提交，标记冲突的地方，然后要对本机文件进行修改然后add，commit

                            HEAD
                              │
                              ▼
                           master
                              │
                              ▼
                            ┌───┐
                         ┌─▶│   │
┌───┐    ┌───┐    ┌───┐  │  └───┘
│   │───▶│   │───▶│   │──┤
└───┘    └───┘    └───┘  │  ┌───┐
                         └─▶│   │
                            └───┘
                              ▲
                              │
                          feature
## 4.3分支管理策略
问题：通常合并时是Fast forward模式,在这种模式下，是将Master指向了分支Dev，然后删除了分支Dev，而我希望保留分支Dev的版本，重新提交一个分支让Master指向该分支
命令：git merge --no-ff -m "merge with no-ff" dev
结果：注意--no-ff 和 -m参数 
                                HEAD
                                  │
                                  ▼
                                master
                                  │
                                  ▼
                                ┌───┐
                         ┌─────▶│   │
┌───┐    ┌───┐    ┌───┐  │      └───┘
│   │───▶│   │───▶│   │──┤        ▲
└───┘    └───┘    └───┘  │  ┌───┐ │
                         └─▶│   │─┘
                            └───┘
                              ▲
                              │
                             dev
## 4.4Bug分支
问题：在分支的任务还未完成的情况下，需要提交一个临时的改动，比如bug。此时我不想提交Master做了一半的任务，但又得提交bug的修改
解决：可以先把工作现场git stash一下，然后在某个分支创建新分支修复bug，修复后，回到原来分支然后
命令： git stash list  查看所有保存的工作现场
       git stash apply 恢复工作现场，但stash list仍保存着记录
       git stash drop   删除记录
       git stash pop   恢复的同时把stash内容也删了
       git stash apply stash@{0}  恢复指定的stash

问题：修改过的bug在分支也应该被修改，即重现修改，比如在master分支上修复的bug，想要合并到当前dev分支
命令：git cherry-pick <commit>
结果：其中的<commit>是commit bug时，{$ git commit -m "fix bug 101"
[issue-101 4c805e2] fix bug 101}里面的```4c805e2```，
把bug提交的修改“复制”到当前分支，避免重复劳动。并且此命令会做一次对dev分支的提交

## 4.5Feature分支
一般新功能在master的分支dev再建一个分支Feature，没有合并该分支并且想要将其删除
命令：git branch -D <name>
结果：强制删除该分支

## 4.6多人协作
1. 将本地commit的分支提交到远程分支
命令：git push origin master
说明：origin（远程分支名，git remote查看） master（本地分支名，git branch查看）

2. 抓取分支
命令：git clone git@github.com:michaelliao/learngit.git
说明：克隆master分支

命令：git checkout -b dev origin/dev
说明：在本地创建一个分支dev对应远程的分支dev ，若没发生冲突可以继续修改提交

3. 推送分支时发生冲突
将本地分支dev与远程分支对应    $ git branch --set-upstream-to=origin/dev dev
用git pull从最新的提交从origin/dev抓下来，
然后，在本地合并，解决冲突，
再推送
