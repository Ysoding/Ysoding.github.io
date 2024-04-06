## Res

- https://github.com/liuyubobobo/Play-with-Algorithm-Visualization
- 思想是奇数宽高，最外围为墙，起始点要是奇数点，每次走两格方格，之间为路，然后遍历时候用不同的算法如 DFS、BFS、Random DFS 。
- [The Buckblog](https://weblog.jamisbuck.org/archives.html)
- https://en.wikipedia.org/wiki/Maze_generation_algorithm
- [Maze Generation](https://professor-l.github.io/mazes/)
- [Maze Algorithms](https://www.jamisbuck.org/mazes/)

## Recursive Backtracking 思想：

递归的遍历每个方块，当走不通了时后就回溯标记成路

## Eller's 思想：

1. 第一个随便将 cell 分成几个 set
2. 每一个 set 下向延伸，至少有一个 cell 加入到当前的 set
3. 将下一行没有加入已有 set 的 cell，自立一个 set
4. 重复 2、3 直到完成
5. 在最后一行，将所有剩余的独立集合通过水平连接合并起来，确保所有的路径都是连通的

## Prime Algorithm：

1. 随便选个 vertex 放到 V。
2. 从 V 中找出最短的边，连接不在 V 集合中的 vertex，将该 vertex 放到 V 中。
3. 重复 1、2  
   将最短变成随机就是,Randomized Prime Algoirhtm

## Kruskal's Algorithm:

1. 构造所有 edge
2. 选取最短的 edge，如果连接了两个不相连的 vertex 则保留。
3. 重复 2  
   同上

## Aldous-Broder Algorithm:

1. 任意选择一个 vertex
2. 概率均匀的访问 neighbor，并选中当下一个，如果没被访问过，则将经过的边添加到生成树。
3. 重复 2

## Wilson's algorithm

1. Choose any vertex at random and add it to the UST.
2. Select any vertex that is not already in the UST and perform a random walk until you encounter a vertex that is in the UST.
3. Add the vertices and edges touched in the random walk to the UST.
4. Repeat 2 and 3 until all vertices have been added to the UST.

## Recursive Division

1. Begin with an empty field.
2. Bisect the field with a wall, either horizontally or vertically. Add a single passage through the wall.
3. Repeat step #2 with the areas on either side of the wall.
4. Continue, recursively, until the maze reaches the desired resolution.

## Hunt-and-Kill algorithm:

1. Choose a starting location.
2. Perform a random walk, carving passages to unvisited neighbors, until the current cell has no unvisited neighbors.
3. Enter “hunt” mode, where you scan the grid looking for an unvisited cell that is adjacent to a visited cell. If found, carve a passage between the two and let the formerly unvisited cell be the new starting location.
4. Repeat steps 2 and 3 until the hunt mode scans the entire grid and finds no unvisited cells.

## Sidewinder algorithm:

1. Work through the grid row-wise, starting with the cell at 0,0. Initialize the “run” set to be empty.
2. Add the current cell to the “run” set.
3. For the current cell, randomly decide whether to carve east or not.
4. If a passage was carved, make the new cell the current cell and repeat steps 2-4.
5. If a passage was *not* carved, choose any one of the cells in the run set and carve a passage north. Then empty the run set, set the next cell in the row to be the current cell, and repeat steps 2-5.
6. Continue until all rows have been processed.

## Growing Tree algorithm

1. Let C be a list of cells, initially empty. Add one cell to C, at random.
2. Choose a cell from C, and carve a passage to any unvisited neighbor of that cell, adding that neighbor to C as well. If there are no unvisited neighbors, remove the cell from C.
3. Repeat #2 until C is empty.
