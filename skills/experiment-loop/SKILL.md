---
name: experiment-loop
description: >
  Experimental research loop based on the PDCA cycle. Turn an open-ended question into a
  hypothesis → experiment → evaluate → iterate cycle with verifiable, reproducible results.
  Use when the user wants to run experiments, compare approaches with metrics, validate
  feasibility by building, or systematically explore an unfamiliar problem by trying things
  and measuring. Not limited to quantitative finance — any "hypothesis → experiment →
  evaluate → iterate" task applies.
  Keywords: run experiments, hypothesis, empirical, compare approaches with metrics,
  validate feasibility, systematic exploration.
  Do NOT use for: gathering facts from docs/APIs/sources (use the read-only `research` skill),
  direct implementation of a well-specified plan (just do it), or pure code review (use a
  code-review skill).
disable-model-invocation: true
---

# Experiment Loop: Plan-Do-Check-Act

Transform open-ended research questions into verifiable, reproducible, iterable experiments.

Core principle: **Clarify the goal before writing code. Verify every step. Let deviations drive iteration. Back conclusions with data.**

---

## Step 0: Initiation (Mandatory)

### 0.1 Confirm Iteration Budget

Before any work begins, confirm the iteration budget with the user (use `AskUserQuestion`):

> This research will involve iterative experiments. The default budget is a **minimum of 2 rounds** (ensuring at least one correction opportunity) and a **maximum of 5 rounds** (preventing unproductive loops). Would you like to adjust?

- **Minimum rounds**: Once reached, if results are still below target, report current progress and bottlenecks to the user and let them decide whether to continue
- **Maximum rounds**: Once reached, force entry into the Act phase — summarize achievements and unresolved issues, do not auto-start a new cycle

### 0.2 Produce a Research Plan

After completing the Plan phase (P1–P4), **you must present a research plan to the user** containing:

1. **Research objective** (one sentence)
2. **Action items** (decomposed into concrete steps, each with an expected deliverable)
3. **Open questions** (list points needing user clarification, use `AskUserQuestion`)

Only enter the Do phase after the user confirms the plan. If the user modifies the plan, revise first, then begin.

---

## Plan — Goal Definition & Hypothesis Modeling

### P1. Understand the Current State

Before defining a goal, build a mental model of the existing system:

- Read the target module's code; understand inputs, outputs, and dependencies
- Verify data source completeness and quality (field semantics, units, missing rates, time coverage)
- Check environment configuration (worktree / virtual env / env vars)
- If the target module is a single file >500 lines, assess whether modularization is needed

### P2. Progressive Goal Discovery

Research goals are often unclear at the outset. Use layered questioning:

```
Layer 0: What problem does the user want to solve? (Business problem, not technical)
  → "Too many ETFs — need a systematic method to select a tradeable pool"

Layer 1: What does success look like? (Quantifiable validation criteria)
  → "Pool covers broad-based + overseas + commodity + sector, no excessive overlap, forward-returns beat benchmark"

Layer 2: What are the constraints? (Hard limits)
  → "≤15 holdings, no LOFs/money-market funds, quarterly rebalancing"

Layer 3: What's wrong with the current approach? (Improvement direction)
  → "Clustering mixes broad-based and sector ETFs — one representative per mega-cluster wastes 100+ ETFs"
```

**When the goal is unclear, run a quick experiment (minimum viable validation) first and let the results help clarify the goal.** Don't speculate in a vacuum — data-driven goal refinement is more reliable than pre-set assumptions.

### P3. Form Hypotheses

State each experiment's hypothesis in one sentence:

> "Two-pass clustering (splitting the largest cluster a second time) can separate broad-based and sector ETFs, improving portfolio diversification."

Hypotheses must be **falsifiable** — you must be able to define what result would disprove them.

### P4. Experiment Design

- Define a **control group**: at least one baseline approach for comparison
- Define **evaluation metrics**: what data will determine success/failure
- Define the **time range**: how much historical data, how many periods
- Define **termination conditions**: when to stop, when to switch approaches

