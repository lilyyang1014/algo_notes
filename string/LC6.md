## 6. Zigzag Conversion

Date: 9/23/2026
Difficulty: Medium
Tags: String, Simulation

![Characters follow a zigzag row sequence and are then concatenated row by row](assets/6.png)

### 一刷 (9/23/2026) ❌ 没思路，未想到用 row + direction 模拟折返

**卡在哪**：想到用 numRows 个容器，但试图用 `i % numRows` 分配字符；后来想按每行、每个方向分别写 if / else。

**真正的坎**：同一个 row 可能往上，也可能往下，只有位置不足以决定下一步，需要额外记录 direction。

#### ① 取余表达循环，不能直接表达折返

```text
numRows = 3

i % numRows：0 → 1 → 2 → 0 → 1 → 2
实际 row：   0 → 1 → 2 → 1 → 0 → 1
```

**根因**：取余到末尾后回到 0；题目要求到末尾后反向走。维护 direction 为 +1 / -1，中间保持方向，只在两端改变。

#### ② array 与 object 的创建分开

```java
StringBuilder rows = new StringBuilder[numsRow];
// ❌ 左侧应为 StringBuilder[]；参数名是 numRows
```

```java
StringBuilder[] rows = new StringBuilder[numRows];
// 此时各格是 null，还没有 StringBuilder 对象

for (int i = 0; i < numRows; i++) {
    rows[i] = new StringBuilder();
}
```

**根因**：创建 object reference array，不等于创建其中的对象。`rows[1]` 是 index 1 的 StringBuilder，不是寻找名为 rows1 的 variable。

#### ③ row 与 direction 的职责混淆

```java
int direction = 0;
if (direction == 0) {
    // ❌ 要判断的是 row 是否到顶部；direction=0 表示不移动
}
```

**根因**：row 记录「当前在哪一行」，direction 记录「下一步往哪走」。是位置触碰边界，才改变方向。

**本轮理解**：已能复述「准备每行容器 → 放字符、换方向、移动 → 按行拼接」，并正确写出容器初始化。完整独立实现结果待补。

**自测点**：输入按原顺序扫描，为什么每个 StringBuilder 内的字符顺序就是最终读取该行所需的顺序？

---

<!-- ↓↓↓ 复习时先自己想一遍，再往下翻看答案 ↓↓↓ -->

### 核心思路

**每行一个 StringBuilder，用 row + direction 模拟字符的落点，最后按行拼接。**

### 正确写法

```java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows == 1) return s;

        StringBuilder[] rows = new StringBuilder[numRows];
        for (int i = 0; i < numRows; i++) {
            rows[i] = new StringBuilder();
        }

        int row = 0;
        int direction = 1;

        for (int i = 0; i < s.length(); i++) {
            rows[row].append(s.charAt(i));

            if (row == 0) {
                direction = 1;
            } else if (row == numRows - 1) {
                direction = -1;
            }

            row += direction;
        }

        StringBuilder result = new StringBuilder();
        for (int i = 0; i < numRows; i++) {
            result.append(rows[i]);
        }
        return result.toString();
    }
}
```

### State 与执行顺序

- **row**：当前字符所属的行，访问时位于 `[0, numRows-1]`。
- **direction**：下一步的移动量，+1 往下，-1 往上。
- **每轮顺序**：先放当前字符 → 到两端则换方向 → 移动到下一行。

同一 row 的下一步可能不同，所以必须记住方向；中间行不需要逐一写 branch。

### Dry run

```text
s = "ABCDEFG", numRows = 3

字符：A  B  C  D  E  F  G
row： 0  1  2  1  0  1  2

rows[0] = "AE"
rows[1] = "BDF"
rows[2] = "CG"

result = "AEBDFCG"
```

`result.append(rows[i])` 一次追加该行的全部字符，不会清空 rows[i]。字符按输入顺序 append，同一行内的先后顺序自然保留。

### Edge case：只有一行

若不单独处理，`s="AB", numRows=1`：

```text
A 放入 rows[0]
row==0 → direction=1 → row=1
下一轮把 B 放入 rows[1] → 越界
```

**根因**：上下移动要求有移动空间；只有一行时无需转换，直接 return s。

> **自检信号**：写到 row += direction，检查下一次访问是否仍在合法范围；从题目允许的最小行数开始 dry run。

这条已讲穿。下次复刷观察能否主动想到并解释，不提前提醒。

### 复杂度分析

设 n = s.length()，R = numRows。

**Time: O(n+R)**：初始化 R 行，分配 n 个字符，再遍历 R 行拼接全部字符。
**Space: O(n+R)**：R 个 StringBuilder 及总计 O(n) 的字符存储。

### 沉淀

- **同一个位置有不同的下一步，说明还缺 state**：本题缺的是 direction。
- **把重复 branch 收敛成共同动作**：往下都是 +1，往上都是 -1，用 row += direction 统一移动。
- **array 可以保存 object reference**：new array 只创建各格，格内对象还要分别 new。
- **edge case 从动作的前提找**：要移动，就检查有没有移动空间；不是最后随意补 if。

### 关联

- LC88 / LC27：pointer update 后，下一次 array 访问是否仍有合法范围保证。
