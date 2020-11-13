# 简单动态规划（以找零钱问题为例）
## 1. 问题描述
假设你是一个自动贩卖机公司的程序员，你的公司希望你写一个算法来求出给定找零金额所需的最小硬币数，假定可用的硬币面额已知。

比如可用的硬币面额是1， 5， 10和25美分，找零金额是63美分，那么最小的找零组合为2个25美分，1个10美分，3个1美分，个数为6个。

再比如可用的硬币面额是1，5，10，21，25美分，找零金额仍然是63美分，那么最小的找零组合为3个21美分，硬币个数3。

这个问题可以用如下公式表示：
```latex
num_coins = min(1+num_coins(original_change-1), 1+num_coins(original_change-5), 1+num_coins(original_change-10), 1+num_coins(original_change-25))
```
## 2. 问题的递归解决
从公式可以看到问题的递归特性，脑海中也可以想象出大致的时间复杂度。如下图所示：
![call tree](/_posts/assets/callTree.png)

树的每个节点理论上会有1~4个子节点，最大层数为n1 = original_change / min(coin_list), 最小层数n2 = original_change / max(coin_list)。original_change是找零金额，coin_list是可用的硬币金额列表。

尽管不是一个完全的4^n，但可以感受到时间复杂度随着original_change是快速增长的。

时间复杂度很大的原因就在于重复计算了很多节点，比如图中的original_change = 15的节点就已经重复计算了至少3次了。

尽管递归解法是很低效的，但它是后续优化的基础，来分析一下递归解法如何写。
### 2.1 递归解法分析
  - 递归3定律的应用
    + 基态
        
        通常复杂问题的基态不容易想清楚，可以反过来想一般状态如何向基态前进。
        由递推公式可以看出，一般情形向基态前进的条件是找零金额大于coin_list中的任何一个面额。
        反之，什么时候停止向基态前进：找零金额恰好等于coin_list中的某个面额时，就不需要继续前进，直接用这个面额找零就行了。
        那么基态就是：找零金额in coin_list
    + 如何向基态前进

        使用递推公式就行了
    + 调用自身

        略
  - 基于上述分析，递归实现如下：
  ```python
    def mk_chg1(chg, coin_list):
        """Find how many coins needed for some change (45+ seconds taken to complete)
        Args:
            chg: money change
            coin_list: available coin list
        Returns:
            minimum coin number needed for this change
        Raises:
        -----------------------------------------------
        Examples:
        >>> mk_chg1(63, [1, 5, 10, 25])
        6
        >>> mk_chg1(63, [1, 5, 10, 21, 25])
        3
        """
        coin_num_min = chg
        if chg in coin_list:
            return 1
        else:
            for i in [coin for coin in coin_list if coin < chg]:
                coin_num = 1 + mk_chg1(chg - i, coin_list)
                if coin_num < coin_num_min:
                    coin_num_min = coin_num   
        return coin_num_min
  ```

### 2.2 递归解法改进
使用缓存保存重复的计算，当再使用到缓存的计算时，直接返回结果，这导致算法的速度极大地提升了
```python
def mk_chg2(chg, coin_list, known_results):
    """Use a container to store known results to cut time cost
    Args:
        chg: change amount
        coin_list: available coin denomination list
        known_results: dictionary to store number of coins for a calculated change amount
    Returns:
        minimum number of coins that sum up to change amount
    --------------------------------------------------------
    Examples:
    >>> mk_chg2(63, [1, 5, 10, 25], {})
    6
    >>> mk_chg2(63, [1, 5, 10, 21, 25], {})
    3
    """
    coin_num_min = chg
    if chg in coin_list:
        known_results[chg] = 1
        return 1
    else:
        for i in [coin for coin in coin_list if coin < chg]:
            if (chg-i) not in known_results:
                previous_coin_num = mk_chg2(chg - i, coin_list, known_results)
                coin_num = 1 + previous_coin_num
                known_results[chg-i] = previous_coin_num
            else:
                coin_num = 1 + known_results[chg-i]
            if coin_num < coin_num_min:
                coin_num_min = coin_num
            
    return coin_num_min
```

