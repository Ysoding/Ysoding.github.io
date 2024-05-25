> single instruction multiple data

[Code](https://github.com/cs-learning-every-day/cs61c-sp22/blob/main/sp22-lab-starter/lab08/simd.c)

[Desc](https://inst.eecs.berkeley.edu/~cs61c/sp22/labs/lab08/#exercise-4-unrolling-simd-loop)

Read over the [Intel Intrinsics Guide](https://software.intel.com/sites/landingpage/IntrinsicsGuide/) to learn about the available SIMD instructions (an intrinsic function is a function whose implementation is handled by the compiler). The [Intrinsics Naming and Usage documentation](https://software.intel.com/content/www/us/en/develop/documentation/cpp-compiler-developer-guide-and-reference/top/compiler-reference/intrinsics/naming-and-usage-syntax.html) will be helpful in understanding the documentation.  
[this guide](https://www.cs.virginia.edu/%7Ecr4bd/3330/S2018/simdref.html) to help you understand how to use the SIMD functions.

[SIMD Visualizer](https://piotte13.github.io/SIMD-Visualiser/#/)

[Compiler Explorer](https://godbolt.org/z/J7HXBk)

```sh
Let's generate a randomized array.
Starting randomized sum.
Time taken: 7.902477 s
Sum: 103161741312

Starting randomized unrolled sum.
Time taken: 6.405713 s
Sum: 103161741312

Starting randomized SIMD sum.
Time taken: 1.645757 s
Sum: 103161741312

Starting randomized SIMD unrolled sum.
Time taken: 1.522628 s
Sum: 103161741312
```
