OpenMP stands for **O**pen specification for **M**ulti-**P**rocessing

[Code](https://github.com/cs-learning-every-day/cs61c-sp22/blob/main/sp22-lab-starter/lab09/dotp.c)

[Summary of OpenMP 3.0 C/C++ Syntax](https://www.openmp.org/wp-content/uploads/OpenMP3.0-SummarySpec.pdf)

`#pragma omp parallel for` 指令用于并行化 `for` 循环，而 `reduction(+:total_sum)` 部分告诉 OpenMP 在所有线程之间对 `total_sum` 变量执行加法归约操作。这意味着每个线程都会计算 `total_sum` 的一个局部值，然后 OpenMP 会在所有线程中归约（合并）这些局部值，最终得到全局的 `total_sum`。

Naive: 1 thread took 4.577141 seconds  
Manual Optimized: 1 thread(s) took 0.875618 seconds  
Manual Optimized: 2 thread(s) took 0.445462 seconds  
Manual Optimized: 3 thread(s) took 0.314087 seconds  
Manual Optimized: 4 thread(s) took 0.296813 seconds  
Manual Optimized: 5 thread(s) took 0.320832 seconds  
Manual Optimized: 6 thread(s) took 0.294578 seconds  
Manual Optimized: 7 thread(s) took 0.287689 seconds  
Manual Optimized: 8 thread(s) took 0.289076 seconds  
Reduction Optimized: 1 thread(s) took 0.877703 seconds  
Reduction Optimized: 2 thread(s) took 0.447750 seconds  
Reduction Optimized: 3 thread(s) took 0.314114 seconds  
Reduction Optimized: 4 thread(s) took 0.299070 seconds  
Reduction Optimized: 5 thread(s) took 0.305538 seconds  
Reduction Optimized: 6 thread(s) took 0.297613 seconds  
Reduction Optimized: 7 thread(s) took 0.289809 seconds  
Reduction Optimized: 8 thread(s) took 0.291706 seconds  
Congrats! All dotp tests passed