**Key deliverable**: An experiment plan document (written to a plan file or TODO list) containing:

1. Goal statement (quantifiable)
2. Hypothesis list
3. Control/baseline approach
4. Evaluation metrics and thresholds
5. Estimated number of experiment rounds

---

## Do — Algorithmic Experiments & Implementation

### D1. Fix Before Researching

Before experimenting on existing code, ensure there are no known bugs:

- Calculation logic: off-by-one errors, numeric overflow, boundary conditions
- Data flow: filter ordering, missing-value handling, time alignment
- Consistency: do comments match the code?

**Layering experiments on top of buggy code = building a house on sand.**

### D2. Rapid Prototype

Run a **single-period / single-shot** experiment first to verify the algorithm produces reasonable output:

- Are cluster/class counts in a reasonable range (not 1, not N-1)?
- Spot-check: do members of a cluster make semantic sense?
- Is the noise/outlier ratio acceptable?

**Never** start with a 24-quarter rolling backtest. Validate logic on 1 period first.

### D3. Parameter Exploration

Explore key parameters, but **understand why before deciding the search range**:

```
❌ Blind grid search: eps from 0.01 to 0.20 step 0.01
✅ Run 3-5 representative values → understand the trend → narrow the range → fine-tune
```

### D4. Log Every Experiment

Each experiment must be recorded:

| Field         | Content                                                                |
| ------------- | ---------------------------------------------------------------------- |
| Experiment ID | EXP-001                                                                |
| Hypothesis    | Two-pass clustering separates broad-based from sector                  |
| Parameters    | eps=0.03, min_cluster_size=3                                           |
| Result        | Broad-based split into 8 sub-clusters ✓, sectors correctly separated ✓ |
| Conclusion    | Hypothesis confirmed, proceed to next step                             |
| Duration      | 2 minutes                                                              |

### D5. Multi-Approach Parallel

Implement 2-4 approaches simultaneously to ensure comparison:

- Each approach runs independently, no shared intermediate state
- Output in a unified evaluation format (same metrics, same time range)
- **Never draw conclusions from a single approach**

---

## Check — Evaluation & Deviation Analysis

### C1. Forward Validation

Validate algorithm effectiveness using **future data** to avoid overfitting:

- Calculation window (past) and evaluation window (future) must be strictly separated
- At least 8-12 periods of forward data for statistically meaningful conclusions
- Compare against a benchmark (market index / equal-weight / random selection)

### C2. Structural Validation

Beyond numeric metrics (return, risk), verify **structural soundness** of the output:

- Does it cover all required categories?
- Are there redundant overlaps (same index, different issuers = ineffective diversification)?
- Are there obvious gaps (an entire asset class missing)?

### C3. Deviation Analysis

Compare Plan-phase expectations with Do-phase actual results:

| Deviation Type           | Symptom                                   | Response                                                 |
| ------------------------ | ----------------------------------------- | -------------------------------------------------------- |
| **Goal deviation**       | Output doesn't meet user's real needs     | Revise goal, return to Plan                              |
| **Hypothesis deviation** | Results contradict the hypothesis         | Analyze root cause, revise hypothesis or switch approach |
| **Parameter deviation**  | Parameters have no sensitivity            | Switch to a more discriminative parameter dimension      |
| **Effect deviation**     | Approach works but magnitude is too small | Evaluate marginal value, decide whether to continue      |

### C4. Outlier Investigation

Identify periods/samples that deviate significantly from the mean:

- Sudden return drop in one period → systemic market factor or selection problem?
- Clustering structure change in one period → data quality issue or real structural shift?
- Outliers often reveal hidden assumption flaws

### C5. Decision Gate

Make a decision based on evaluation results:

```
Effective and stable     → Proceed to Act (document + solidify)
Effective but unstable   → Return to Do (tune parameters / add constraints)
Ineffective              → Return to Plan (revise goal / change hypothesis)
Completely wrong         → Return to Plan (redefine the problem)
```

