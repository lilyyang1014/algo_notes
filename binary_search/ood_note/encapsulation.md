# Encapsulation

**My version:** Encapsulation bundles data and methods into one class
and restricts direct access — all interaction with the internal state
must go through methods. But the methods must set up validation, so
that only valid data can reach the private fields.

**Definition:** Bundle data + methods into one class; restrict direct
access so all interaction with internal state goes through exposed
methods.

> 捆绑数据和方法;限制直接访问,所有对内部状态的操作必须走暴露的方法

**Why:** Methods are checkpoints — withdraw() can refuse (an invalid
operation), a public field can't (refuse anything) → class keeps its
state valid (balance never negative) → internals can change without
breaking callers.

> 三环因果链:
>
> 1. 方法是必经关卡:方法调用会执行方法体内的检查代码,所以能拒绝;
>    字段赋值不执行任何代码,直接写内存,无法拒绝
> 2. 改 balance 的唯一通道是带检查的方法 → 非法值进不来 →
>    对象状态永远合法(这就是 invariant 不变量)
> 3. 外部只认识方法签名,摸不到内部字段 → 内部随便重构
>    (如 double 换 BigDecimal),调用方一行不用改

**Note:** "state" = the current values of all fields(所有字段的当前值;
可理解为某一时刻的"快照").

**Follow-ups:**

- Setters break encapsulation? → No-validation setter ≈ public field
  (a checkpoint with no guard 有关卡之名、无守卫之实). Only expose
  what callers need; validate inside methods.
- vs. abstraction? → Encapsulation hides state (protect data 保护数据);
  abstraction hides how (simplify interface 简化接口).

<!-- vocab: field 成员变量 | caller 调用方 | invariant 不变量 |
     validity 合法性 | refuse 拒绝 | expose 暴露 -->
