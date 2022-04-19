### **一、斐波那契数列**

**1、暴力递归**

斐波那契数列的数学形式就是递归的，写成代码就是这样：

```text
int fib(int N) {
    if (N == 1 || N == 2) return 1;
    return fib(N - 1) + fib(N - 2);
}
```

**递归算法的时间复杂度：子问题个数乘以解决一个子问题需要的时间**

即 O(2^n)，指数级别，爆炸。

**2、带备忘录的递归解法**「自顶向下」

一般使用一个数组充当这个「备忘录」，当然你也可以使用哈希表（字典），时间复杂度是 O(n)。

https://leetcode-cn.com/problems/stone-game-iii/submissions/

这题，倒过来的斐波那契，难在题解
