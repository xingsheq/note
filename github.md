# create hexo project at PC

```
mkdir myblog
cd myblog
hexo init
//启动测试
hexo s
```

# create repository

## create repository at github.com

Head over to [GitHub](https://github.com/) and [create a new repository](https://github.com/new) named *username*.github.io, where *username* is your username (or organization name) on GitHub.

我有自己的VPS主机，不需要通过github访问，创建的是普通的repository,仓库名为myblog，如果需要通过github page访问，就要求创建的仓库名为： *username*.github.io

## create master branch

在myblog文件夹右键，选择Git Bash Here

```
cd myblog
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:xingsheq/myblog.git
git push origin master
```

## create hexo branch

```
git branch hexo
//切换到hexo分支
git checkout hexo
git push origin hexo
```

## login in github.com,set hexo branch as default branch

# commit hexo source to hexo branch

现在hexo分支，只有一个README.md文件，可以把需要上传的文件上传到hexo分支，.gitignore文件配置不需要上传的文件或文件夹，默认内容如下：

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

提交文件

```
git add .
git commit -m “add hexo source"
git push origin hexo
```



# deploy to master branch

发布前需安装发布模块

```
npm install hexo-deployer-git --save
```

修改_config.yml

```
deploy:
  type: git  
  repo: git@github.com:xingsheq/myblog.git
  branch: master
```

部署

```
hexo g -d
```

现在在github的myblog仓库的master分支，可以看到生成的public目录的网站文件。



create your theme repository

git remote add origin git@github.com:xingsheq/hexo-theme-next-xingsheq.git



git submodule add git@github.com:xingsheq/hexo-theme-next-xingsheq.git themes/next-xingsheq

## modify theme and commit

`cd theme/next-xingsheq` 

 `git add` & `git commit`

`git push origin master`.

## commit theme modify to hexo

xingsheq@CV0015735N1 MINGW64 /e/VPS/myblog (hexo)
$ git status
On branch hexo
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   themes/next-xingsheq (new commits)

no changes added to commit (use "git add" and/or "git commit -a")

xingsheq@CV0015735N1 MINGW64 /e/VPS/myblog (hexo)
$ git add themes/next-xingsheq

xingsheq@CV0015735N1 MINGW64 /e/VPS/myblog (hexo)
$ git commit -m "add theme update to hexo"
[hexo 8df9ef4] add theme update to hexo
 1 file changed, 1 insertion(+), 1 deletion(-)

xingsheq@CV0015735N1 MINGW64 /e/VPS/myblog (hexo)
$ git status
On branch hexo
nothing to commit, working directory clean

xingsheq@CV0015735N1 MINGW64 /e/VPS/myblog (hexo)
$ git push origin hexo
Enter passphrase for key '/c/Users/xingsheq/.ssh/id_rsa':
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 332 bytes | 0 bytes/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To git@github.com:xingsheq/myblog.git
   4901d43..8df9ef4  hexo -> hexo

xingsheq@CV0015735N1 MINGW64 /e/VPS/myblog (hexo)
$

## Home PC  get 

```
git clone -b hexo git@github.com:xingsheq/myblog.git
git submodule init
git submodule update
```





