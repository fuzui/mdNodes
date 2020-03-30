---
title: git合并分支
tags: git
categories: git
date: 2019-07-01
---

<div align='center' ><font size='70'>git合并分支</font></div>

将远程分支合并到自己分支上：
先在自己分支上，
切到目标分支，

```shell
`git checkout 你的分支名`
`git fetch <远程主机名> 目标分支`
`git merge <远程主机名>/目标分支`

if（有冲突）{
	解决冲突
	git add .
	git commit -m "解决冲突"
}
git push -u <远程主机名> 目标分支
```

fetch是只拉去，未合并，所以之后需要merge一下。
而pull是拉下来直接合并。

执行`git fetch <远程主机名> <分支名>`之后会出现一个`FETCH_HEAD`，通过`git log -b FETCH_HEAD`可查看（更新的文件名、作者、时间、代码），从而来判断是否产生冲突，最后决定是否merge到当前分支。