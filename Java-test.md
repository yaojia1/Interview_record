# 经典题型讲解

### DFS深度优先搜索
https://mp.weixin.qq.com/s/nMUHqvwzG2LmWA9jMIHwQQ
https://mp.weixin.qq.com/s/IZQkb-M27dt-AZ1VICThOw
Java参考视频: https://www.bilibili.com/video/BV1P5411N7Xc/?spm_id_from=333.788.recommend_more_video.1
Java(语速较慢，可1.5倍速): https://www.bilibili.com/video/BV1254y1976m?spm_id_from=333.337.search-card.all.click

### BFS广度优先搜索
https://mp.weixin.qq.com/s/WH_XGm1-w5882PnenymZ7g
C/C++语言参考(DFS在视频P2里): https://www.bilibili.com/video/BV1JU4y1p7Ue?spm_id_from=333.337.search-card.all.click

### ️二分查找

https://mp.weixin.qq.com/s/M1KfTfNlu4OCK8i9PSAmug
视频(伪代码)强推: https://www.bilibili.com/video/BV1d54y1q7k7?spm_id_from=333.337.search-card.all.click

### 滑动窗口

https://mp.weixin.qq.com/s/ioKXTMZufDECBUwRRp3zaA
视频(伪代码): https://www.bilibili.com/video/BV1V44y1s7zJ?spm_id_from=333.337.search-card.all.click

# 机考部分题目📋

这个是我们的机考题库，整个题库很大，这个是其中的一小部分
常考**类型字符串处理、二分查找、深度优先搜索、广度优先搜索**
http://www.amoscloud.com/?cat=57

进度3/85

Q81，计数器把4都跳过去，这不就是9进制吗？又一个数学大于编程的T_T

我靠，没给我保存，笔记全丢了！！！！！！！

Q67这题竟然考了。。。。竟然才通过36，，，为什么，，不理解

Q56？？？？？

# 练习题

这个是我去找的一些比较有代表的题目，有空可以做做呀

常考

- 类型字符串处理
- 二分查找
- 深度优先搜索
- 广度优先搜索

## 数组

- [x] https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/
- [x] https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/
- [ ] https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/
- [ ] https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/
- [ ] https://leetcode-cn.com/problems/contains-duplicate/
- [ ] https://leetcode-cn.com/problems/two-sum/
- [x] 二分查找：https://leetcode-cn.com/problems/binary-search/
- [x] https://leetcode-cn.com/problems/squares-of-a-sorted-array/

[^]: 额，原来还能归并排序。。。

## 字符串
- [x] https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/（看了
- [ ] https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/
- [ ] https://leetcode-cn.com/problems/ransom-note/
- [x] 可能有难度：https://leetcode-cn.com/problems/valid-parentheses/
- [ ] https://leetcode-cn.com/problems/reverse-string/
- [ ] https://leetcode-cn.com/problems/reverse-words-in-a-string-iii/

## 双指针
- [x] https://leetcode-cn.com/problems/move-zeroes/


## 迭代递推
- [x] https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/
- [x] https://leetcode-cn.com/problems/climbing-stairs/（斐波那契数列T_T

这题竟然标easy？计算机的尽头是数学。。。。。。。。。

## 位运算
- [x] https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/

范例好牛，学到了知识。。。

```
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count=0;
        while (n!=0){
            count+=n&1;
            n>>>=1;
        }
        return count;
    }
}
```

根据 与运算 定义，设二进制数字 nn ，则有：
若 n \& 1 = 0n&1=0 ，则 nn 二进制 最右一位 为 00 ；
若 n \& 1 = 1n&1=1 ，则 nn 二进制 最右一位 为 11 。

#### 方法二：巧用 n \& (n - 1)*n*&(*n*−1)

(n - 1)(n−1) 解析： 二进制数字 nn 最右边的 11 变成 00 ，此 11 右边的 00 都变成 11 。
n \& (n - 1)n&(n−1) 解析： 二进制数字 nn 最右边的 11 变成 00 ，其余不变。

<img src="E:\面试\pic\Binary.png" style="zoom:50%;" />

循环消去最右边的 11 ：当 n = 0n=0 时跳出。
res += 1 ： 统计变量加 11 ；
n &= n - 1 ： 消去数字 nn 最右边的 11 。

作者：jyd
链接：https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/solution/mian-shi-ti-15-er-jin-zhi-zhong-1de-ge-shu-wei-yun/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


