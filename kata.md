
#kata

> https://github.com/XmchxUp/Kata

## [coding interview university](https://github.com/XmchxUp/coding-interview-university)

## [go concurrency exercises](https://github.com/XmchxUp/go-concurrency-exercises)

## [rustlings](https://github.com/XmchxUp/rustlings/tree/round1)

Small exercises to get you used to reading and writing Rust code!

## Advent Of Code

### [Advent Of Code 2023](https://adventofcode.com/2023)

Enjoy  

Advent Of Code 2023 Day 10: Pipe Maze  

管道有两个端点 , phase1就是通过bfs求次数(从S出发向四个方向访问，看有没有管道可走)

phase2：本来以为从.出发不可到达的地方个数就是结果，就用floodfill，后面仔细看题发现是pipe围起来的地方。

后面找资料发现其实是一个[# Point in polygon](https://en.wikipedia.org/wiki/Point_in_polygon)问题，用Ray casting algorithm去解。
有许多特殊情况， 
```sh
### **射线刚好与边重合**

如果射线刚好与多边形的一条边重合（不仅仅是与顶点相交，而是完全重合），则可能导致无限多个交点。

**处理方法**：

- 这种情况通常需要被视为一种特殊情况，通常可以简单地忽略该边（视为没有交点）或只计为一个交点。
```

![image](https://github.com/XmchxUp/picx-images-hosting/raw/master/20240825/image.3k7xyowwem.webp)

Advent Of Code 2023 Day 6 ~ Day 9

[Advent Of Code 2023 Day 5: If You Give A Seed A Fertilizer](https://adventofcode.com/2023/day/5)

phase-2 几十亿的数遍历，直接CPU拉满卡死，后面改用channel一个一个处理，不致于处理太快。不知道要跑多久，可以改成并发的。

```

换种思考方式

  

// getAnswerOptimaze

// 将所有操作都使用区间（交集，差集，合并），最后得出所有location的区间(排序后的)，每一个区间的start就是结果

func getAnswerOptimaze() int {

// seeds-range

// seeds-range.intersection(cur-level-range)

// calculate next-level-offset-range

// seeds-range.difference(cur-level-range)= diff-seeds-range

// diff-seeds-range.union(next-level-offset-range) all-next-level-offset-range

// repeat all level

return 0

}

```

[Advent Of Code 2023 Day 4: Scratchcards](https://adventofcode.com/2023/day/4)

在阶段 2 时，使用了一个 map 记录 cardID 可以有哪些 cardID 生成，然后获取值的时候递归的去找，注意使用 Cache，剪枝。

```go

Card 1: 41 48 83 86 17 | 83 86 6 31 17 9 48 53

Card 2: 13 32 20 16 61 | 61 30 68 82 17 32 24 19

Card 3: 1 21 53 59 44 | 69 82 63 72 16 21 14 1

Card 4: 41 92 73 84 69 | 59 84 76 51 58 5 54 83

Card 5: 87 83 26 28 32 | 88 30 70 12 93 22 82 36

Card 6: 31 18 13 56 72 | 74 77 10 23 35 67 36 11

END

map[2:[1] 3:[1 2] 4:[1 2 3] 5:[1 3 4]]

```

[Advent Of Code 2023 Day 3: Gear Ratios](https://adventofcode.com/2023/day/3)

先构建物体，在从坐标出发

// 找出所有 symbol, 所有数的坐标

// 遍历 symbol，对角线，左右判断有没有数的坐标一样

[Advent Of Code 2023 Day 2: Cube Conundrum](https://adventofcode.com/2023/day/2)

[Advent Of Code 2023 Day 1: Trebuchet?!](https://adventofcode.com/2023/day/1)

## LeetCode

[146. LRU Cache](https://leetcode.cn/problems/lru-cache/description/)

一个 map 存储数据，一个 list 当 LRU 的队列，想要在 list 中快速删除对应的值可以考虑在使用个 map 记录对应 list 的 node。

[74. Search a 2D Matrix](https://leetcode.cn/problems/search-a-2d-matrix/description/)

方法一：可以考虑成数组，只不过需要坐标转换成 matrix O(log(m\*n))

方法二：可以从右上角开始，小于就往左，大于就往下。O(m+n)

[704. Binary Search](https://leetcode.cn/problems/binary-search/description/)

考虑好边界，相等处理。

[114. Flatten Binary Tree to Linked List](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

如果使用 O(n) space 比较简单，前序遍历回放即可。

O(1) space 的话，利用递归思想 flatten(left), flatten(right)，最后再将 right, left 合并。

[199. Binary Tree Right Side View](https://leetcode.cn/problems/binary-tree-right-side-view/)

层序遍历，每层取最后一个元素

[2462. Total Cost to Hire K Workers](https://leetcode.cn/problems/total-cost-to-hire-k-workers/)

使用两个堆一个维护前面的，一个维护后面的最小 cost

需要注意两个堆中元素不能重复即(2\*candidates+k<=n)，如果元素不足，而可以直接排序取。

[1329. Sort the Matrix Diagonally](https://leetcode.cn/problems/sort-the-matrix-diagonally/)

对角线特性，同一对角线的元素 i-j 都相等，可以先收集，排序，放回。

[数学解法](https://leetcode.cn/problems/sort-the-matrix-diagonally/solutions/2760094/dui-jiao-xian-pai-xu-fu-yuan-di-pai-xu-p-uts8/)

[1. Two Sum](https://leetcode.cn/problems/two-sum/)

字典运用

[1017. Convert to Base -2](https://leetcode.cn/problems/convert-to-base-2/)

十进制 转 X 进制

```

n = a * (x)^5 + b * (x)^4 + c * (x)^3 + d * (x)^2 + e * (x)^1 + f * (x)^0

```

本质就是求 abcdef，可以每次求出 f(n%x)，然后 n-f，n//=x 就是下一个

[2007. Find Original Array From Doubled Array](https://leetcode.cn/problems/find-original-array-from-doubled-array/)

首先排序，每次配对最小的元素

## Codewars

[Training on Decode the Morse code | Codewars](https://www.codewars.com/kata/54b724efac3d5402db00065e/train/go)
