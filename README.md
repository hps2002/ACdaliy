# huangpeishen的刷题日记
@[toc]
## 动态规划专题
动态规划五部曲 ： 确定状态含义 -> 确定状态方程 -> 初始化 -> 遍历顺序 -> 举例推导dp[i];

## 01背包 2023.3.30
01背包就是对于n个物品以及1个背包，其中n个物品中，每个物品的重量为weight[i]，价值为value[i]，背包的容量为V, 且每个物品只能选一次。

一般有两种问题：
问背包能装下的最大价值为多少 ？
问装满V的方案数有多少？

### 求背包能装下的最大价值
使用二维的状态进行表示：dp[i][j]，表示在前i个物品中选，背包容量为j时背包能装的最大价值

对每个物品都进行挑选，每个物品都可以选，也可以不选，选的时候就要加上背包容量为 j - weight[i] 时能装的最大价值，然后和不选这个物品的情况进行比较，取最大值。
（这里是我最难理解的地方，所以还是需要多点做题，进行印象加深）

核心代码实现：(伪代码)
```
int dp[n][V + 1];
// 初始化
dp[0][0] = 0;
for (int i = wieght[0]; i <= V; i++)
    dp[0][i] = weight[0];

for (int i = 1; i < n; i++)
    for (int j = 0; j <= V; j++)
        if (wieght[i] > j) dp[i][j] = dp[i - 1][j];//这里因为当前i
        else dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i]);

cout << dp[n - 1][V];//此时最大

```

因为二维的状态表示基本上都是从上一层进行拷贝，所以可以将状态压缩成一维的状态量。
状态方程是dp[j] = max(dp[j], dp[j - weight[i]] + value[i])
含义是在[0 - i]个物品中选，选出在背包容量为j的时候背包所能装下的最大的价值。

核心代码：
```
/**初始化是一样的**/
for (int i = 1; i < n; i++)
    for (int j = V; v >= werght[i]; j --)
        dp[j] = max(dp[j], dp[j - weight[i]] + value[i]);
```
上面的代码用用的时倒序遍历V，目的是避免重复遍历上一层同一个状态
比如：
i = 1， 且wieght[1] = 1的时候,正序遍历
dp[1] = max(dp[1], dp[1 - weight[1]] + value[1]);
dp[2] = max(dp[2], dp[2 - weight[1]] + value[2]);

此时我们发现，正序遍历的dp[2]加上的正是dp[1]刚刚更新的值，所以我们进行倒序遍历的话就能在dp[1]更新之前获取上一层的值。

但是为什么使用二维状态记录的时候，正序就可以完成问题的解答？
因为使用二位的时候dp[i][j] = max(dp[i - 1], dp[i - 1][j - weight[i]] + value[i]);，直接使用的时上一层的值，而上一层的值并不会受到正序遍历的影响。


01背包初始化

01背包初始化要视情况而定，初始化第0个物品的时候需要对于所有背包空间大于等于第0个物品的时候需要初始化为第0个物品的价值，因为此时的背包空间大于等于第0个物品所占用的空间，所以当前能装的最大价值为第0个物品的价值。

### 求装满背包的方案数
背包容量为j, 每个元素容量为nums[i]，求装满j的方案数
dp[j] += dp[j - nums[i]]

dp[j]的在循环中的含义是，前i个元素中装满容量为j的背包的方案数。

因此公式的含义是，下一个nums[i]的循环中刚好装满j容量的方案数等于刚好装满j - nums[i]的方案数。当计算到所有元素的时候，dp[n][j]就是前 n 个元素中能装满容量为j的背包的方案数。

但是为什么要使用 += 进行答案的搜索呢？
因为在j + 1的时候需要加上对不同的num[i]进行组合都有不同的方案数，所以要加上。
比如说：
i = 2, nums[2] = 1, dp[4] = 2, dp[2] = 2,此时dp[4]应该更新为dp[4]' = dp[4] + dp[2];
i = 3, nums[3] = 2, dp[4] = 2, dp[1] = 1,此时dp[4]应该更新为dp[4]'' = dp[4]' + dp[1];

核心代码：
```
//初始化
dp[0] = 1;

for (int i = 0; i < n; i++)
    for (int j = m; j >= nums[i]; j --)
        dp[j] += dp[j - nums[i]];
cout << dp[m];//为刚好装满不同组合的方案数
```
如果重量不是一的话就很难处理
### 刷题日记

## 完全背包
完全背包和01背包的不同之处在于，对于给出的物品：01背包每个物品只能选一次，完全背包每个物品能选无限次。

### 求能装下的最大价值
状态含义：dp[i][j] 表示的是在前i个物品中选，容量不超过j的最大价值
后面压缩成一位状态dp[j]，容量为j的时候能选出的最大价值

将完全背包的状态表示从二维压缩到一维的时候遍历顺序有些许不同。

在01背包的时候我们先针对每个物品（顺序）再针对背包的不同容量（倒序）进行分析，这样的目的是为了修改第i个物品的时候，放置前面的i - j个方案出现重复的现象。

但是在完全背包中，因为每个物品都能选无数次，所以我们要把每个物品中重复的情况算进去，这样才能使有限容量的背包达到最大值。

状态转移方程:dp[j] = max(dp[j], dp[j - wirght[i]] + value[i]);

核心代码：
```
//初始化
dp[0] = 0;

for (int i = 0; i < n; i++)
    for (int j = weight[i]; j <= V; j++)
        dp[j] = max(dp[j], dp[j - weight[i]] + value[i]);
cout << dp[n - 1][V];
```

### 求装满背包的方案数 2023.4.1
状态含义:dp[i][j]， 表示在前i个物品中选，装满容量为j的方案数
状态压缩成一维,dp[j] 表示装满j容量的方案数

