# Requirements: Claude Code Critical-Doers Framework

## 1. Proposed repository name

Recommended repo name:

```text
claude-code-critical-doers
```

Description:

```text
A Claude Code skills and subagents framework for critical-task multi-doer implementation, differential verification, evals, and deterministic release gates.
```

Alternative names:

```text
claude-code-nversion
claude-code-multidoer
claude-code-critical-path
agentic-nversion-dev
llm-nversion-dev
```

The recommended name is intentionally practical and explicit.

---

## 2. Purpose

Build a test framework for Claude Code that evaluates whether **multiple independent doers** improve reliability on critical software tasks compared with a standard single-agent workflow.

The framework should support:

```text
Frozen spec
→ multiple independent doers
→ independent tests
→ differential comparison
→ adversarial verification
→ deterministic gates
→ UI review
→ eval scoring
```

The framework is not intended to prove that LLMs become deterministic. It is intended to test whether an engineered workflow can reduce probabilistic failure modes enough to be useful for real development.

---

## 3. Research framing

The framework is inspired by:

1. **Quantum/classical analogy**  
   Reliable macroscopic behavior can emerge from probabilistic microscopic events through constraints, averaging, environmental stabilization, and measurement.

2. **N-version programming**  
   Multiple independently produced program variants reduce the chance of shared fault modes.

3. **LLM self-consistency and test-time scaling**  
   Multiple attempts, diversified rollouts, verifiers, and selection strategies can improve LLM/agent outcomes.

4. **Multi-agent software generation**  
   Agent roles such as programmer, test designer, executor, verifier, and repair agent can outperform a single monolithic agent when coordinated properly.

Key references:

- Galápagos: Automated N-Version Programming with LLMs  
  https://arxiv.org/abs/2408.09536

- Enhancing LLM Code Generation with Ensembles  
  https://arxiv.org/abs/2503.15838

- AgentCoder: Multi-Agent-based Code Generation with Iterative Testing and Optimisation  
  https://arxiv.org/abs/2312.13010

- CodeCoR: An LLM-Based Self-Reflective Multi-Agent Framework for Code Generation  
  https://arxiv.org/abs/2501.07811

- Scaling Test-time Compute for LLM Agents  
  https://arxiv.org/abs/2506.12928

- EvalPlus / rigorous LLM code evaluation  
  https://arxiv.org/abs/2305.01210

---

## 4. Claude Code platform assumptions

The framework should use current Claude Code primitives:

### 4.1 Subagents

Claude Code supports custom subagents for specialized workflows and context management.

Reference:

https://docs.anthropic.com/en/docs/claude-code/sub-agents

Framework use:

```text
- planner
- spec-freezer
- doer-a
- doer-b
- optional doer-c
- independent-test-designer
- differential-comparator
- adversarial-verifier
- fixer
- final-reviewer
- eval-judge
```

### 4.2 Skills

Claude Code skills are packaged as directories containing `SKILL.md` files with YAML frontmatter and markdown instructions.

Reference:

https://docs.anthropic.com/en/docs/claude-code/skills

Framework use:

```text
- critical-task-workflow
- spec-freezing
- multi-doer-implementation
- independent-test-design
- differential-verification
- adversarial-testing
- eval-harness
- ui-reporting
- release-gate
```

### 4.3 Hooks

Claude Code hooks can run automation at key lifecycle points, including validation and enforcement.

Reference:

https://docs.anthropic.com/en/docs/claude-code/hooks-guide

Framework use:

```text
- prevent forbidden commands
- prevent direct merge on critical path
- validate required artifacts exist
- run fast tests after edits
- block completion if final-review.md is missing
- block release if eval artifacts are missing
```

### 4.4 Project files

Claude Code can read project-level files from `.claude/`.

Reference:

https://code.claude.com/docs/en/claude-directory

Framework use:

```text
.claude/
  agents/
  skills/
  settings.json
  hooks/
```

---

## 5. Goals

### 5.1 Primary goal

Evaluate whether a multi-doer critical-task workflow improves reliability over a single-doer Claude Code workflow.

### 5.2 Engineering goals

The framework must:

