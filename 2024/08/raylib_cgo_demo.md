
#cgo

#wasm

[They Made a Sequel to C - YouTube](https://www.youtube.com/watch?v=Qzw1m7PweXs&t=9s)

[# Go Wiki: cgo](https://go.dev/wiki/cgo)  
[# Go Wiki: WebAssembly](https://go.dev/wiki/WebAssembly)

**[raylib_go_demo](https://github.com/Ysoding/raylib_go_demo)**

## CGo

```sh
go build -x

WORK=/tmp/go-build1176050387
cd /home/ultraman/Desktop/workspace/raylib_go_demo
git status --porcelain
cd /home/ultraman/Desktop/workspace/raylib_go_demo
git -c log.showsignature=false show -s --format=%H:%ct
mkdir -p $WORK/b001/
cd /home/ultraman/Desktop/workspace/raylib_go_demo
TERM='dumb' CGO_LDFLAGS='"-L./raylib-5.0_linux_amd64/lib" "-lraylib" "-lm"' /home/ultraman/go/pkg/mod/golang.org/toolchain@v0.0.1-go1.22.5.linux-amd64/pkg/tool/linux_amd64/cgo -objdir $WORK/b001/ -importpath github.com/Ysoding/raylib_go_demo -- -I $WORK/b001/ -I./raylib-5.0_linux_amd64/src ./main.go
# github.com/Ysoding/raylib_go_demo
./main.go:18:2: could not determine kind of name for C.CloseWindow
./main.go:17:2: could not determine kind of name for C.InitWindow
./main.go:15:8: could not determine kind of name for C.free
```

相对路径使用方法不对，没找到方法，只看到设置一个$SRCDIR，暂时用绝对  
[How to use a relative path for LDFLAGS in golang](https://stackoverflow.com/questions/28037827/how-to-use-a-relative-path-for-ldflags-in-golang)

```go
/*
#cgo CFLAGS: -I/home/ultraman/Desktop/workspace/raylib_go_demo/raylib-5.0_linux_amd64/include
#cgo LDFLAGS: -L/home/ultraman/Desktop/workspace/raylib_go_demo/raylib-5.0_linux_amd64/lib -lraylib -lm
#include "raylib.h"
*/
import "C"
```

```sh
ü/Desktop/workspace/raylib_go_demo (master ✘)✭ ᐅ go build
ü/Desktop/workspace/raylib_go_demo (master ✘)✭ ᐅ ldd raylib_go_demo 
  linux-vdso.so.1 (0x00007ffcb29ce000)
  libraylib.so.500 => not found
  libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x000074b23c600000)
  /lib64/ld-linux-x86-64.so.2 (0x000074b23c875000)
```

指定动态链接库（shared libraries）的搜索路径。

```sh
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/ultraman/Desktop/workspace/raylib_go_demo/raylib-5.0_linux_amd64/lib

ldd raylib_go_demo
  linux-vdso.so.1 (0x00007ffffcf41000)
  libraylib.so.500 => /home/ultraman/Desktop/workspace/raylib_go_demo/raylib-5.0_linux_amd64/lib/libraylib.so.500 (0x000073bcc9000000)
  libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x000073bcc8c00000)
  libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x000073bcc8f19000)
  libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x000073bcc92fb000)
  libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x000073bcc92f6000)
  /lib64/ld-linux-x86-64.so.2 (0x000073bcc9314000)
```

> 目录下有.so和.a优化，上面方法优先使用了.so

也可以直接放到build根目录

```go
/*
#cgo CFLAGS: -I.
#cgo LDFLAGS: -L. -lraylib -lm
#include "raylib.h"
*/
```

由于raylib.h里面用到了`libm.a`，所以go build时找不到这个库，可以以cgo LDFLAGS加上 `-lm`

```sh
Desktop/workspace/raylib_go_demo (master ✘)✭ ᐅ go build # github.com/Ysoding/raylib_go_demo /home/ultraman/go/pkg/mod/golang.org/toolchain@v0.0.1-go1.22.5.linux-amd64/pkg/tool/linux_amd64/link: running gcc failed: exit status 1 /usr/bin/ld: /home/ultraman/Desktop/workspace/raylib_go_demo/libraylib.a(rcore.o): in function érgVector2Distance': rcore.c:(.text+0x6d3b): undefined reference to ésqrtf' /usr/bin/ld: /home/ultraman/Desktop/workspace/raylib_go_demo/libraylib.a(rcore.o): in function érlRotatef': rcore.c:(.text+0xbb16): undefined reference to ésincosf' /usr/bin/ld: rcore.c:(.text+0xbd88): undefined reference to ésqrtf' /usr/bin/ld: /home/ultraman/Desktop/workspace/raylib_go_demo/libraylib.a(rcore.o): in function érlGenTextureMipmaps': rcore.c:(.text+0xe31b): undefined reference to élog' /usr/bin/ld: /home/ultraman/Desktop/workspace/raylib_go_demo/libraylib.a(rcore.o): in function éFloatEquals': rcore.c:(.text+0x11a0d): undefined reference to éfmaxf' /usr/bin/ld: rcore.c:(.text+0x11a1d): undefined reference to éfmaxf' /usr/bin/ld: /home/ultraman/Desktop/workspace/raylib_go_demo/libraylib.a(rcore.o): in function éVector2Length': rcore.c:(.text+0x11b8c): undefined reference to ésqrtf' /usr/bin/ld: 
```

在go里的的init object时用到了` C.GetScreenWidth()`但是 init在main之前调用，则窗口还没有初始化，得出的值是0。

## WASM

[CGO library build to JS WASM file](https://stackoverflow.com/questions/54946214/cgo-library-build-to-js-wasm-file)  
[# build: support emscripten compiler for cgo on wasm](https://github.com/golang/go/issues/55351)  
cgo没有办法直接编译成wasm。  

将raylib c代码编译 使用[Emscripten: An LLVM-to-WebAssembly Compiler](https://github.com/emscripten-core/emscripten)  

然后在用go调用raylib wasm模块，在通过go build成wasm？ ？？(是不是多此一举)
