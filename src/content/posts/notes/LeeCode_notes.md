---
title: LeeCode_notes
published: 2026-07-25T08:49:19.200Z
description: ''
updated: 2026-11-03
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
  - [四、定长滑动窗口模板](#四定长滑动窗口模板)
- [栈](#栈)
  - [一、单调栈的核心套路](#一单调栈的核心套路)
  - [二、739. 每日温度](#二739-每日温度)
  - [三、单调栈模板](#三单调栈模板)
- [二叉树](#二叉树)
  - [一、先理解二叉树和递归](#一先理解二叉树和递归)
  - [二、144. 二叉树的前序遍历](#二144-二叉树的前序遍历)
  - [三、104. 二叉树的最大深度](#三104-二叉树的最大深度)
  - [四、236. 二叉树的最近公共祖先](#四236-二叉树的最近公共祖先)
  - [五、102. 二叉树的层序遍历](#五102-二叉树的层序遍历)
  - [六、98. 验证二叉搜索树](#六98-验证二叉搜索树)
  - [七、105. 从前序与中序遍历序列构造二叉树](#七105-从前序与中序遍历序列构造二叉树)
  - [八、六道题放在一起看](#八六道题放在一起看)
  - [九、二叉树递归与遍历模板](#九二叉树递归与遍历模板)
- [回溯](#回溯)
  - [一、77. 组合：逐行理解回溯](#一77-组合逐行理解回溯)
  - [二、回溯的基本框架：状态、选择与恢复](#二回溯的基本框架状态选择与恢复)
  - [三、46. 全排列：通过 used 维护路径中的使用状态](#三46-全排列通过-used-维护路径中的使用状态)
  - [四、78. 子集：每个搜索节点都是答案](#四78-子集每个搜索节点都是答案)
  - [五、39. 组合总和：可以重复选，所以递归传 i](#五39-组合总和可以重复选所以递归传-i)
  - [六、40. 组合总和 II：同层去重，同路允许相等值](#六40-组合总和-ii同层去重同路允许相等值)
  - [七、131. 分割回文串：枚举下一刀切在哪里](#七131-分割回文串枚举下一刀切在哪里)
  - [八、79. 单词搜索：格子只在当前路径中占用](#八79-单词搜索格子只在当前路径中占用)
  - [九、回溯题型的统一框架与差异](#九回溯题型的统一框架与差异)
  - [十、回溯算法模板](#十回溯算法模板)
- [图论搜索](#图论搜索)
  - [一、网格图建模与搜索方法](#一网格图建模与搜索方法)
  - [二、200. 岛屿数量：发现一次新陆地，处理整块陆地](#二200-岛屿数量发现一次新陆地处理整块陆地)
  - [三、130. 被围绕的区域：从边界保护不能翻转的 O](#三130-被围绕的区域从边界保护不能翻转的-o)
  - [四、994. 腐烂的橘子：多源 BFS 同时扩散](#四994-腐烂的橘子多源-bfs-同时扩散)
  - [五、1091. 二进制矩阵中的最短路径：BFS 第一次到达就是最短](#五1091-二进制矩阵中的最短路径bfs-第一次到达就是最短)
  - [六、网格搜索的通用套路与题型对比](#六网格搜索的通用套路与题型对比)
  - [七、图与网格搜索模板](#七图与网格搜索模板)

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

| 题目 | 窗口长度 | 窗口里维护什么 | 如何记录答案 |
| --- | --- | --- | --- |
| 1456 | `k` | 元音数量 | 最大值 |
| 643 | `k` | 元素和 | 最大和，最后除以 `k` |
| 1343 | `k` | 元素和 | 满足条件就计数 |
| 2090 | `2 * k + 1` | 元素和 | 写入对应中心位置 |
| 2379 | `k` | 白块数量 | 最小值 |
| 2841 | `k` | 元素和、数字频率 | 不同数字不少于 `m` 时更新最大和 |
| 1423 | `n - k` | 剩余牌的元素和 | 总和减去最小窗口和 |
| 1052 | `minutes` | 原本不满意、可被挽回的顾客数 | 基础满意人数加最大额外收益 |

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

### 四、定长滑动窗口模板

#### 窗口和：移除旧贡献，加入新贡献

适用于长度固定的连续区间。下面返回所有长度为 k 的窗口和，要求 1 ≤ k ≤ 数组长度。求最大值、计数或填写指定位置时，只需改变记录答案的方式。

```python
def fixed_window_sums(nums, k):
    window_sum = sum(nums[:k])
    result = [window_sum]
    for right in range(k, len(nums)):
        window_sum -= nums[right - k]
        window_sum += nums[right]
        result.append(window_sum)
    return result
```

若仅求最大窗口和，可将 result 改为 best = window_sum，并在循环中执行 best = max(best, window_sum)，辅助空间可降为 O(1)。初始答案取第一扇窗口的值，避免全负数输入时出错。

#### 条件计数：把元素转换成贡献

元音、白块、原本不满意的顾客等，都可以表示为每个元素对窗口的贡献。contribution 是接收一个元素并返回数值的函数，以下模板返回最大窗口贡献。

```python
def max_window_contribution(items, k, contribution):
    current = sum(contribution(items[i]) for i in range(k))
    best = current
    for right in range(k, len(items)):
        current -= contribution(items[right - k])
        current += contribution(items[right])
        best = max(best, current)
    return best
```

元音计数的贡献可写为 lambda ch: int(ch in 'aeiou')；求最少白块时把 max 改为 min。贡献函数应只依赖当前元素，并在整个滑动过程中保持一致。

#### 频率字典：同时维护窗口和与种类数

```python
def max_sum_with_distinct(nums, k, min_distinct):
    freq = {}
    current = 0
    best = None
    for right, value in enumerate(nums):
        current += value
        freq[value] = freq.get(value, 0) + 1
        if right >= k:
            outgoing = nums[right - k]
            current -= outgoing
            freq[outgoing] -= 1
            if freq[outgoing] == 0:
                del freq[outgoing]
        if right >= k - 1 and len(freq) >= min_distinct:
            best = current if best is None else max(best, current)
    return best
```

这里 None 表示不存在合法窗口，调用方按题目要求转换成 0 或其他值。零频键必须删除，否则 len(freq) 不能代表当前种类数。该模板同样要求窗口长度合法。

半径题先转换窗口长度 k → 2k + 1；两端取牌先转换为中间留下长度 n - k 的窗口；固定收益题先分离基础收益，再用窗口处理额外收益。转换的是状态定义，滑动时的增减逻辑不变。


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

| 要寻找的目标 | 常用遍历方向 | 栈中等待的元素 | 新元素何时弹栈 |
| --- | --- | --- | --- |
| 右边第一个更大元素 | 从左往右 | 还没遇到更大值的下标 | `current > stack_top` |
| 右边第一个更小元素 | 从左往右 | 还没遇到更小值的下标 | `current < stack_top` |
| 左边第一个更大元素 | 从右往左，或正向维护 | 取决于答案定义 | 遇到能解决栈顶的更大值 |
| 左边第一个更小元素 | 从右往左，或正向维护 | 取决于答案定义 | 遇到能解决栈顶的更小值 |

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

| 当前日期 `i` | 当前温度 | 发生的操作 | 栈中剩余下标 | 已确定的答案 |
| --- | ---: | --- | --- | --- |
| 0 | 73 | 无人可比较，`0` 入栈 | `[0]` | 暂无 |
| 1 | 74 | `74 > 73`，弹出 `0` | `[1]` | `answer[0] = 1` |
| 2 | 75 | `75 > 74`，弹出 `1` | `[2]` | `answer[1] = 1` |
| 3 | 71 | `71` 不高于 `75`，直接入栈 | `[2, 3]` | 暂无新增 |
| 4 | 69 | `69` 不高于 `71`，直接入栈 | `[2, 3, 4]` | 暂无新增 |
| 5 | 72 | 依次弹出 `4` 和 `3` | `[2, 5]` | `answer[4] = 1`，`answer[3] = 2` |
| 6 | 76 | 依次弹出 `5` 和 `2` | `[6]` | `answer[5] = 1`，`answer[2] = 4` |
| 7 | 73 | `73` 不高于 `76`，直接入栈 | `[6, 7]` | 暂无新增 |

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

### 三、单调栈模板

#### 右侧第一个严格更大元素

栈保存尚未确定答案的下标。当前元素能够解决栈顶时，弹出栈顶并记录答案，直到无法继续解决为止。

```python
def next_greater_indices(nums):
    answer = [-1] * len(nums)
    stack = []
    for i, value in enumerate(nums):
        while stack and value > nums[stack[-1]]:
            previous = stack.pop()
            answer[previous] = i
        stack.append(i)
    return answer
```

答案为目标下标，-1 表示不存在。每日温度把记录语句改为 answer[previous] = i - previous，并把初值改为 0；求目标值则记录 nums[i]。

#### 左侧第一个严格更大元素

从左往右维护候选下标。当前元素之前不大于自己的候选都应移除，剩下的栈顶才是左侧最近且严格更大的元素。

```python
def previous_greater_indices(nums):
    answer = [-1] * len(nums)
    stack = []
    for i, value in enumerate(nums):
        while stack and nums[stack[-1]] <= value:
            stack.pop()
        if stack:
            answer[i] = stack[-1]
        stack.append(i)
    return answer
```

| 查找目标 | 右侧模板的弹栈条件 | 左侧模板的弹栈条件 |
| --- | --- | --- |
| 严格更大 | current > top | top <= current |
| 严格更小 | current < top | top >= current |
| 大于等于 | current >= top | top < current |
| 小于等于 | current <= top | top > current |

两个模板的栈含义不同：右侧模板在弹栈时解决旧元素的答案；左侧模板先排除无效候选，再为当前元素记录答案。每个下标至多入栈、出栈各一次，时间 O(n)，空间 O(n)。


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

| 遍历方式 | 顺序 | 上面这棵树的结果 |
| --- | --- | --- |
| 前序 | 根 → 左 → 右 | `[3, 9, 20, 15, 7]` |
| 中序 | 左 → 根 → 右 | `[9, 3, 15, 20, 7]` |
| 后序 | 左 → 右 → 根 | `[9, 15, 7, 20, 3]` |
| 层序 | 从上到下，每层从左到右 | `[[3], [9, 20], [15, 7]]` |

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

| 当前子树的情况 | 返回什么 |
| --- | --- |
| `p`、`q` 都不在里面 | `None` |
| 只包含一个目标 | 那个目标节点 |
| 同时包含两个目标 | 它们的最近公共祖先节点 |

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

| 轮次 | 本轮开始时的队列（展示节点值） | 固定处理数量 | 本层结果 | 本轮结束时的队列 |
| --- | --- | --- | --- | --- |
| 1 | `[3]` | 1 | `[3]` | `[9, 20]` |
| 2 | `[9, 20]` | 2 | `[9, 20]` | `[15, 7]` |
| 3 | `[15, 7]` | 2 | `[15, 7]` | `[]` |

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

| 题目 | 核心问题 | 函数返回什么 | 当前节点要做什么 |
| --- | --- | --- | --- |
| 144 前序遍历 | 按什么顺序访问 | 内层 `dfs` 不返回结果，外层返回列表 | 先记录自己，再访问左右子树 |
| 104 最大深度 | 子树有多高 | 一个整数 | 左右深度取最大值，再加一 |
| 236 最近公共祖先 | 两个目标在哪里汇合 | 节点或 `None` | 两侧都有结果则返回自己，否则传递单侧结果 |
| 102 层序遍历 | 怎样一层一层处理 | 二维列表 | 用队列，每轮处理固定数量的节点 |
| 98 验证 BST | 是否符合所有祖先的限制 | `True` 或 `False` | 检查上下界，并收紧范围传给孩子 |
| 105 构造二叉树 | 根是谁，左右分别是谁 | 新构造的根节点或 `None` | 用前序定根、中序分左右，再连接子树 |

这几题虽然都在写树，但递归传递的信息不同。拿到新题时，可以先在草稿上写出函数含义，再决定代码：

```text
1. 我传入的是一个节点，还是一个数组区间？
2. 我希望这个函数返回整数、真假、节点，还是只向列表追加数据？
3. 空节点或空区间应该怎么处理？
4. 左右子树已经给出结果后，当前这一层怎么完成任务？
```

#### 六道题的核心区别

| 题目 | 核心依据 | 对应实现 |
| --- | --- | --- |
| 144 前序遍历 | 当前节点在左右子树之前处理 | 先记录根，再递归 |
| 104 最大深度 | 一条向下路径只能进入一侧子树 | 左右深度取最大值再加一 |
| 236 最近公共祖先 | 两侧分别返回目标时，当前节点构成汇合点 | 双侧非空返回当前节点，单侧非空传递结果 |
| 102 层序遍历 | 同层节点需要统一处理 | 每轮先固定队列长度 |
| 98 验证 BST | 节点必须满足所有祖先施加的大小约束 | 向下传递并收紧上下界 |
| 105 构造二叉树 | 前序确定根，中序确定左右子树范围 | 按左子树节点数划分前序区间 |

### 九、二叉树递归与遍历模板

#### 深度优先遍历：处理位置决定遍历顺序

```python
def traversal(root, order='pre'):
    result = []
    def dfs(node):
        if node is None:
            return
        if order == 'pre':
            result.append(node.val)
        dfs(node.left)
        if order == 'in':
            result.append(node.val)
        dfs(node.right)
        if order == 'post':
            result.append(node.val)
    dfs(root)
    return result
```

order 取 pre、in、post，分别对应前序、中序、后序。遍历类问题的核心是安排当前节点的处理时机。

#### 自底向上：先求子树结果，再合并

以下为高阶模板：empty_value 是空子树结果，merge(node, left, right) 根据当前节点和左右结果构造返回值。这里的空值适用于整数、布尔值、None 等不可变结果。

```python
def tree_fold(root, empty_value, merge):
    if root is None:
        return empty_value
    left = tree_fold(root.left, empty_value, merge)
    right = tree_fold(root.right, empty_value, merge)
    return merge(root, left, right)


def tree_depth(root):
    return tree_fold(root, 0, lambda node, left, right: max(left, right) + 1)


def node_count(root):
    return tree_fold(root, 0, lambda node, left, right: left + right + 1)
```

最大深度与节点数量只改变空树值和合并规则。最近公共祖先还需要在命中目标时提前返回，采用下面的专用结构。

```python
def lowest_common_ancestor(root, p, q):
    if root is None or root is p or root is q:
        return root
    left = lowest_common_ancestor(root.left, p, q)
    right = lowest_common_ancestor(root.right, p, q)
    if left is not None and right is not None:
        return root
    return left if left is not None else right
```

该模板要求两个目标节点都存在。返回结果既可能是单个目标，也可能是已经确定的公共祖先，不能把所有非空返回值都解释成“两个目标均已找到”。

#### 自顶向下：把祖先约束传给子树

```python
def is_valid_bst(root):
    def dfs(node, lower, upper):
        if node is None:
            return True
        if not lower < node.val < upper:
            return False
        return (dfs(node.left, lower, node.val)
                and dfs(node.right, node.val, upper))
    return dfs(root, float('-inf'), float('inf'))
```

当前节点继承所有祖先的限制，再向孩子传递更严格的边界。路径和、路径深度等问题也可向下传状态，但参数含义与更新规则需要按题意重新定义。

#### 层序遍历：固定本层数量

```python
from collections import deque


def level_order(root):
    if root is None:
        return []
    queue = deque([root])
    result = []
    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)
            if node.left is not None:
                queue.append(node.left)
            if node.right is not None:
                queue.append(node.right)
        result.append(level)
    return result
```

range(len(queue)) 在本轮 for 开始前计算，后来加入的孩子不会改变本轮处理数量。每层最大值、每层和等题目，只需改变 level 的统计方式。

#### 分治构造：确定根，划分区间，连接子树

下面要求前序、中序对应同一棵树，节点值互不重复。make_node(value) 是创建节点的函数，例如力扣提供的 TreeNode。

```python
def build_from_preorder_inorder(preorder, inorder, make_node):
    position = {value: i for i, value in enumerate(inorder)}
    def build(pre_left, in_left, in_right):
        if in_left >= in_right:
            return None
        value = preorder[pre_left]
        mid = position[value]
        left_size = mid - in_left
        node = make_node(value)
        node.left = build(pre_left + 1, in_left, mid)
        node.right = build(pre_left + 1 + left_size, mid + 1, in_right)
        return node
    return build(0, 0, len(inorder))
```

中序区间统一采用左闭右开形式。右子树前序起点需要跳过当前根及整个左子树；其他遍历组合的根位置、区间划分方式不同，不能直接照搬下标公式。


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


---

### 二、回溯的基本框架：状态、选择与恢复

回溯将候选解组织成一棵搜索树：每个节点表示已经做出的选择，每条边表示一次新的选择。从当前节点向下搜索时扩展状态，返回时恢复状态，使不同分支能够独立展开。组合、排列、子集和字符串分割的区别，主要体现在候选范围、答案收集条件和去重规则上。

来源：[第 41 节课程](https://www.bilibili.com/video/BV1GdTj6sEJS)、[作者配套代码](https://github.com/jusway/Leetcode_learn)。

回溯函数通常由四部分构成：

| 组成部分 | 含义 | 常见实现 |
| --- | --- | --- |
| 当前状态 | 已经完成的部分选择 | `path`、剩余目标值、字符串下标 |
| 候选范围 | 当前节点允许扩展的分支 | `start_index`、`used`、相邻格子 |
| 收集与终止条件 | 何时形成答案，何时停止扩展 | 长度达到要求、剩余值归零、输入处理完毕 |
| 状态恢复 | 消除当前分支对其他分支的影响 | 弹出路径末尾元素、恢复访问标记 |

```text
判断是否收集答案 / 是否终止
遍历这一层的候选项
    跳过不合法或重复的选择
    做选择
    递归探索
    撤销选择
```

**下一层的候选范围由题目约束决定：组合通过下标限制消除顺序重复，排列通过访问标记限制同一路径中的重复使用。**

### 三、46. 全排列：通过 used 维护路径中的使用状态

#### 题目要求

输入没有重复元素的数组，返回所有排列。[1, 2] 和 [2, 1] 是两种不同答案。题目强调顺序，就不能像组合那样只向后选。

#### 思考过程

每个位置都可以选任何尚未使用的下标。`used[i]` 表示这个元素已经在**当前排列**中使用，不能再选；回到上一层时必须恢复为 `False`。

```python
class Solution:
    def permute(self, nums):
        result, path = [], []
        used = [False] * len(nums)

        def backtrack():
            if len(path) == len(nums):
                result.append(path[:])
                return
            for i in range(len(nums)):
                if used[i]:
                    continue
                path.append(nums[i])
                used[i] = True
                backtrack()
                path.pop()
                used[i] = False

        backtrack()
        return result
```

#### 示例与易错点

```text
[] → [1] → [1, 2]：保存；逐层撤销，回到 []
[] → [2] → [2, 1]：保存
```

选完 2 后仍需允许选择它前面的 1，所以每层从下标 0 开始。`used` 只记录当前路径的使用状态，不同排列分支之间可以再次使用同一元素。因此返回上一层时，需要同时恢复 `path` 和 `used`。

时间 O(n × n!)，因为有 n! 个答案，每个复制 n 个元素；辅助空间 O(n)，不含输出。

### 四、78. 子集：每个搜索节点都是答案

#### 思考过程

77 只收集长度为 k 的组合；子集要求所有长度，包括空集。因此每次进入递归就保存 `path`，保存之后还要继续探索。

```python
class Solution:
    def subsets(self, nums):
        result, path = [], []

        def backtrack(start_index):
            result.append(path[:])
            for i in range(start_index, len(nums)):
                path.append(nums[i])
                backtrack(i + 1)
                path.pop()

        backtrack(0)
        return result
```

对于 [1, 2]，依次收集 []、[1]、[1, 2]、[2]。`start_index` 防止再生成 [2, 1]。不能在保存后立即 `return`，否则第一次保存空集就结束了。循环没有候选项时自然返回。

两题的收集位置不同：**组合在路径达到指定长度时收集，子集在搜索树的每个节点收集。** 时间 O(n × 2^n)，辅助空间 O(n)，不含输出。这里沿用原题数组元素互不相同的前提。

### 五、39. 组合总和：可以重复选，所以递归传 i

#### 题目要求与推导

候选数互不相同且都是正整数，每个数可以重复选择，找出和为 `target` 的组合。`remaining` 表示还差多少：选了 x，下一层就还差 `remaining` - x。

因为允许重复使用当前数字，下一层从 i 开始；又因为是组合，只向后考虑，不回头生成不同顺序的同一答案。

```python
class Solution:
    def combinationSum(self, candidates, target):
        nums = sorted(candidates)
        result, path = [], []

        def backtrack(start_index, remaining):
            if remaining == 0:
                result.append(path[:])
                return
            for i in range(start_index, len(nums)):
                if nums[i] > remaining:
                    break
                path.append(nums[i])
                backtrack(i, remaining - nums[i])
                path.pop()

        backtrack(0, target)
        return result
```

#### 示例与剪枝依据

`candidates` = [2, 3, 6, 7]、`target` = 7：沿 2 → 2 → 3 得到一个答案，直接选 7 得到另一个。

排序后当前数字如果大于剩余值，后面的数字只会更大，因此可以 `break`，结束当前层循环。没排序就不能这样 `break`，因为后面可能还有更小的数。

所有数为正保证剩余值不断减少，搜索能终止。若允许 0，不断重复选 0 就无法推进。因此，候选数为正是该递归与剪枝成立的前提。

设最小数为 a，D = `target` // a，候选数数量为 m，递归深度最多 D。时间取决于搜索树规模，呈指数增长；用 O((D + 1)(m + 1)^D) 可作宽松上界。辅助空间 O(m + D)，不含输出。

### 六、40. 组合总和 II：同层去重，同路允许相等值

#### 与上一题的区别

数组可能有重复值，但每个位置只能选一次。需要同时处理：

- 同一个位置不能重复选：递归传 `i + 1`。
- 不同位置的相同值不能产生重复答案：排序后，同一层跳过重复值。

```python
class Solution:
    def combinationSum2(self, candidates, target):
        nums = sorted(candidates)
        result, path = [], []

        def backtrack(start_index, remaining):
            if remaining == 0:
                result.append(path[:])
                return
            for i in range(start_index, len(nums)):
                if nums[i] > remaining:
                    break
                if i > start_index and nums[i] == nums[i - 1]:
                    continue
                path.append(nums[i])
                backtrack(i + 1, remaining - nums[i])
                path.pop()

        backtrack(0, target)
        return result
```

#### 为什么是 i > start_index，而不是 i > 0

用 [1a, 1b, 2] 表示两个不同位置的 1，目标为 2：

```text
同一层：先选 1a 和先选 1b，会造成等值分支重复，所以跳过后者。
同一路径：选完 1a，再选 1b，是合法答案 [1, 1]，必须允许。
```

进入下一层时，`i == start_index` 的第一个候选不跳过。若写成 i > 0，第二个 1 会被错误挡掉，漏掉 [1, 1]。

**去重限制的是同一层的相同选择，不是禁止答案出现相同的数。** 时间宽松上界 O(n × 2^n)，辅助空间 O(n)，不含输出。

### 七、131. 分割回文串：枚举下一刀切在哪里

#### 思考过程

一次选择是一段连续子串。`start_index` 表示尚未分割部分的起点，循环枚举这一段的终点 `end`。只有这一段是回文，才能加入 `path`；下一段从 `end + 1` 开始。

```python
class Solution:
    def partition(self, s):
        result, path = [], []

        def backtrack(start_index):
            if start_index == len(s):
                result.append(path[:])
                return
            for end in range(start_index, len(s)):
                piece = s[start_index:end + 1]
                if piece != piece[::-1]:
                    continue
                path.append(piece)
                backtrack(end + 1)
                path.pop()

        backtrack(0)
        return result
```

对于 aab，答案为 [["a", "a", "b"], ["aa", "b"]]。选了 aa 后直接从下标 2 继续，不能又从第二个 a 开始。

回文指正读反读相同。切片的 `end + 1` 用于包含终点。必须等整串用完才保存，单独选到一个回文片段并不表示分割完成。考虑切片、回文检查和结果构造，最坏时间 O(n × 2^n)，辅助空间 O(n)，不含输出。

### 八、79. 单词搜索：格子只在当前路径中占用

#### 思考过程

从每个格子尝试作为起点，向上下左右寻找单词。定义 dfs(r, c, index)：尝试用格子 (r, c) 匹配 `word[index]`，并判断剩余字母能否接上。

格子不能在同一条路径内重复使用，但其他尝试仍然可以使用。因此临时改成占位符，搜索结束后恢复，作用与排列题的 `used` 相同。

```python
class Solution:
    def exist(self, board, word):
        if not word:
            return True
        if not board or not board[0]:
            return False
        rows, cols = len(board), len(board[0])
        directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]

        def dfs(r, c, index):
            if not (0 <= r < rows and 0 <= c < cols):
                return False
            if board[r][c] != word[index]:
                return False
            if index == len(word) - 1:
                return True

            saved = board[r][c]
            board[r][c] = '#'
            found = False
            for dr, dc in directions:
                if dfs(r + dr, c + dc, index + 1):
                    found = True
                    break
            board[r][c] = saved
            return found

        for r in range(rows):
            for c in range(cols):
                if dfs(r, c, 0):
                    return True
        return False
```

board = [["A", "B"]] 可以找到 AB，不能找到 ABA，因为最后一步不能回头重复使用起点。即使找到答案，也先恢复占用的格子再返回，避免给调用者留下被修改的棋盘。

可选剪枝：单词长度超过格子数直接失败；用 `Counter` 统计字母，如果棋盘某字母数量少于单词需求，也直接失败。但数量足够不代表位置能连起来，仍要搜索。

设单词长 L，宽松时间上界 O(rows × cols × 4^L)，实际非首步最多有三个新方向；递归辅助空间 O(min(L, rows × cols))。

### 九、回溯题型的统一框架与差异

| 题目 | 当前选择 | 下一层候选范围 | 收集答案的条件 | 撤销什么 |
| --- | --- | --- | --- | --- |
| 77 组合 | 一个数 | `i + 1` 开始 | 长度等于 k | `path` |
| 46 排列 | 一个未使用的数 | 每次从 0 开始，跳过 `used` | 长度等于 n | `path` 和 `used` |
| 78 子集 | 一个数 | `i + 1` 开始 | 每次进入递归 | `path` |
| 39 组合总和 | 一个可重复使用的数 | i 开始 | 剩余值为 0 | `path` |
| 40 组合总和 II | 一个数，同层去重 | `i + 1` 开始 | 剩余值为 0 | `path` |
| 131 分割回文串 | 一段回文子串 | `end + 1` 开始 | 整串已分完 | `path` |
| 79 单词搜索 | 一个相邻格子 | 四个方向 | 最后一个字母匹配成功 | 格子的临时占用标记 |

`remaining - nums[i]`、`index + 1` 作为参数传入新调用，不需要手动加回去；共享列表和棋盘的原地修改才需要恢复。是否需要恢复，取决于操作是否修改了调用之间共享的状态。

### 十、回溯算法模板

#### 组合与子集：递增下标限定候选范围

下面假设 nums 中没有重复值。k 为整数时返回固定长度组合；k 为 None 时返回全部子集。

```python
def choose_subsets(nums, k=None):
    result, path = [], []
    def dfs(start):
        if k is None:
            result.append(path[:])
        elif len(path) == k:
            result.append(path[:])
            return
        if k is not None and len(path) + len(nums) - start < k:
            return
        for i in range(start, len(nums)):
            path.append(nums[i])
            dfs(i + 1)
            path.pop()
    dfs(0)
    return result
```

剪枝条件表示“已选数量加上全部剩余数量仍然不足 k”。子集在每个节点保存，固定长度组合只在达到长度要求时保存。

#### 排列：每层重新枚举，路径内排除已用位置

```python
def permutations(nums):
    result, path = [], []
    used = [False] * len(nums)
    def dfs():
        if len(path) == len(nums):
            result.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]:
                continue
            used[i] = True
            path.append(nums[i])
            dfs()
            path.pop()
            used[i] = False
    dfs()
    return result
```

此版本用于元素互不相同的输入。若有重复值且答案要求去重，可先排序，再在循环中增加条件：i > 0 and nums[i] == nums[i - 1] and not used[i - 1] 时跳过。该条件约束相等元素的选择顺序，不阻止在同一排列中使用多个相等元素。

#### 目标和组合：复用规则、剪枝与同层去重

下面统一处理“允许复用”和“每个位置最多使用一次”两类问题。候选数必须为正整数，target 为非负整数。

```python
def target_combinations(candidates, target, reuse=False):
    nums = sorted(candidates)
    result, path = [], []
    def dfs(start, remaining):
        if remaining == 0:
            result.append(path[:])
            return
        for i in range(start, len(nums)):
            if nums[i] > remaining:
                break
            if i > start and nums[i] == nums[i - 1]:
                continue
            path.append(nums[i])
            dfs(i if reuse else i + 1, remaining - nums[i])
            path.pop()
    dfs(0, target)
    return result
```

reuse=True 时当前值可以再次选择；reuse=False 时下一层越过当前位置。排序后的同层去重避免等值分支重复；break 则排除当前层及其后所有过大的候选。

#### 字符串分割：枚举当前片段的终点

valid(piece) 判断一个片段能否被接受，例如回文判断。该模板要求最终覆盖整个字符串。

```python
def split_string(s, valid):
    result, path = [], []
    def dfs(start):
        if start == len(s):
            result.append(path[:])
            return
        for end in range(start + 1, len(s) + 1):
            piece = s[start:end]
            if not valid(piece):
                continue
            path.append(piece)
            dfs(end)
            path.pop()
    dfs(0)
    return result


def palindrome_partitions(s):
    return split_string(s, lambda piece: piece == piece[::-1])
```

这里 end 是右开端点，与正文中包含末字符的下标写法不同。切片使用 s[start:end]，下一段也从 end 开始，两处必须保持一致。

#### 网格路径回溯：占用、探索、恢复

下面以匹配字符序列为例，单元格值不修改，通过 used 保存当前路径访问状态。输入为矩形字符网格。

```python
def word_path_exists(board, word):
    if not word:
        return True
    if not board or not board[0]:
        return False
    rows, cols = len(board), len(board[0])
    used = set()
    directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
    def dfs(r, c, index):
        if not (0 <= r < rows and 0 <= c < cols):
            return False
        if (r, c) in used or board[r][c] != word[index]:
            return False
        if index == len(word) - 1:
            return True
        used.add((r, c))
        found = any(dfs(r + dr, c + dc, index + 1)
                    for dr, dc in directions)
        used.remove((r, c))
        return found
    return any(dfs(r, c, 0) for r in range(rows) for c in range(cols))
```

any 在发现成功分支时停止继续枚举，但当前调用仍会执行 remove 后再返回。因此成功和失败的分支都能正确恢复路径状态。


---

## 图论搜索

### 一、网格图建模与搜索方法

网格可以看成图：每个合法格子是一个节点，相邻且允许移动的格子之间有边。方向数组描述有哪些边。

```python
directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
```

r + dr 是下一行，c + dc 是下一列。先检查边界，再访问数组。

| 问题目标 | 常用方法 | 标记是否恢复 |
| --- | --- | --- |
| 枚举不同路径、寻找满足条件的路径 | 回溯 DFS | 当前路径占用，离开时恢复 |
| 找连通块、标记可达区域 | DFS 或 BFS | 已处理的格子不恢复 |
| 每一步代价相同的最少步数 | BFS | 首次发现即标记，不恢复 |
| 多处同时扩散需要多少轮 | 多源 BFS | 所有源点一起入队，不恢复 |

**同样是 DFS，是否撤销取决于访问状态的含义。** 单词搜索换一条路径后允许重用格子；岛屿计数中已经归属某座岛的格子不能再次计数。

下面 200、130 使用显式栈，保持 DFS 思路并避免 Python 深递归可能超出递归深度限制；与作者递归版的连通块标记逻辑相同。200、994、1091 的实现会修改输入网格，需要保留原数据时，先用 `[row[:] for row in grid]` 复制。

### 二、200. 岛屿数量：发现一次新陆地，处理整块陆地

#### 推导

外层循环找新岛屿；内层搜索把该岛所有连通陆地标记完。只有遇到未访问陆地，才将答案加一，不能每访问一个格子都加一。

```python
class Solution:
    def numIslands(self, grid):
        if not grid or not grid[0]:
            return 0
        rows, cols = len(grid), len(grid[0])
        directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
        answer = 0
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] != '1':
                    continue
                answer += 1
                grid[r][c] = '0'
                stack = [(r, c)]
                while stack:
                    x, y = stack.pop()
                    for dx, dy in directions:
                        nx, ny = x + dx, y + dy
                        if (0 <= nx < rows and 0 <= ny < cols
                                and grid[nx][ny] == '1'):
                            grid[nx][ny] = '0'
                            stack.append((nx, ny))
        return answer
```

```text
1 1 0
0 1 0
0 0 1
```

左上三个 1 是一座岛，右下是另一座，答案为 2。对角线不算相连。本题格子是字符串 '1'、'0'，不能与整数 1、0 混用。入栈时就标记，防止不同邻居重复把同一格加入栈。

时间 O(rows × cols)，辅助空间最坏 O(rows × cols)。外层双循环加内层搜索不会变成平方倍，因为每个陆地格只会被标记、入栈一次。

### 三、130. 被围绕的区域：从边界保护不能翻转的 O

#### 反向思考

直接判断每一块 O 是否被包围比较绕。换成寻找一定不能翻转的格子：**所有与边界 O 四方向连通的 O 都要保留。** 从边界出发，把它们标成 #；最后剩下的 O 全部翻成 X，# 恢复为 O。

```python
class Solution:
    def solve(self, board):
        if not board or not board[0]:
            return
        rows, cols = len(board), len(board[0])
        stack = []

        def protect(r, c):
            if board[r][c] == 'O':
                board[r][c] = '#'
                stack.append((r, c))

        for r in range(rows):
            protect(r, 0)
            protect(r, cols - 1)
        for c in range(cols):
            protect(0, c)
            protect(rows - 1, c)

        while stack:
            r, c = stack.pop()
            for dr, dc in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                nr, nc = r + dr, c + dc
                if 0 <= nr < rows and 0 <= nc < cols:
                    protect(nr, nc)

        for r in range(rows):
            for c in range(cols):
                if board[r][c] == 'O':
                    board[r][c] = 'X'
                elif board[r][c] == '#':
                    board[r][c] = 'O'
```

```text
原棋盘：       处理后：
X X X X       X X X X
X O O X       X X X X
X X O X       X X X X
X O X X       X O X X
```

不能只保护边界上的格子，还要保护与它连通的内部格子。最终恢复 # 是为了得到要求的棋盘，不是回溯中的撤销路径。时间和最坏辅助空间均为 O(rows × cols)。

### 四、994. 腐烂的橘子：多源 BFS 同时扩散

#### 为什么要把所有腐烂橘子一起入队

所有初始腐烂橘子在第 0 分钟就开始传播。先把它们全部加入队列，才能模拟“同时开始”；不能把一个源头传播完，再让另一个开始。

每轮开始记录 `size = len(queue)`，只处理这些格子。过程中新增的腐烂橘子属于下一轮，不能在同一分钟继续无限传播。

```python
from collections import deque


class Solution:
    def orangesRotting(self, grid):
        rows, cols = len(grid), len(grid[0])
        queue = deque()
        fresh = 0
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == 2:
                    queue.append((r, c))
                elif grid[r][c] == 1:
                    fresh += 1

        minutes = 0
        while queue and fresh > 0:
            size = len(queue)
            for _ in range(size):
                r, c = queue.popleft()
                for dr, dc in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                    nr, nc = r + dr, c + dc
                    if not (0 <= nr < rows and 0 <= nc < cols):
                        continue
                    if grid[nr][nc] != 1:
                        continue
                    grid[nr][nc] = 2
                    fresh -= 1
                    queue.append((nr, nc))
            minutes += 1

        return minutes if fresh == 0 else -1
```

#### 示例与边界

[[2, 1, 1, 1, 2]]：第 1 分钟两端同时向内扩散，第 2 分钟中间腐烂，答案是 2。

- 一开始没有新鲜橘子：直接得到 0。
- 有新鲜橘子，但没有腐烂源头：队列为空，返回 -1。
- 搜索结束仍有新鲜橘子：被空格隔开等原因造成不可达，返回 -1。

新鲜橘子入队时立即改成 2 并减少 `fresh`，否则可能被多个邻居重复计数。`while queue and fresh > 0` 避免全部腐烂后再额外算一轮。时间和最坏辅助空间均为 O(rows × cols)。

### 五、1091. 二进制矩阵中的最短路径：BFS 第一次到达就是最短

#### 题目要求

在 n × n 矩阵中，从左上走到右下，只能经过 0，可以向八个方向移动。答案是路径经过的**格子数量**，包含起点和终点；不可达返回 -1。

来源：[力扣原题](https://leetcode.com/problems/shortest-path-in-binary-matrix/description/)。

#### 为什么选择 BFS

所有移动都算走一步，移动代价相同。BFS 按距离从近到远扩散：先处理起点，再处理一步能到的位置，再处理两步能到的位置。

如果当前距离为 d，新发现邻居的距离就是 d + 1。如果有更短的路，它应当在更早的一层就被发现，因此第一次发现某个格子时已经得到最短距离。

普通 DFS 会先沿一条路深入，它第一次到终点的路径不一定最短。这里不需要枚举所有路线再比较长度。

#### 代码

```python
from collections import deque


class Solution:
    def shortestPathBinaryMatrix(self, grid):
        n = len(grid)
        if grid[0][0] != 0 or grid[n - 1][n - 1] != 0:
            return -1

        directions = [
            (-1, -1), (-1, 0), (-1, 1),
            (0, -1),           (0, 1),
            (1, -1),  (1, 0),  (1, 1),
        ]
        queue = deque([(0, 0, 1)])
        grid[0][0] = 1

        while queue:
            r, c, distance = queue.popleft()
            if r == n - 1 and c == n - 1:
                return distance
            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                if not (0 <= nr < n and 0 <= nc < n):
                    continue
                if grid[nr][nc] != 0:
                    continue
                grid[nr][nc] = 1
                queue.append((nr, nc, distance + 1))

        return -1
```

#### 队列状态与关键实现

`queue = deque([(0, 0, 1)])`：保存“行、列、到达这里经过的格子数”。起点已经算一个格子，所以初始距离为 1。

`grid[0][0] = 1`：先标记起点，防止从邻居走回起点。这里原始障碍和访问过的格子都不能再次进入，因此可以共用 1。

`popleft()`：从最早加入的节点开始处理，保证距离顺序。不能换成 `pop()`，否则变成后进先出的深度优先搜索。

`grid[nr][nc] = 1` 放在入队之前：第一次发现就登记，避免一个格子被不同父节点重复入队。

`return -1` 放在循环结束后：当前格子没有路不等于整张图没路，队列里的其他分支可能到达终点。

#### 队列变化示例

```text
grid = [[0, 0, 0],
        [1, 1, 0],
        [1, 1, 0]]

初始：[(0, 0, 1)]
取出 (0, 0, 1)，发现 (0, 1, 2)
取出 (0, 1, 2)，发现 (0, 2, 3)、(1, 2, 3)
距离为 3 的节点处理后，发现 (2, 2, 4)
取出终点 (2, 2, 4)，返回 4
```

一条最短路线是 (0, 0) → (0, 1) → (1, 2) → (2, 2)。移动了三次，但经过四个格子，所以答案为 4。

#### 边界情况

| 输入或情况 | 答案/处理 | 原因 |
| --- | --- | --- |
| [[0]] | 1 | 起点就是终点，也算一个格子 |
| [[1]] | -1 | 起点被挡住 |
| [[0, 1], [1, 0]] | 2 | 允许对角线移动，不额外要求两侧格子畅通 |
| 终点为 1 | -1 | 无法进入终点 |
| 起终点为 0，但中间不连通 | -1 | 等队列耗尽后确定 |

每格最多入队一次，每格最多检查八个邻居，因此时间 O(n²)，最坏队列空间 O(n²)。本实现已把距离保存在队列里，不需要再用 `size` 分层计数；994 使用按层计时，两种写法选择一种即可。

### 六、网格搜索的通用套路与题型对比

#### 网格搜索的六个组成部分

网格搜索可以统一拆分为状态定义、起点集合、移动规则、合法性检查、访问标记和答案统计。不同题目的代码结构相近，但这六部分的含义并不相同。

| 组成部分 | 设计依据 | 典型差异 |
| --- | --- | --- |
| 状态定义 | 状态必须包含决定后续选择的全部信息 | 普通连通性只需坐标；单词搜索还需要匹配下标 |
| 起点集合 | 根据题目确定搜索从哪些节点开始 | 单起点、多源起点、边界起点、逐个枚举起点 |
| 移动规则 | 按题意定义相邻节点 | 四方向与八方向不能混用 |
| 合法性检查 | 排除越界、障碍和不符合条件的节点 | 先检查边界，再读取格子内容 |
| 访问标记 | 明确标记属于当前路径还是整个搜索 | 路径占用需要恢复，连通块归属不恢复 |
| 答案统计 | 按题目要求定义计数单位 | 连通块数、传播轮数、移动次数、经过的格子数 |

如果钥匙、剩余消除障碍次数等信息会改变后续可达位置，那么相同坐标不一定代表相同状态。此时访问记录也必须包含这些额外信息，仅按坐标判重可能漏掉合法路径。

| 题目 | 起点 | 方向 | 搜索目标 | 标记方式 |
| --- | --- | --- | --- | --- |
| 79 单词搜索 | 逐个格子尝试 | 四方向 | 一条符合单词的路径 | 临时占用，回来恢复 |
| 200 岛屿数量 | 每块尚未访问的陆地 | 四方向 | 标完整个连通块 | 永久标记 |
| 130 被围绕的区域 | 所有边界 O | 四方向 | 保护与边界连通的区域 | 保护标记，最后统一还原 |
| 994 腐烂的橘子 | 所有初始腐烂橘子 | 四方向 | 同时扩散的轮数 | 入队即腐烂 |
| 1091 最短路径 | 左上角 | 八方向 | 到右下角的最短距离 | 入队即访问 |

#### 什么时候可以直接用 BFS 求最短路

前提是每条边代价相同。本节上下、左右、斜向都只算一次移动。如果不同格子的通行费用不同，不能直接套用“第一次发现就是最优”的普通 BFS，需要重新选择算法。

#### 访问标记的作用范围

访问标记分为路径内标记和全局标记。单词搜索中的标记表示“当前路径已经使用这个格子”，离开分支后必须恢复；岛屿搜索中的标记表示“这个格子已经属于某个被处理的连通块”，整个搜索期间都应保留。

在等权最短路径 BFS 中，队列按距离非递减顺序处理节点。首次发现某个状态时，其最短距离已经确定，因此可以在入队时标记，防止同一状态被不同父节点重复加入队列。

#### 分层计数与显式距离

BFS 常见的距离记录方式有两种：按层处理时统一增加计数，或将距离直接保存在队列元素中。

腐烂的橘子使用前一种方式，每轮固定处理当前队列中的节点，将新增节点留到下一分钟。最短路径使用后一种方式，队列保存坐标与距离，邻居距离由当前距离加一得到。两种写法都依赖先进先出的顺序，区别在于距离存储的位置。

计数起点需要与题意一致：传播开始时经过的时间是 0；从起点出发的移动次数是 0；若计算路径包含的格子数，起点本身就贡献 1。

#### 从题目要求到搜索方案

连通块问题关注哪些节点属于同一区域，DFS 与 BFS 都可完成标记；边界保护问题通过反向搜索找出必须保留的区域；同时扩散问题将所有源点作为第 0 层；等权最短路径问题利用 BFS 的分层顺序确定最短距离。

这些题目的共同流程是：确定起点，标记已发现状态，按照移动规则扩展合法邻居，再根据题目要求统计答案。具体实现中的方向数量、标记时机与计数含义，需要与这一流程保持一致。

### 七、图与网格搜索模板

#### 网格邻居生成：方向、边界与通行条件分离

```python
FOUR_DIRECTIONS = [(1, 0), (-1, 0), (0, 1), (0, -1)]
EIGHT_DIRECTIONS = [
    (-1, -1), (-1, 0), (-1, 1),
    (0, -1),           (0, 1),
    (1, -1),  (1, 0),  (1, 1),
]


def grid_neighbors(r, c, rows, cols, directions):
    for dr, dc in directions:
        nr, nc = r + dr, c + dc
        if 0 <= nr < rows and 0 <= nc < cols:
            yield nr, nc
```

该函数只处理方向和边界，障碍判断由调用方完成。下面的连通块、边界保护和二进制矩阵模板复用本节辅助函数与方向数组。

#### 连通块计数：外层找起点，内层标完整块

is_land(value) 判断格子是否属于待统计区域，例如字符串网格中可传 lambda value: value == '1'。

```python
def count_components(grid, is_land):
    if not grid or not grid[0]:
        return 0
    rows, cols = len(grid), len(grid[0])
    seen = set()
    count = 0
    for r in range(rows):
        for c in range(cols):
            if (r, c) in seen or not is_land(grid[r][c]):
                continue
            count += 1
            seen.add((r, c))
            stack = [(r, c)]
            while stack:
                x, y = stack.pop()
                for nx, ny in grid_neighbors(x, y, rows, cols, FOUR_DIRECTIONS):
                    if (nx, ny) not in seen and is_land(grid[nx][ny]):
                        seen.add((nx, ny))
                        stack.append((nx, ny))
    return count
```

使用 seen 不会修改输入网格，代价是额外访问集合。若改用原地标记，应在入栈时修改，且标记值必须与未访问区域区分开。

#### 边界反向搜索：求出所有与外部连通的区域

```python
def boundary_connected_cells(board, open_value='O'):
    if not board or not board[0]:
        return set()
    rows, cols = len(board), len(board[0])
    safe = set()
    stack = []
    def add(r, c):
        if (r, c) not in safe and board[r][c] == open_value:
            safe.add((r, c))
            stack.append((r, c))
    for r in range(rows):
        add(r, 0)
        add(r, cols - 1)
    for c in range(cols):
        add(0, c)
        add(rows - 1, c)
    while stack:
        r, c = stack.pop()
        for nr, nc in grid_neighbors(r, c, rows, cols, FOUR_DIRECTIONS):
            add(nr, nc)
    return safe
```

被围绕区域中，所有值为 O 且不在 safe 中的格子需要翻转。边界搜索只负责求可达集合，最终翻转、计数或保留由题目决定。

#### 多源 BFS：一次求出到最近源点的距离

sources 是初始源点集合；neighbors(state) 返回合法后继状态；状态必须可哈希。以下模板用于每条边代价均为 1 的图。

```python
from collections import deque


def bfs_distances(sources, neighbors):
    distance = {}
    queue = deque()
    for source in sources:
        if source not in distance:
            distance[source] = 0
            queue.append(source)
    while queue:
        state = queue.popleft()
        for nxt in neighbors(state):
            if nxt in distance:
                continue
            distance[nxt] = distance[state] + 1
            queue.append(nxt)
    return distance
```

所有源点距离均为 0。distance 同时承担距离记录和访问标记，入队前写入，避免重复入队。普通单源搜索只需传入一个元素的源点列表。

腐烂橘子可把初始腐烂位置作为 sources，邻居仅允许非空橘子格。若某个初始新鲜橘子不在 distance 中，答案为 -1；否则答案是所有初始新鲜橘子的最大距离，没有新鲜橘子时为 0。

#### 单源 BFS：首次到达目标时结束

```python
from collections import deque


def shortest_steps(start, is_goal, neighbors):
    queue = deque([(start, 0)])
    seen = {start}
    while queue:
        state, steps = queue.popleft()
        if is_goal(state):
            return steps
        for nxt in neighbors(state):
            if nxt not in seen:
                seen.add(nxt)
                queue.append((nxt, steps + 1))
    return -1
```

该模板返回边数，即移动次数。调用方需要先验证起点合法，并保证 neighbors 只产生合法状态。若题目统计路径节点数，成功结果加一；失败的 -1 不应加一。

#### 二进制矩阵：将网格规则接入 BFS

```python
def binary_matrix_path_length(grid):
    if not grid or not grid[0]:
        return -1
    rows, cols = len(grid), len(grid[0])
    target = (rows - 1, cols - 1)
    if grid[0][0] != 0 or grid[target[0]][target[1]] != 0:
        return -1
    def neighbors(state):
        r, c = state
        for nr, nc in grid_neighbors(r, c, rows, cols, EIGHT_DIRECTIONS):
            if grid[nr][nc] == 0:
                yield nr, nc
    steps = shortest_steps((0, 0), lambda state: state == target, neighbors)
    return -1 if steps == -1 else steps + 1
```

这个版本不修改输入矩阵，复用邻居生成与最短步数模板。它允许八方向移动；四方向迷宫只需替换方向数组，其他距离逻辑保持不变。

图搜索的空间与时间取决于状态数和边数。DFS/BFS 通常为 O(V + E) 时间、O(V) 辅助空间；固定方向网格每格邻居数为常数，因此时间可写为 O(rows × cols)。如果状态还包含钥匙或剩余次数，V 应计算完整状态的数量，而不只是格子数量。

