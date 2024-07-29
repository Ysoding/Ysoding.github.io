
![](https://raw.githubusercontent.com/Ysoding/qclock-go/main/qlock.gif)

[GitHub - Psteven5/CQClock: A Quine clock in C inspired by Tsoding](https://github.com/Psteven5/CQClock)  
[Ok, but can you do this in C? - YouTube](https://www.youtube.com/watch?v=plFwBqBYpcY&t=10640s)

[GitHub - tsoding/qlock-toolset: Toolset for reproducing Quine Clock in C](https://github.com/tsoding/qlock-toolset)

```
\ -> \\ 
\n (regex match in vscode) -> \\n
" -> \"
```

使用二进制来当成5 * 3的数字展示，例如

```python
>>> bin(31599)
'0b111101101101111'


111
101
101
101
111

这就是0
```

由于go代码需要严格的格式化，则暂时不知道有啥办法，只能使用 tsoding提供的std_c_lexer.h来format go代码当成文件embed到的代码中。

带有颜色的输出

```go
fmt.Printf("\033[1;41;30m%c\033[0m", ch)
```

当每次输出完，屏幕有个cursor指针，则移动这个cursor到输出前的位置，则在下一次时候完成刷新屏幕。

```go
// Print the escape sequences to move the cursor up and left
fmt.Printf("\033[%dA\033[%dD", h, w)
```
