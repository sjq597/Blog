title: Vim 大小写转换
date: 2015-11-22 21:09:09
tags: [Vim,Linux]
categories: Vim
---
Vim有个命令还是挺好用的，就是大小写转换，因为我把键盘上的`Caps Lock`键改成了`Esc`键，所以有时候如果直接写一个大写字母还好，可以用`Shift`键，但是如果写一个大写的单词那就比较麻烦了，这个时候大写转换命令就派上大用场了。

```bash
gu	# 转小写
gU	# 转大写
```

当然对于`Vim`这么强大的工具来说，这个命令远远不止这么点作用，这个命令的强大之处在于可以配合光标移动命令达到各种效果：
```
～		# 将光标下的字母改变大小写
3～		# 将光标位置开始的3个字母改变其大小写
g~~		# 改变当前行字母的大小写
U		# 将可视模式下选择的字母全改写成大写字母
u		# 将可视模式下选择的字母全改成小写字母
gUU		# 将当前行的字母改成大写
guu		# 将当前行的字母改成小写
3gUU		# 将从光标开始到下面3行字母改成大写
gUw		# 将那个光标下的单词改成大写
guw		# 将光标下的单词改成小写
```
