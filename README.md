# CodeSense – A Smart Code Complexity Analyzer Using AVL Trees

## Description
CodeSense is a desktop code-complexity analyzer for Java, Python, and JavaScript. It performs lightweight structural parsing of source files and indexes every analyzed method inside a **custom, zero-dependency AVL tree**, keyed by complexity score. This keeps lookups, range queries, and max/min queries bounded at `O(log n)`, even as a codebase grows. CodeSense ships as a single runnable jar with both a full Swing GUI and a headless CLI.

## Features

### Analysis engine
- **Multi-language scanning** — Java, Python, and JavaScript source files via a lightweight regex/brace-matching structural parser (`CodeParser`).
- **Complexity metrics** — Cyclomatic Complexity (CC), Cognitive Complexity (Cog), Maximum Nesting Depth (MND), Maintainability Index (MI), and an overall Risk Level per method.
- **Custom AVL tree** — Self-balancing (LL/RR/LR/RL rotations), guarantees `O(log n)` height; used in place of `java.util.TreeMap` for all core indexing.
- **Max/min & exact-match queries** — `O(log n)` retrieval of the most/least complex methods, or methods matching an exact score.
- **Range queries** — `O(log n + k)` retrieval of all methods between two complexity bounds, with subtree pruning (see `VIVA_NOTES.md`).
- **Threshold alerts** — Configurable danger thresholds trigger automated refactor suggestions (`AlertManager`).

### Productivity features (plan-gated — see Licensing below)
- Technical debt estimation and ROI calculator
- Refactoring simulator / "what-if" impact panel
- Codebase diff engine and version-to-version comparison
- Quality gate engine for CI/CD pipelines
- Unit test generator
- Team dashboard and leaderboard
- Snapshot manager for point-in-time codebase health tracking

### Interfaces
- **GUI** — Swing-based dashboard with dedicated panels for the AVL playground, dependency graphs, heatmaps, radar charts, security matrix, design-pattern advisor, roadmap timeline, and more (see `src/com/codesense/ui/`).
- **CLI** — Interactive shell and direct single-shot pipeline execution.
- **Reporting** — Export to HTML, Markdown, JSON, CSV, and SARIF.

## Licensing tiers
CodeSense gates certain features behind a local license file, validated via RSA-signed payloads (`LicenseManager` / `LicenseCrypto`):

| Plan | Access |
|---|---|
| Free | Core scanning, complexity metrics, AVL indexing, alerts |
| Pro | + Technical debt estimator, ROI calculator, refactor simulator, advanced analysis |
| Team | + Team dashboard, CI/CD quality gates |
| Enterprise | + Enterprise controls |
| Education | + Education dashboard, all Pro-tier features |

License state is stored locally under `.codesense_db/`. `src/scratch/KeyGen.java` is a **dev-only utility** used to generate the signed sample license payloads that seed the demo license database during development/testing — it is not part of the shipped application.

## How to Build
```cmd
make.bat clean
make.bat compile
```

## How to Test
All JUnit 5 tests run automatically against the compiled binaries:
```cmd
make.bat test
```

## How to Package
```cmd
make.bat package
```
Produces the standalone `codesense.jar`.

## How to Run

**GUI**
```cmd
java -jar codesense.jar
# OR
make.bat run
```

**CLI**
```cmd
java -jar codesense.jar --cli
# OR
make.bat run --cli

# Direct headless analysis of a file or folder
java -jar codesense.jar --analyze samples
```

**Help**
```cmd
java -jar codesense.jar --help
```

## Custom AVL Tree Architecture
CodeSense deliberately avoids `java.util.TreeMap` in favor of a hand-built AVL tree. Every time the analyzer discovers a method, it's inserted into the tree keyed by its complexity score, with continuous, real-time rebalancing through LL/RR/LR/RL rotations. This guarantees `O(log n)` bounds for max/min extraction, exact search, and range queries — e.g. "give me every method scoring between 50 and 100" — without ever degrading into a linear scan, even on pathologically sorted input. See `VIVA_NOTES.md` for the full rotation logic and benchmark numbers (AVL stays at height 14 for 10,000 sorted inserts, vs. a plain BST degenerating to height 10,000).

## Project Structure
