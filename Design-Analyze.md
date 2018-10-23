##Dynamic Programming##
>动态规划

钢条切割问题：

问题1：一共有多少种切割方式？

思路一：对于一个长度为n英寸的钢条，其中一共有n-1个节点可供切割，在每一个节点处都可以选择切割或者不切割，将对一根钢条的切割过程视为从第一个节点直到第n-1个节点逐一选择切割或者不切割的一个过程，利用乘法原理，可以算出来总共有2n-1种切割方案。以四个节点的钢条为例：

思路二：也可以将切割一个长度为n英寸的钢条视作是从n-1个节点当中选择i（i=0,1,2,....,n-1）个节点进行分割的过程。那么总的分割方式m的计算方式可以使用排列组合的相关知识进行运算。m=C0n-1+C1n-1+.....+Cn-1n-1，根据牛顿二项式公式m=2n-1。