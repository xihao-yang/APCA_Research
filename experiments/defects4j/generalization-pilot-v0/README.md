# Generalization Pilot v0 — Analyzer 实验结果与反思

> 日期：2026-10-04  
> 阶段：Execution-Aware APCA / Patch Analyzer generalization pilot  
> 当前状态：Analyzer baseline 已完成，下一步进入 Input Generator generalization

---

## 1. 实验目的

前面的 Chart-13 实验已经证明：对于一个已知案例，可以从补丁中的条件出发，构造 additional input，并通过执行 buggy / patched program 观察运行时行为差异。

但 Chart-13 只是一个单独案例，不能说明当前 pipeline 能泛化到其他项目、bug 和 APR 工具。

因此，本轮实验的目标是：

1. 冻结当前的 v0 `Patch Analyzer`，不针对单个新 patch 临时修改规则；
2. 在一组 **unseen patches** 上测试当前 analyzer 的可用性；
3. 记录：
   - 是否能正常执行 analyzer；
   - 是否能生成 JSON；
   - 是否能提取 downstream Input Generator 所需要的 predicate；
4. 观察当前 heuristic analyzer 的泛化边界，为后续修改提供依据。

本轮实验关注的是：

**Analyzer 是否能为 downstream 阶段提供 usable predicate。**

它不是对 patch correctness 的最终判断，也不是完整 pipeline 的最终效果评估。

---

## 2. Generalization Pilot 数据集

本轮使用 10 个新的 patch，**不包含 Chart-13**。

### 2.1 数据组成

- 总 patch 数：10
- Correct：5
- Overfitting：5
- Projects：3
  - Chart
  - Lang
  - Math
- APR Tools：3
  - AVATAR
  - TBar
  - SimFix

### 2.2 Patch 列表

| Patch ID | Project | Bug | Tool | Label |
|---|---|---:|---|---|
| Chart_19_AVATAR | Chart | 19 | AVATAR | correct |
| Lang_6_AVATAR | Lang | 6 | AVATAR | correct |
| Math_11_TBar | Math | 11 | TBar | correct |
| Lang_33_SimFix | Lang | 33 | SimFix | correct |
| Chart_20_SimFix | Chart | 20 | SimFix | correct |
| Chart_12_TBar | Chart | 12 | TBar | overfitting |
| Lang_12_SimFix | Lang | 12 | SimFix | overfitting |
| Math_49_AVATAR | Math | 49 | AVATAR | overfitting |
| Lang_39_AVATAR | Lang | 39 | AVATAR | overfitting |
| Math_15_TBar | Math | 15 | TBar | overfitting |

---

## 3. 当前 Analyzer v0 的成功标准

当前 `pilot_runner.py` 对 Analyzer 的判断分为三层：

1. `patch_analyzer.py` 是否正常退出；
2. analyzer 输出 JSON 是否存在；
3. JSON 中的 `predicate` 是否为非空。

当前定义：

```text
ANALYZER_SUCCESS
= process completed
+ output JSON exists
+ predicate is available
```

当前定义：

```text
ANALYZER_FAIL
= downstream 所需 predicate 不可用
```

需要特别注意：

> **ANALYZER_SUCCESS 不等于整个 patch analysis 完全正确。**

当前 success criterion 只是一个工程性的 downstream-availability criterion：

> 当前 Analyzer 是否至少成功生成了 Input Generator 后续可以使用的 predicate。

---

## 4. 实验结果

### 4.1 总体结果

```text
Total patches        = 10
ANALYZER_SUCCESS     = 6
ANALYZER_FAIL        = 4
Predicate coverage   = 6 / 10 = 60%
```

### 4.2 按 Correct / Overfitting 分类

| Label | Success | Fail | Coverage |
|---|---:|---:|---:|
| Correct | 3 | 2 | 60% |
| Overfitting | 3 | 2 | 60% |
| **Total** | **6** | **4** | **60%** |

在当前 10-patch pilot 中，Analyzer 的 predicate availability 在 correct 和 overfitting 两组上都是 60%。

因此，目前没有观察到明显的 label-side coverage imbalance。

但由于样本只有 10 个，不能据此得出统计性结论。

---

## 5. Patch-level 结果

| Patch ID | Label | Analyzer Result | Extracted Predicate / Failure |
|---|---|---|---|
| Chart_19_AVATAR | correct | FAIL | `PREDICATE_NOT_FOUND` |
| Lang_6_AVATAR | correct | SUCCESS | `consumed == 0` |
| Math_11_TBar | correct | SUCCESS | `vals.length != dim` |
| Lang_33_SimFix | correct | SUCCESS | `array.length == 0` |
| Chart_20_SimFix | correct | FAIL | `PREDICATE_NOT_FOUND` |
| Chart_12_TBar | overfitting | FAIL | `PREDICATE_NOT_FOUND` |
| Lang_12_SimFix | overfitting | SUCCESS | `start == 0 && end == 0` |
| Math_49_AVATAR | overfitting | FAIL | `PREDICATE_NOT_FOUND` |
| Lang_39_AVATAR | overfitting | SUCCESS | `textIndex == -1` |
| Math_15_TBar | overfitting | SUCCESS | `y >= TWO_POWER_52 || y <= -TWO_POWER_52` |