在完全背包中求装满背包的方案数和在01背包中求装满背包的方案数不一样，因为01背包的不可重复性，所以只能倒叙遍历背包容量这样来加上上一层的方案数。

完全背包直接顺序遍历背包容量，只要能装满的都直接给他装满，求出最大的方案数。

但是这里由两个要注意的地方：**物品先遍历** 和 **容量先遍历** 两者在去重方面由不同的效果。

先遍历物品再遍历容量, 例如：
```
for (int i = 0; i < n; i++)
    for (j = weight[i]; j <= V; j ++)
        dp[j] += dp[j - weight[i]];
```
此时只能按照物品一种组合的一种排列，所以当所求的方案数要求所选的物品的组合数唯一的时候先遍历物品，再遍历容量；

先遍历容量再遍历物品
```
for (j = weight[i]; j <= V; j ++)
    for (int i = 0; i < n; i++)
        dp[j] += dp[j - weight[i]];
```
此时能将一种组合的不同排列形成的方案数也加进去，所以当所求的方案数要求计算所选物品的所有组合数时，先遍历容量再遍历物品。

### 完全背包阶段性经验总结
背包背包模型的变形很多，关键在于解读出背包问题的本质，分析问题在背包问题上映射的情况。合理运用背包问题去解决。

背包问题的本质都是容量固定的情况下求最大价值、最小价值、最大方案数、最小方案数

#### 题型总结
完全背包求方案数、完全背包求最大价值、完全背包求方案数（排列）

##### 打家劫舍问题
就是完全背包的扩展，其中核心的思想就是在到达第i间房子的时候偷或者不偷:

偷的时候收益应该是当前房子的价值 + 上偷第i - 2间房子的最大价值。

不偷的时候就是偷上一间房子的最大价值。

## 状态机DP 2023.4.5
状态机dp是动态规划中某个具体的问题在不同时刻有不同的状态，通过状态之间的不同转移，使得问题递推出最值或者在过程中达到最值。

结合买卖股票系列进行理解：
定义状态f [i][j]，表示第i天完成第j次交易获得的最大收益；
定义状态g[i][j]，表示第i天开始第j次交易获得的最大收益；

状态转移：f[i][j] = max(f[i - 1][j], g[i - 1][j] + price[i - 1]
表示完成第j次交易的时候可以延续第i - 1天时候的状态，也可以通过前一天的第j次买进股票的是偶及进行卖出，表示第i天完成第j次交易，g[i -1][j] + price[i - 1];

状态转移：g[i][j] = max(g[i - 1][j], f[i - 1][j - 1] - price[i - 1])
表示完成开始第j次的交易的时候可以延续第j - 1次的状态，也可以理解为当天买进当天卖出；也可以通过完成上一次交易的时候开始第j次交易，即f[i - 1][j - 1] - price[i - 1];

状态机DP主要的难点在于找到状态转移图，以及状态的入口。

## 序列DP
### 不连续子序列
#### 最长上升子序列
定义状态
dp[i]，以nums[i]结尾的最长的递增子序列长度。

状态转移方程

找到以当前数字结尾的最长递增子序列
如果num[j] < num[i]，dp[i] = max(dp[i], dp[j] + 1)
其中j是i前面下标，从前面的数字中找到当前数字的最大递增子序列。

#### 最长公共子序列

状态定义
dp[i][j]表示str1第i个字符和str2第j个字符前面所构成的最长公共子序列的长度

状态方程：
当下标为i - 1和j - 1的字符串相同的时候，dp[i][j] = dp[i - 1][j - 1] + 1，表示的是公共子序列的长度增加

当下标为i - 1和j  - 1的字符串不相同的时候，dp[i][j] = max(dp[i - 1][j], dp[[i]][j - 1])，表示当前字符不同的时候，当前最长公共子序列的长度可以取的是：str1[i - 1]遍历str2的时候求得的最长公共子序列长度或者是str[i]对str2进行遍历的时候求出的最长公共子序列，对两个求最大值即可。

因为在遍历str1[i][1 ~ j]的时候，可能str[i - 1][j]会存在公共子序列，所以要将他和目前所遍历的这一层求最大值，才能使得全局的公共子序列获得最大值。

### 连续子序列
#### 最长重复子数组
状态定义：
dp[i][j]，表示数组nums1中以第(i - 1)个元素结尾，且数组nums2中以(j - 1)个元素结尾的重复子数组的最大值。

状态转移：
if (nums1[i - 1] == nums2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
如果相同的话就时长度 + 1
因为可能存在多个相同的重复子数组，所以dp数组不能将最终结果推导到最后面。因此要在过程中记录出现过的最大重复子数组的长度

初始化：
dp[0][0]没有意义，初始化为0
#### 最大子序和
状态表示
dp[i]表示考虑前i个数包括i，最长的子数组和

状态转移
dp[i] = max(nums[i], dp[i - 1] + nums[i])
表示的是，如果前面的子数组和加上当前的数字还没有当前的数字大的话，就从一个新的区间开始，比如说[-2, 1], -2 + 1还没有1本身大，所以直接开始一个新的区间才能找到最大值。
在过程中记录一下最大值，因为数组中存在负数，可能会出现较大的负数使得整个子数组和瞬间小于零。    

难点在于怎么理解将前面的子序列和加上当前的数字，还没有当前的数字大，说明前面的子序和肯定是一个负数，此时是不能达到最大的。因此调整计算从当前数开始的子数组和。

### 序列DP总结
做完连续序列以及非连续序列之后，给我一个感觉就是，在寻找转移方程的时候是特别要记住每一个dp状态表示的是什么，这样才能顺利的将状态转移方程推出来。
在连续序列问题中要考虑的是连续性，非连续序列问题中要考虑更多的是全局性。