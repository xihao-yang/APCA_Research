# Experiment design

This is a proposed evaluation design, not a report of completed experiments.

## Research questions

- Does execution evidence improve assessment over static patch information?
- Which kinds of probes reveal incorrect behavior?
- How much does the result depend on repair tool and probe selection?

## Baselines and ablations

| Variant | Input |
| --- | --- |
| Static-only | Buggy code, candidate code, or patch diff |
| Execution-only | Structured probe observations |
| Combined | Static information plus execution evidence |
| Exceptions removed | Combined input without exception features |
| Outputs removed | Combined input without output features |

Use comparable data partitions and classifiers where possible so differences can be attributed to the available evidence.

## Dataset and evaluation

Record the bug ID, project, repair tool, patch ID, correctness label source, and original-test outcome. Keep patches for the same bug together when making train/test partitions to reduce leakage.

Report the class definition, confusion matrix, precision, recall, and F1. Also report how many candidates could be applied, compiled, and probed. A failed run should not silently become a correctness label.

Evaluate on held-out bugs and, where data permits, held-out repair tools. Report manually chosen probes separately from automatically generated ones.

## Pilot

Begin with Chart-13. Retain the Range-construction probe for AVATAR and add layout probes for BorderArrangement changes. Record what each probe targets and which patches it reveals.

## Limitations to track

- Dependence on the developer-fixed reference.
- Manual probe selection and limited input coverage.
- Uncertain or externally supplied patch labels.
- Missing execution outcomes and tool-specific failures.
- Small pilot size and limited generalization.

Historical metrics remain in [experiment records](../experiments/reproduction/CACHE/). They require validation before supporting conclusions.
