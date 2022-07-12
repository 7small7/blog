## Linux环境安装

Go的安装相比其他语言环境非常简单，直接下载安装包，解压安装即可。

1. 下载安装包(官方地址:https://golang.org/dl/ 国内已被墙了)，直接去对应的Go语言中文网下载即可，稍等一下下就下载好了。

```shell
wget https://studygolang.com/dl/golang/go1.17.1.linux-amd64.tar.gz
```
2. 解压到应用程序目录。

```shell
$ tar -zxvf go1.17.1.linux-amd64.tar.gz -C /usr/local/
```
3. 验证安装。

```shell
/usr/local/go/bin/go version
# 出现下面的信息，表示安装成功。
go version go1.17.1 linux/amd64  
```
4. 添加到系统环境变量中。

```shell
$ echo 'export GOROOT=/usr/local/go' >> /etc/profile
$ echo 'export GOPATH=$HOME/go'      >> /etc/profile
$ echo 'export PATH=$PATH:$GOROOT/bin:$GOPATH/bin' >> /etc/profile
$ source /etc/profile
```

## Mac环境安装

在Mac上安装，就非常的简单，可以直接使用.dmg格式文件点击安装，也可以使用brew命令安装。这里就直接演示`brew`方式安装。

1. 检测是否存在Golang安装包。

```shell
╰─ brew search golang
==> Formulae
golang  golang-migrate  golangci-lint  glslang Casks goland
```
> 上图中出现golang，说明包是存在的。

2. 查看包版本以及额外的信息。

```shell
brew info golang
go: stable 1.18.1 (bottled), HEAD
Open source programming language to build simple/reliable/efficient software
https://go.dev/
Not installed
From: https://mirrors.ustc.edu.cn/homebrew-core.git/Formula/go.rb
License: BSD-3-Clause
==> Options
--HEAD
	Install HEAD version
==> Analytics
install: 95,161 (30 days), 384,518 (90 days), 1,434,361 (365 days)
install-on-request: 72,451 (30 days), 304,745 (90 days), 1,143,931 (365 days)
build-error: 606 (30 days)
```
> 通过上面的命令，可以看到，默认的版本是1.18.1。

1. 使用命令安装。

```shell
brew install golang
```
> 通过上面的命令，直接就会安装成功。

4. 检测安装效果。

```shell
go env
GO111MODULE="on"
GOARCH="amd64"
GOBIN=""
...省略部分信息...
```
> 出现上面的信息，则表示安装成功了。