## 3 问题的动态规划解决
|1|2|3|4|5|6|7|8|9|10|11|
|-|-|-|-|-|-|-|-|-|--|--|
|1| | | | | | | | |  |  |
|1|2| | | | | | | |  |  |
|1|2|3| | | | | | |  |  |
|1|2|3|4| | | | | |  |  |
|1|2|3|4|1| | | | |  |  |
|1|2|3|4|1|2| | | |  |  |
|1|2|3|4|1|2|3| | |  |  |
|1|2|3|4|1|2|3|4| |  |  |
|1|2|3|4|1|2|3|4|5|  |  |
|1|2|3|4|1|2|3|4|5|1 |  |
|1|2|3|4|1|2|3|4|5|1 |2 |
我们来关注下面的场景：

关注上表的第11行，假设我们不知道当找零为11美分时需要几个coin。
|1|2|3|4|5|6|7|8|9|10|11|
|-|-|-|-|-|-|-|-|-|--|--|
|1|2|3|4|1|2|3|4|5|1 |??|
但此时找零面额为1-10时最少需要几个coin都已经知道了（1）

对于（1）这句话的理解是：要从上面的表得到找零面额为11时所需的最小硬币数，一定是从已有的结果加上可用的coin面额。这个加数coin必须比11小，换句话说就是1，5，10三种coin。
也就是说，可以从找零面额为10，6，1加上1，5，10得到找零面额11。因为找零面额比11小的所有面额所需的最小硬币数都已知了，且找零面额为10，6，1时需要的最小硬币数为1，2，1，加上1个硬币就是找零面额为11需要的硬币数：2，3，2。因此找零面额为11时所需的最小硬币数为2。

上面这个过程用一个更熟悉的场景来解释就是：求爬到第10级台阶共有多少种爬法，每次只能爬1或2级台阶。
这个问题可以等价为硬币找零问题中，可用的硬币面额只有1和2两种面额。
```python
def mk_chg3(chg, coin_list, coin_table):
    """Use dynamic programming to solve this problem
    ------------------------------------------------
    Examples:
    >>> mk_chg3(63, [1, 5, 10, 25], [0]*64)
    6
    >>> mk_chg3(63, [1, 5, 10, 21, 25], [0]*64)
    3
    """
    for chg_amount in range(1, chg+1):
        num_min = chg_amount
        for coin in [c for c in coin_list if c <= chg_amount]:
            num_coin = 1 + coin_table[chg_amount-coin]
            if num_coin < num_min:
                num_min = num_coin
        coin_table[chg_amount] = num_min
    return coin_table[chg]
```
但mk_chg3并不能给出是哪几个硬币凑出了找零金额，只能给出最少的硬币数量。要给出哪几个硬币，需要一个额外的参数coin_trace: List[int]来记录凑出每一个金额所需的额外硬币：
```python
def mk_chg4(chg, coin_list, coin_table, coin_trace):
    """Added one extra parameter coin_used to store what coin used
    """
    for chg_amount in range(chg+1):
        new_coin = 1
        num_min = chg_amount
        for coin in [c for c in coin_list if c <= chg_amount]:
            num_coin = 1 + coin_table[chg_amount-coin]
            if num_coin < num_min:
                new_coin = coin
                num_min = num_coin
        coin_table[chg_amount] = num_min
        coin_trace[chg_amount] = new_coin
    return coin_table[chg], coin_trace

def used_coins(chg, coin_trace):
    """Given change amount and coin trace, return used coins that sum up to change amount
    """
    while chg > 0:
        yield coin_trace[chg]
        chg = chg - coin_trace[chg]


if __name__ == "__main__":
    chg = 63
    coin_list = [1, 5, 10, 21, 25]
    coin_table = [0]*(chg+1)
    coin_trace = [0]*(chg+1)
    num_min, coin_trace = mk_chg4(chg, coin_list, coin_table, coin_trace)
    print(num_min, list(used_coins(chg, coin_trace)))
```
```
3 [21, 21, 21]
```