> 看老哥的视频真是太爽了，OnlyFans，将所学的知识打通，找到应用的地方。


## BrainF*ck JIT

[I made JIT Compiler for Brainf\*ck lol - YouTube](https://www.youtube.com/watch?v=mbFY3Rwv7XM&t=15s)  
[Brain F\*\*k in 100 Seconds | Prime Reacts: - YouTube](https://www.youtube.com/watch?v=NAwAeOATEfg)

[flat assembler](https://flatassembler.net/)  
[# Byte Positions Are Better Than Line Numbers](https://www.computerenhance.com/p/byte-positions-are-better-than-line)  

[ChromiumOS - Linux Syscall Table -](https://www.chromium.org/chromium-os/developer-library/reference/linux-constants/syscalls/#syscall-availability)

[Backpatching in Compiler Design - GeeksforGeeks](https://www.geeksforgeeks.org/backpatching-in-compiler-design/)

[[Golang怎么通过mmap执行汇编代码]]

[jitbf-rust](https://github.com/Ysoding/jitbf-rust)  
[jitbf-go](https://github.com/Ysoding/jitbf)

网络字节序都是用的大端，x86_64机器码用的小端

stdin(fd:0), stdout(fd:1)

搞汇编想起了之前学的一些关键知识点，寄存器的caller, callee，哪些用于参数传递，返回值。

再用rust写的时候，有个有意思的bug。  

一开始我初始化memory时，是这样的。

```rust
let mut memory = vec![0u8; JIT_MEMORY_CAP];
```

写的时候完全没有想到这个地方，所以先看下调试过程  
用rust-gdb

```

```sh
(gdb) p memory
$1 = Vec(size=10000000) = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
  0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
  0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
  0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
  0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
  0, 0, 0, 0, 0, 0, 0, 0...}
(gdb) p &memory
$2 = (*mut alloc::vec::Vec<u8, alloc::alloc::Global>) 0x7fffffffd350

(gdb) layout asm
(gdb) si
0x00007ffff7ffa000 in ?? ()

(gdb) info registers rdi
rdi            0x7fffffffd350      140737488343888

(gdb) x/10xb $rdi
0x7fffffffd350: 0x80    0x96    0x98    0x00    0x00    0x00    0x00    0x00
0x7fffffffd358: 0x10    0x60

(gdb) si
0x00007ffff7ffa003 in ?? ()
(gdb) x/10xb $rdi
0x7fffffffd350: 0x81    0x96    0x98    0x00    0x00    0x00    0x00    0x00
0x7fffffffd358: 0x10    0x60
```

为什么在原生的数据表示形式不全是初始化的0？因为用的是vec(动态数组)想想都应该知道底层有个len, capacity，items，更不用说在rust中是怎么样的。

然后又写出了这种，`let mut memory: [u8; JIT_MEMORY_CAP] = [0; JIT_MEMORY_CAP];`这是在stack上分配的，空间不会很大。  

- **Linux 和 macOS**: 默认的栈大小通常为 2 MiB。
- **Windows**: 默认的栈大小通常为 1 MiB。

这里分配了~=10MB

```rust 
const JIT_MEMORY_CAP: usize = 10 * 1000 * 1000; 
```

然后使用box将分配到heap上的写法。  
`let mut memory: Box<[u8; JIT_MEMORY_CAP]> = Box::new([0; JIT_MEMORY_CAP]);`  

会报stack overflowed，问问chatgpt是咋回事。

```sh
thread 'main' has overflowed its stack  
fatal runtime error: stack overflow  
[1] 4108596 IOT instruction (core dumped) cargo run
```

正确的写法

```rust
// type AsmFuncType = extern "C" fn(*mut [u8]);
type AsmFuncType = extern "C" fn(*mut u8);
fn jit_compile(ops: &Ops) -> Result<AsmFuncType, io::Error> 

        match jit_compile(&ops) {
            Ok(code) => {
                // let mut memory = vec![0u8; JIT_MEMORY_CAP];
                // let mut memory: [u8; JIT_MEMORY_CAP] = [0; JIT_MEMORY_CAP];
                // let mut memory: Box<[u8; JIT_MEMORY_CAP]> = Box::new([0; JIT_MEMORY_CAP]);
                let mut memory: Box<[u8]> = vec![0; JIT_MEMORY_CAP].into_boxed_slice();
                code(memory.as_mut_ptr());
                // println!("{}", memory[0]);
            }
            Err(e) => eprintln!("Error: {}", e),
        } 
```

```
ChatGPT: 


- 使用 `vec![0; JIT_MEMORY_CAP].into_boxed_slice()` 代替 `Box::new([0; JIT_MEMORY_CAP])`。这样可以确保在堆上分配内存，并且避免编译器在栈上分配大量临时空间的问题。

- 使用 `Box<[u8]>` 而不是 `Box<[u8; JIT_MEMORY_CAP]>`，这样可以避免固定大小数组在编译时带来的栈分配问题。

```
