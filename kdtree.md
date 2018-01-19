# 如何使用KdTree搜索
* [英文原文][ref1]
* [中文参考][ref2]

这个教程里我们复习一下如何如何使用KdTree查找某个点或者位置的K最近邻点，然后复习一下在用户给定半径内如何查找所有最近邻点(在本案例中随机)。

## 理论指南
一课k-d树，或者k维树，是一种用于计算机科学的数据结构，用来组织k维空间中一些数量的点。它是带有额外约束的二叉搜索树。k-d树在范围或者最近邻居搜索里很有用。我们的目的通常只是处理三维点云，所以我们所有的k-d树都是三维的。k-d树的每层用一个垂直于对应轴的超平面按照一个特定维度分成子树。在根节点，所有子树都按照第一维分叉（比如说小的在左侧，大的在右侧）。往下一层就按照下一个维度进行分叉，当所有都被穷举后就返回成第一维度。
```
j = (i mod k) + 1 //j是i之后的维度
```
最有效的建立k-d树的方法就是用快排讲中位数作为根节点，然后所有小的在左，大的在右边。重复这个过程直到最后的子树只含有一个元素。

[ref1]: http://pointclouds.org/documentation/tutorials/kdtree_search.php "Documentaton - Point Cloud - How to use a KdTree to search"
[ref2]: http://www.cnblogs.com/aTianTianTianLan/articles/3902963.html "KD-TREE 算法原理"
