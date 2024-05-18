# What can i say

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
