# Res

[code](https://github.com/cs-learning-every-day/cs61b-code-sp21/tree/master/proj3/byow/Core/map)  
[Section 3 - Generating Maps - Roguelike Tutorial - In Rust](https://bfnightly.bracketproductions.com/chapter23-prefix.html)

## Simple Alogirthm

随机生成几个 room 用矩阵来表示，然后每次生成一个 room 就与前一个 room 相连，可以随机的选定方向  
![image](https://github.com/XmchxUp/picx-images-hosting/raw/master/20240406/image.6bgulfj44y.png)

## Binary Space Partition

可以看成一个整体的 room，然后可以任意选取随机大小的四个候选 room，不断操作直到生成需要的个数。  
![image](https://github.com/XmchxUp/picx-images-hosting/raw/master/20240406/image.4xubhedor2.webp)
