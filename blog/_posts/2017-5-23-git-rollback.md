---
date: 2017-5-23
tag: 
  - git
  - 回滚
author: NeoYo
location: Shenzhen
---

# Git 回滚

由于提交的代码会处于不同的位置：工作区，暂存区，只在本地提交commit 和 已推送到远程的 commit。 本文对处于不同位置的回滚，进行了区分和采取对应的回滚方法。

## 一、清空工作区

```bash
# delete tracked files
git checkout .
git checkout -- filename
(这里只回滚了以前跟踪过的文件，reset --hard 也同理)

# delete untracked
git clean -n			 #查看没被跟踪的文件
git clean -n -d	    	 #查看没被跟踪的文件夹
git clean -f 		     #删除与 git clean -n 对应的文件
git clean -f -d          #删除与 git clean -nd 对应的文件
```
    
[discard-local-changes](#discard-local-changes)
## 二、只在本地提交

```
#用于还没push到远程，可以清除commit记录                  
git reset --[] <revision>
                    head   暂存区  工作区
    reset --hard    回退     回退   回退
    reset --mixed   回退     回退   
    reset --soft    回退     
```

<pre>
只回退head:
会导致原本干净的工作区多了Modify...
如果commit 这些modify 相当于head又前进到之前版本
同时回退工作区的意思就是清空这些Modify...
</pre>



## 三、已推送到远程

使用 revert，它会带来新的 commit 记录。

revert 与 reset 不同的地方除了用于回滚处于不同的位置的commit。

reset 命令的结果是回滚 revision 之后提交。而 revert 命令的结果是 revision 对应的 commit 和 revision 之后的 commits 都会被回滚.

```bash
git revert <revision>
```

在 `sourcetree` 软件上使用如下
![](http://img.yuweixi.cn/17-5-23/89824937-file_1495506054361_aaad.png)

> git push --force 也可以回滚，但它并不安全，可能会把别人 commit 也回滚掉，一般不建议使用

## 四、git-reflog

有些指令，如 `git rebase` 和 `git reset [commitId] --hard`，执行后，在 `git log` 是找不到的。这时就需要用到 `git-reflog`

```
git reflog
git checkout <commitId>
```

## 参考
[stackoverflow.com/how-to-remove-local-untracked-files-from-the-current-git-working-tree](https://stackoverflow.com/questions/61212/how-to-remove-local-untracked-files-from-the-current-git-working-tree)

[http://www.open-open.com/lib/view/open1397013992747.html](http://www.open-open.com/lib/view/open1397013992747.html)
