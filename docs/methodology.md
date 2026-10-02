# Methodology

## Prototype workflow

1. Select an APR patch that passes the original tests.
2. Check out the buggy version and apply the patch.
3. Compile and run the original tests to confirm plausibility.
4. Inspect the patch diff and identify the affected behavior.
5. Design a targeted probe for that behavior.
6. Execute the same probe on the patched candidate and developer-fixed reference.
7. Record and compare observable behavior.

## Evidence to record

| Field | Meaning |
| --- | --- |
| Patch and bug identifiers | Connect the observation to its candidate and defect |
| Probe input | Reproduce the exercised behavior |
| Exception type | Compare whether and how execution throws |
| Output value | Compare observable returned or printed values |
| Probe outcome | Record the assertion result |
| Execution status | Distinguish a completed run from compilation failure or timeout |
| Reference comparison | Describe the candidate/reference behavioral difference |

Outputs and exceptions are candidates for structured features. Coverage and state differences require additional instrumentation.

## Interpretation

The developer-fixed version is an evaluation reference in the current prototype. A deployable method cannot assume this reference is always available; that assumption must be made explicit in experiments.

A behavioral difference is evidence to inspect, and a matching outcome only describes the tested input. Neither observation alone proves correctness for all inputs.

The existing prototype extracts signals manually. Automatic probe generation and a trained APCA classifier are future work.

See [prototype notes](prototype/Prototype&Attention_A.md) for the source workflow and case details.
