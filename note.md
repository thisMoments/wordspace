git 小记

1，本地新建文件夹，运行git;
 输入git clone https://.... 从远端克隆仓库到本地；

2，查看分支 git cbranch;
   查看分支状态 git status；

3，创建分支 git checkout -b +分支名;
   切换分支 git checkout +分支名；

4，删除本地分支 git branch -D +分支名;
   删除远程分支 git push origin --delete +分支名；

5，添加修改的数据到缓存区 git add .（添加所有的）;
                        git add +文件名（只添加指定的文件） ；

6，提交修改到本地分支 git commit -m '备注' ;
   设置全局变量 git config --global user.email "xxx@xxx.com";
               git config --global user.name "xxx" ；

7,提交本地分支到远程分支上 git push origin +文件名 ；

8，合并add和commit操作 git commity -am '' ;

9, 创建秘钥 ssh-keygen -t rsa -C +账号 ;

10, 比较分支按的不同 git diff 分支1 分支2 ;

11， 合并 git merge 分支 ;

12, 打tag标签 git tag -a 版本号 -m   备注;
             git tag                查看分支;
             git tag -d             删除本地版本号;
             git stash apply stash@{x}  还原缓存;
