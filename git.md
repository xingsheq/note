git原理

http://www.jianshu.com/p/8f424c40da82

snap 快照



# commit

## 本地目录加入版本控制

```
git init
```

## 添加文件

```
git add -A   保存所有的修改
git add .     保存新的添加和修改，但是不包括删除
git add -u   保存修改和删除，但是不包括新建文件。
```

## 提交

```
git commit -m "comment"
```

## 本地目录建立与远端仓库的联系

```
git remote add origin urlxxx
```

## 同步到远程库

```
git push origin master 同步到远端仓库master分支
```

# delete

```
git rm readme.txt 从当前跟踪列表移除文件，并完全删除
git rm –cached readme.txt 仅在暂存区删除，保留文件在当前目录，不再跟踪
```

# update

下载最新代码，但不merge

```
git fetch
```

下载最新代码，自动merge

```
git pull
```

git fetch更安全一些，在merge前，可以查看更新情况，然后再决定是否合并

克隆远端仓库到本地目录，clone来的代码，已自动建立与远端仓库的联系

```
git clone (-b branch) urlxxx
```

# 查

```
git log 查看日志
git status 查看本地文件状态
git remote -v 查看远端仓库
```

