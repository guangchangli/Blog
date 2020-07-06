# Go-Action

## 1.Go Module

```go
go module
go mod init modName
go list -m all 查看所有依赖
	主版本号 次版本号 修订号
	indirect 标示不是本模块直接使用，而是其他依赖项使用
go list -m -versions 模块名 查看版本信息
go doc 模块名 查看文档
go.sum 文件是存储的依赖校验哈希
go mod tidy 清楚没用到的依赖
go build ,go test 会查找下载需要依赖
go get 模块名
```

## 常用命令

```go
go mod init 初始化当前文件夹, 创建go.mod文件
go mod edit 编辑go.mod文件
go mod graph 打印模块依赖图
go mod tidy 增加缺少的module，删除无用的module
go mod vendor 将依赖复制到vendor下
go mod verify 校验依赖
go mod why 解释为什么需要依赖
go mod download 下载依赖的module到本地cache（默认为$GOPATH/pkg/mod目录）
go mod edit -fmt 格式化
go mod edit -require=golang.org/x/text 添加依赖
```

## 替换包

```go
replace (
    golang.org/x/crypto v0.0.0-20180820150726-614d502a4dac => github.com/golang/crypto v0.0.0-20180820150726-614d502a4dac
    golang.org/x/net v0.0.0-20180821023952-922f4815f713 => github.com/golang/net v0.0.0-20180826012351-8a410e7b638d
    golang.org/x/text v0.3.0 => github.com/golang/text v0.3.0
)
```

```
可以使用命令 go list -m -u all 来检查可以升级的package
使用go get -u need-upgrade-package 升级后会将新的依赖版本更新到go.mod * 
也可以使用 go get -u 升级所有依赖

```

## 发布

```go
没有tag 默认是v0.0.0后面接上时间和commitid
git tag v1.0.0
git push --tags
如果更新版本信息
 git commit -m "msg" xxx.go
 git tag v1.0.1
 git push --tags origin v+主版本
go get -u 以使用最新的 minor 版本或修补程序版本（即它将从1.0.0更新到例如1.0.1，或者，如果可用，则更新为1.1.0）
go get -u=patch 以使用最新的 修补程序 版本（即，将更新为1.0.1但不更新 为1.1.0）
go get package@version 以更新到特定版本（例如

```