```text
1. Classify critical tasks.
2. Freeze a canonical spec.
3. Spawn isolated doers.
4. Prevent doers from seeing each other’s work before comparison.
5. Generate tests independently from the implementation.
6. Run deterministic validation.
7. Compare variants structurally and behaviorally.
8. Generate an evidence-based final recommendation.
9. Provide a UI/report for human review.
10. Record eval metrics for each run.
```

### 5.3 Research/eval goals

The framework must answer:

```text
- Does multi-doer improve hidden-test pass rate?
- Does multi-doer reduce critical defects?
- Does multi-doer increase useful disagreement?
- Does the comparator choose the best implementation?
- Does adversarial verification find issues missed by normal tests?
- Is the added cost justified for critical tasks?
```

---

## 6. Non-goals

The MVP should not attempt to:

```text
- replace human approval for critical changes
- auto-merge production-critical code
- solve all coding tasks
- handle huge refactors initially
- perform production DB migrations
- rely on majority vote alone
- rely on self-review alone
- build a general-purpose IDE replacement
```

---

## 7. Criticality classification

The system must classify a task as **critical** if it touches any of these areas:

```text
- authentication
- authorization
- payments / money movement
- financial calculations
- pricing logic
- PII / sensitive data
- secrets
- security controls
- production config
- data deletion
- database migrations
- public API contract
- concurrency / locking
- infra provisioning
- CI/CD release logic
- large cross-module refactor
- generated SQL
- legal/compliance-facing output
```

Classification output:

```yaml
criticality:
  level: normal | elevated | critical
  reasons:
    - string
  required_path: normal | critical
  human_approval_required: true | false
```

---

## 8. Workflow overview

### 8.1 Normal path

For non-critical tasks:

```text
Planner
→ Doer
→ Test Designer
→ Reviewer
→ CI
```

### 8.2 Critical path

For critical tasks:

```text
Planner
→ Spec Freezer
→ Doer A
→ Doer B
→ optional Doer C
→ Independent Test Designer
→ Test Executor
→ Differential Comparator
→ Adversarial Verifier
→ Fixer
→ Final Reviewer
→ Eval Judge
→ UI Report
→ Human Approval
```

---

## 9. Required artifacts

Each critical task must produce a run directory:

```text
.ai/critical-runs/<run-id>/
```

Required structure:

```text
.ai/critical-runs/<run-id>/
  request.md
  classification.yaml
  spec.yaml
  doer-a/
    assumptions.md
    plan.md
    patch.diff
    changed-files.txt
    test-output.txt
    final-summary.md
  doer-b/
    assumptions.md
    plan.md
    patch.diff
    changed-files.txt
    test-output.txt
    final-summary.md
  doer-c/
    assumptions.md
    plan.md
    patch.diff
    changed-files.txt
    test-output.txt
    final-summary.md
  tests/
    independent-test-plan.md
    generated-tests.patch
    test-matrix.yaml
    hidden-test-placeholders.md
  comparison/
    structural-diff.md
    behavioral-diff.md
    assumptions-comparison.md
    variant-scorecard.yaml
  verification/
    adversarial-findings.md
    security-checklist.md
    additional-tests.patch
  final/
    selected-variant.md
    final-review.md
    decision.md
    residual-risk.md
  eval/
    eval-input.json
    eval-result.json
    metrics.yaml
  ui/
    report.html
    report.json
```

---

## 10. Canonical spec format

The spec file is the source of truth.

File:

```text
.ai/critical-runs/<run-id>/spec.yaml
```

Schema:

```yaml
task_id: string
title: string
created_at: string
risk_level: normal | elevated | critical

goal: string

scope:
  include:
    - string
  exclude:
    - string

non_goals:
  - string

affected_areas:
  - string

allowed_files:
  - string

forbidden_files:
  - string

acceptance_criteria:
  - id: AC-001
    description: string
    verification: test | review | static-analysis | manual

required_tests:
  - id: T-001
    description: string
    type: unit | integration | contract | security | regression | property

security_considerations:
  - string

data_considerations:
  - string

rollback_plan:
  - string

open_questions:
  - id: Q-001
    question: string
    resolution: string

definition_of_done:
  - string
```

Requirements:

```text
- Doers must implement the frozen spec, not the original chat request.
- Any ambiguity must be recorded in open_questions.
- If an ambiguity materially changes behavior, the system must block implementation until resolved or explicitly assume a safe default.
```

---

## 11. Subagent requirements

### 11.1 `critical-task-planner`

Purpose:

```text
Classify the task and produce initial plan.
```

Allowed tools:

```text
Read, Grep, Glob
```

Disallowed:

```text
Write/Edit/MultiEdit/Bash that changes files
```

Outputs:

```text
classification.yaml
request.md
planner-notes.md
```

---

### 11.2 `spec-freezer`

Purpose:

```text
Convert user request and planner notes into frozen spec.yaml.
```

Allowed tools:

```text
Read, Grep, Glob, Write
```

Rules:

```text
- Must produce spec.yaml.
- Must include acceptance criteria.
- Must include non-goals.
- Must include forbidden changes.
- Must mark unresolved ambiguities.
```

---

### 11.3 `doer-a`

Role:

```text
Minimal safe implementation.
```

Prompt strategy:

```text
Implement the smallest correct change satisfying the frozen spec. Avoid unrelated refactors.
```

Outputs:

```text
assumptions.md
plan.md
patch.diff
changed-files.txt
test-output.txt
final-summary.md
```

---

### 11.4 `doer-b`

Role:

```text
Maintainability-first implementation.
```

Prompt strategy:

```text
Implement the spec with clear structure, maintainability, and explicit edge-case handling.
```

Outputs same as `doer-a`.

---

### 11.5 Optional `doer-c`

Role:

```text
Defensive edge-case implementation.
```

Prompt strategy:

```text
Assume edge cases will break naive solutions. Implement defensively while staying in scope.
```

Use only when:

```text
- task is high criticality
- prior runs show high disagreement
- domain is security/payment/auth/data
```

---

### 11.6 `independent-test-designer`

Purpose:

```text
Generate tests from frozen spec before reading doer implementations where possible.
```

Rules:

```text
- Must not copy implementation logic.
- Must map every test to acceptance criteria.
- Must include negative tests when relevant.
- Must identify missing testability.
```

Outputs:

```text
independent-test-plan.md
generated-tests.patch
test-matrix.yaml
```

---

### 11.7 `test-executor`

Purpose:

```text
Run deterministic checks against each variant.
```

Checks:

```text
- existing test suite
- generated tests
- type checks
- lint
- formatting check
- contract tests
- security/secret scan if configured
```

Outputs:

```text
test-output.txt
test-matrix.yaml
```

---

### 11.8 `differential-comparator`

Purpose:

```text
Compare doer outputs.
```

Comparison dimensions:

```text
- files changed
- lines changed
- assumptions
- acceptance criteria coverage
- test results
- behavioral divergence
- complexity
- security risk
- blast radius
- rollback simplicity
```

Outputs:

```text
structural-diff.md
behavioral-diff.md
assumptions-comparison.md
variant-scorecard.yaml
```

Required scorecard fields:

```yaml
variants:
  doer-a:
    tests_passed: true
    acceptance_coverage: 0.0
    complexity_score: 0
    blast_radius_score: 0
    security_risk_score: 0
    recommendation: accept | reject | needs-repair
  doer-b:
    tests_passed: true
    acceptance_coverage: 0.0
    complexity_score: 0
    blast_radius_score: 0
    security_risk_score: 0
    recommendation: accept | reject | needs-repair

selected_variant: doer-a | doer-b | doer-c | none
selection_reason: string
```

---

### 11.9 `adversarial-verifier`

Purpose:

```text
Try to break the selected candidate.
```

Responsibilities:

```text
- Challenge assumptions.
- Generate edge cases.
- Look for security issues.
- Look for data corruption.
- Look for race conditions.
- Look for missing authorization checks.
- Look for rollback failure.
- Propose additional discriminative tests.
```

Outputs:

```text
adversarial-findings.md
additional-tests.patch
security-checklist.md
```

Finding format:

```yaml
id: AV-001
severity: low | medium | high | critical
category: security | correctness | regression | data | operations | scope
description: string
evidence: string
recommended_action: string
blocks_release: true | false
```

---

### 11.10 `fixer`

Purpose:

