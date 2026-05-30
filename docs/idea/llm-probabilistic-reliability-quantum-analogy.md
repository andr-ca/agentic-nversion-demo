# From Quantum Uncertainty to Reliable AI Systems: Engineering Around the Probabilistic Nature of LLMs

## Executive summary

This article captures a design idea that emerged from a discussion about quantum mechanics, classical determinism, and large language models:

> The useful lesson is not that probability disappears. The useful lesson is that reliable systems can be engineered on top of probabilistic substrates.

In quantum mechanics, individual microscopic outcomes can be probabilistic, but macroscopic systems such as electrical circuits, lasers, and particle accelerators can be highly reliable because they are constrained, stabilized, averaged over large numbers of events, and verified by physical feedback loops. The world does not literally transform from “random quantum” to “100% deterministic classical.” Instead, classical predictability emerges as an effective approximation through mechanisms such as decoherence, environmental stabilization, and statistical averaging.

A similar design principle can be applied to large language model systems. LLMs are probabilistic next-token engines. A single completion can hallucinate, omit requirements, misread context, overfit to prompt phrasing, or produce a plausible but subtly wrong implementation. But an engineered workflow can reduce those risks:

- Freeze the requirement before implementation.
- Spawn multiple independent “doers.”
- Use diverse prompts, roles, and possibly models.
- Generate tests independently from the implementation.
- Compare variants structurally and behaviorally.
- Use adversarial verification.
- Use deterministic gates such as type checks, tests, linters, security scans, contract validation, and CI.
- Require human approval for critical changes.

The proposed framework is best described as:

> **LLM-assisted N-version development with verifier-guided selection and deterministic release gates.**

This is not just a metaphor. It has a direct software-engineering precedent in **N-version programming**, and recent arXiv work such as **Galápagos: Automated N-Version Programming with LLMs** explores using LLMs to produce diverse program variants that can be validated and composed for fault tolerance.

---

## 1. Why the quantum analogy is useful, but limited

The original question was essentially:

> If the quantum world is probabilistic, how do we get a real world where electricity, lasers, and proton accelerators are so predictable?

The answer is that everyday systems are not predictable because quantum uncertainty “goes away.” They are predictable because the useful macroscopic variables are stabilized by scale, constraints, and feedback.

That distinction is important for LLMs.

A weak interpretation would be:

> “LLMs are random, so ask several times and average the answers.”

A stronger interpretation is:

> “LLMs are probabilistic generators. Build a surrounding system that constrains their degrees of freedom, samples diverse alternatives, tests the results, compares behavior, and accepts only artifacts that pass independent verification.”

The second interpretation is the one worth implementing.

---

## 2. Quantum mechanics: deterministic evolution, probabilistic measurement

Quantum mechanics is not pure randomness.

A quantum system’s wave function evolves deterministically under the Schrödinger equation. The probabilistic part appears when the theory predicts measurement outcomes. Before measurement, the state may encode multiple possible outcomes; after measurement, we observe one actual outcome with probabilities given by the quantum state.

The transition from quantum behavior to classical-looking behavior is often explained using **decoherence**. In decoherence, the system becomes entangled with its environment. The environment effectively “monitors” certain observables, suppressing interference between alternatives and making stable “pointer states” behave classically.

Wojciech Zurek’s work is central here. In *Decoherence and the Transition from Quantum to Classical — Revisited*, Zurek describes how the environment can monitor system observables and how the resulting eigenstates can decohere and behave like classical states.

The important point for our analogy:

> The classical world is not fundamental determinism replacing quantum probability. It is a robust operational regime that emerges when the system is large, open, constrained, and continually interacting with its environment.

### References

- Wojciech H. Zurek, **Decoherence and the transition from quantum to classical — Revisited**, arXiv:quant-ph/0306072.  
  https://arxiv.org/abs/quant-ph/0306072

- Wojciech H. Zurek, **Decoherence, einselection, and the quantum origins of the classical**, arXiv:quant-ph/0105127.  
  https://arxiv.org/abs/quant-ph/0105127

- Wojciech H. Zurek, **Emergence of the Classical from within the Quantum Universe**, arXiv:2107.03378.  
  https://arxiv.org/abs/2107.03378

- P. A. Knott, **Decoherence, quantum Darwinism, and the generic emergence of our objective classical reality**, arXiv:1811.09062.  
  https://arxiv.org/abs/1811.09062

---

## 3. Why electricity is predictable even if electrons are quantum

