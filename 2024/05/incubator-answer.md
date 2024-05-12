# incubator-answer

[GitHub - apache/incubator-answer: A Q&A platform software for teams at any scales. Whether it's a community forum, help center, or knowledge management platform, you can always count on Apache Answer.](https://github.com/apache/incubator-answer)

[Apache Answer | Free Open-source Q&A Platform](https://answer.apache.org/)

[GitHub - segmentfault/pacman: Pacman eat ghosts](https://github.com/segmentfault/pacman)

## 如何构建

### Dockerfile

一系列环境安装，构建，最后执行 script/entrypoint.sh 脚本

## 字段检验和国际化

做法都差不多，还是在 spring boot 中写的比较容易。[repo](https://github.com/cs-learning-every-day/mall/blob/main/backend/c-missyou/src/main/resources/config/exception-code.properties)

## 老数据表更新怎么操作？

通过 upgrade v1.1.0 每个版本有一个对应 Migration 里面有是否清楚缓存，更新表的函数

## 前端 build 出的文件怎么嵌入到可执行文件中？

使用 embed 可以在对应的文件夹同级目录下创建个 go 文件

```go
//go:embed build
var Build embed.FS

//go:embed template
var Template embed.FS

```

[react gin template](https://github.com/songquanpeng/gin-template)

## 依赖注入怎么搞？

使用 wire,在编译时生成代码  
还是 spring boot 爽啊