```text
Apply minimal fixes to the selected candidate.
```

Rules:

```text
- Fix only verifier/comparator findings.
- No unrelated refactors.
- Every change must reference finding ID.
- Must rerun relevant tests.
```

---

### 11.11 `final-reviewer`

Purpose:

```text
Produce final decision package.
```

Checks:

```text
- acceptance criteria coverage
- all blocking findings resolved
- deterministic checks passed
- forbidden files untouched
- rollback documented
- eval artifacts recorded
```

Outputs:

```text
final-review.md
decision.md
residual-risk.md
```

Decision values:

```text
approve-for-human-review
reject
needs-more-work
needs-human-decision
```

---

### 11.12 `eval-judge`

Purpose:

```text
Evaluate the framework run.
```

Responsibilities:

```text
- compare final result to expected outcome
- score quality and reliability
- record costs/time if available
- classify failures
- update aggregate metrics
```

Outputs:

```text
eval-result.json
metrics.yaml
```

---

## 12. Skills requirements

Skills live under:

```text
.claude/skills/
```

### 12.1 `critical-task-workflow`

Purpose:

```text
Defines when and how to run the critical path.
```

Must include:

```text
- criticality rules
- artifact requirements
- workflow steps
- blocking conditions
- completion criteria
```

### 12.2 `spec-freezing`

Purpose:

```text
Reusable instructions for creating spec.yaml.
```

Must include:

```text
- spec schema
- acceptance criteria examples
- non-goal examples
- ambiguity handling
```

### 12.3 `multi-doer-implementation`

Purpose:

```text
Reusable rules for isolated doer work.
```

Must include:

```text
- isolation rules
- no-cross-contamination rule
- branch/worktree convention
- assumptions.md template
- patch.diff output requirement
```

### 12.4 `independent-test-design`

Purpose:

```text
Generate tests from spec, not implementation.
```

Must include:

```text
- mapping tests to acceptance criteria
- negative/edge-case prompts
- property test guidance
- hidden-test thinking
```

### 12.5 `differential-verification`

Purpose:

```text
Compare variants.
```

Must include:

```text
- scorecard schema
- structural comparison checklist
- behavioral comparison checklist
- assumption comparison checklist
```

### 12.6 `adversarial-testing`

Purpose:

```text
Try to break the selected variant.
```

Must include:

```text
- security checklist
- edge-case generation rules
- discriminative test generation
- release-blocking criteria
```

### 12.7 `eval-harness`

Purpose:

```text
Evaluate framework effectiveness.
```

Must include:

```text
- baseline definitions
- metric definitions
- run schema
- scoring rubric
- failure taxonomy
```

### 12.8 `ui-reporting`

Purpose:

```text
Generate human-readable report artifacts.
```

Must include:

```text
- report.json schema
- report.html requirements
- scorecard display
- decision display
```

### 12.9 `release-gate`

Purpose:

```text
Final gate before merge.
```

Must include:

```text
- required artifacts
- required checks
- blocking findings
- human approval rule
```

---

## 13. Repository layout

Recommended initial structure:

```text
claude-code-critical-doers/
  README.md
  LICENSE
  docs/
    concept.md
    architecture.md
    eval-plan.md
    ui-design.md
  .claude/
    agents/
      critical-task-planner.md
      spec-freezer.md
      doer-a.md
      doer-b.md
      doer-c.md
      independent-test-designer.md
      test-executor.md
      differential-comparator.md
      adversarial-verifier.md
      fixer.md
      final-reviewer.md
      eval-judge.md
    skills/
      critical-task-workflow/
        SKILL.md
      spec-freezing/
        SKILL.md
        templates/
          spec.yaml
      multi-doer-implementation/
        SKILL.md
        templates/
          assumptions.md
      independent-test-design/
        SKILL.md
      differential-verification/
        SKILL.md
        templates/
          variant-scorecard.yaml
      adversarial-testing/
        SKILL.md
      eval-harness/
        SKILL.md
      ui-reporting/
        SKILL.md
      release-gate/
        SKILL.md
  src/
    critical_doers/
      __init__.py
      cli.py
      runs.py
      schemas.py
      git_worktrees.py
      validators.py
      test_matrix.py
      comparator.py
      report.py
      evals.py
  scripts/
    run_test_matrix.sh
    compare_changed_files.sh
    validate_artifacts.py
    generate_report.py
  tests/
    unit/
    integration/
    fixtures/
      sample_repo/
      tasks/
  examples/
    sample-run/
  ui/
    package.json
    src/
      App.tsx
      components/
      pages/
```

