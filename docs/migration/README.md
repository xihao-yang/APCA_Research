# Migration record

This workspace consolidates the default-branch contents of `AI_Research`, `Paper_close_Reading`, `Meeting-minutes-Reflections`, and `Defects4J`.

[manifest.json](manifest.json) records every source file, its source commit, destination, Git blob SHA, and SHA-256 checksum. Byte-identical files sharing a destination were deduplicated. All source files remain represented; imported source content was not edited.

- `AI_Research` remains the base repository, retaining its existing commit history.
- Contents from the other three repositories are imported as snapshots. Their original commit histories remain in the original repositories; this migration does not merge those histories into the base repository.
- Original top-level READMEs are in [original-readmes](original-readmes/).
- Original licenses are in [licenses](licenses/). No new blanket license is imposed on the combined repository.
- CACHE's duplicate PDF is stored once in `papers/CACHE/`.
- Blank paper index files are retained as `notes.md`. README casing is normalized for Windows compatibility.
- The PRISM folder is new and contains only a landing page.
- CHATASSERT image references point to images absent from the source repository.
- Source repositories are not deleted or archived by this migration.

Paths have changed. Any external local scripts that reference the old workspace paths may need updating. The migration checks file preservation and documentation links; it does not execute experiments.
