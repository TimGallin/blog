##TREE##
>树结构

##Binary Tree##
>每一个节点最多只有两个分支的树数据结构

**应用**

- 搜索树
- 作为具有分叉特性的相关数据结构

##搜索树##

****

##完美二叉树（Perfect Binary Tree）##
>所有的节点都具有两个分支，所有的叶子节点都具有相同的深度

##完全二叉树（Complete Binary Tree）##
>除了最后一级的节点外，所有的节点都是填充满的状态，且所有的最后一级所有的节点都尽量的靠像左边
![](https://i.imgur.com/Rq6qX4O.jpg)

**堆排序**
堆是一个可以近似看做完全二叉树的数组。
```
pic
```

属性：(A为i个元素的数组)
PARENT(i)=[i/2];
LEFT(i)=[2*i];
RIGHT(i)=[2*i + 1];

最大堆:任何节点的值不大于其父节点的值
A[PARENT(i)]>=A[i]

最小堆:任何节点的值不小于其父节点的值
A[PARENT(i)]<=A[i]

以最大堆为例
- MAX_HEAPIFY(A,i) (输入一个下标i，通过调整使以A[i]为根节点的子树满足最大堆性质)
时间复杂度=O(lg(n))

- BUILD_MAX_HEAP(A)（构建包含n个元素的最大堆）
时间复杂度=O(n)

- HEAPSORT(A) (堆数组A进行堆排序，因为根据最大堆的性质树的根节点即A[0]一定是最大的元素)
时间复杂度=O(n*lg(n))
 
**优先队列（Priority Queue）**
>多用于抢占式多任务系统的作业调度
>以最大优先队列为例，其基础为**最大堆**

性质：
- HEAP_MAXIMUM (取最大权重的key。即A[0])
时间复杂度=O(1)

- HEAP_EXTRACT_MAX (取最大权重的key并将其从队列中移除)
时间复杂度=O(lg(n))

- HEAP_INCREASE_KEY(A,i,key) (将A[i]的值增加到key)
时间复杂度=O(lg(n))

- HEAP_INSERT_KEY(key) (添加一个key到队列中，等同于添加为key为无穷小的节点都末尾，再讲该节点的值增加到key)
时间复杂度=O(lg(n))


##无限完全二叉树（Infinite Complete Binary Tree）##

##平衡二叉树（Balanced Binary Tree）##

##红黑树（Red-Black Tree）##
>是一种自平衡二叉搜索树
 	

