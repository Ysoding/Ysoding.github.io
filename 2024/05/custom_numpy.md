## Numpy

[Describe](https://inst.eecs.berkeley.edu/~cs61c/sp22/projects/proj4/#setup-git)

[Code](https://github.com/cs-learning-every-day/cs61c-sp22/blob/main/projects/fa21-proj4-starter/src/matrix.c)

测试中的 dumbpy 好像是学校内部的库，自己使用 numpy 封装了下。

task2 部分测试未测完。

### CUnit 安装

To use CUnit framework in your project, you must install it first

```
sudo apt-get install libcunit1 libcunit1-doc libcunit1-dev
```

Then in your test.c

```
#include <CUnit/CUnit.h>
```

And finally, you must add the flag `–lcunit` to the gcc command (at the end)

```
gcc  -o test test.c  -lcunit
```