A single electron in a conductor is not a little deterministic bead traveling along a clean wire. It is a quantum object interacting with a lattice, impurities, phonons, fields, and boundaries.

Yet ordinary electricity is predictable because engineering does not depend on the exact path of one electron.

A current of 1 ampere corresponds to roughly 6.24 × 10^18 elementary charges per second. The circuit-level behavior depends on aggregate properties:

- voltage,
- current,
- resistance,
- capacitance,
- inductance,
- temperature,
- material properties,
- geometry.

The path of one electron may be complex and probabilistic. The bulk current is a stable statistical quantity.

This is the first major lesson for LLM systems:

> Do not bet the system on one microscopic event. Build around aggregate behavior, repeated attempts, constraints, and measurements.

---

## 4. Why lasers are reliable even though they are quantum devices

A laser is deeply quantum. Its key mechanism is stimulated emission: an incoming photon stimulates an excited atom or molecule to emit another photon with matching phase, frequency, direction, and polarization.

Individual emission events can involve quantum uncertainty. But the laser cavity is engineered to amplify only certain modes while suppressing others. The system converts microscopic quantum effects into a stable macroscopic beam by imposing strong constraints:

- cavity geometry,
- gain medium,
- mirrors,
- allowed modes,
- pumping energy,
- losses that suppress unwanted modes.

This gives the second lesson for LLM systems:

> Reliability improves when the system constrains the allowed output space.

In LLM terms, this means using:

- structured specs,
- schemas,
- typed artifacts,
- narrow task boundaries,
- restricted tools,
- allowed-file lists,
- forbidden-change lists,
- deterministic checks,
- generated tests,
- CI gates.

A laser is not reliable because photons are individually deterministic. It is reliable because the device strongly constrains which probabilistic events get amplified.

A robust LLM workflow should do the same.

---

## 5. Why proton accelerators are predictable even though collisions are probabilistic

Particle accelerators are especially useful as an analogy because they combine deterministic engineering with quantum-probabilistic events.

The accelerator beam can be steered using electromagnetic fields with very high precision. Electric fields accelerate particles; magnetic fields bend them. The beam trajectory is engineered and measured.

But individual particle collisions are probabilistic. Physicists cannot usually say exactly what one proton-proton collision will produce. They can say that after many collisions, the distribution of outcomes will follow precise statistical laws.

This distinction maps well to LLM-based software delivery:

| Accelerator | LLM workflow |
|---|---|
| Beam steering is engineered and predictable | Workflow orchestration should be deterministic |
| Individual collisions are probabilistic | Individual LLM outputs are probabilistic |
| Millions/billions of events reveal stable distributions | Multiple independent attempts reveal robust or divergent patterns |
| Detectors collect evidence | Tests, logs, evals, and diffs collect evidence |
| Physicists accept statistical confidence, not magical certainty | Engineers accept gated confidence, not “Claude said so” |

The accelerator lesson:

> Use deterministic infrastructure to control where probabilistic events happen, then use measurement to decide what happened.

---

## 6. LLMs as probabilistic engines

A large language model produces output by estimating probability distributions over tokens conditioned on context. Even with low temperature, output can vary due to sampling, inference infrastructure, context changes, model updates, tie-breaking, hidden nondeterminism in execution, and prompt ambiguity.

The practical risk is not “randomness” in a simple dice-roll sense. The practical risk is that the model may produce a fluent artifact that looks correct but fails in one of several ways:

- misinterprets the requirement,
- omits an edge case,
- changes unrelated files,
- silently broadens scope,
- invents unsupported assumptions,
- generates tests that merely confirm its own implementation,
- passes shallow tests but fails hidden cases,
- violates security or authorization assumptions,
- creates an operationally risky migration,
- hides uncertainty behind confident language.

So the goal is not to make one LLM call deterministic. The goal is to make the overall system robust:

> LLMs generate and critique. Deterministic systems verify, enforce, and release.

---

## 7. The engineering principle: constrain, diversify, verify

The proposed pattern has three layers.

### 7.1 Constrain

Before generation, restrict the problem.

Examples:

- Freeze the spec.
- Define scope and non-goals.
- Define affected files.
- Define forbidden files.
- Define acceptance criteria.
- Define security requirements.
- Define rollback plan.
- Require structured output.
- Require diffs instead of direct merge.
- Force work into isolated branches or worktrees.

This is analogous to a laser cavity: we define which modes are allowed to be amplified.

### 7.2 Diversify

