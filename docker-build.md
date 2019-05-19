# Docker 源码编译
## Docker 13.1编译
下载docker 引擎源代码：
```bash
$ git clone https://github.com/docker/engine.git
```
把docker 源代码放入到 gopath目录中，并进入到 docker 源代码目录,这一步可选：
```bash
$ mv engine $GOPATH/src/github.com/docker/docker
$ cd $GOPATH/src/github.com/docker/docker
```

编译docker:
```bash
# make build
```
这个过程是初始化编译环境，最终会根据你选定的 docker 源码版本号，生成一个docker image,具体可以看下Makefile:
```bash
build: bundles init-go-pkg-cache
	docker build ${BUILD_APT_MIRROR} ${DOCKER_BUILD_ARGS} -t "$(DOCKER_IMAGE)" -f "$(DOCKERFILE)" .
```

build 日志:
```bash
docker build   -t "docker-dev:1-13-x" -f "Dockerfile" .
......
Removing intermediate container 298b06347448
Step 51/52 : ENTRYPOINT hack/dind
 ---> Running in 9ea42ffd7bda
 ---> c06f7194795d
Removing intermediate container 9ea42ffd7bda
Step 52/52 : COPY . /go/src/github.com/docker/docker
 ---> 14299bb7bff4
Successfully built 14299bb7bff4
Successfully tagged docker-dev:heads-v1-13-1
```
时间有点长，取决你的网络情况，然后，这个image里的 /go/src/github.com/docker/docker 目录就是你的docker源码目录了，具体也可以看下Dockerfile：
```golang
FROM debian:jessie  //基础镜像
.... //中间步骤省略，安装依赖包,比如golang
# Upload docker source
COPY . /go/src/github.com/docker/docker
```
生成的image:
```
$ docker images | grep docker
docker-dev                                                              heads-v1-13-1       14299bb7bff4        2 minutes ago       2.46GB
```

这一步build的目的就是为了生成image，应该叫docker build， 下面进入到真正构建过程，编译 docker 二进制,比如docker 1.13.1版本
```bash
docker run --rm -i --privileged -e BUILDFLAGS -e KEEPBUNDLE -e DOCKER_BUILD_GOGC -e DOCKER_BUILD_PKGS -e DOCKER_CLIENTONLY -e DOCKER_DEBUG -e DOCKER_EXPERIMENTAL -e DOCKER_GITCOMMIT -e DOCKER_GRAPHDRIVER=overlay2 -e VERSION=13.1 -e DOCKER_GITCOMMIT=092cba3727bb9b -e DOCKER_INCREMENTAL_BINARY -e DOCKER_REMAP_ROOT -e DOCKER_STORAGE_OPTS -e DOCKER_USERLANDPROXY -e TESTDIRS -e TESTFLAGS -e TIMEOUT -v "/root/engine/bundles:/go/src/github.com/docker/docker/bundles" -t "docker-dev:heads-v1-13-1" bash
```
然后在容器中执行编译脚本：
```
root@3b4451a497cb:/go/src/github.com/docker/docker# ./hack/make.sh binary
# ./hack/make.sh binary
---> Making bundle: binary (in bundles/1.13.1/binary)
Building: bundles/1.13.1/binary-client/docker-1.13.1
Created binary: bundles/1.13.1/binary-client/docker-1.13.1
Building: bundles/1.13.1/binary-daemon/dockerd-1.13.1
Created binary: bundles/1.13.1/binary-daemon/dockerd-1.13.1
Copying nested executables into bundles/1.13.1/binary-daemon
```
查看编译结果：
```
root@3b4451a497cb:/go/src/github.com/docker/docker# ls  bundles/1.13.1/binary-daemon/docker* -l | grep -v "sha256" | grep -v "md5"
-rwxr-xr-x 1 root root  8932648 Apr  8 07:49 bundles/1.13.1/binary-daemon/docker-containerd
-rwxr-xr-x 1 root root  8381448 Apr  8 07:49 bundles/1.13.1/binary-daemon/docker-containerd-ctr
-rwxr-xr-x 1 root root  3047368 Apr  8 07:49 bundles/1.13.1/binary-daemon/docker-containerd-shim
-rwxr-xr-x 1 root root   772408 Apr  8 07:49 bundles/1.13.1/binary-daemon/docker-init
-rwxr-xr-x 1 root root  2534781 Apr  8 07:49 bundles/1.13.1/binary-daemon/docker-proxy
-rwxr-xr-x 1 root root  7092800 Apr  8 07:49 bundles/1.13.1/binary-daemon/docker-runc
lrwxrwxrwx 1 root root       14 Apr  8 07:49 bundles/1.13.1/binary-daemon/dockerd -> dockerd-1.13.1
-rwxr-xr-x 1 root root 39941680 Apr  8 07:49 bundles/1.13.1/binary-daemon/dockerd-1.13.1

root@3b4451a497cb:/go/src/github.com/docker/docker# ls  bundles/1.13.1/binary-client/docker* -l | grep -v "sha256" | grep -v "md5"      
lrwxrwxrwx 1 root root       13 Apr  8 07:48 bundles/1.13.1/binary-client/docker -> docker-1.13.1
-rwxr-xr-x 1 root root 14125488 Apr  8 07:48 bundles/1.13.1/binary-client/docker-1.13.1
```

## Docker 18.06编译
原理同 1.13 相同，主要区别是 build 镜像过程不同，还有就是执行docker run 命令时参数不同，比如
```bash
docker run --rm -i --privileged -e BUILDFLAGS -e KEEPBUNDLE -e DOCKER_BUILD_GOGC -e DOCKER_BUILD_PKGS -e DOCKER_CLIENTONLY -e DOCKER_DEBUG -e DOCKER_EXPERIMENTAL -e DOCKER_GITCOMMIT -e DOCKER_GRAPHDRIVER=overlay2 -e VERSION=18.06-ce -e DOCKER_GITCOMMIT=cbe11bdc6da -e DOCKER_INCREMENTAL_BINARY -e DOCKER_REMAP_ROOT -e DOCKER_STORAGE_OPTS -e DOCKER_USERLANDPROXY -e TESTDIRS -e TESTFLAGS -e TIMEOUT -v "/root/gopath/src/github.com/docker/docker/bundles:/go/src/github.com/docker/docker/bundles" -t "docker-dev:18.06"
bash
```
编译：
```bash
 # export  DOCKER_GITCOMMIT=bb80604
 # ./hack/make.sh binary
```

 现在可以将docker-daemon和docker-client目录下的docker可以执行文件复制到容器的/usr/bin/目录下了。

### PS
1. 编译过程中，可能需要输入 commit id，那么就获取下你要编译源码的某个commit,以备编译的时候用：
 ```bash
 $ git log
 commit 092cba3727bb9b4a2f0e922cd6c0f93ea270e363 (HEAD -> v1.13.1, tag: v1.13.1)
 Author: Victor Vieux <victorvieux@gmail.com>
 Date:   Tue Feb 7 21:58:41 2017 -0800

     bump version to 1.13.1

     Signed-off-by: Victor Vieux <victorvieux@gmail.com>
 ```
 选择对应的版本就可以了，比如：092cba3727bb9b

2. 保存镜像  
由于构建 docker 镜像时，速度特别慢，所以可以把生成的镜像保存起来，以备下次换地方再用：
```bash
$ docker tag docker-dev:heads-v1-13-1 bmwx4/docker-dev:v1-13-1
$ docker push bmwx4/docker-dev:v1-13-1
```

[1.13.1 镜像下载地址](https://hub.docker.com/r/bmwx4/docker-dev)