---

## 6. Failure Reason

在 `pilot_runner.py` 中进一步增加了 `failure_reason`：

```text
PROCESS_ERROR
OUTPUT_JSON_MISSING
PREDICATE_NOT_FOUND
```

当前 4 个失败案例全部属于：

```text
PREDICATE_NOT_FOUND
```

也就是说，本轮没有观察到：

- analyzer process crash；
- output JSON missing。

当前失败模式更具体地表现为：

```text
patch_analyzer.py 正常运行
        ↓
JSON 正常生成
        ↓
无法抽取 usable predicate
        ↓
ANALYZER_FAIL
```

因此，当前最主要的 bottleneck 不是 process-level executability，而是：

> **predicate extraction coverage**

---

## 7. 成功案例中出现的 Predicate 类型

Chart-13 的 predicate 比较简单：

```text
lower > upper
```

但是在 unseen patches 中已经出现了更加复杂的形式：

```text
consumed == 0
vals.length != dim
array.length == 0
start == 0 && end == 0
textIndex == -1
y >= TWO_POWER_52 || y <= -TWO_POWER_52
```

这些 predicate 已经覆盖了不同类型：

- variable vs constant
- variable vs variable
- field / property access
- `.length`
- conjunction：`&&`
- disjunction：`||`
- negative constant
- symbolic constant

这说明下一阶段的 Input Generator 将不再只是处理 Chart-13 的简单 numeric boundary case。

下一步真正要测试的是：

> 当前 Input Generator 是否能把这些不同形态的 predicate 转换成有效的 additional inputs。

---

## 8. 一个重要发现：Predicate Success ≠ Analyzer Fully Correct

虽然当前有 6 个 `ANALYZER_SUCCESS`，但日志表明 Analyzer 的其他字段仍然存在明显问题。

例如：

```text
Lang_33_SimFix
class = handles
predicate = array.length == 0
```

这里 predicate 被提取出来，因此按当前 criterion 被判为 SUCCESS。

但是：

```text
class = handles
```

明显不像正确的 Java class name。

类似问题还包括：

```text
Lang_39_AVATAR
class = defines
```

以及：

```text
Math_15_TBar
class = and
```

Chart-19 中甚至出现：

```text
class = to
```

因此当前 Analyzer 至少包含两个不同层次的问题：

### A. Predicate extraction coverage

有些 patch 完全无法提取 predicate：

```text
PREDICATE_NOT_FOUND
```

### B. Structural parsing accuracy

即使 predicate 被提取出来：

- class 可能解析错误；
- method 也可能解析到控制流语句，而不是真正 method signature。

因此不能简单报告：

```text
Analyzer accuracy = 60%
```

更准确的说法是：

```text
Usable predicate availability = 60%
```

或者：

```text
Predicate extraction coverage = 60%
```

---

## 9. Chart-19 暴露出的典型问题

Chart-19 是本轮第一个 unseen patch。

Analyzer 输出：

```text
change_type  = throw_insertion
predicate    = NOT_FOUND
change_blocks = 2
```

但实际 patch 包含类似：

```java
if (axis == null) {
    throw new IllegalArgumentException(...);
}
```

因此语义上存在非常明确的 predicate：

```text
axis == null
```

当前 Analyzer 没有提取出来。

这个 case 说明：

> v0 heuristic 对某些 multi-location / inserted guard / throw-insertion 类型的 patch 支持不足。

重要的是，本轮实验中没有立刻为 Chart-19 修改 Analyzer。

这是有意的实验设计：

```text
先冻结 v0
→ 跑完全部 unseen patches
→ 记录 baseline
→ 汇总 failure patterns
→ 再系统性修改
```

这样可以避免：

```text
看到一个失败
→ 为这个 patch 加一个 special case
→ 再看下一个失败
→ 再加另一个 special case
```

否则很容易把 pipeline 做成 case-by-case patching，而不是可泛化的方法。

---

## 10. 实验反思

### 10.1 当前最重要的结果不是“60% 高不高”

10 个 patch 样本量很小，因此 60% 本身不能作为强实验结论。

更重要的是：

> Chart-13 上能工作的机制，在 unseen patches 上第一次暴露出了明确的 coverage boundary。

这意味着研究已经从：

```text
single-case demonstration
```

进入：

```text
generalization debugging / pipeline evaluation
```

这是比继续手工展示 Chart-13 更重要的一步。

---

### 10.2 不应该现在立刻修 Analyzer

目前已经得到了一版完整的 frozen-v0 baseline：

```text
10 patches
6 predicate available
4 predicate not found
```

如果现在直接修改 Analyzer，再重新跑，就会失去对当前版本能力边界的清晰记录。

因此更合理的顺序是：

```text
v0 baseline
→ downstream test on 6 analyzable patches
→ collect failure patterns
→ revise Analyzer systematically
→ rerun same benchmark
→ compare v0 vs revised version
```

这样后续才有真正的 ablation / improvement story。

---

