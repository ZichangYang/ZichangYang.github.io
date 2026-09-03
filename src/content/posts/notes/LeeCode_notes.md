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