---

## 14. CLI requirements

The framework should expose a local CLI.

Example:

```bash
critical-doers classify --request request.md
critical-doers init-run --request request.md
critical-doers validate-artifacts --run .ai/critical-runs/2026-05-30-001
critical-doers test-matrix --run .ai/critical-runs/2026-05-30-001
critical-doers compare --run .ai/critical-runs/2026-05-30-001
critical-doers eval --run .ai/critical-runs/2026-05-30-001
critical-doers report --run .ai/critical-runs/2026-05-30-001
```

Required commands:

```text
classify
init-run
validate-artifacts
test-matrix
compare
eval
report
clean
```

---

## 15. Git isolation requirements

Each doer must work in isolation.

Supported strategies:

```text
- git worktree per doer
- branch per doer
- patch files only
```

Recommended MVP:

```text
git worktree per doer
```

Naming:

```text
critical/<run-id>/doer-a
critical/<run-id>/doer-b
critical/<run-id>/doer-c
```

Rules:

```text
- Doer branches must start from the same base commit.
- Doers must not read each other’s worktrees.
- Comparator runs only after all doers finish.
- Selected candidate becomes the final candidate branch.
```

---

## 16. Deterministic validation requirements

The framework must support project-configurable validation commands.

Config file:

```text
critical-doers.yaml
```

Example:

```yaml
validation:
  commands:
    typecheck: "npm run typecheck"
    lint: "npm run lint"
    unit: "npm test"
    integration: "npm run test:integration"
    security: "npm audit --audit-level=high"
  required_for_critical:
    - typecheck
    - lint
    - unit
```

Rules:

```text
- Critical task cannot pass if required validation fails.
- Failed validation must be attached to final-review.md.
- Claude claims are never sufficient without command output.
```

---

## 17. Eval requirements

Evaluation is a first-class requirement.

### 17.1 Eval modes

The framework must support at least three eval modes:

```text
1. Offline fixture eval
2. Live repo eval
3. Regression eval
```

#### Offline fixture eval

Use small controlled repositories with known expected outcomes.

#### Live repo eval

Run against real project tasks, but require human scoring.

#### Regression eval

Re-run previous tasks to ensure framework changes do not reduce reliability.

---

### 17.2 Baselines

The eval harness must compare:

```text
baseline_single_doer
baseline_single_doer_with_review
baseline_single_doer_with_tests
multi_doer_no_adversary
multi_doer_with_adversary
full_critical_path
```

---

### 17.3 Metrics

Required metrics:

```yaml
task_success:
  description: Did the final implementation satisfy the spec?

hidden_test_pass_rate:
  description: Pass rate on hidden tests not shown to doers.

public_test_pass_rate:
  description: Pass rate on visible/generated tests.

regression_rate:
  description: Did unrelated behavior break?

critical_defect_rate:
  description: Number of high/critical issues introduced.

scope_creep_rate:
  description: Did the solution modify out-of-scope files or behavior?

doer_disagreement_rate:
  description: How often doers made materially different assumptions?

useful_disagreement_rate:
  description: How often disagreement exposed a real ambiguity or defect?

comparator_selection_accuracy:
  description: Did comparator choose the best variant according to hidden tests/human review?

adversarial_finding_yield:
  description: Number of valid issues found by adversarial verifier.

false_block_rate:
  description: How often framework blocked a good solution.

false_pass_rate:
  description: How often framework approved a bad solution.

cost_per_success:
  description: Token/cost/time per successful critical task.

human_review_time:
  description: How long human took to approve/reject using UI report.
```

---

### 17.4 Failure taxonomy

Each failed run must classify failure:

```text
- spec ambiguity
- bad implementation
- bad generated tests
- test insufficiency
- comparator chose wrong variant
- verifier missed issue
- fixer introduced regression
- deterministic gate misconfigured
- UI/report misleading
- human rejected despite framework approval
- framework blocked valid solution
```

