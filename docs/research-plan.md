# Research plan

This working plan summarizes the repository's existing prototype notes. Proposed work below has not yet been completed.

## Question

Can execution evidence help identify incorrect patches that pass the original tests?

## Current evidence

The Chart-13 prototype uses a manually designed probe, `new Range(2.0, 1.0)`, to exercise behavior changed by an AVATAR patch. The prototype notes record that the patched version accepts this invalid range while the developer-fixed version rejects it with an exception.

This probe targets Range construction. Other Chart-13 patches modify BorderArrangement and require layout-oriented probes. Failure to reveal a difference does not establish patch correctness.

## Next work

1. Inventory the available patches, their modified methods, and known labels.
2. Add targeted layout probes for BorderArrangement.
3. Store probe inputs, execution outcomes, and comparison evidence in a consistent format.
4. Implement and evaluate static-only, execution-only, and combined assessment baselines.
5. Check how results vary by probe type and repair tool.

## Records

- [Methodology](methodology.md)
- [Experiment design](experiment-design.md)
- [Original prototype notes](prototype/Prototype&Attention_A.md)
- [Research reflections](../reflections/)