### 10.3 当前 pipeline 的 bottleneck 开始清楚

当前没有出现：

```text
PROCESS_ERROR
OUTPUT_JSON_MISSING
```

4 个失败全部是：

```text
PREDICATE_NOT_FOUND
```

因此当前 Analyzer 阶段最值得优先研究的问题是：

> 如何提高 patch-guided predicate extraction coverage。

但在修改 Analyzer 之前，还需要继续观察 downstream。

原因是：

即使 Analyzer 能给出 predicate，Input Generator 也未必能处理。

因此现在不能把所有精力都放在 Analyzer。

---

### 10.4 下一阶段需要区分“无法分析”和“分析错误”

当前 `ANALYZER_FAIL` 只覆盖显式 failure。

但 SUCCESS case 中已经出现：

```text
class = handles
class = defines
class = and
```

这说明未来需要进一步区分：

```text
predicate available
```

与：

```text
metadata / structural analysis correct
```

未来可能需要更细的 validation，例如：

```text
predicate_available
class_parse_valid
method_parse_valid
change_type_valid
```

但当前 pilot 暂时不需要一次解决全部问题。

---

### 10.5 当前样本设计的优点

这 10 个 patch 相比只用 Chart-13 更有价值，因为它们：

- 跨 3 个 project；
- 跨 3 个 APR tool；
- 同时包含 correct / overfitting；
- 不包含 development case Chart-13；
- 暴露出了多种 predicate 结构。

因此它们适合作为当前 pipeline development 的小型固定 benchmark。

后续修改 Analyzer / Input Generator 时，应该继续使用同一组 patch 做 regression comparison，而不是每次换一组样本。

---

## 11. 当前实验的局限

### 11.1 样本量小

目前只有 10 个 patches。

因此：

```text
6/10 = 60%
```

只能作为 pilot observation，不能作为最终 quantitative conclusion。

### 11.2 当前 success criterion 较弱

当前 SUCCESS 只要求：

```text
predicate != None
```

并没有验证：

- predicate 是否语义正确；
- class 是否正确；
- method 是否正确；
- generated input 是否可执行；
- generated input 是否能触达 patch；
- runtime evidence 是否能揭示 overfitting。

所以当前结果只能称为：

```text
Analyzer predicate availability
```

不能称为：

```text
full pipeline success
```

### 11.3 还没有进入 Execution-aware 证据阶段

目前只是完成：

```text
Patch
→ Analyzer
```

真正研究目标仍然是：

```text
Patch
→ Analyzer
→ Input Generator
→ Execution Runner
→ Runtime Observation
→ Evidence Extractor
```

所以当前结果只是 pipeline 前端的 coverage measurement。

---

## 12. 下一步实验计划

当前建议保持 Analyzer v0 不变，继续：

### Step 1 — Input Generator generalization

只对 6 个 `ANALYZER_SUCCESS` patch 运行 Input Generator。

目标得到第二个关键数字：

```text
Input Generator coverage = ? / 6
```

重点观察：

- `==`
- `!=`
- `.length`
- `&&`
- `||`
- symbolic constant

当前 generator 能支持多少。

### Step 2 — 记录 Input Generator failure taxonomy

例如未来可能出现：

```text
UNSUPPORTED_PREDICATE
VARIABLE_TYPE_UNKNOWN
SYMBOLIC_CONSTANT_UNRESOLVED
COMPOUND_CONDITION_UNSUPPORTED
NO_INPUT_GENERATED
```

不要看到一个 case 就立即修。

先跑完整个 frozen-v0。

### Step 3 — 如果时间允许，选择 1–2 个可继续的 patch 做 Execution Runner

目标不是马上完成 10 个 patch。

而是先证明：

```text
unseen patch
→ analyzer
→ generated input
→ buggy / patched execution
→ runtime observation
```

至少能在 Chart-13 之外跑通。

---

## 13. 周二组会可以汇报的核心结论

可以简化成：

> We froze the current v0 analyzer and evaluated it on 10 unseen patches from three Defects4J projects and three APR tools. The analyzer produced usable predicates for 6 out of 10 patches. All four failures were due to missing predicate extraction rather than process or output-generation errors. The successful cases also contain more complex predicates than Chart-13, including compound conditions and symbolic constants. We will next evaluate whether the current input generator can handle these six analyzable patches before revising the analyzer.

对应的中文逻辑：

```text
Chart-13
↓
证明机制可行

10 unseen patches
↓
Analyzer v0 coverage = 6 / 10

4 failures
↓
全部是 PREDICATE_NOT_FOUND

6 success cases
↓
predicate 结构明显更复杂

Next
↓
测试 Input Generator coverage
↓
再决定如何系统性修改 Analyzer
```

---

## 14. 当前阶段一句话总结

> **当前 v0 Patch Analyzer 在 10 个 unseen patches 中为 6 个 patch 提取出了 downstream 可用的 predicate；4 个失败全部来自 predicate extraction failure。该实验首次明确暴露了 Chart-13 prototype 向 unseen patches 泛化时的 coverage 和 parsing 局限，并为下一阶段 Input Generator generalization 提供了固定 baseline。**
