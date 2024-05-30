## [Advent Of Code 2023](https://adventofcode.com/2023)
Enjoy

### [Advent Of Code 2023 Day 3: Gear Ratios](https://adventofcode.com/2023/day/3)

先构建物体，在从坐标出发

// 找出所有symbol, 所有数的坐标
// 遍历symbol，对角线，左右判断有没有数的坐标一样

### [Advent Of Code 2023 Day 2: Cube Conundrum](https://adventofcode.com/2023/day/2)
### [Advent Of Code 2023 Day 1: Trebuchet?!](https://adventofcode.com/2023/day/1)

## [74. Search a 2D Matrix](https://leetcode.cn/problems/search-a-2d-matrix/description/)

方法一：可以考虑成数组，只不过需要坐标转换成 matrix O(log(m\*n))

方法二：可以从右上角开始，小于就往左，大于就往下。O(m+n)

## [704. Binary Search](https://leetcode.cn/problems/binary-search/description/)

考虑好边界，相等处理。

## [114. Flatten Binary Tree to Linked List](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

如果使用 O(n) space 比较简单，前序遍历回放即可。

O(1) space 的话，利用递归思想 flatten(left), flatten(right)，最后再将 right, left 合并。

## [199. Binary Tree Right Side View](https://leetcode.cn/problems/binary-tree-right-side-view/)

层序遍历，每层取最后一个元素

## [2462. Total Cost to Hire K Workers](https://leetcode.cn/problems/total-cost-to-hire-k-workers/)

使用两个堆一个维护前面的，一个维护后面的最小 cost  
需要注意两个堆中元素不能重复即(2\*candidates+k<=n)，如果元素不足，而可以直接排序取。

## [1329. Sort the Matrix Diagonally](https://leetcode.cn/problems/sort-the-matrix-diagonally/)

对角线特性，同一对角线的元素 i-j 都相等，可以先收集，排序，放回。

[数学解法](https://leetcode.cn/problems/sort-the-matrix-diagonally/solutions/2760094/dui-jiao-xian-pai-xu-fu-yuan-di-pai-xu-p-uts8/)

## [1. Two Sum](https://leetcode.cn/problems/two-sum/)

字典运用

## [1017. Convert to Base -2](https://leetcode.cn/problems/convert-to-base-2/)

十进制 转 X 进制

```
 n = a * (x)^5 + b * (x)^4 + c * (x)^3 + d * (x)^2 + e * (x)^1 + f * (x)^0
```

本质就是求 abcdef，可以每次求出 f(n%x)，然后 n-f，n//=x 就是下一个

## [2007. Find Original Array From Doubled Array](https://leetcode.cn/problems/find-original-array-from-doubled-array/)

首先排序，每次配对最小的元素

## [Training on Decode the Morse code | Codewars](https://www.codewars.com/kata/54b724efac3d5402db00065e/train/go)
