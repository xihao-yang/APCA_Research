# about

Yamasaki -anti LLMs

Koumei -Linux Source Code

Font size -black:bullets


# 1. Prototype Goal

写清楚当前 prototype 只验证什么：

The Phase-1 prototype tests whether execution-based behavior probes can expose behavioral deviations in plausible overfitting patches that pass the original Defects4J tests.

同时写明不验证：

full APCA classifier
automatic probe generation
oracle generation
calibration layer
general effectiveness

# 2. Exact Workflow

用一条短流程：

Select plausible patch
→ checkout buggy version
→ apply APR patch
→ compile and run original tests
→ inspect patch diff
→ identify affected behavior
→ design targeted probe
→ run on patched candidate and developer fixed version
→ compare observable outputs

这比 Step3、Step4、Step5 更适合 defence。

# 3. What the Model/System Actually Sees

虽然当前还没有模型，也要写清楚未来候选 execution signals：

exception observed
output value
NaN / infinity status
probe pass/fail
patched-vs-fixed output match
behavioral deviation flag

并注明：

These signals are currently extracted manually in the prototype and are not yet integrated into a learning-based classifier.

# 4. Chart-13 Case Study

这是你最需要的核心段落。

Patch change

AVATAR 删除：

throw new IllegalArgumentException(msg);
Affected behavior
Range(lower, upper) should reject invalid input when lower > upper.
Probe
new Range(2.0, 1.0)
Why this probe
2.0 > 1.0 directly exercises the invalid-input boundary modified by the patch.
Result
Patched candidate:
created_range=Range[2.0,1.0]
AssertionError

Developer fixed:
PASS_THROW
Interpretation

The patch passes the original tests but accepts an invalid Range that the developer fixed version rejects.

# 5. Important Correction About the Five Chart-13 Patches

这一节必须加入，因为这是目前最关键的分析结果。

写：

The current Range(2.0, 1.0) probe is directly targeted only at the AVATAR patch because AVATAR modifies Range.java. The other four patches modify BorderArrangement.java, so they require layout-oriented probes.

然后列：

AVATAR → invalid Range exception behavior
Arja-plusible → rightBlock state/layout
Cardumen → fixed-constraint arrangement
DynaMoth → leftBlock arrangement skipped
FixMiner → leftBlock skipped when rightBlock exists

最后注明：

“Not detected” does not mean correct. It only means the current probe does not target or expose that patch’s modified behavior.

这一节比大部分命令都重要。

# 6. Evidence Strength and Limitation

写成 defence 风格：

What this prototype supports

A manually designed, patch-targeted execution probe can expose a behavioral deviation in at least one plausible overfitting patch that passes the original tests.

What it does not support

It does not yet show that behavior probes generalize across patches, that probe coverage is sufficient, or that execution-aware APCA outperforms static baselines.

# 7. Likely Professor Questions and Safe Answers

建议至少放这 5 个。

Why is the probe not arbitrary?

I inspected the patch diff first and designed the probe around the behavior modified by the patch.

Why compare with the developer fixed version?

The developer fixed version provides the expected reference behavior for the same probe input.

Why are four patches not detected?

Because the current probe targets invalid Range construction, while those patches modify BorderArrangement layout behavior.

Does not detected mean correct?

No. It only means the current probe did not expose a behavioral difference.

What is the next step?

Design patch-targeted layout probes for the BorderArrangement patches, then convert their outputs into structured execution-aware signals.