---

## Act — Documentation, Solidification & Direction

### A1. Research Log

Write a `RESEARCH_LOG.md` in the module directory — this is one of the most important deliverables:

- **What was tried**: core idea and parameters of each approach
- **What were the results**: quantified conclusions (returns, excess, coverage, etc.)
- **What was discarded**: which approaches were eliminated and why (**most important** — prevents others from repeating dead ends)
- **Code changes**: which files were modified and how
- **Future directions**: guide the next person/agent who picks up the work

### A2. Conventions Document

When domain-specific concepts are involved, write a `CONVENTIONS.md`:

- Terminology definitions (rebalance date, calculation window, forward period, etc.)
- Timeline mapping tables
- Naming conventions

### A3. Deliverable Solidification

Solidify validated approaches into:

- Reproducible scripts/modules (not notebooks)
- Structured output files (Excel/CSV/YAML)
- Configuration files ready for the next pipeline step

### A4. Direction Guidance

In the research log, clearly annotate future research directions, prioritized:

```
1. [High] Independent per-group clustering — cluster each group separately to avoid cross-contamination
2. [Medium] Adaptive parameters — auto-tune clustering params based on market regime
3. [Low] Event-driven rebalancing — only rotate pool when cluster structure changes significantly
```

Annotate each direction with: estimated effort, dependencies, potential risks.

---

## Iteration Rules

PDCA is not a one-shot process — it is an upward spiral:

```
Plan (goal definition)
  ↓
Do (experiment execution) ←───────┐
  ↓                               │
Check (deviation analysis) ──────→│ fix params/hypothesis
  ↓                               │
Act (document & solidify)         │
  ↓                               │
New Plan (revised goal) ──────────┘
```

### Iteration Budget

Iterations are bounded by the budget confirmed in Step 0:

- **Below minimum rounds**: even if results meet target, complete at least the minimum (ensures thorough exploration)
- **At minimum rounds**: if results are still below target, report progress and bottlenecks to the user; let them decide whether to continue or stop
- **At maximum rounds**: force entry into Act phase — summarize achievements and unresolved issues, do not auto-start a new cycle
- **User may terminate at any time**: regardless of when the user stops, always execute one Act round for documentation

### Iteration Triggers

- Check phase reveals deviation → return to Do or Plan
- User provides new information/constraints → return to Plan
- Current approach validated as effective → enter Act; if rounds remain, start the next research goal

### Stop Conditions

- Goal achieved and minimum rounds exhausted (Act complete)
- Maximum rounds reached
- Goal proven infeasible (Act documents the reason)
- User explicitly requests to stop

---

## Common Mistakes

| Mistake                                             | Consequence                              | Correct Practice                                                |
| --------------------------------------------------- | ---------------------------------------- | --------------------------------------------------------------- |
| Writing code before defining the goal               | Half a day wasted in the wrong direction | Plan first; use quick experiments to clarify goals when unclear |
| Running only one approach                           | Cannot judge relative quality            | At least 2 approaches + 1 baseline for comparison               |
| Evaluating on historical data and claiming validity | Overfitting                              | Validate on forward (future) data                               |
| Insufficient samples (<8 periods)                   | Statistically meaningless                | Accumulate enough samples before concluding                     |
| Blind parameter search                              | Wasted time                              | Understand trends first, then fine-tune                         |
| Not logging discarded approaches                    | Others repeat the same dead ends         | Record every discarded approach with the reason                 |
| Single file grows to 1000+ lines                    | Unmaintainable                           | Modularize when >500 lines                                      |
| Experimenting on buggy code                         | Results unreliable                       | Fix known bugs before starting research                         |

## Related Skills

- **writing-plans** — Structure the research plan as a formal plan document
- **verification-before-completion** — Run verification before claiming completion
- **systematic-debugging** — Systematic approach to bug investigation
- **research** — Background knowledge research from high-trust sources (read-only, no code changes)