---

### 17.5 Eval dataset requirements

The MVP should include at least 20 fixture tasks:

```text
5 normal tasks
5 elevated tasks
10 critical tasks
```

Critical tasks should include:

```text
- auth guard bug
- payment amount rounding bug
- API contract change
- database migration simulation
- secret leakage prevention
- concurrency/race condition
- input validation bypass
- regression-prone refactor
- ambiguous requirement
- hidden edge-case bug
```

Each fixture task must include:

```text
- request.md
- expected_behavior.md
- hidden_tests/
- scoring_rubric.yaml
```

---

### 17.6 Eval output format

File:

```text
.ai/critical-runs/<run-id>/eval/eval-result.json
```

Schema:

```json
{
  "run_id": "string",
  "task_id": "string",
  "mode": "offline_fixture | live_repo | regression",
  "workflow": "single_doer | multi_doer | full_critical_path",
  "scores": {
    "task_success": true,
    "hidden_test_pass_rate": 0.0,
    "public_test_pass_rate": 0.0,
    "scope_creep": false,
    "critical_defects": 0,
    "comparator_selection_correct": true,
    "human_approved": true
  },
  "failure_taxonomy": [],
  "notes": "string"
}
```

---

## 18. UI requirements

The framework must include a local UI or generated HTML report.

### 18.1 MVP UI

MVP can be static HTML generated per run:

```text
.ai/critical-runs/<run-id>/ui/report.html
```

The report must be viewable without a backend.

### 18.2 Future UI

A React UI can be added later:

```text
ui/
  src/
    App.tsx
    components/
    pages/
```

---

### 18.3 UI pages

Required pages:

```text
1. Run Overview
2. Frozen Spec
3. Doer Comparison
4. Test Matrix
5. Adversarial Findings
6. Final Decision
7. Eval Metrics
8. Artifacts
```

---

### 18.4 Run Overview page

Must show:

```text
- run ID
- task title
- criticality level
- current status
- selected variant
- final recommendation
- blocking issues
- human approval status
```

Status values:

```text
created
classified
spec-frozen
doers-running
tests-running
comparison-running
verification-running
fixing
final-review
approved
rejected
blocked
```

---

### 18.5 Frozen Spec page

Must show:

```text
- goal
- scope
- non-goals
- acceptance criteria
- required tests
- allowed files
- forbidden files
- rollback plan
- open questions
```

---

### 18.6 Doer Comparison page

Must show a matrix:

```text
- Doer
- Strategy
- Files changed
- Lines changed
- Assumptions
- Tests passed
- Acceptance coverage
- Complexity score
- Blast radius score
- Security risk score
- Recommendation
```

Must highlight:

```text
- assumption disagreements
- file-change disagreements
- behavior disagreements
- unique risks
```

---

### 18.7 Test Matrix page

Must show:

```text
- validation command
- doer-a result
- doer-b result
- doer-c result
- selected candidate result
- logs link
```

---

### 18.8 Adversarial Findings page

Must show:

```text
- finding ID
- severity
- category
- description
- evidence
- blocking status
- resolution status
```

---

### 18.9 Final Decision page

Must show:

```text
- selected variant
- selection reason
- final reviewer recommendation
- residual risks
- release gate status
- human approval checkbox/status
```

---

### 18.10 Eval Metrics page

Must show:

```text
- baseline comparison
- task success
- hidden test pass rate
- public test pass rate
- cost/time
- disagreement rate
- comparator accuracy
- adversarial finding yield
- false pass / false block
```

---

### 18.11 UI design principle

The UI must not be a transcript viewer.

It must be an evidence dashboard.

The human reviewer should quickly answer:

```text
- What was the frozen spec?
- What did each doer assume?
- Where did implementations differ?
- Which tests passed or failed?
- What did the verifier find?
- Why was this variant selected?
- What residual risk remains?
- Should I approve this?
```

---

## 19. Hooks requirements

The framework should provide optional Claude Code hook configurations.

### 19.1 Pre-implementation hook

Block critical implementation unless spec exists.

Condition:

```text
criticality.level == critical
AND spec.yaml missing
```