## DFS/BFS
- [x] https://leetcode-cn.com/problems/flood-fill/

↑不是很难，我把int[]当成不可更改的了，搞了个很麻烦的list、、、、、懒得改了。。。。。

- [x] 有难度：https://leetcode-cn.com/problems/permutations/
- [x] 有难度：https://leetcode-cn.com/problems/combinations/

## 真题（已完成）

- [x] 1.【最佳装载快递的货车】

> 题目描述：
> 一辆运送快递的货车，运送的快递放在大小不等的长方体快递盒中，
> 为了能够装载更多的快递，同时不能让货车超载，需要计算最多能装多少个快递。
> 注意：
> 快递的体积不受限制快递数最多 1000 个 ，货车载重最大 50000
> 输入描述：
> 第一行输入每个快递的重量用英文逗号隔开 如 5,10,2,11
> 第二行输入货车的载重量，如 20
> 输出描述：
> 输出最多能装多少个快递，如 3
> Case
> 输入
> 5,10,2,11
> 20
> 输出
> 3

- [x] 2.【取相同字母最长的子串】

**懂了，先按每组长度对子串排序，再对字母排序，每个字母按最长的算，输出k的最长，md写这么复杂，里面的说明简直反向增加读题难度！**

Q57又是他

> 题目描述：给定一个字符串，只包含大写字母，求在包含同一字母的子串中，长度第 k 长的子串的长度，相同字母**只取最长**的那个子串。
> 输入描述：
> 第一行有一个子串( 长度 <= 100)，只包含大写字母
> 第二行为k 的值 。
> 输出描述：
> 输出连续出现次数第k 多的字母的次数。
> Case
> 输入：
> AABAAA
> 2
> 输出：
> 1
> 说明：
> 同一字母连续出现最多的
> A 3 次
> 第二多
> 2 次 但 A 出现连续 3 次 。
> 输入：
> AAAAHHHBBCDHHHH
> 3
>
> 输出：
> 2
> 说明：
> 同一字母连续出现的最多的是
> A 和 H 4 次
> 第二多的是
> H 3 次

- [x] 3.【消除相邻且相同】

> 题目描述：输入一个只包含英文字母的字符串，字符串中的俩个字母如果相邻且相同，就可以消除。在字符串上反复执行消除的动作，直到无法继续消除为止，此时游戏结束 。 输出最终得到的字符串长度。
> 输入描述：
> 原始字符串只能包含大小写英文字母，字母的大小写敏感，长度不超过100 。
> 输出描述：
> 输出游戏结束后字符串的长度。
> 注意：
> 输入中包含非大小写英文字母是均为异常输入，直接返回 0 。
> Case
> 输入：
> gg
> 输出：
> 0
> 输入：
> mMbccbc
> 输出：
> 3
> 说明：
> mMbcc （两个 c 消除） --> mMb b （两个 b 消除） --> mMc

- [x] 4.【找出最小绝对值】

> 题目描述：
> 给定一个随机的整数数组（可能存在正整数和负整数） nums 请你在该数组中找出两个数，其和的绝对值（ （|nums[x]+nums[ y]|）为最小值 并返回这 个最小值 。 每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。
> 输入描述：
> 一个通过空格空格分割的有序整数序列字符串，最多1000 个整数，且整数数值范围是 65535,65535] 。
> 输出描述：
> 两个数和两数之和绝对值。
>
> 注意：
> 输入中包含非大小写英文字母是均为异常输入，直接返回 0 。
> Case
> 输入：
> -3 1 5 7 11 15
> 输出：
> 2
> 说明：
> 3 和 5 相加为 2 ，其绝对值为最小

- [x] 5.【 找 出最小绝对值 】

> 题目描述：
> 已知连续正整数数列 {K}=K 1 ,K 2 ,K 3 …K i 的各个数相加之和为 S i=N(0<S<100000, 0<N<100000), 求此数列 K 。
> 输入描述：
> 输入包含两个参数，
> 1 ）连续正整数数列和 S 
>
> 2 ）数列里数的个数 N 。
> 输出描述：
> 如果有解输出数列K ，如果无解输出 1 。
> Case
> 输入：
> 525 6
> 输出：
> 85 86 87 88 89 90
> 说明：
> 85+86+87+88+89+90=525 ，且连续正整数列个数为 6

已死亡、、、

26%+50%+50%*2≈170