For critical tasks, spawn multiple independent doers.

Do not merely ask the same prompt five times. Diversity should be deliberate:

- different prompts,
- different roles,
- different model settings,
- different assumptions style,
- different implementation philosophy,
- possibly different models,
- isolated context windows,
- no visibility into each other’s outputs until comparison.

Useful doer diversity:

- **Doer A:** minimal safe implementation.
- **Doer B:** maintainability-first implementation.
- **Doer C:** defensive edge-case implementation.
- **Doer D:** compatibility-preserving implementation.

This is analogous to statistical sampling and N-version redundancy.

### 7.3 Verify

Verification must not be performed only by the same agent that generated the solution.

Verification should include:

- independent test generation,
- execution-based tests,
- type checking,
- linting,
- contract tests,
- API schema checks,
- security scans,
- secret scans,
- migration dry-runs,
- differential behavior comparison,
- adversarial review,
- human approval for critical paths.

This is the equivalent of a detector, measurement apparatus, and release gate.

---

## 8. Why “majority vote” is not enough

A common mistake is to assume that if five LLMs produce similar answers, the answer is correct.

That can be false.

LLMs are not independent random variables drawn from unrelated distributions. They can share training data, reasoning patterns, benchmark artifacts, common misconceptions, and prompt-induced biases. Five agents can confidently make the same mistake.

Therefore, the target pattern is not:

```text
Generate five answers → pick majority
```

The target pattern is:

```text
Generate diverse implementations
→ compare assumptions
→ run independent tests
→ generate discriminative tests
→ examine behavioral divergence
→ select the smallest safe candidate
→ verify deterministically
→ human approval for critical work
```

The strongest mechanism is not majority. It is **behavioral disagreement plus external verification**.

---

## 9. Research background: self-consistency, multi-agent reasoning, and test-time compute

Several research threads support pieces of this design.

### 9.1 Self-consistency

The self-consistency paper shows that instead of using a single greedy chain-of-thought path, one can sample multiple reasoning paths and select the most consistent answer.

- Xuezhi Wang et al., **Self-Consistency Improves Chain of Thought Reasoning in Language Models**, arXiv:2203.11171.  
  https://arxiv.org/abs/2203.11171

This supports the general idea that multiple sampled attempts can improve reliability. But software work requires more than selecting a consistent text answer.

### 9.2 Universal self-consistency

Universal Self-Consistency extends the idea to free-form answers by using an LLM to select among multiple candidates.

- Xinyun Chen et al., **Universal Self-Consistency for Large Language Model Generation**, arXiv:2311.17311.  
  https://arxiv.org/abs/2311.17311

This is relevant because code, architecture, and requirements are often free-form. But again, for critical software, LLM selection should be combined with tests and deterministic gates.

### 9.3 Multi-agent debate

Multi-agent debate uses multiple model instances that propose and debate answers over several rounds.

- Yilun Du et al., **Improving Factuality and Reasoning in Language Models through Multiagent Debate**, arXiv:2305.14325.  
  https://arxiv.org/abs/2305.14325

This supports adversarial review and independent critique. However, debate alone can still create false consensus. It should be paired with external evidence.

### 9.4 Tree of Thoughts

Tree of Thoughts generalizes chain-of-thought into a search process over multiple intermediate reasoning paths, with evaluation and backtracking.

- Shunyu Yao et al., **Tree of Thoughts: Deliberate Problem Solving with Large Language Models**, arXiv:2305.10601.  
  https://arxiv.org/abs/2305.10601

This supports the idea that an orchestrated search over alternatives can outperform a single linear generation.

### 9.5 Reflexion and Self-Refine

Reflexion and Self-Refine show that agents can improve via feedback and iterative refinement.

- Noah Shinn et al., **Reflexion: Language Agents with Verbal Reinforcement Learning**, arXiv:2303.11366.  
  https://arxiv.org/abs/2303.11366

- Aman Madaan et al., **Self-Refine: Iterative Refinement with Self-Feedback**, arXiv:2303.17651.  
  https://arxiv.org/abs/2303.17651

These are useful for repair loops. But for critical tasks, self-refinement is weaker than independent verification, because the same model may preserve the same blind spot.

### 9.6 Test-time scaling for agents

Test-time scaling for agents studies strategies such as parallel sampling, sequential revision, verifiers, merging, and diversified rollouts.

- King Zhu et al., **Scaling Test-time Compute for LLM Agents**, arXiv:2506.12928.  
  https://arxiv.org/abs/2506.12928

