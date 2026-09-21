---
title: LeeCode_notes
published: 2026-07-25T08:49:19.200Z
description: ''
updated: ''
tags:
  - Tag
draft: false
pin: 0
toc: true
lang: ''
abbrlink: 'leecode'
---

# 目录

- [滑动窗口](#滑动窗口)
  - [一、定长滑动窗口的核心思路](#一定长滑动窗口的核心思路)
  - [二、已完成题目笔记](#二已完成题目笔记)
  - [1. 1456. 定长子串中元音的最大数目](#1-1456-定长子串中元音的最大数目)
  - [2. 643. 子数组最大平均数 I](#2-643-子数组最大平均数-i)
  - [3. 1343. 大小为 K 且平均值大于等于阈值的子数组数目](#3-1343-大小为-k-且平均值大于等于阈值的子数组数目)
  - [4. 2090. 半径为 k 的子数组平均值](#4-2090-半径为-k-的子数组平均值)
  - [5. 2379. 得到 K 个黑块的最少涂色次数](#5-2379-得到-k-个黑块的最少涂色次数)
  - [6. 2841. 几乎唯一子数组的最大和](#6-2841-几乎唯一子数组的最大和)
  - [7. 1423. 可获得的最大点数](#7-1423-可获得的最大点数)
  - [8. 1052. 爱生气的书店老板](#8-1052-爱生气的书店老板)
  - [三、八道题横向对比](#三八道题横向对比)
- [栈](#栈)
  - [一、单调栈的核心套路](#一单调栈的核心套路)
  - [二、739. 每日温度](#二739-每日温度)
- [二叉树](#二叉树)
  - [一、先理解二叉树和递归](#一先理解二叉树和递归)
  - [二、144. 二叉树的前序遍历](#二144-二叉树的前序遍历)
  - [三、104. 二叉树的最大深度](#三104-二叉树的最大深度)
  - [四、236. 二叉树的最近公共祖先](#四236-二叉树的最近公共祖先)
  - [五、102. 二叉树的层序遍历](#五102-二叉树的层序遍历)
  - [六、98. 验证二叉搜索树](#六98-验证二叉搜索树)
  - [七、105. 从前序与中序遍历序列构造二叉树](#七105-从前序与中序遍历序列构造二叉树)
  - [八、六道题放在一起看](#八六道题放在一起看)
- [回溯](#回溯)
  - [一、77. 组合：逐行理解回溯](#一77-组合逐行理解回溯)

---

## 滑动窗口

这个题单是师兄推给我的, 感觉师兄在我博客里面的出镜率很高啊哈哈哈哈哈哈, 他真的是我读研以来遇到的最厉害的人! 但是我又很害怕被他刷到我的帖子, 感觉像是大人看小孩儿日记一样有些羞耻😐😮😯😕🫤🫠好了进入正题! 

题单：[滑动窗口与双指针](https://leetcode.cn/circle/discuss/0viNMK/)

### 一、定长滑动窗口的核心思路

#### 识别定长滑动窗口

当题目处理的是一段**连续**的子数组或子串，并且区间长度固定时，可以优先考虑定长滑动窗口。固定长度有时直接写成 `k`，有时会换一种方式表达，例如“中心左右各取 `k` 个元素”，此时实际窗口长度是：

```python
size = 2 * k + 1
```

#### 滑动的依据

相邻两个窗口的大部分元素是重合的。

例如窗口长度为 `3`：

```text
旧窗口：[2, 4, 6]
新窗口：   [4, 6, 8]
```

两个相邻窗口重合了大部分元素。窗口右移一格时，只有最左边的元素离开，同时有一个新元素从右边进入：

```text
左边的 2 离开窗口
右边的 8 进入窗口
```

因此每次不必重新遍历整个窗口，只需要在原状态上减去离开的贡献，再加上进入的贡献：

```python
window -= left_item
window += right_item
```

#### 基本写法

先明确窗口长度 `size`，再单独计算第一扇窗口并初始化答案。之后从第一扇窗口右侧的位置开始遍历：`right` 表示新进入窗口的元素，`right - size` 就是离开窗口的元素。窗口更新后，再根据题意记录最大值、最小值、合格窗口数量或指定位置的结果。

目前使用的通用骨架如下：

```python
size = k
window = sum(nums[:size])
answer = 处理第一扇窗口

for right in range(size, len(nums)):
    left = right - size

    window -= nums[left]       # 左边元素离开
    window += nums[right]      # 右边元素进入

    更新答案
```

其中的下标关系是：

```python
right = 新进入窗口的元素下标
left = right - size = 离开窗口的元素下标
```

---

### 二、已完成题目笔记

### 1. 1456. 定长子串中元音的最大数目

题目：[1456. 定长子串中元音的最大数目](https://leetcode.cn/problems/maximum-number-of-vowels-in-a-substring-of-given-length/)

#### 题目要求

从字符串中找出一个长度为 `k` 的连续子串，使其中的元音字母数量最多。

#### 思考过程

“长度为 `k` 的连续子串”已经确定了这是定长滑动窗口。题目只关心元音数量，因此没有必要保存窗口中的全部字符，只需维护当前窗口的元音数量 `vowel_count`。

第一扇窗口是 `s[:k]`。窗口右移时，检查离开的字符和进入的字符：离开的字符是元音就减一，进入的字符是元音就加一。每次移动后，用当前数量更新最大值。

```text
窗口状态：当前窗口中的元音数量
答案：所有窗口中最大的元音数量
```

#### 代码

```python
class Solution:
    def maxVowels(self, s: str, k: int) -> int:
        vowels = set("aeiou")

        vowel_count = 0
        for i in range(k):
            if s[i] in vowels:
                vowel_count += 1

        answer = vowel_count

        for right in range(k, len(s)):
            left = right - k

            if s[left] in vowels:
                vowel_count -= 1
            if s[right] in vowels:
                vowel_count += 1

            answer = max(answer, vowel_count)

        return answer
```

#### 关键点

滑动窗口维护的不一定是元素和，也可以是满足某种条件的元素数量。这里每个字符对窗口的贡献只有两种：元音贡献 `1`，其他字符贡献 `0`。

---

### 2. 643. 子数组最大平均数 I

题目：[643. 子数组最大平均数 I](https://leetcode.cn/problems/maximum-average-subarray-i/)

#### 题目要求

找出长度为 `k` 的连续子数组，使平均值最大。

#### 思考过程

所有窗口的长度都是 `k`，分母相同，所以比较平均值等价于比较窗口和：

```text
窗口平均值最大，等价于窗口和最大。
```

滑动过程中维护 `window_sum`，并用 `max_sum` 记录最大的窗口和。全部窗口检查完以后，再用 `max_sum / k` 得到最大平均值。这样既省去了重复除法，也避免在比较过程中引入不必要的小数。

```python
window_sum = 当前窗口的和
max_sum = 出现过的最大窗口和
```

#### 代码

```python
class Solution:
    def findMaxAverage(self, nums: List[int], k: int) -> float:
        window_sum = sum(nums[:k])
        max_sum = window_sum

        for right in range(k, len(nums)):
            left = right - k

            window_sum -= nums[left]
            window_sum += nums[right]

            max_sum = max(max_sum, window_sum)

        return max_sum / k
```

#### 关键点

`max_sum` 应当初始化为第一扇窗口的和，不能直接设成 `0`。如果数组全部由负数组成，真正的最大窗口和仍然是负数，初始化为 `0` 会得到一个并不存在的答案。

---

### 3. 1343. 大小为 K 且平均值大于等于阈值的子数组数目

题目：[1343. 大小为 K 且平均值大于等于阈值的子数组数目](https://leetcode.cn/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/)

#### 题目要求

统计有多少个长度为 `k` 的连续子数组，其平均值大于等于 `threshold`。

#### 思考过程

这题与 643 的窗口移动方式相同，仍然维护长度为 `k` 的窗口和。不同之处在于，它不需要最大值，而是要统计有多少扇窗口满足平均值条件。

平均值条件为：

```python
window_sum / k >= threshold
```

两边同时乘以 `k`：

```python
window_sum >= k * threshold
```

将两边同时乘以 `k` 后，只需要进行整数比较，判断条件更直接，也不会涉及小数精度。两道题的区别可以概括为：

```text
643：记录最大的窗口和
1343：统计合格窗口的数量
```

#### 代码

```python
class Solution:
    def numOfSubarrays(
        self, arr: List[int], k: int, threshold: int
    ) -> int:
        target = k * threshold
        window_sum = sum(arr[:k])

        answer = 0
        if window_sum >= target:
            answer += 1

        for right in range(k, len(arr)):
            left = right - k

            window_sum -= arr[left]
            window_sum += arr[right]

            if window_sum >= target:
                answer += 1

        return answer
```

#### 关键点

第一扇窗口在进入滑动循环之前就已经建立，因此也要单独判断一次。后面的循环只会处理第二扇及之后的窗口，如果把判断全部写在循环内，就会漏掉第一扇窗口。

---

### 4. 2090. 半径为 k 的子数组平均值

题目：[2090. 半径为 k 的子数组平均值](https://leetcode.cn/problems/k-radius-subarray-averages/)

#### 题目要求

对于每个中心下标 `i`，计算下面这段连续子数组的平均值：

```python
nums[i - k : i + k + 1]
```

如果中心左右没有足够的元素，该位置的答案就是 `-1`。

#### 思考过程

这题给出的不是窗口长度，而是半径。一个合法窗口包括中心元素、左边的 `k` 个元素和右边的 `k` 个元素，因此窗口的实际长度为：

```python
size = 2 * k + 1
```

并不是每个下标都能作为中心。中心 `i` 的左边和右边都必须有足够的元素，也就是：

```python
i - k >= 0
i + k < len(nums)
```

整理后，合法中心的范围为：

```python
k <= i < len(nums) - k
```

第一扇窗口是 `nums[0:size]`，对应的中心下标正好是 `k`。由于不合法的位置都应该保留为 `-1`，可以先将整个答案数组初始化为 `-1`，再计算并填写合法中心：

```python
answer = [-1] * len(nums)
```

如果 `size > len(nums)`，说明连一扇完整窗口都无法形成，可以直接返回这个全为 `-1` 的答案数组。

#### 代码

```python
class Solution:
    def getAverages(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        size = 2 * k + 1
        answer = [-1] * n

        if size > n:
            return answer

        window_sum = sum(nums[:size])
        answer[k] = window_sum // size

        for center in range(k + 1, n - k):
            left_out = center - k - 1
            right_in = center + k

            window_sum -= nums[left_out]
            window_sum += nums[right_in]

            answer[center] = window_sum // size

        return answer
```

#### 下标关系

这里循环使用的是中心下标，而不是新进入元素的下标。中心从 `center - 1` 移到 `center` 时，旧窗口最左边的元素离开，新窗口最右边的元素进入：

```python
left_out = center - k - 1  # 旧窗口最左边的元素
right_in = center + k      # 新窗口最右边的元素
```

虽然下标写法与前三题不同，但窗口的更新方式没有变化：

```text
减去左边离开的元素，加上右边进入的元素。
```

---

### 5. 2379. 得到 K 个黑块的最少涂色次数

题目：[2379. 得到 K 个黑块的最少涂色次数](https://leetcode.cn/problems/minimum-recolors-to-get-k-consecutive-black-blocks/)

#### 题目要求

找出一个长度为 `k` 的连续子串，把其中的白块 `W` 涂成黑块 `B`，使这一段全部变成黑块。求最少需要涂色多少次。

#### 思考过程

最终只需要出现一段连续的 `k` 个黑块，因此可以依次检查每个长度为 `k` 的窗口。对于某一扇窗口，黑块已经符合要求，只有白块需要重新涂色，所以：

```text
窗口中有几个 W，就需要涂色几次。
```

原问题就转换成了：

```text
在所有长度为 k 的窗口中，寻找 W 数量最少的窗口。
```

窗口中只需维护白块数量 `white_count`。窗口右移时，如果离开的字符是 `W`，数量减一；如果进入的字符是 `W`，数量加一。所有窗口中最小的 `white_count` 就是答案。

```text
窗口状态：当前窗口中的白块数量
答案：所有窗口中最少的白块数量
```

#### 代码

```python
class Solution:
    def minimumRecolors(self, blocks: str, k: int) -> int:
        white_count = blocks[:k].count('W')
        answer = white_count

        for right in range(k, len(blocks)):
            left = right - k

            if blocks[left] == 'W':
                white_count -= 1
            if blocks[right] == 'W':
                white_count += 1

            answer = min(answer, white_count)

        return answer
```

#### 与 1456 的联系

两题的窗口更新完全相同，都是统计窗口中某类字符的数量。区别只在于答案的更新方向：

```text
1456：统计元音数量，求最大值
2379：统计白块数量，求最小值
```

---

### 6. 2841. 几乎唯一子数组的最大和

题目：[2841. 几乎唯一子数组的最大和](https://leetcode.cn/problems/maximum-sum-of-almost-unique-subarray/)

#### 题目要求

在所有长度为 `k` 的连续子数组中，找出至少包含 `m` 种不同数字的窗口，并返回这些合法窗口的最大元素和。如果不存在合法窗口，返回 `0`。

这里的“至少有 `m` 个互不相同的元素”是指窗口中至少有 `m` **种**数字，并不是要求每个数字都只出现一次。例如 `[7, 3, 1, 7]` 中有 `7、3、1` 三种数字，因此当 `m = 3` 时，这个窗口是合法的。

#### 思考过程

窗口长度固定为 `k`，但每扇窗口需要同时满足两个要求：一方面要知道窗口的元素和，另一方面要判断不同数字的种数是否不少于 `m`。因此需要同时维护两个状态：

```python
window_sum = 当前窗口的元素和
freq = 当前窗口中每个数字的出现次数
```

只要及时删除出现次数已经变成 `0` 的数字，`freq` 中键的数量就是当前窗口中不同数字的种数：

```python
len(freq)
```

窗口满足下面的条件时，才用 `window_sum` 更新最大值：

```python
len(freq) >= m
```

#### 频率字典的必要性

集合只能说明某个数字是否存在，却无法记录它出现了几次。假设当前窗口是：

```python
[1, 1, 2]
```

当一个 `1` 离开窗口时，窗口里仍然还有另一个 `1`。如果直接从集合中删除 `1`，不同数字的种数就会计算错误。

频率字典可以区分“离开一个”和“已经全部离开”。左侧数字离开时先将次数减一，只有次数变成 `0` 才删除对应的键：

```python
freq[left_num] -= 1

if freq[left_num] == 0:
    del freq[left_num]
```

#### 代码

```python
class Solution:
    def maxSum(self, nums: List[int], m: int, k: int) -> int:
        freq = {}
        window_sum = 0

        # 建立第一扇窗口
        for i in range(k):
            num = nums[i]
            window_sum += num
            freq[num] = freq.get(num, 0) + 1

        answer = 0

        if len(freq) >= m:
            answer = window_sum

        # 窗口向右滑动
        for right in range(k, len(nums)):
            left = right - k
            left_num = nums[left]
            right_num = nums[right]

            # 左边数字离开窗口
            window_sum -= left_num
            freq[left_num] -= 1

            if freq[left_num] == 0:
                del freq[left_num]

            # 右边数字进入窗口
            window_sum += right_num
            freq[right_num] = freq.get(right_num, 0) + 1

            if len(freq) >= m:
                answer = max(answer, window_sum)

        return answer
```

#### 关键点

前面的题通常只维护窗口和或某类元素的数量，这题开始同时维护多个窗口状态：

```text
窗口和 + 窗口内每个数字的出现次数
```

当题目既要求窗口和，又限制不同元素的种数时，可以用 `window_sum` 维护数值，用频率字典维护元素种类。`len(freq)` 能否表示真实种数，取决于是否及时删除频率为 `0` 的键。

---

### 7. 1423. 可获得的最大点数

题目：[1423. 可获得的最大点数](https://leetcode.cn/problems/maximum-points-you-can-obtain-from-cards/)

#### 题目要求

每次只能从数组最左边或最右边拿走一张牌，必须拿走 `k` 张，求能够拿到的最大点数。

#### 思考过程

直接枚举每一步从左边拿还是从右边拿不容易处理，但可以观察没有被拿走的牌。假设一共有 `n` 张牌，拿走 `k` 张后会剩下：

```python
size = n - k
```

由于牌只能从左右两端拿走，最后没有被拿走的 `n-k` 张牌一定连续地留在中间。因此可以进行下面的转换：

```text
从两端拿走 k 张牌
等价于
在中间留下长度为 n-k 的连续子数组
```

所有牌的总点数固定，并且：

```python
拿走的点数 = 所有牌的总和 - 剩下的点数
```

因此，拿走的点数最大，等价于剩下的点数最小。原问题最终转换为：

```text
寻找长度为 n-k、元素和最小的连续子数组。
```

#### 代码

```python
class Solution:
    def maxScore(self, cardPoints: List[int], k: int) -> int:
        n = len(cardPoints)
        total = sum(cardPoints)

        # 中间留下的窗口长度
        size = n - k

        # 所有牌都要拿走
        if size == 0:
            return total

        window_sum = sum(cardPoints[:size])
        min_sum = window_sum

        for right in range(size, n):
            left = right - size

            window_sum -= cardPoints[left]
            window_sum += cardPoints[right]

            min_sum = min(min_sum, window_sum)

        return total - min_sum
```

#### 示例

```python
cardPoints = [1, 2, 3, 4, 5, 6, 1]
k = 3
```

全部牌的总和为 `22`，留下的窗口长度为：

```python
size = 7 - 3 = 4
```

所有长度为 `4` 的窗口和是：

```text
[1, 2, 3, 4] → 10
[2, 3, 4, 5] → 14
[3, 4, 5, 6] → 18
[4, 5, 6, 1] → 16
```

最小的剩余点数是 `10`，所以最大可得点数是：

```python
22 - 10 = 12
```

#### 边界情况

当 `k == n` 时，所有牌都要拿走，剩余窗口长度为 `0`。这种情况应该直接返回所有牌的总和。

#### 关键点

这题的窗口不是“拿走的牌”，而是“没有拿走的牌”。当题目要求从两端选择元素时，可以尝试观察中间剩下的部分是否连续，再利用总和将最大化问题转换成最小化问题：

```text
两端拿走 k 张的最大和
→ 中间留下 n-k 张的最小和
→ 长度为 n-k 的定长滑动窗口
```

---

### 8. 1052. 爱生气的书店老板

题目：[1052. 爱生气的书店老板](https://leetcode.cn/problems/grumpy-bookstore-owner/)

#### 思考过程

先考虑完全不使用秘密技巧的情况。所有 `grumpy[i] == 0` 的分钟里，老板本来就没有生气，这些顾客一定满意，而且不会受到技巧使用位置的影响。先将这部分顾客相加，记作固定的基础满意人数 `base`。

```python
base = 0

for i in range(len(customers)):
    if grumpy[i] == 0:
        base += customers[i]
```

计算完 `base` 后，剩下的问题是确定秘密技巧的使用位置，使额外变满意的顾客尽可能多。

秘密技巧覆盖一个长度固定为 `minutes` 的连续区间。在这个区间里，只有 `grumpy[i] == 1` 的顾客会从不满意变为满意；`grumpy[i] == 0` 的顾客已经计入 `base`，不属于额外收益。

因此可以用长度为 `minutes` 的滑动窗口，统计每个窗口中原本不满意的顾客数量。窗口中维护的 `extra` 表示当前使用技巧能够额外挽回的顾客，`max_extra` 记录所有窗口中的最大值。

最终答案由固定部分和可优化部分组成：

```text
原本就满意的顾客 base + 最多能够挽回的顾客 max_extra
```

#### 代码

```python
class Solution:
    def maxSatisfied(
        self,
        customers: List[int],
        grumpy: List[int],
        minutes: int
    ) -> int:
        n = len(customers)

        # 原本就满意的顾客
        base = 0

        for i in range(n):
            if grumpy[i] == 0:
                base += customers[i]

        # 第一扇窗口能够额外挽回的顾客
        extra = 0

        for i in range(minutes):
            if grumpy[i] == 1:
                extra += customers[i]

        max_extra = extra

        # 窗口向右滑动
        for right in range(minutes, n):
            left = right - minutes

            if grumpy[left] == 1:
                extra -= customers[left]

            if grumpy[right] == 1:
                extra += customers[right]

            max_extra = max(max_extra, extra)

        return base + max_extra
```

#### 示例

```python
customers = [1, 0, 1, 2, 1, 1, 7, 5]
grumpy    = [0, 1, 0, 1, 0, 1, 0, 1]
minutes = 3
```

先统计 `grumpy` 中为 `0` 的位置，对应的顾客数量是 `1、1、1、7`：

```python
base = 1 + 1 + 1 + 7 = 10
```

接着让长度为 `3` 的窗口从左向右移动。窗口内只统计 `grumpy[i] == 1` 的位置，因为只有这些顾客能被技巧挽回。

每个窗口能够额外挽回的顾客数分别是：

```text
第 0～2 分钟：0
第 1～3 分钟：2
第 2～4 分钟：2
第 3～5 分钟：3
第 4～6 分钟：1
第 5～7 分钟：6
```

最后一扇窗口覆盖第 `5、6、7` 分钟，其中老板原本生气的位置是第 `5` 和第 `7` 分钟，因此可以额外挽回：

```python
1 + 5 = 6
```

基础满意人数为 `10`，技巧最多额外挽回 `6` 人，所以最终答案为：

```python
10 + 6 = 16
```

#### 关键点

窗口不能统计其中的所有顾客，否则 `grumpy[i] == 0` 的顾客会被计算两遍：一次在 `base` 中，一次在窗口中。

所以建立窗口和滑动窗口时，都要加上这个判断：

```python
if grumpy[i] == 1:
    extra += customers[i]
```

`extra` 只表示窗口带来的额外收益，不是最终答案。所有窗口处理完成后，还需要加上固定的 `base`。

#### 总结

这道题没有直接要求寻找一个固定长度的子数组。关键在于将满意顾客拆成两部分：不受技巧影响的基础收益，以及通过固定长度操作获得的额外收益。类似题目可以尝试转换为：

```text
固定的基础收益 + 滑动窗口带来的最大额外收益
```

---

### 三、八道题横向对比

| 题目 | 窗口长度    | 窗口里维护什么               | 如何记录答案                    |
| ---- | ----------- | ---------------------------- | ------------------------------- |
| 1456 | `k`         | 元音数量                     | 最大值                          |
| 643  | `k`         | 元素和                       | 最大和，最后除以 `k`            |
| 1343 | `k`         | 元素和                       | 满足条件就计数                  |
| 2090 | `2 * k + 1` | 元素和                       | 写入对应中心位置                |
| 2379 | `k`         | 白块数量                     | 最小值                          |
| 2841 | `k`         | 元素和、数字频率             | 不同数字不少于 `m` 时更新最大和 |
| 1423 | `n - k`     | 剩余牌的元素和               | 总和减去最小窗口和              |
| 1052 | `minutes`   | 原本不满意、可被挽回的顾客数 | 基础满意人数加最大额外收益      |

它们共同的部分是：

```text
固定窗口长度
→ 先计算第一扇窗口
→ 减去左边离开的元素
→ 加上右边进入的元素
→ 按题目要求记录答案
```

变化的主要是两件事：

1. 窗口里要维护什么信息，例如和、数量或者频率字典；
2. 题目要最大值、最小值、数量，还是每个位置的结果。

---

## 栈

### 一、单调栈的核心套路

普通栈强调“后进先出”，单调栈则在这个基础上，额外要求栈中的元素始终保持单调递增或单调递减。

它最擅长解决的不是一般的入栈、出栈问题，而是下面这一类“最近关系”问题：

```text
对每一个元素，寻找它左边或右边第一个比它大（或比它小）的元素。
```

常见的题目描述包括：

- 下一个更大元素；
- 下一个更小元素；
- 左边第一个更大或更小的元素；
- 还要等待几天、相隔多远；
- 某个元素能向左右延伸到哪里。

如果题目同时出现“每个元素”“左边或右边”“第一个更大或更小”，就应该优先想到单调栈。

#### 单调栈到底在保存什么

单调栈里通常保存的是**还没有找到答案的元素**。

遍历到一个新元素时，新元素会尝试解决栈顶元素的问题：

```text
新元素满足栈顶等待的条件
→ 栈顶找到了答案
→ 弹出栈顶并记录答案
→ 继续检查新的栈顶
```

一次可能连续解决多个旧元素，所以这里通常使用 `while`，而不是 `if`。

#### 为什么经常保存下标

如果题目只问“下一个更大元素的值”，栈中可以根据需要保存值。

但只要题目要求下面这些信息，就应该保存下标：

- 两个元素之间的距离；
- 答案应该写回哪个位置；
- 同时需要得到元素的值和位置。

保存下标以后，既可以通过 `nums[stack[-1]]` 取得值，也可以通过下标相减计算距离，因此信息更完整。

#### 四类问题的基本方向

| 要寻找的目标       | 常用遍历方向         | 栈中等待的元素       | 新元素何时弹栈         |
| ------------------ | -------------------- | -------------------- | ---------------------- |
| 右边第一个更大元素 | 从左往右             | 还没遇到更大值的下标 | `current > stack_top`  |
| 右边第一个更小元素 | 从左往右             | 还没遇到更小值的下标 | `current < stack_top`  |
| 左边第一个更大元素 | 从右往左，或正向维护 | 取决于答案定义       | 遇到能解决栈顶的更大值 |
| 左边第一个更小元素 | 从右往左，或正向维护 | 取决于答案定义       | 遇到能解决栈顶的更小值 |

不要只死记“更大用递减栈、更小用递增栈”。更稳妥的思考方式是：

```text
1. 栈里是谁在等待答案？
2. 它在等待什么条件？
3. 当前元素能不能解决栈顶？
4. 如果能，答案应该记录在哪里？
```

---

### 二、739. 每日温度

题目：[739. 每日温度](https://leetcode.cn/problems/daily-temperatures/)

#### 题目要求

对于第 `i` 天，找出它右边第一个温度严格高于 `temperatures[i]` 的日期，并返回需要等待的天数。如果之后没有更高温度，答案就是 `0`。

例如：

```python
temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
answer       = [ 1,  1,  4,  2,  1,  1,  0,  0]
```

这句话中的三个信号非常关键：

```text
对每一天
右边
第一个更高温度
```

因此，这是一道典型的“寻找右边第一个更大元素”的单调栈题。

#### 暴力解法为什么慢

最直接的做法是从每一天出发，继续向右寻找第一个更热的日期。

```python
for i in range(n):
    for j in range(i + 1, n):
        if temperatures[j] > temperatures[i]:
            answer[i] = j - i
            break
```

如果温度一直下降，内层循环会反复扫描后面的元素，最坏时间复杂度是 `O(n²)`。

单调栈的优化点是：不让每个旧元素主动向后搜索，而是在新元素到来时，一次性解决所有能被它解决的旧元素。

#### 栈中保存什么

栈中保存尚未找到更高温度的日期下标：

```python
stack = []  # 尚未找到更高温度的日期下标
```

这里不能只保存温度，因为答案要求的是等待天数：

```python
等待天数 = 当前日期下标 - 旧日期下标
```

保存下标后，可以同时取得温度和距离：

```python
previous_index = stack[-1]
previous_temperature = temperatures[previous_index]
distance = current_index - previous_index
```

#### 栈内不变量

从栈底到栈顶，对应的温度保持单调不升，也可以称为单调递减栈（这里允许相等）：

```text
temperatures[stack[0]] >= temperatures[stack[1]] >= ... >= temperatures[stack[-1]]
```

原因是：当新温度严格高于栈顶温度时，栈顶已经找到答案，会立刻被弹出；只有无法被当前温度解决的日期才会继续留在栈里。

#### 标准处理流程

遍历到第 `i` 天时：

1. 如果栈不为空，并且当前温度比栈顶日期的温度高，说明第 `i` 天就是栈顶日期右边第一个更热的日期；
2. 弹出栈顶下标 `previous_index`；
3. 记录 `answer[previous_index] = i - previous_index`；
4. 继续比较新的栈顶，因为当前温度可能同时解决多个日期；
5. 当前日期处理完以后，将下标 `i` 入栈，等待未来更高的温度。

对应的核心代码只有两部分：

```python
while stack and temperatures[i] > temperatures[stack[-1]]:
    previous_index = stack.pop()
    answer[previous_index] = i - previous_index

stack.append(i)
```

#### 示例推演

以开头的 `[73, 74, 75, 71, 69, 72, 76, 73]` 为例：

| 当前日期 `i` | 当前温度 | 发生的操作                 | 栈中剩余下标 | 已确定的答案                     |
| ------------ | -------: | -------------------------- | ------------ | -------------------------------- |
| 0            |       73 | 无人可比较，`0` 入栈       | `[0]`        | 暂无                             |
| 1            |       74 | `74 > 73`，弹出 `0`        | `[1]`        | `answer[0] = 1`                  |
| 2            |       75 | `75 > 74`，弹出 `1`        | `[2]`        | `answer[1] = 1`                  |
| 3            |       71 | `71` 不高于 `75`，直接入栈 | `[2, 3]`     | 暂无新增                         |
| 4            |       69 | `69` 不高于 `71`，直接入栈 | `[2, 3, 4]`  | 暂无新增                         |
| 5            |       72 | 依次弹出 `4` 和 `3`        | `[2, 5]`     | `answer[4] = 1`，`answer[3] = 2` |
| 6            |       76 | 依次弹出 `5` 和 `2`        | `[6]`        | `answer[5] = 1`，`answer[2] = 4` |
| 7            |       73 | `73` 不高于 `76`，直接入栈 | `[6, 7]`     | 暂无新增                         |

遍历结束后，栈里剩下的下标 `6、7` 都没有在右边遇到更高温度，所以它们的答案保持初始值 `0`。

#### 代码

```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        n = len(temperatures)
        answer = [0] * n
        stack = []

        for current_index in range(n):
            while (
                stack
                and temperatures[current_index] > temperatures[stack[-1]]
            ):
                previous_index = stack.pop()
                answer[previous_index] = current_index - previous_index

            stack.append(current_index)

        return answer
```

也可以把当前温度保存到变量中，使判断更短：

```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        answer = [0] * len(temperatures)
        stack = []

        for i, current_temperature in enumerate(temperatures):
            while stack and current_temperature > temperatures[stack[-1]]:
                previous_index = stack.pop()
                answer[previous_index] = i - previous_index

            stack.append(i)

        return answer
```

#### 为什么这里必须使用 `while`

一个新温度可能比前面连续多个尚未解决的温度都高。

例如：

```python
temperatures = [75, 71, 69, 72]
```

遇到 `72` 时：

```text
72 > 69，解决温度 69 对应的日期
72 > 71，继续解决温度 71 对应的日期
72 < 75，停止弹栈
```

如果使用 `if`，只能弹出 `69`，就会漏掉 `71`。因此必须一直弹到当前温度不能解决栈顶为止。

#### 为什么条件是 `>` 而不是 `>=`

题目要求未来出现**更高**的温度，相等不算。

例如：

```python
temperatures = [73, 73, 74]
```

第二天的 `73` 不能作为第一天的答案。两个 `73` 都应该继续留在栈中，直到遇到 `74` 后再依次弹出：

```python
answer = [2, 1, 0]
```

所以弹栈条件必须是：

```python
current_temperature > temperatures[stack[-1]]
```

#### 为什么弹栈时遇到的一定是“第一个”更高温度

栈中下标一直没有被弹出，说明从它入栈以后到当前日期之前，没有出现过更高温度；否则它早就已经被弹出了。

当当前温度第一次满足条件时，当前日期自然就是它右边第一个更高温度的日期。这正是正序遍历和“满足条件立即弹栈”共同保证的。

#### 复杂度

- 时间复杂度：`O(n)`；
- 空间复杂度：`O(n)`。

代码虽然有一层 `for` 和一层 `while`，但并不是 `O(n²)`。每个下标只会入栈一次，并且最多出栈一次，所以全部弹栈操作加起来最多执行 `n` 次。

#### 截图中写法的评价与简化

截图中的代码思路是正确的：栈里保存下标，遇到更高温度时弹栈并填写距离。

原来的结构大致是：

```python
while stack != []:
    if current_temperature > temperatures[stack[-1]]:
        # 弹栈并记录答案
    else:
        break
```

可以直接把 `if` 的条件合并到 `while` 中：

```python
while stack and current_temperature > temperatures[stack[-1]]:
    previous_index = stack.pop()
    answer[previous_index] = i - previous_index
```

这样省去了 `else: break`，也更接近单调栈的通用模板。`stack` 本身就可以用来判断是否为空，不需要写成 `stack != []`。

#### 易错点

1. **栈里存了温度，而不是下标**

   这会导致无法计算等待天数，也不知道应该把答案写到哪个位置。

2. **只弹出一次，没有连续弹栈**

   一个较高温度可能解决多个旧日期，所以必须使用 `while`。

3. **把严格更高写成大于等于**

   相同温度不符合题意，弹栈条件只能使用 `>`。

4. **把答案写在当前下标**

   当前日期是来帮助旧日期确定答案的，答案应该写回刚弹出的下标：

   ```python
   previous_index = stack.pop()
   answer[previous_index] = current_index - previous_index
   ```

5. **遍历结束后再次处理栈中元素**

   剩余元素的右边不存在更高温度。因为答案数组已经初始化为全 `0`，无需额外处理。

6. **看到双层循环就误判成 `O(n²)`**

   判断复杂度时要看每个元素被操作多少次。本题每个下标最多入栈、出栈各一次，所以总时间仍是 `O(n)`。

#### 本题模板

这类“寻找右边第一个更大元素，并计算距离”的题可以直接套用下面的骨架：

```python
answer = [0] * len(nums)
stack = []  # 保存还没有找到答案的下标

for i in range(len(nums)):
    while stack and nums[i] > nums[stack[-1]]:
        previous_index = stack.pop()
        answer[previous_index] = i - previous_index

    stack.append(i)
```

真正需要根据题意修改的通常只有两处：

```text
弹栈条件：找更大还是找更小，严格还是允许相等
记录内容：记录元素值、下标，还是下标之间的距离
```

#### 一句话总结

```text
栈中保存还没等到更高温度的日期；
新温度到来时，连续解决所有比它低的栈顶日期；
弹出谁，就把距离写到谁的答案中；
最后再把当前日期入栈，等待未来处理。
```

---

## 二叉树

本节对应课程目录中的第 25～30 节：144、104、236、102、98、105。以下根据题目整理核心思路，便于课后复习；课程截图仅包含目录，不代表老师逐句讲解的记录。

### 一、先理解二叉树和递归

#### 一个节点里有什么

二叉树的每个节点最多有两个孩子。Python 中通常用下面的结构表示，力扣题目已经提供 `TreeNode`，提交时一般不需要重新定义：

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

`root` 是一个节点对象，`root.val` 是节点的值，`root.left` 是左孩子节点，不是左孩子的数值。`None` 表示这里没有节点，因此必须先判断节点是否为空，再访问它的属性。

```text
        3
       / \
      9  20
        /  \
       15   7
```

以 `20` 为根的部分本身也是一棵二叉树。这就是递归能够成立的原因：**整棵树的问题，可以交给左右两棵更小的树去解决。**

#### 写递归之前先回答三个问题

1. **这个函数接收什么、返回什么？** 例如 `depth(node)` 返回以 `node` 为根的子树的最大深度。
2. **什么时候可以直接给出答案？** 例如空树的深度是 `0`。
3. **知道左右子树的答案后，当前节点怎么得到自己的答案？** 例如左右深度取最大值，再加上当前节点这一层。

不要一开始就在脑中展开整棵树的所有调用。先假设左右子树已经正确算完，想清楚当前这一层如何使用结果；再用一棵小树检查终止条件。

```text
递归向下：把问题交给更小的子树
递归返回：把子树的结果交回上一层
```

每次调用都有自己的局部变量。左子树执行完 `return`，会回到调用它的位置继续运行，并不会直接结束最外层函数。递归背后是调用栈，树越高，同时等待子树结果的调用就越多。

#### 前序、中序、后序到底是什么意思

名字中的“前、中、后”，指的是**当前根节点的处理位置**。左右子树的顺序始终是先左后右。

| 遍历方式 | 顺序                   | 上面这棵树的结果          |
| -------- | ---------------------- | ------------------------- |
| 前序     | 根 → 左 → 右           | `[3, 9, 20, 15, 7]`       |
| 中序     | 左 → 根 → 右           | `[9, 3, 15, 20, 7]`       |
| 后序     | 左 → 右 → 根           | `[9, 15, 7, 20, 3]`       |
| 层序     | 从上到下，每层从左到右 | `[[3], [9, 20], [15, 7]]` |

前三种属于深度优先遍历（DFS），会沿一条分支深入；层序遍历通常用广度优先遍历（BFS），先处理完同一层。

下面六题会用到两类常见递归：一类通过 `answer.append(...)` 收集结果；另一类通过 `return` 把子树的信息交给父节点。要分清当前函数采用的是哪一种。

---

### 二、144. 二叉树的前序遍历

题目：[144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/)

#### 题目要求与思考过程

按“根 → 左 → 右”的顺序返回所有节点的值。这里的“根”会随着递归变化：到达节点 `20` 时，`20` 就是当前子树的根。

定义 `dfs(node)`：把以 `node` 为根的子树，按前序顺序追加到 `answer` 中。它不返回列表，结果保存在外层的 `answer` 中。

```text
遇到空节点：什么都不做，直接返回
遇到非空节点：先记录自己，再遍历左子树，最后遍历右子树
```

#### 代码

```python
class Solution:
    def preorderTraversal(self, root):
        answer = []

        def dfs(node):
            if node is None:
                return

            answer.append(node.val)
            dfs(node.left)
            dfs(node.right)

        dfs(root)
        return answer
```

#### 示例推演

以上面的树为例，先记录 `3`，进入左子树记录 `9`。`9` 的两个孩子都为空，分别返回后，执行回到 `3` 的那次调用，继续进入右子树记录 `20`，再记录 `15` 和 `7`。

这里最容易困惑的是“走到 `9` 以后怎么回到 `3`”：程序在调用 `dfs(9)` 时会记住暂停的位置，子调用结束后，自然从下一行 `dfs(20)` 继续执行。

#### 关键点与复杂度

- 前序的关键是 `answer.append(node.val)` 放在两次递归之前；移到中间就是中序，移到最后就是后序。
- 空树返回空列表 `[]`，单节点树返回 `[root.val]`。
- 时间 `O(n)`；递归栈空间 `O(h)`，输出列表另占 `O(n)`。`n` 是节点数，`h` 是树高。

---

### 三、104. 二叉树的最大深度

题目：[104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

#### 题目要求

求从根节点到最远叶子节点的路径上的**节点数量**。空树深度是 `0`，只有根节点时深度是 `1`。

#### 思考过程

定义 `depth(node)`：返回以 `node` 为根的子树的最大深度。

假设左子树已经告诉我它有多深，右子树也告诉我它有多深。经过当前节点的最长向下路径，只能选择其中更深的一边，再加上当前节点这一层：

```text
当前子树的最大深度 = max(左子树深度, 右子树深度) + 1
```

这里不能将左右深度相加，因为一条从根向下的路径不会同时走进左右两棵子树。

#### 代码

```python
class Solution:
    def maxDepth(self, root):
        def depth(node):
            if node is None:
                return 0

            left_depth = depth(node.left)
            right_depth = depth(node.right)
            return max(left_depth, right_depth) + 1

        return depth(root)
```

#### 示例推演

```text
depth(9)  = max(0, 0) + 1 = 1
depth(15) = max(0, 0) + 1 = 1
depth(7)  = max(0, 0) + 1 = 1
depth(20) = max(1, 1) + 1 = 2
depth(3)  = max(1, 2) + 1 = 3
```

调用从 `3` 开始向下展开，但深度从下面算好后向上传。这是“先得到左右子树结果，再处理当前节点”的后序思路。

#### 易错点与复杂度

- 返回值是“当前子树有多高”，不是“当前节点距离整棵树的根有多远”。前者向上返回，后者通常要作为参数向下传。
- `+ 1` 表示当前节点本身，不能漏掉。
- 时间 `O(n)`，递归栈空间 `O(h)`；链状树的 `h = n`，不能一律写成 `O(log n)`。

---

### 四、236. 二叉树的最近公共祖先

题目：[236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

#### 题目要求

给定两个节点 `p` 和 `q`，找到同时是它们祖先、并且位置最深的节点。**一个节点可以是它自己的祖先。** 原题保证 `p`、`q` 不同，并且都存在于树中。

```text
        3
       / \
      5   1
     / \
    6   2
       / \
      7   4
```

- `p = 6, q = 4`：最近公共祖先是 `5`。
- `p = 5, q = 4`：最近公共祖先仍然是 `5`。
- `p = 6, q = 1`：最近公共祖先是 `3`。

#### 最重要的是理解返回值

定义 `dfs(node)` 返回当前子树提供的查找结果：

| 当前子树的情况      | 返回什么               |
| ------------------- | ---------------------- |
| `p`、`q` 都不在里面 | `None`                 |
| 只包含一个目标      | 那个目标节点           |
| 同时包含两个目标    | 它们的最近公共祖先节点 |

因此，一个非空返回值不一定说明“两个目标都找到了”，也可能只是“找到其中一个”。父节点要结合左右两边的结果进行判断。

#### 思考过程

遇到空节点返回 `None`。遇到 `p` 或 `q`，直接返回当前节点：如果另一个目标在它下面，当前节点就是答案；如果另一个在外面，就让上层继续合并查找结果。

其他情况下，分别搜索左右子树：

```text
左边非空，右边非空 → 两个目标分居两侧，返回当前节点
只有左边非空       → 把左边的结果继续向上传
只有右边非空       → 把右边的结果继续向上传
两边都为空         → 返回 None
```

#### 代码

```python
class Solution:
    def lowestCommonAncestor(self, root, p, q):
        def dfs(node):
            if node is None or node is p or node is q:
                return node

            left = dfs(node.left)
            right = dfs(node.right)

            if left is not None and right is not None:
                return node
            if left is not None:
                return left
            return right

        return dfs(root)
```

#### 示例推演：找 `6` 和 `4`

```text
节点 6：命中 p，返回节点 6
节点 7：没有目标，返回 None
节点 4：命中 q，返回节点 4
节点 2：左边 None，右边 4，返回节点 4
节点 5：左边 6，右边 4，两边都非空，返回节点 5
节点 1：没有目标，返回 None
节点 3：左边 5，右边 None，继续返回节点 5
```

为什么最后不会把答案改成 `3`？因为在 `3` 处只有左边有查找结果，代码只是把 `5` 原样传上去。

#### 易错点与复杂度

- 返回的是**节点对象**，不是节点值。代码用 `is` 判断是不是指定的那个节点。
- 只有一侧非空时，应当返回那一侧的结果，不能返回当前节点。
- 本题是普通二叉树，不能按值大小决定向左还是向右搜索。
- 该写法依赖原题中两个目标都存在的保证。如果题目允许目标缺失，需要额外确认两个节点都被找到。
- 时间 `O(n)`，递归栈空间 `O(h)`。

---

### 五、102. 二叉树的层序遍历

题目：[102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

#### 思考过程

题目要求按层返回结果，因此使用先进先出的队列：先进入队列的节点先处理。处理当前节点时，将它的左、右孩子依次加入队尾，它们就会排在当前层剩余节点之后。

关键问题是：**队列里会逐渐加入下一层的节点，怎样知道当前层什么时候结束？**

答案是在每层开始时，先保存队列的长度 `level_size`。此时队列里恰好是当前层的全部节点，这轮只取出这么多个；新加入的孩子留到下一轮处理。

#### 代码

```python
from collections import deque


class Solution:
    def levelOrder(self, root):
        if root is None:
            return []

        queue = deque([root])
        answer = []

        while queue:
            level_size = len(queue)
            level = []

            for _ in range(level_size):
                node = queue.popleft()
                level.append(node.val)

                if node.left is not None:
                    queue.append(node.left)
                if node.right is not None:
                    queue.append(node.right)

            answer.append(level)

        return answer
```

#### 示例推演

仍使用根为 `3`、左孩子为 `9`、右孩子为 `20` 的树：

| 轮次 | 本轮开始时的队列（展示节点值） | 固定处理数量 | 本层结果  | 本轮结束时的队列 |
| ---- | ------------------------------ | ------------ | --------- | ---------------- |
| 1    | `[3]`                          | 1            | `[3]`     | `[9, 20]`        |
| 2    | `[9, 20]`                      | 2            | `[9, 20]` | `[15, 7]`        |
| 3    | `[15, 7]`                      | 2            | `[15, 7]` | `[]`             |

#### 易错点与复杂度

- 队列中保存节点，结果中保存值；如果队列只存值，就无法继续访问孩子。
- `level = []` 必须放在外层 `while` 内，每一层重新创建。
- 用 `deque.popleft()` 从队头取出节点。列表的 `pop(0)` 会搬移后面的元素，单次可能需要线性时间。
- 时间 `O(n)`；队列辅助空间 `O(w)`，`w` 为最大层宽，最坏 `O(n)`；输出另占 `O(n)`。

---

### 六、98. 验证二叉搜索树

题目：[98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

#### 先区分二叉树和二叉搜索树

普通二叉树只限制每个节点最多有两个孩子。二叉搜索树（BST）还要求，对每个节点而言：

```text
左子树的所有节点值 < 当前节点值 < 右子树的所有节点值
```

这里是“所有节点”，而不只是直接的左右孩子；并且本题要求严格大小关系，重复值不合法。

#### 为什么只比较父子节点会出错

```text
        5
       / \
      1   7
         / \
        4   8
```

每条直接父子关系都满足“左小右大”，但 `4` 在 `5` 的右子树里，必须大于 `5`，因此整棵树不合法。

#### 思考过程：把祖先留下的限制传下去

定义 `dfs(node, lower, upper)`：判断当前子树是否合法，其中当前节点必须满足 `lower < node.val < upper`。

从根开始，它的范围是负无穷到正无穷。向下走时：

```text
进入左子树：保留原下界，把上界缩小为当前值
进入右子树：把下界提高为当前值，保留原上界
```

保留另一侧边界很重要，因为它可能来自更上面的祖先。

#### 代码

```python
class Solution:
    def isValidBST(self, root):
        def dfs(node, lower, upper):
            if node is None:
                return True

            if not (lower < node.val < upper):
                return False

            return (
                dfs(node.left, lower, node.val)
                and dfs(node.right, node.val, upper)
            )

        return dfs(root, float('-inf'), float('inf'))
```

#### 示例推演

在上面的反例中：

```text
节点 5：允许范围 (-∞, +∞)，合法
节点 7：在 5 的右侧，允许范围 (5, +∞)，合法
节点 4：在 7 的左侧，但仍在 5 的右子树中
        允许范围 (5, 7)，4 不在其中，返回 False
```

#### 与中序遍历的联系

严格二叉搜索树的中序结果一定**严格递增**。也可以先中序遍历得到列表，再检查每个相邻元素是否满足 `previous < current`。反例的中序结果是 `[1, 5, 4, 7, 8]`，其中 `5 > 4`，因此不合法。

初学时先理解上下界写法：它直接表达了“每个节点都必须遵守祖先留下的约束”。

#### 易错点与复杂度

- 不能只比较 `node.left.val`、`node.val`、`node.right.val`。
- 不能使用 `<=`，相等的值不符合这道题的定义。
- 左子树或右子树只要有一个不合法，整棵树就不合法，因此用 `and`。
- 空子树没有违反限制，返回 `True`。
- 最坏时间 `O(n)`，递归栈空间 `O(h)`。

---

### 七、105. 从前序与中序遍历序列构造二叉树

题目：[105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

#### 题目要求

给出同一棵树的前序和中序遍历结果，恢复这棵树并返回根节点。原题保证输入合法、节点值不重复，两份数组包含相同的节点。

#### 两个数组分别提供什么信息

```text
前序：根 | 左子树 | 右子树  → 确定根是谁
中序：左子树 | 根 | 右子树  → 确定左右子树分别有哪些节点
```

前序第一个元素一定是根。在中序数组中找到这个根后，它左边就是左子树，右边就是右子树。随后对两棵子树重复这个过程。

#### 示例拆分

```text
前序：[3, 9, 20, 15, 7]
中序：[9, 3, 15, 20, 7]

第一步：前序第一个值是 3，所以根是 3。
第二步：在中序里找到 3。
        左子树中序：[9]，共 1 个节点
        右子树中序：[15, 20, 7]，共 3 个节点
第三步：前序去掉根后，接下来的 1 个节点属于左子树。
        左子树前序：[9]
        右子树前序：[20, 15, 7]
```

对右子树继续拆：根是 `20`；在右子树中序 `[15, 20, 7]` 中，左边是 `15`，右边是 `7`。这样就恢复了开头那棵树。

**前序不是从中间平均分成两段，而是按照中序确定的左子树节点数来分。**

#### 用下标表示区间

为了避免递归中反复复制数组，可以只传下标。定义：

```text
build(pre_left, in_left, in_right)

作用：构造中序区间 [in_left, in_right) 对应的子树
pre_left：这棵子树的根在前序数组中的下标
返回值：构造好的子树根节点
```

中序区间采用左闭右开形式，所以 `in_left == in_right` 表示空树。

假设当前根在中序中的下标是 `mid`：

```text
左子树节点数 = mid - in_left
左子树根的前序下标 = pre_left + 1
右子树根的前序下标 = pre_left + 1 + 左子树节点数

左子树中序范围：[in_left, mid)
右子树中序范围：[mid + 1, in_right)
```

右子树起点的 `+ 1` 是跳过当前根，后面的左子树节点数是跳过整个左子树。

#### 代码

```python
class Solution:
    def buildTree(self, preorder, inorder):
        # 值不重复，因此每个值可以对应唯一的中序下标
        position = {value: i for i, value in enumerate(inorder)}

        def build(pre_left, in_left, in_right):
            if in_left >= in_right:
                return None

            root_value = preorder[pre_left]
            mid = position[root_value]
            left_size = mid - in_left

            node = TreeNode(root_value)
            node.left = build(pre_left + 1, in_left, mid)
            node.right = build(
                pre_left + 1 + left_size, mid + 1, in_right
            )
            return node

        return build(0, 0, len(inorder))
```

#### 易错点与复杂度

- `mid` 是根在整个中序数组中的下标，左子树大小是 `mid - in_left`，不能直接写成 `mid`。
- 左右子树的中序区间都必须排除当前根，否则问题不会正确缩小。
- 必须把递归返回的节点接到 `node.left`、`node.right` 上，最后返回 `node`。
- 上面的代码先建字典，用下标查找并递归，时间 `O(n)`；辅助空间为字典 `O(n)` 加递归栈 `O(h)`，合计 `O(n)`；构造的树另占 `O(n)`。
- 如果每次用 `inorder.index(...)` 搜索，再用切片复制子数组，写法直观，但在链状树上最坏时间可达到 `O(n²)`。

---

### 八、六道题放在一起看

| 题目             | 核心问题               | 函数返回什么                        | 当前节点要做什么                         |
| ---------------- | ---------------------- | ----------------------------------- | ---------------------------------------- |
| 144 前序遍历     | 按什么顺序访问         | 内层 `dfs` 不返回结果，外层返回列表 | 先记录自己，再访问左右子树               |
| 104 最大深度     | 子树有多高             | 一个整数                            | 左右深度取最大值，再加一                 |
| 236 最近公共祖先 | 两个目标在哪里汇合     | 节点或 `None`                       | 两侧都有结果则返回自己，否则传递单侧结果 |
| 102 层序遍历     | 怎样一层一层处理       | 二维列表                            | 用队列，每轮处理固定数量的节点           |
| 98 验证 BST      | 是否符合所有祖先的限制 | `True` 或 `False`                   | 检查上下界，并收紧范围传给孩子           |
| 105 构造二叉树   | 根是谁，左右分别是谁   | 新构造的根节点或 `None`             | 用前序定根、中序分左右，再连接子树       |

这几题虽然都在写树，但递归传递的信息不同。拿到新题时，可以先在草稿上写出函数含义，再决定代码：

```text
1. 我传入的是一个节点，还是一个数组区间？
2. 我希望这个函数返回整数、真假、节点，还是只向列表追加数据？
3. 空节点或空区间应该怎么处理？
4. 左右子树已经给出结果后，当前这一层怎么完成任务？
```

#### 复习时先口头回答这几个问题

1. **144：为什么先记录根？** 因为题目要求前序，顺序是根、左、右。
2. **104：为什么取 `max` 而不是相加？** 一条向下的路径只能选择一侧。
3. **236：为什么一侧非空时返回子树结果？** 当前节点还没有提供新的汇合点，答案或目标线索应该继续向上传。
4. **102：为什么先保存队列长度？** 避免把新加入的下一层节点算进当前层。
5. **98：为什么需要上下界？** 节点还要遵守更远祖先的大小限制。
6. **105：为什么需要中序数组？** 它告诉我们根的左右分别有哪些节点，从而确定前序的拆分位置。

建议复习顺序是 `144 → 104 → 102 → 98 → 236 → 105`。先用三五个节点的小树手算，再遮住代码，根据函数定义重写；如果卡住，优先检查自己是否说清楚了“这一层到底返回什么”。


---

## 回溯

### 一、77. 组合：逐行理解回溯

题目：[77. 组合](https://leetcode.cn/problems/combinations/)

题目要求：从 `1～n` 中选出 `k` 个不同的数字，返回所有组合。例如 `n = 3、k = 2` 时，答案是：

```python
[[1, 2], [1, 3], [2, 3]]
```

组合不考虑顺序，所以 `[1, 2]` 和 `[2, 1]` 只算一种。

#### 完整代码

```python
class Solution:
    def combine(self, n: int, k: int) -> list[list[int]]:
        nums = [i for i in range(1, n + 1)]
        result = []

        def backtrack(path, start_index):
            if len(path) == k:
                result.append(path[:])
                return

            for i in range(start_index, len(nums)):
                path.append(nums[i])
                backtrack(path, i + 1)
                path.pop()

        backtrack([], 0)
        return result
```

#### 先用“拿牌”理解回溯

把 `1、2、3` 想成三张牌，要拿两张。可以这样尝试：

```text
第一张拿 1：
    第二张拿 2 → 记下 [1, 2]
    把 2 放回去
    第二张拿 3 → 记下 [1, 3]
    把 3 放回去
把第一张 1 放回去

第一张拿 2：
    第二张拿 3 → 记下 [2, 3]
    把 3 放回去
把第一张 2 放回去
```

回溯就是把“拿起来试试，试完放回去，再换一个”的过程写成代码：

```text
path   = 手里现在拿着哪些牌
result = 已经记下的完整组合
```

#### 从头执行代码

```python
nums = [i for i in range(1, n + 1)]
```

生成可选择的数字。当 `n = 3` 时：

```python
nums = [1, 2, 3]
# 下标： 0  1  2
```

```python
result = []
```

保存全部答案，开始时还没有组合。

```python
def backtrack(path, start_index):
```

定义一个搜索函数，可以读成：

> 我已经选好了 `path`，接下来从 `start_index` 开始继续选择，直到凑够 `k` 个数。

`path` 是已经做出的选择；`start_index` 是后续允许开始选择的下标。例如 `backtrack([1], 1)` 表示已经选了 `1`，接下来只能考虑 `nums[1]` 和 `nums[2]`，也就是 `2` 和 `3`。

定义函数时，函数体暂时不会执行；直到下面这句出现才真正开始搜索：

```python
backtrack([], 0)
```

它表示：目前一个数都没选，并且从 `nums` 的第 `0` 个下标开始考虑。

#### 选够了就保存

```python
if len(path) == k:
    result.append(path[:])
    return
```

当 `path` 的长度等于 `k`，当前组合完成。`path[:]` 会复制一份列表再保存，像是给当前组合拍一张照片。

如果直接写 `result.append(path)`，`result` 保存的是同一个、之后还会被 `pop()` 修改的列表，后面撤销选择时，已经保存的答案也会跟着变化。

`return` 只结束当前这一次递归调用，不会结束整个搜索。程序会回到调用它的上一层，继续执行上一层递归调用后面的代码。

#### `for` 循环负责尝试当前这一步的每个选择

```python
for i in range(start_index, len(nums)):
```

如果当前 `start_index = 1`，`i` 会依次取 `1、2`，也就是依次尝试数字 `2、3`。每一次循环都代表：当前这个位置选择一个不同的数字。

接下来的三行必须连起来看：

```python
path.append(nums[i])      # 选择当前数字
backtrack(path, i + 1)    # 探索这个选择下面的所有可能
path.pop()                # 探索结束，撤销当前选择
```

第一行把数字加入当前组合。第二行递归寻找后续所有搭配；递归没有结束前，不会执行第三行。第三行删除刚才加入的最后一个数字，使当前层恢复到选择之前的状态，然后循环才能尝试下一个数字。

#### 完整追踪 `n = 3、k = 2`

第一次调用：

```python
backtrack([], 0)
```

第 1 层的 `path` 是空列表，循环先令 `i = 0`，执行：

```python
path.append(nums[0])  # path = [1]
backtrack(path, 1)
```

进入第 2 层后，`path = [1]`，只能从下标 `1` 开始尝试：

```text
i = 1：加入 2，path = [1, 2]
```

再次调用后，`len(path) == k`，于是：

```python
result = [[1, 2]]
```

这一层返回到第 2 层，接着执行：

```python
path.pop()  # 删除 2，path = [1]
```

第 2 层循环继续：

```text
i = 2：加入 3，path = [1, 3]
```

保存后变成：

```python
result = [[1, 2], [1, 3]]
```

再次 `pop()` 后恢复为：

```python
path = [1]
```

第 2 层探索完毕，返回第 1 层。第 1 层接着执行自己的 `pop()`：

```python
path.pop()  # 删除 1，path = []
```

然后第 1 层循环尝试 `i = 1`：

```text
加入 2 → path = [2]
下一层只能从下标 2 开始
加入 3 → path = [2, 3]
保存 [2, 3]
```

最后尝试 `i = 2`：

```text
加入 3 → path = [3]
后面没有数字可以继续选择，长度仍然只有 1
无法凑够 k = 2，因此不保存
```

最终：

```python
result = [[1, 2], [1, 3], [2, 3]]
```

#### 为什么必须使用 `path.pop()`

递归调用使用的是同一个 `path` 列表。假设当前是 `[1]`，尝试加入 `2` 后变成 `[1, 2]`；如果递归返回后不 `pop()`，下一轮加入 `3` 就会变成错误的 `[1, 2, 3]`，而不是想要的 `[1, 3]`。

因此每次选择都必须遵循：

```text
选择 → 递归探索 → 撤销选择
```

每一层负责撤销自己加入的那个数字。子调用里的选择由子调用自己撤销，最终每层都能恢复到进入之前的状态。

#### 为什么传 `i + 1`

假设当前循环实际选择的是 `nums[i]`。下一层必须从它后面开始：

```python
backtrack(path, i + 1)
```

这样可以保证：

1. 同一个数字不会再次被选择，例如不会出现 `[2, 2]`；
2. 下标始终递增，不会生成 `[2, 1]` 这种顺序重复的组合。

不能写成 `start_index + 1`，因为当前循环的 `i` 可能已经走到了比 `start_index` 更大的位置。下一层应该跳过“这次实际选中的位置”，所以必须使用 `i + 1`。

#### 为什么 `i` 不会被递归调用弄乱

每次函数调用都有自己的一份局部变量：每层的 `i`、`start_index` 都互相独立。但多层调用共同使用同一个 `path` 列表，所以 `path` 的修改必须用 `append` 和 `pop` 成对恢复。

```text
i、start_index：每次调用各自拥有
path：多次调用共同修改同一个列表
result：所有调用共同向其中追加答案
```

#### 回溯代码的通用阅读方式

遇到类似代码，可以依次回答：

```text
1. path 表示当前已经做出的选择是什么？
2. 什么条件表示一个完整答案已经形成？
3. for 循环当前允许尝试哪些选择？
4. 递归调用后，为什么必须撤销当前选择？
5. 下一层从哪里开始，如何避免重复？
```

这道题的核心可以压缩成一句话：

```text
for 负责换着选择当前这一位；
递归负责继续选择下一位；
pop 负责从递归回来后撤销当前选择。
```