Action:

```text
block
```

### 19.2 Pre-completion hook

Block final completion unless required artifacts exist.

Required:

```text
classification.yaml
spec.yaml
at least two doer outputs
test-matrix.yaml
variant-scorecard.yaml
final-review.md
eval-result.json
```

### 19.3 Dangerous command hook

Block or require approval for:

```text
rm -rf
git push --force
git reset --hard
database migration execution
production deploy commands
secret file access
```

### 19.4 Post-edit hook

Optional fast validation:

```text
- format check
- lint changed files
- run focused tests
```

---

## 20. Configuration requirements

Config file:

```text
critical-doers.yaml
```

Example:

```yaml
project:
  name: sample-project
  default_branch: main

criticality:
  force_critical_paths:
    - "src/auth/**"
    - "src/payments/**"
    - "migrations/**"
  forbidden_without_human_approval:
    - "production.yaml"
    - ".env*"

doers:
  required_count: 2
  optional_count: 1
  strategies:
    doer-a: minimal-safe
    doer-b: maintainability-first
    doer-c: defensive-edge-case

validation:
  commands:
    typecheck: "npm run typecheck"
    lint: "npm run lint"
    unit: "npm test"
  required_for_critical:
    - typecheck
    - lint
    - unit

eval:
  enabled: true
  hidden_tests_dir: "tests/hidden"
  record_costs: true

ui:
  mode: static-html
  output: ".ai/critical-runs/{run_id}/ui/report.html"
```

---

## 21. MVP requirements

The MVP must include:

```text
1. Repo scaffold.
2. Claude Code subagent definitions.
3. Claude Code skill definitions.
4. Spec schema.
5. Critical run artifact structure.
6. CLI artifact validator.
7. Test matrix runner.
8. Comparator scorecard generator.
9. Static HTML report generator.
10. Offline eval harness with at least 5 fixture tasks.
```

MVP subagents:

```text
critical-task-planner
spec-freezer
doer-a
doer-b
independent-test-designer
differential-comparator
final-reviewer
eval-judge
```

MVP skills:

```text
critical-task-workflow
spec-freezing
multi-doer-implementation
independent-test-design
differential-verification
eval-harness
ui-reporting
release-gate
```

MVP excluded:

```text
- doer-c
- adversarial-verifier
- fixer
- live interactive UI
- full dashboard backend
```

---

## 22. Phase 2 requirements

Add:

```text
- doer-c
- adversarial-verifier
- fixer
- hook integration
- React UI
- hidden test generation
- security scans
- worktree automation
- aggregate eval dashboard
```

---

## 23. Phase 3 requirements

Add:

```text
- multi-model doers
- configurable model effort
- model/cost routing
- historical lessons learned
- automatic risk classifier tuning
- organization policy packs
- GitHub PR integration
- CI/CD release gate integration
```

---

## 24. Acceptance criteria for framework MVP

The MVP is accepted when:

```text
1. A critical task can be initialized from request.md.
2. The system creates a valid spec.yaml.
3. Two isolated doer outputs can be stored.
4. Independent tests can be generated/stored.
5. Test matrix can be run and recorded.
6. Comparator produces variant-scorecard.yaml.
7. Final reviewer produces final-review.md.
8. Eval judge produces eval-result.json.
9. Static HTML report is generated.
10. At least 5 fixture tasks can be run end-to-end.
```

---

## 25. Definition of done for a critical run

A critical run is complete only when:

```text
- classification.yaml exists
- spec.yaml exists
- at least two doer outputs exist
- tests are mapped to acceptance criteria
- all required deterministic checks have run
- comparator has produced scorecard
- all blocking verifier findings are resolved or explicitly accepted by human
- final-review.md exists
- eval-result.json exists
- UI report exists
- human approval status is recorded
```

---

## 26. Key design principle

The framework must embody this principle:

> Claude agents are generators and critics. The framework, tests, CI, artifacts, and human reviewer are the authority.

A passing run is not:

```text
Claude says it is done.
```

A passing run is:

```text
The frozen spec is satisfied, evidence exists, variants were compared, deterministic checks passed, evals were recorded, and a human can see why the selected implementation is safe enough to approve.
```
