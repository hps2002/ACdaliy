# huangpeishen的刷题日记
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

## 前后缀分解 2023.4.2

凡是看到有最多进行两次的那种基本上都可以使用前后缀分解来做

前后缀分解是经过前面一次遍历找到目标值，再经过从后面开始的倒序遍历找到最终的目标值。

使用前后缀分解的特点必须是：第一次计算和第二次计算是独立的，且第一次计算在第二次计算之前，才能使用这个方法。

结合题目来讲：leetcode题库中的打家劫舍III，链接 https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/
```
我们可以先遍历一次求出只进行一次交易的最大值，第二次从后往前遍历，枚举在尽量高的价格将股票卖出求出最大值。

因为第一次已经求出了进行一次交易的时候得到的收益，第二次枚举的是进行第二次交易的分界点，在第i天的时候进行第二次交易，用后面最大的价格减去第i - 1天的价格加上第i - 1天的最大价值就是进行第二次交易的总收益；

枚举第二次交易的过程中记录后面天数价格最高的时候，用价格最高的减去买到的价格就是第i天进行第二次交易收益最大
代码：
int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<int> f(n + 2);
        /*使用minp记录当前价格的最小值，
          并求出只进行一次交易时候的最大收益记录在f[i]中
        */
        for (int i = 1, minp = INT_MAX; i < n; i++)
        {
            f[i] = max(f[i - 1], prices[i - 1] - minp);
            minp = min(minp, prices[i - 1]);
        }

        int ans = 0;
        /*使用maxp记录后面的价格的最大值，
          用后面的最大值减去所有枚举的第二次交易，取第二次交易的最大收益加上f[i],
          其中f[i]是进行一次交易的时候第i天获得的最大收益
        */
        for (int i = n, maxp = 0; i; i--)
        {
            ans = max(ans, maxp + f[i - 1] - prices[i - 1]);
            maxp = max(maxp, prices[i - 1]);
        }
        return ans;
    }
因为第一次交易和第二次交易的都是分开进行的，且第一次交易必定在第二次之前，所以可以使用上述的做法进行对问题的求解，当然这道题也可以用DP来做。

具体的做法等到后面最多交易K次的时候在进行详细的解释吧，现在对最多交易K次的状态转移还不是很理解。

最多进行一次交易，最多两次交易，最多进行K次交易，其实都属于最多进行K次交易的范畴之中。所以只要掌握最多进行K次交易的做法之后，就能通杀了。此外，股票系列会持续更新。
```