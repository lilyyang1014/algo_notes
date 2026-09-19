## Find Subarray with Target Sum

Date: 9/18/2026
Tags: Array, Prefix Sum, Hash Table
来源：Visa 面经 #2

### 题目

给定 integer array `nums` 和 `target`，找出任意一段 sum 等于 target 的**非空连续 subarray**，返回这段 array；无解返回 empty array。

```text
nums = [1,4,0,0,3,10,5], target = 7
返回：[4,0,0,3]
```

原面经未给完整 constraints，本笔记按允许 zero、negative 的版本练习。与 LC560 不同：本题返回一段 subarray，LC560 返回数量。

### 一刷 (9/18/2026) ❌ 没思路，未建立 prefix sum 与区间的关系

我的初稿：

```java
Set<Integer> set = new HashSet<>();
for (int num : nums) {
    set.add(num); // ❌ 原数值去重后，无法表达位置与连续关系
}

while (target >= 0) { // ❌ target 没有更新，会进入 infinite loop
    for (int i = 0; i < nums.length; i++) {
        int left = target - nums[i]; // ❌ 只减一个数，没有累计一段 subarray
    }
}
```

**真正的坎**：把 LC128 的去重、Two Sum 的找差值直接搬过来，但没保留「连续一段」的信息。应查找的是 **prefix sum 的差值**，不是两个原数值的差值。

看解后重写的两处 compile error：

```java
map.put(OL, -1); // ❌ 字母 O；应为数字 0：0L
// ❌ 方法末尾漏了无解时的 return new int[0];
```

**自测点**：map 的 key、value 分别代表什么？为什么查到的 index 要加 1，才是答案的起点？

---

<!-- ↓↓↓ 复习时先自己想一遍，再往下翻 ↓↓↓ -->

### 核心思路（一句话）

**当前 prefix sum − 之前的 prefix sum = 中间那段 subarray 的 sum，因此去 map 查 sum − target。**

### 从 brute force 到优化

Brute force 固定起点，再向右移动终点、累加 sum，Time 为 O(n²)。

优化后不逐个尝试起点，而是直接查找需要的旧 prefix sum：

```text
nums = [1, 4, 0, 0, 3]，target = 7

当前 prefix sum = 8
需要去掉的 prefix sum = 8 - 7 = 1

[1] | [4, 0, 0, 3]
去掉    剩下的 sum = 7
```

map 中：

- **key：从 index 0 开始的 prefix sum。**
- **value：这个 prefix sum 结束的最早 index。**

例如 `1 → 0` 表示 `[1]` 在 index 0 结束，答案从下一格开始，所以 `start = 0 + 1`。

### 正确写法：先查再存

```java
public static int[] findSubarray(int[] nums, int target) {
    Map<Long, Integer> map = new HashMap<>();
    map.put(0L, -1);

    long sum = 0;

    for (int i = 0; i < nums.length; i++) {
        sum += nums[i];
        long need = sum - target;

        if (map.containsKey(need)) {
            int start = map.get(need) + 1;
            return Arrays.copyOfRange(nums, start, i + 1);
        }

        map.putIfAbsent(sum, i);
    }

    return new int[0];
}
```

需要 `import java.util.*;`。`sum` 使用 long，避免 int 累加时发生 overflow。

### 为什么放 `0 → -1`

这条记录表示：**尚未读取任何 element，sum 为 0，结束位置记作 -1。**

当 `sum == target`：

```java
need = sum - target;       // 0
start = map.get(0L) + 1;   // -1 + 1 = 0
```

于是自动返回从 index 0 开始的答案，不是真的访问 `nums[-1]`。

**不放也可以**：删掉初始记录，在 `sum += nums[i]` 后单独处理：

```java
if (sum == target) {
    return Arrays.copyOfRange(nums, 0, i + 1);
}
```

两种写法效果相同，初始记录只是把边界情况合并进普通查找。

### API 速记

**① `putIfAbsent(key, value)`**

key 不存在或对应 value 为 null 时才写入；已有非 null value 就不覆盖。本题记录该 prefix sum 最早出现的 index。

```java
map.putIfAbsent(5L, 1); // 存入 5 → 1
map.putIfAbsent(5L, 2); // 保留 5 → 1
```

返回原有 value；原来不存在或为 null 时返回 null。

本题只求任意一段，当前这种先查再存的写法用 `put` 也可以；保留最早 index 对寻找最长 subarray 更有用。

**② `Arrays.copyOfRange(nums, from, to)`**

返回新的 array，复制范围为 **`[from, to)`，左闭右开**。

```java
Arrays.copyOfRange(nums, start, i + 1);
// 包含 index start 到 i
```

返回 k 个 element，需要 O(k) time 和 O(k) space。

**③ `0L`**

`0` 是 int literal，`0L` 是 long literal；autoboxing 后分别为 Integer、Long。

```java
Map<Long, Integer> map = new HashMap<>();
map.put(0L, -1); // Long key，Integer value
```

`L` 只指定类型，数值仍是 0。注意数字 `0` 与字母 `O`。

### 复杂度分析

**Time: 平均 O(n)**：扫描一次，HashMap 操作按平均 O(1) 计；找到后复制结果最多再花 O(n)。

**Space: O(n)**：map 最多记录 n + 1 个 prefix sum，返回 array 最多占 O(n)。

### 沉淀

- **连续区间的 sum，可以转化为两个 prefix sum 的差。**
- **先查再存，保证查到的是之前的位置。** 若先存，target 为 0 时可能用当前 prefix sum 减自己，误选 empty subarray。
- **map 存的是被去掉部分的终点，答案从下一格开始。**
- **允许 negative 时，sum 超过 target 不能直接停止。** 后面的负数可能让 sum 降回来。

### 关联

- LC1：都是查差值；Two Sum 查原数值，本题查 prefix sum。
- LC560：同样查 `sum - target`，但 map 的 value 存 frequency，用来统计符合条件的 subarray 数量。
