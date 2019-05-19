# 如何查看镜像里的内容
```bash
docker save registry.xxxxxxx.com/xxxxxxx/sre-xxxx-centos7-init | tar -xvf -
rwxr-xr-x 14 root root 4096 Apr 15 17:59 25dc3f88197901acc07ba9b6b4e7215cc13304a85165c7155e2d202baf3958a9
drwxr-xr-x  2 root root 4096 Dec 24 11:34 4cc26bbce4abd98d12df676ae2e4b50b73123141412dc69dbea06841a3369d06
drwxr-xr-x  2 root root 4096 Dec 24 11:34 beee21f1c68bc9498143d61bdba1be813090f43f22c79d1d5aa51643c7176364
-rw-r--r--  1 root root 6479 Dec 24 11:34 c01082689c2f0c6432c09881976340727d6e7e020da7bcab6346d2fa37a4a1d9.json
drwxr-xr-x  3 root root 4096 Apr 15 18:00 d8e0e16bdaa001b2e084d0fdf7557c12937ec2f9d279e8fcf96436006cbe0745
drwxr-xr-x  2 root root 4096 Dec 24 11:34 d9ce52fa0436df9bc90e4121f59855633808716e25cc257f6611897164fe177e
drwxr-xr-x  2 root root 4096 Dec 24 11:34 dc3a69990c3d923965a4b63e3f766c838ba9bdca2b7752dfee3b3b01a289fd83
drwxr-xr-x  2 root root 4096 Dec 24 11:34 fd63471c43683e27ae0122119ffd5683c47dcb82a49aba37fbd741bba060458a
-rw-r--r--  1 root root  714 Jan  1  1970 manifest.json
-rw-r--r--  1 root root  139 Jan  1  1970 repositories
```
查看各镜像层信息：
```bash
cat manifest.json
[{"Config":"c01082689c2f0c6432c09881976340727d6e7e020da7bcab6346d2fa37a4a1d9.json","RepoTags":["registry.xiaojukeji.com/didionline/sre-didi-centos7-init:stable"],"Layers":["25dc3f88197901acc07ba9b6b4e7215cc13304a85165c7155e2d202baf3958a9/layer.tar","d8e0e16bdaa001b2e084d0fdf7557c12937ec2f9d279e8fcf96436006cbe0745/layer.tar","4cc26bbce4abd98d12df676ae2e4b50b73123141412dc69dbea06841a3369d06/layer.tar","beee21f1c68bc9498143d61bdba1be813090f43f22c79d1d5aa51643c7176364/layer.tar","dc3a69990c3d923965a4b63e3f766c838ba9bdca2b7752dfee3b3b01a289fd83/layer.tar","fd63471c43683e27ae0122119ffd5683c47dcb82a49aba37fbd741bba060458a/layer.tar","d9ce52fa0436df9bc90e4121f59855633808716e25cc257f6611897164fe177e/layer.tar"]}]
```
然后可以进入到各镜像层的目录:
```bash
$ ls 25dc3f88197901acc07ba9b6b4e7215cc13304a85165c7155e2d202baf3958a9
anaconda-post.log  bin  etc  home  json  layer.tar  lib  lib64  lost+found  media  mnt  opt  root  run  sbin  srv  tmp  usr  var  VERSION
```

#### 也可使用一个开源工具： [undocker](https://github.com/larsks/undocker/)
