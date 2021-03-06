#如何理解InnoDB索引#

>InnoDB任何对于数据的增删查改都是基于索引结构的操作。Innodb的索引采取了B+ 树的数据结构来组织数据。

##B+ Tree##
<a href="https://en.wikipedia.org/wiki/B%2B_tree">B+树</a>是由B树发展而来。它是一种平衡树。换言之对于任何叶子节点的查询所需要经过的路径长度都只与这颗树的高度h相关。这对于大量磁盘数据的存取将十分有意义。
通常当数据量过大而不适合保存在内存中时，将会把数据转移至磁盘中管理。而对于磁盘的读写操作，也就是磁盘I/O的代价，都是十分昂贵的。因为基于磁盘的设计，数据I/O将会涉及到机械运动，这对于读写速度而言是十分低效的。而对于B+树而言，查询任何一个叶子节点的路径长度都只与高度h相关，即只需要h次I/O操作。
同时B+树通常具有一个相当大的**<a href="https://en.wikipedia.org/wiki/Branching_factor">分支因子</a>**，即每一个节点的最大子节点个数，也称为出度。这将确保B+树可以存储海量的数据。

##InnoDB的物理空间管理##
Page：
Innodb最基本的存储单位称为page（页），通常每个页面大小为16KB。每一个页面都分配一个32bit的页编号，这个编号代表了从第0个页面起的偏移量，例如第一个page0的编号为0，则第二页page1的偏移地址则为 16*2^10=16384。

Extent：
连续的64页空间组合在一起成为一个‘Extent’。系统在每个起始extent的第一页page0写入包含了从本extent开始紧接着的共256个extent的描述，称为Extent Descriptor。它包含了每个Extent的使用情况。

Space：
N个Extent组合在一起成为一个Space，即表空间。理论上来说InnoDB的space最大空间为64TiB，即理论上的32bit页编号的最大偏移地址 2^32*16*2^10=64*2^40=64TiB。

![](file:///F:/test.png)

##INDEX B+ Tree##
B+树的一些重要特性
1、B+树是分支因子很大的平衡树。
2、若节点可存储的最大关键字个数为N，则该B+ TREE的分支因子b=N+1
3、除根节点外的节点关键字个数至少为 ⌈b/2 - 1⌉，所有节点最多有b-1个关键字

在innodb中根节点常驻内存中，表查询首先就需要通过data directory查询到对应的根节点位置。所有节点都包含了一个逻辑上的最小key值infimum和最大值supremum。在叶子节点上保存了主键key以及行数据。而在非叶子节点中则保存了子节点的最小key值子节点的page编号。所有page的关键字在页内在逻辑上均以单链増序排列（在物理地址上则不是），同高度的page之间则以双链形式连接。
![](https://i.imgur.com/gRNreIU.png)

1、	该树的一个节点最多可包含3个key。其分支因子为4。
2、	
Innodb B+TREE的操作：
1、	SERACH：
在这颗B+TREE中查询key为3的数据，则会从根节点开始搜索，沿page3-》4-》7最终在page7中查询到数据值为‘D’。而当我们不使用索引树查询，则直接通过简单的表扫描由page6->7->8->9的顺序遍历并将在page7中查询到数据值为‘D’。
2、	INSERTION 
**Innodb对于Insertion的优化：**
按照标准的B+树的插入步骤，当数据是有序插入时，将造成大量页面称为半满状态，造成<a href="https://stackoverflow.com/questions/48364549/how-does-the-leaf-node-split-in-the-physical-space-in-innodb">空间浪费</a>。Innodb对于有序插入在子节点已满时只会在父节点下重新分配一个空白节点并将数据插入。


**Secondary indexes**
>二级索引

innodb除了主键以外的索引称为二级索引。当前二级索引与主键有重叠的字段，将删除主键中的对应字段。例如PKV=KEY(a,b,c),S-PKV=KEY(a,d),则实际主键将为KEY(b,c)。二级索引中的叶子页面和非叶子页面中都将包含其记录对应的主键值，因为二级索引没有非空和唯一性的限制，需要使用主键以确保唯一性，叶子节点中只保存主键值，避免了数据行的重复存储。因此使用二级索引做查询是实际上将做两次查询，第一次通过二级索引得到主键值，再通过主键索引得到数据行。