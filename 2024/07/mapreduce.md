
## Lab 1: MapReduce

[MR](https://github.com/cs-learning-every-day/mit-6.5840/pull/1)

[Desc](https://pdos.csail.mit.edu/6.824/labs/lab-mr.html)

```
$ cd ~/6.5840
$ cd src/main
$ go build -buildmode=plugin ../mrapps/wc.go
$ rm mr-out*
$ go run mrsequential.go wc.so pg*.txt
$ more mr-out-0
A 509
ABOUT 2
ACT 8
...
```

主要是基于channel实现，可以不需要关心锁，(channel底层有)

抽象出一个Task，worker不关心是什么Task，只需要去Coordinator请求一个Task，当Task完成上报，这样可以做到worker是无状态的。

## Worker

写文件的原子性：

- **写入临时文件**：首先将数据写入一个临时文件，而不是直接写入目标文件。这一步的目的是确保写入操作的完整性，因为即使在写入过程中发生错误或中断，目标文件仍然保持原样，不会受到影响。
    
- **原子性替换**：在确认数据已经完整且正确地写入临时文件后，使用 `OS.Rename` 将临时文件替换为目标文件。`OS.Rename` 在大多数操作系统中是一个原子操作，这意味着它要么完全成功，要么完全不执行。这样可以确保目标文件的内容要么是旧版本，要么是新版本，不会出现中间状态。

## Coordinator

主要是启动一个goroutine通过监听channel去处理所以操作，可以确保并发安全

当task处理超时，如果没有可用的worker则不需要处理，有可用的worker请求则分配过去，从而做到懒分配。