
#go  
[# A Quick Guide to Go's Assembler](https://go.dev/doc/asm)

[Is it possible to include inline assembly in Go code?](https://stackoverflow.com/questions/2951028/is-it-possible-to-include-inline-assembly-in-go-code)

原理是通过 `mmap` 将待执行的汇编代码的二进制映射到可执行的虚拟内存中。

在使用 `mmap` 时，需要设置 `PROT_EXEC` 标志，`mmap`会返回一个虚拟地址。接着，将二进制代码复制到这个地址，并将其当作函数地址进行转换和执行。

### 怎么得到go代码的汇编代码二进制？

```go
package inc

func inc(n int) int {
	return n + 1
}
```

编译 `go tool compile -S -N inc.go `

```sh
╭─ultraman@ultraman-desktop  ~/Desktop/workspace/jitbf ‹node-›  ‹› (main*) 
╰─$ go tool compile -S -N inc/inc.go
<unlinkable>.inc STEXT nosplit size=34 args=0x8 locals=0x10 funcid=0x0 align=0x0
        0x0000 00000 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:3)      TEXT    <unlinkable>.inc(SB), NOSPLIT|ABIInternal, $16-8
        0x0000 00000 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:3)      PUSHQ   BP
        0x0001 00001 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:3)      MOVQ    SP, BP
        0x0004 00004 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:3)      SUBQ    $8, SP
        0x0008 00008 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:3)      FUNCDATA        $0, gclocals·g2BeySu+wFnoycgXfElmcg==(SB)
        0x0008 00008 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:3)      FUNCDATA        $1, gclocals·g2BeySu+wFnoycgXfElmcg==(SB)
        0x0008 00008 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:3)      FUNCDATA        $5, <unlinkable>.inc.arginfo1(SB)
        0x0008 00008 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:3)      MOVQ    AX, <unlinkable>.n+24(SP)
        0x000d 00013 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:3)      MOVQ    $0, <unlinkable>.~r0(SP)
        0x0015 00021 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:4)      INCQ    AX
        0x0018 00024 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:4)      MOVQ    AX, <unlinkable>.~r0(SP)
        0x001c 00028 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:4)      ADDQ    $8, SP
        0x0020 00032 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:4)      POPQ    BP
        0x0021 00033 (/home/ultraman/Desktop/workspace/jitbf/inc/inc.go:4)      RET
        0x0000 55 48 89 e5 48 83 ec 08 48 89 44 24 18 48 c7 04  UH..H...H.D$.H..
        0x0010 24 00 00 00 00 48 ff c0 48 89 04 24 48 83 c4 08  $....H..H..$H...
        0x0020 5d c3                                            ].
go:cuinfo.producer.<unlinkable> SDWARFCUINFO dupok size=0
        0x0000 2d 4e 20 72 65 67 61 62 69                       -N regabi
go:cuinfo.packagename.<unlinkable> SDWARFCUINFO dupok size=0
        0x0000 69 6e 63                                         inc
gclocals·g2BeySu+wFnoycgXfElmcg== SRODATA dupok size=8
        0x0000 01 00 00 00 00 00 00 00                          ........
<unlinkable>.inc.arginfo1 SRODATA static dupok size=3
        0x0000 00 08 ff 
```

获得assemble code `go tool objdump -S inc.o`

```sh
╰─$ go tool objdump -S inc.o        
TEXT <unlinkable>.inc(SB) /home/ultraman/Desktop/workspace/jitbf/inc/inc.go
func inc(n int) int {
  0x4f0                 55                      PUSHQ BP
  0x4f1                 4889e5                  MOVQ SP, BP
  0x4f4                 4883ec08                SUBQ $0x8, SP
  0x4f8                 4889442418              MOVQ AX, 0x18(SP)
  0x4fd                 48c7042400000000        MOVQ $0x0, 0(SP)
        return n + 1
  0x505                 48ffc0                  INCQ AX
  0x508                 48890424                MOVQ AX, 0(SP)
  0x50c                 4883c408                ADDQ $0x8, SP
  0x510                 5d                      POPQ BP
  0x511                 c3                      RET
```

### 执行

```go
package main

import (
	"fmt"
	"syscall"
	"unsafe"
)

func main() {

	code := []byte{
		0x55, 0x48, 0x89, 0xe5, 0x48, 0x83, 0xec, 0x08,
		0x48, 0x89, 0x44, 0x24, 0x18, 0x48, 0xc7, 0x04,
		0x24, 0x00, 0x00, 0x00, 0x00, 0x48, 0xff, 0xc0,
		0x48, 0x89, 0x04, 0x24, 0x48, 0x83, 0xc4, 0x08,
		0x5d, 0xc3,
	}

	memory, err := syscall.Mmap(-1, 0, len(code), syscall.PROT_READ|syscall.PROT_WRITE|syscall.PROT_EXEC, syscall.MAP_ANON|syscall.MAP_PRIVATE)
	if err != nil {
		panic(err)
	}
	defer syscall.Munmap(memory)

	copy(memory, code)

	memory_ptr := &memory
	ptr := unsafe.Pointer(&memory_ptr)
	inc := *(*func(int) int)(ptr)

	fmt.Println(inc(13)) // 14
}

```

```sh
go run main.go                                                             
14
```
