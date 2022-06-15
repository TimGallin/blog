
## Hamming Weight
汉明重量，指一串符号中非零符号的个数。以Richard Hammming Weigh 名称命名。在信息论，编码理论，密码学中均有应用

leetcode⎷⎷
通过按位判断1的个数即可解决。同时根据n&n-1会把n的最低位的1翻转，因为可以通过不断的计算n&n-1，计算的次数就是1的个数

```
    int hammingWeight(uint32_t n) {
        int ret = 0;
        while (n) {
            n &= n - 1;
            ret++;
        }
        return ret;
    }
```

## Prime Number
>[leetcode204 计算质数](https://leetcode-cn.com/problems/count-primes/)
质数：又称素数，在大于1的自然数中，除了1和它本身以外不再有其他因数的自然数。（与质数相对应的成为合数）
1. 枚举优化
判断一个数是否是质数，我们可以判断在[2,n-1]区间范围内有没有n的因数即可，这样在最差的情况下时间复杂度是O(n),为了优化这个过程，考虑假设x是n的因数，那么 n/x也必然是n的因数。假设x < y，那么x必然落在[2,⎷n]，因此我们只需要判断该区间的有无n的因数即可。

2. the Sieve of Eratosthenes
埃拉托色尼筛选法，增加一个flag[n]的空间用于保存[0,n]每个数是否是质数的结果。遍历[2,⎷n],把这个数的倍数，也是就是2x，3x，4x ... 全部置为合数，如果这个数已经被标记为合数就跳过。同时标记过程还可以进一步优化，可以从x*x开始标记，因为像2x，3x必然是在x以前就已经被其他数标记过了。
```
    int countPrimes(int n) {
        vector<int> isPrime(n, 1);
        int ans = 0;
        for (int i = 2; i < n; ++i) {
            if (isPrime[i]) {
                ans += 1;
                if ((long long)i * i < n) {
                    for (int j = i * i; j < n; j += i) {    
                        isPrime[j] = 0;
                    }
                }
            }
        }
        return ans;
    }
```

3. 线性筛选

4. 回溯
  类似DFS，需要迭代顺序进行分布尝试。在回溯前应该尽可能对结果集进行预处理，例如排序，以减少问题的复杂度

## Greatest Common Divisor(GCD) 最大公约数
约数(Divisor)，又称因子(Factor),其实从英文字面意思会更清楚，就是这个个数作为除数把一个数分几个部分了，也就是说a/b整除，这样b就称为a的divisor,那么顾名思义，common divisor就是两个数的公共约数，而这种约数如果存在多个的话，那么那个最大的就成为最大公约数(Greatest Common Divisor),也被称为Highest Common Factor (HCF)
GCD算法用于求解两个数的最大公约数，朴素的思想是我们可以枚举两个数的所有公约数然后挑选最大，但这个算法的时间复杂度在O(n)，而通过递归的查找可以将复杂度降到O(log(n)),这种算法也就是欧几里得算法
对欧几里得算法(Euclidean algorithm)的证明如下：
两个数a,b必然可以用a=k*b + r来表示，k=a/b, r=a mod b。假设d是a和b的公约数，r = a-k*b，那个两边同时除以d，r/d必定也是整除项，也就是说d也必定是r的公约数。
```
  def gcd(a, b):
    return a if b==0 else gcd(b, a%b)
```
