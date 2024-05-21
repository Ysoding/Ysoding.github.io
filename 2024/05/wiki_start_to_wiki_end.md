[Code](https://github.com/cs-learning-every-day/CS106L/tree/main/cs106L-assignment1)

## Problem

一个start_wiki_page中点击wiki内部连接最终到达想要的end_wiki_page中。

## 思想

BFS，优先遍历最相关的网站。  
相关性可以简单定义为:start_wiki_page中的所有link，和end_wiki_page中的所有link，共有的link数。
