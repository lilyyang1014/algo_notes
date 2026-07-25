## Abstraction

**Definition:** Abstraction works at two levels —

1. Within a class: expose _what_ it does, hide _how_ it does it.
2. When modeling: keep only the details the problem cares about.
   (geometry Circle → radius; paint Circle → radius + color)

**Why:** area() names _what_, the formula stays inside（名字说要干什么,
公式藏在里面）→ the caller's only dependency is the method signature
（调用方只需知道 area() 这个签名,用不到公式）→ anything providing that
signature is a substitute（任何也有 area() 的类都能顶替）, so Circle and
Rectangle can both be a Shape and swap freely.

**Counter-example:** if the caller computes 3.142*r*r itself, it depends
on more than the signature → swapping in Rectangle breaks it.
依赖签名 → 能换;依赖公式 → 不能换。

**Mechanism:** interface / abstract class — or simply well-named public
methods. Circle.area() is already abstraction, just the weakest form;
the payoff shows up when one abstract type has many implementations.

**Note:**

- Hides _how_ (implementation) → to simplify.
  Encapsulation hides _state_ (data) → to protect.
  Not both "data hiding" — that phrasing is what causes the confusion.
- Timing: abstraction = design-time decision (what to expose);
  encapsulation = implementation-time mechanism (private + validating methods).
- Test: exposing it forces the caller to learn details → abstraction;
  exposing it lets the caller corrupt data → encapsulation.

**Follow-ups:**

- abstract class vs interface → 留到 inheritance
- Same mechanism (callers depend only on signatures) yields two payoffs:
  refactor freedom (encapsulation view) and swappable implementations
  (abstraction view).

<!-- vocab
method signature = 方法名 + 参数类型列表(不含实现)
  Java 不含返回类型 → 所以不能只靠返回类型重载
selective ignorance = 只保留与当前问题相关的细节(Circle 只留 radius,不要 color)
-->
