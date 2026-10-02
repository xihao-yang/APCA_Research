# APCA-Research

My research workspace for Automated Program Repair (APR) and Automated Patch Correctness Assessment (APCA).

The research question is how execution evidence can help identify incorrect patches that pass the original tests. This repository collects paper reading, prototype notes, experiment records, and research reflections.

## Paper reading

| Topic | Contents |
| --- | --- |
| [APR](papers/APR/README.md) | Automated program repair reading |
| [APCA](papers/APCA/README.md) | Patch correctness assessment reading |
| [CACHE](papers/CACHE/README.md) | Paper PDF and links to reproduction records |
| [APPT](papers/APPT/README.md) | Paper PDF and reading notes |
| [CHATASSERT](papers/CHATASSERT/README.md) | Test oracle generation reading notes |
| [PRISM](papers/PRISM/README.md) | Reserved for future reading notes |

## Research records

- [Chart-13 demo](experiments/chart-13/demo/README.md): existing behavior-check logs.
- [Prototype notes](docs/prototype/): probe design, AVATAR, and BorderArrangement notes.
- [CACHE reproduction](experiments/reproduction/CACHE/README.md): reproduction notes, snapshots, logs, and a preliminary result file.
- [Defects4J](experiments/defects4j/README.md): workspace scaffold and earlier experiment records.
- [CodeBERT](experiments/codebert/): earlier learning workspace and its original documentation.
- [Meetings](meetings/README.md): dated meeting files imported from the former meeting repository.
- [Reflections](reflections/): research plan review and reflections.
- [Background reading](papers/background/): other paper-reading folders.

## Current scope

The prototype records manually designed, patch-targeted behavior probes. It does not establish a complete APCA classifier or general effectiveness across patches. Some imported files are placeholders, and older experiment READMEs may describe planned code that is not present. The migration does not rerun or validate experiment results.

## Organization

| Path | Purpose |
| --- | --- |
| [papers/](papers/README.md) | APR, APCA, CACHE, APPT, CHATASSERT, and PRISM reading |
| [experiments/](experiments/) | Defects4J, Chart-13, reproduction, and CodeBERT work |
| [scripts/](scripts/README.md) | Shared runners and analysis scripts |
| [results/](results/README.md) | Consolidated summaries and links to existing results |
| [meetings/](meetings/README.md) | Records grouped under 2026-05, 2026-06, and 2026-07 |
| [reflections/](reflections/) | Research reflections |
| [docs/](docs/) | [Research plan](docs/research-plan.md), [methodology](docs/methodology.md), and [experiment design](docs/experiment-design.md) |

Paper PDFs and reading notes belong in `papers/`. Experiment code, logs, and reproduction records belong in `experiments/`. Meeting records belong in `meetings/`, and research reflections belong in `reflections/`.

See [migration notes](docs/migration/README.md) for source revisions and the file mapping. Original licenses are retained with their source material; paper PDFs remain subject to their respective authors' and publishers' rights.