This is very close to the system-level principle here: spend more compute at inference time to improve agent reliability.

---

## 10. Research background: code-specific multi-agent and ensemble systems

The most relevant papers are the ones specifically about code generation and redundant implementations.

### 10.1 Galápagos: automated N-version programming with LLMs

This is the closest conceptual match.

- Javier Ron et al., **Galápagos: Automated N-Version Programming with LLMs**, arXiv:2408.09536.  
  https://arxiv.org/abs/2408.09536

The paper connects LLM code generation to classical N-version programming. N-version programming uses multiple diverse program variants to reduce the risk of overlapping fault modes. Galápagos uses LLMs to generate functionally equivalent but internally diverse variants and validates correctness/equivalence.

This is almost exactly the direction proposed for critical Claude Code tasks:

```text
Frozen spec
→ multiple independent variants
→ equivalence/correctness checks
→ differential behavior comparison
→ safe selection or redundant composition
```

### 10.2 Code generation ensembles

- Tarek Mahmud et al., **Enhancing LLM Code Generation with Ensembles: A Similarity-Based Selection Approach**, arXiv:2503.15838.  
  https://arxiv.org/abs/2503.15838

This paper generates multiple candidate programs and selects using syntactic similarity, semantic similarity, and behavioral equivalence. It supports the principle that multiple implementations can improve code-generation performance when selection is structured.

### 10.3 AgentCoder

- Dong Huang et al., **AgentCoder: Multi-Agent-based Code Generation with Iterative Testing and Optimisation**, arXiv:2312.13010.  
  https://arxiv.org/abs/2312.13010

AgentCoder uses specialized agents such as a programmer, test designer, and test executor. It is relevant because it separates code generation from test design and execution.

### 10.4 CodeCoR

- Ruwei Pan et al., **CodeCoR: An LLM-Based Self-Reflective Multi-Agent Framework for Code Generation**, arXiv:2501.07811.  
  https://arxiv.org/abs/2501.07811

CodeCoR uses multiple agents to generate prompts, code, tests, and repair advice; agents produce more than one output and prune low-quality outputs. This supports the idea of internal redundancy and structured pruning.

### 10.5 EvalPlus and stronger code evaluation

- Jiawei Liu et al., **Is Your Code Generated by ChatGPT Really Correct? Rigorous Evaluation of Large Language Models for Code Generation**, arXiv:2305.01210.  
  https://arxiv.org/abs/2305.01210

EvalPlus is important because it shows that shallow benchmark tests can overestimate correctness. This supports a key requirement for our framework: do not rely only on the tests that happen to exist. Generate additional discriminative tests and measure hidden failure modes.

---

## 11. Proposed thesis

The resulting thesis is:

> Critical software tasks should be handled by an LLM-assisted N-version development workflow. Multiple isolated doers produce independent implementations of a frozen spec. Independent testers and verifiers generate evidence. A deterministic orchestrator compares candidates, runs tests and gates, and requires human approval for high-risk changes.

This is stronger than “ask Claude to double-check itself.”

It is closer to:

```text
human-quality software review
+ N-version programming
+ test-time compute scaling
+ multi-agent verification
+ deterministic CI
```

---

## 12. How this maps to Claude Code

Claude Code is a natural environment for this experiment because it supports specialized subagents and skills.

The relevant primitives:

- **Subagents:** specialized agents with distinct prompts, descriptions, tools, and context.  
  https://docs.anthropic.com/en/docs/claude-code/sub-agents

- **Skills:** reusable instructions packaged as `SKILL.md` files with YAML frontmatter and markdown instructions.  
  https://docs.anthropic.com/en/docs/claude-code/skills

- **Hooks:** automation points that can run validation commands or agent checks at key lifecycle points.  
  https://docs.anthropic.com/en/docs/claude-code/hooks-guide

The framework should treat these as different layers:

| Layer | Purpose |
|---|---|
| Subagent | Role-specific reasoning or implementation |
| Skill | Reusable procedure or playbook |
| Script | Deterministic comparison, artifact validation, test execution |
| Hook | Enforcement at lifecycle boundaries |
| CI | Final automated authority |
| Human reviewer | Final high-risk approval |

---

## 13. Recommended critical-task workflow

