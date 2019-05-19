# 源码本地化

1. 创建一个本地的仓库地址

2. 修改docker enine 的 git配置,添加一个新的上游
```bash
git remote add upstream  git@git.xxxx.com/my-user/engine.git
```

3. 设置上游分支并push代码
```
git push --set-upstream upstream master
git push --set-upstream upstream 18.06
```
比如修改完配置如下：
```toml
cat .git/config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[remote "origin"]
	url = https://github.com/docker/engine.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = upstream
	merge = refs/heads/master
[remote "upstream"]
	url = git@git.xxxx.com:my-user/engine.git
	fetch = +refs/heads/*:refs/remotes/upstream/*
[branch "18.06"]
	remote = upstream
	merge = refs/heads/18.06
```

再添加一个18.09的branch
```bash
git checkout remotes/origin/18.09
Note: checking out 'remotes/origin/18.09'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 1046c63711... Merge pull request #185 from thaJeztah/18.09_backport_fix_leaking_task_resources
 didi@bogon  ~/gopath/src/github.com/docker/engine  ➦ 1046c63711  git checkout -b 18.09
Switched to a new branch '18.09'
```
上传分支到远程
```
git push --set-upstream upstream 18.09
Counting objects: 2739, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (985/985), done.
Writing objects: 100% (2739/2739), 871.93 KiB | 0 bytes/s, done.
Total 2739 (delta 1845), reused 2468 (delta 1621)
remote: Resolving deltas: 100% (1845/1845), completed with 608 local objects.
remote:
remote: To create a merge request for 18.09, visit:
remote:   http://git.xxxx.com/my-user/engine/merge_requests/new?merge_request%5Bsource_branch%5D=18.09
remote:
To git.xiaojukeji.com:dingguanjun/engine.git
 * [new branch]            18.09 -> 18.09
Branch 18.09 set up to track remote branch 18.09 from upstream.
```
再看自己的仓库应该如下图所以，说明已成功：

![repos](/images/docker-engin-repos.png)