```text
1. User request arrives.
2. Planner classifies normal vs critical.
3. If critical, the system freezes a spec.
4. Doer A implements in isolated branch/worktree.
5. Doer B implements in isolated branch/worktree.
6. Optional Doer C implements with a different strategy.
7. Independent test designer creates tests from the spec.
8. Test executor runs existing and generated tests against all variants.
9. Comparator compares changed files, assumptions, and behavior.
10. Adversarial verifier tries to break the leading candidate.
11. Fixer applies only verified fixes.
12. Final reviewer checks acceptance criteria and evidence.
13. CI runs deterministic gates.
14. Human approves or rejects merge.
15. Eval harness records outcome for framework improvement.
```

---

## 14. Evaluation must be a first-class requirement

The framework itself needs evaluation.

It is not enough to build an impressive multi-agent workflow. We need to prove whether it improves reliability compared with a baseline.

Required eval dimensions:

### 14.1 Baselines

Compare:

```text
A. Single Claude Code doer
B. Single doer + reviewer
C. Single doer + independent tests
D. Multi-doer + comparator
E. Multi-doer + comparator + adversarial verifier
F. Full critical workflow
```

### 14.2 Metrics

Measure:

```text
- task success rate
- hidden test pass rate
- regression rate
- security finding rate
- scope-creep rate
- time/cost per task
- number of repair loops
- number of human interventions
- disagreement rate between doers
- useful-disagreement rate
- false confidence rate
- merge-block precision/recall
```

### 14.3 Test tasks

Use a controlled benchmark set:

```text
- simple backend validation change
- API contract change
- auth guard change
- data migration simulation
- concurrency-sensitive update
- bug fix with hidden edge case
- security-sensitive change
- ambiguous requirement requiring spec clarification
```

### 14.4 Outcome-based grading

Do not grade only transcripts.

Grade the final environment state:

- Did tests pass?
- Did hidden tests pass?
- Was the diff minimal?
- Were forbidden files untouched?
- Did the implementation meet the spec?
- Was a security issue introduced?
- Was rollback documented?
- Did the human reviewer agree with the framework’s recommendation?

This aligns with Anthropic’s eval guidance for agents: the outcome is the final state of the environment, not merely what the agent claims it did.

- Anthropic Engineering, **Demystifying evals for AI agents**.  
  https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents

---

## 15. UI should make reliability visible

The framework should include a UI because the purpose is not only to run agents but to make the reliability evidence visible to a human.

The UI should show:

- frozen spec,
- doer branches,
- assumptions by doer,
- changed files by doer,
- test results by variant,
- behavioral differences,
- verifier findings,
- final recommendation,
- confidence level,
- merge gate status,
- cost/time,
- eval score.

A useful display is not a chat transcript. It is a reliability dashboard.

Suggested UI model:

```text
Task Overview
├── Criticality classification
├── Frozen spec
├── Doer A / B / C summaries
├── Variant comparison matrix
├── Test matrix
├── Divergence report
├── Verifier findings
├── Final reviewer decision
├── CI gate status
└── Human approval controls
```

The human should be able to answer quickly:

```text
Why did the framework choose this implementation?
Where did the doers disagree?
What evidence supports the final decision?
What risk remains?
What would fail rollback?
```

---

## 16. Limitations and risks

### 16.1 Correlated failure

Multiple doers may share the same blind spot. This is why diversity matters.

### 16.2 Test contamination

If tests are generated from the chosen implementation instead of the frozen spec, they may merely validate the implementation’s assumptions.

### 16.3 Cost and latency

The critical path will cost more. That is acceptable only for high-risk tasks.

### 16.4 False confidence

A polished final report can create confidence without evidence. The UI must emphasize artifacts, diffs, tests, and measured outcomes.

### 16.5 Overengineering

Not every task needs multiple doers. The framework must classify risk and keep a cheaper normal path.

### 16.6 Human approval bottleneck

For critical tasks, human approval is a feature, not a bug. But the system should make approval efficient.

---

## 17. Practical conclusion

The right analogy is not:

> Quantum randomness becomes deterministic, so LLM randomness can become deterministic.

The right analogy is:

> Probabilistic substrates can support reliable engineered systems when constrained, sampled, stabilized, measured, and verified.

For LLM software engineering, that means:

```text
Do not trust one completion.
Do not trust majority vote alone.
Do not trust self-review alone.

Freeze the spec.
Diversify implementation.
Generate independent tests.
Compare behavior.
Verify adversarially.
Gate deterministically.
Measure outcomes.
Let humans approve critical changes.
```

That is the foundation for the Claude Code critical-doers framework.
