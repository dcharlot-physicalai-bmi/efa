# EFA-M — the attractor-memory program

_2026-07-23 · Charlot Lab, Institute for Physical AI @ BMI. Move 1 of [BENCHMARKS-2026.md](BENCHMARKS-2026.md): the
brain-inspired energy-first architecture pointed at the field's named unsolved enablement axis — MEMORY. The
Hopfield/energy slot in robot memory is empty (3-way verified); RoboMME's own findings read as a spec for it.
Clock: RoboMME CVPR challenge open now; CoRL "Memory for Robot Foundation Models" workshop Nov 9, 2026._

## The spike — mechanism proven (`experiments/ebm_efam.rs`, pure CPU, gates fixed pre-run)

A Dense Associative Memory whose **retrieval is descent on an explicit energy**
`E(q) = −(1/β)·log Σᵢ exp(−β‖q−ξᵢ‖²/2)` — the same inference primitive as EFA actuation, pointed at memory.
Stored patterns = (context ⊕ goal-encoding); recall = heteroassociation from partial/corrupted cues under
distractor pressure; the recalled goal is handed to the **shipped efa-1 artifact** (memory modulates the flagship,
which is unchanged).

| gate | result |
|---|---|
| content-addressable recall (M=20, 25% cue corruption) | **99.8%** (100% clean; 92.5% @ 50%) |
| **energy = memory-confidence certificate** (stored vs novel cues) | **AUROC 0.998**; 98.8% of novel cues flagged at τ_mem |
| closed loop: recalled goal → shipped efa-1 | **100% reach** (memoryless baselines: mean-goal 35%, random-stored 33%) |
| memory-gated agency (act iff E(q0) ≤ τ_mem) | **0 wrong-goal actions; 60/60 novel cues correctly refused** (4/60 stored over-refused) |
| capacity at fixed β=24 | 99.8% / 98.2% / **94.8%** at M = 20 / 100 / 500 |
| price · determinism | 16.3 kFLOP/recall ≈ 42% of one decision · bit-exact ✓ |

**The mechanism finding (recorded negative that defines the design):** v1 used the CONVERGED energy as the
confidence signal and it failed at chance (AUROC 0.469→0.614) — a novel cue still falls into *some* basin, and its
bottom looks like any other bottom. **Confidence lives in the CUE's energy E(q0)** — how close the query starts to
any stored pattern — not the resting state. (The same lesson as the ledger's garden-path result: the information is
in the work, not the equilibrium.) Second finding: capacity requires β scaled to pattern separation (β=8 collapsed
at M=100; β=24 + 16-D contexts holds at M=500) — exactly the modern-Hopfield theory.

**Why this shape wins on RoboMME's own evidence:** perceptual representation wins (DAM stores continuous feature
patterns), light modulation wins (retrieval output conditions the policy without touching it), recurrent
write-mechanisms fail (DAM writes are storage, not backprop-through-time), symbolic needs oracle grounding (DAM is
content-addressable — the cue IS the grounding). And the confidence certificate answers the CoRL workshop's open
problem #6 — "metrics distinguishing genuine memory use from shortcuts" — with a number the model computes about
itself.

## Honest scope (spike)
Direct pattern writes (no consolidation guarantee yet); synthetic context vectors standing in for perceptual
embeddings; 1-DOF body (the body is deliberately not the point — the shipped artifact closes the loop at 100% when
handed the right goal). One seed everywhere; hash-deterministic.

## Stage 2 DONE — sequence attractors: the program counter is an attractor (`experiments/ebm_efam2.rs`)

The suites where RoboMME's perceptual memory loses to symbolic (counting, imitation), solved as **chained attractor
dynamics**: the task program is a heteroassociative chain ξ₀→ξ₁→… (each phase carries a goal readout to the shipped
efa-1); arrival events step the chain; **no counter variable exists anywhere in the agent**.

| gate | result |
|---|---|
| counting — "touch A exactly N times, then settle at B" (harness-counted) | **100% for every N ∈ 1..5** (memoryless: 0%) |
| procedural replay (8-waypoint pattern) | **100%**, waypoint RMS 0.101 rad |
| mid-sequence content-addressable entry (25%-corrupted cue, random phase) | **100%** (200 trials) — a frame buffer can't do this without search |
| associative cleanup under program-counter corruption (σ=0.6) | 70% → **95%** with the energy-gated settle |
| price · determinism | 5.5 kFLOP/chain-step ≈ 14% of a decision · bit-exact ✓ |

**The fix that got there is itself a result:** the first run failed counting (25–77%) because the chain-step event
demanded settling within 0.15 rad — tighter than the artifact's own *certified* attractor residual (≤0.32 rad) — and
the harness counted band fly-throughs as visits. **The certificate supplied the legitimate event criterion** —
the certified residual bound is now operationally load-bearing, not just a claim on a card. (Recorded negative:
first-run event criterion untestably tight; same class as the cert1 harness bug.)

## Stage 3 DONE — certified consolidation: every write audited, refusals with stated reasons (`experiments/ebm_efam3.rs`)

The answer to RoboTTT-style unverified test-time writes: a **write gate** judged in context space (novel → append;
same-context + agreeing goal → consolidate by running average; same-context + contradicting goal → **refuse as
contradiction**; conflict-zone proximity to a *different* memory → **refuse as aliasing risk**), plus a per-pattern
**retrieval certificate** — separation dᵢ ⇒ certified basin ρᵢ = dᵢ/4 and error bound εᵢ = (M−1)·e^(−β dᵢ²/4)·D —
recomputed after every accepted write and **sample-verified end-to-end**.

| gate | result (600-event stream: 100 novel · 403 noisy repeats · 97 engineered conflicts) |
|---|---|
| write-gate verdicts | 100/100 novels appended · 399/403 repeats consolidated · **97/97 conflicts refused** · false-refusal 0.8% |
| gated recall (all legitimate memories, 25% & 50% cue corruption) | **100%** |
| certificate honesty | **0/800 sampled violations** (bound sound; conservative — worst err/ε ≈ 0) |
| consolidation gain (goal noise σ=0.15) | error **0.079 → 0.034 rad** (noise averages out, as it should) |
| closed loop via shipped efa-1, post-stream store | **100%** · bit-exact · write audit ~8 kFLOP |

**Two findings recorded exactly as measured:**
1. *(v1 negative that defines the gate)* Auditing writes in FULL pattern space misclassifies "same context,
   different goal" conflicts as novel — the goal coordinates make them look far — so they get appended and poison
   recall (65% on protected memories). **The audit must live in context space, where cues actually arrive.**
2. *(a prediction that did not survive measurement)* The expected interference damage to the naive append-all
   store did **not** materialize at this scale — redundant repeat clouds defend its recall. The TTT-analog's real
   measured costs are different: **5× store size, 5× per-recall compute, unbounded growth, contradictions silently
   averaged in, and no certificate.** Stated as found.

## Stage 4 DONE — the perceptual front-end: pixels → embedding → certified memory → controller (`experiments/ebm_efam4.rs`)

The whole pipeline on rendered observations: scenes (landmark layouts) drawn into 24×24 pixels WITH task-irrelevant
dynamic clutter (the arm at a random pose per frame) + pixel noise → frozen random-feature embedding (JL projection,
no training) → the stage-3 certified memory (thresholds **calibrated on held-out scenes**, evaluated on fresh ones)
→ the shipped efa-1.

| gate | result |
|---|---|
| manifold margin (within-scene vs between-scene) | **EXISTS**: 0.395 vs 1.245 median (τ_dup 0.632 < τ_sep 0.766) |
| recall from fresh observations (new clutter + noise) | **100%** (200 probes) · closed loop pixels→recall→efa-1 **100%** |
| novel-scene refusal (cue energy) | **AUROC 1.000**, 100% flagged |
| REAL aliasing wave (one landmark nudged 0.15 rad, contradicting goal) | 17/20 refused with stated reasons; 3 appended past τ_sep — and parent recall stayed **100%** (no silent poisoning; reported as measured) |
| consolidation under 2× pixel noise | 1-shot 95% → 6-shot **100%** |
| certificates on the real manifold | separation min 0.81 (synthetic era ~1.2) — tighter, still sound; ε median 0.35 |
| price · determinism | embed 18.4 + retrieve 16.3 kFLOP · render→embed bit-exact ✓ |

**The stage's central finding (v1 negative → design principle):** a single-frame embedding FAILS outright — the
moving arm dominates the linear features (within-scene ≈ between-scene distance; **margin ABSENT**; recall 61.5%;
14/20 aliases silently appended; parents poisoned to 41%). Generic instantaneous features cannot suppress dynamic
clutter. The training-free fix is structure every robot has: **temporal aggregation** (the scene is static, the
clutter moves — average 8 frames; arm ~1/K, noise ~1/√K, landmarks persist). One change: 61.5→100% recall,
0.687→1.000 AUROC, margin restored. Perception for memory is an *invariance* problem before it is a *features*
problem — measured, not asserted.

## Stage 5 DONE — hidden-property attractors: memory ∩ system-identification (`experiments/ebm_efam5.rs`)

The combination nobody is adjacent to: PhyPush infers a physical property but re-infers every encounter; RoboMME
recalls identity but infers no property. EFA-M does both in one energy — **identify a latent parameter from
interaction, store it as an attractor keyed by the object's perceptual identity (stage 4), recall it without
re-probing, gate on two confidences, and correct the shipped efa-1.**

*Choosing the parameter was itself the result — two recorded negatives:* (A) a constant **load bias** the feedback
policy silently **absorbs** (reach stays 100% — nothing to compensate); (B) an **actuator gain** that genuinely
degrades reach but where naive output-rescaling u/k̂ makes it **worse** (saturates, overshoots) and no scaling can
manufacture missing torque. The clean case is (C) a **sensor/mounting offset δ** — the controller drives the
*observed* angle to goal so the *true* angle settles off by δ, and the feedback loop **cannot self-correct** (it
thinks it succeeded). Gravity depends on the true angle, so interaction leaks δ (δ̂ = argmin SSE over the probe log).

| gate | result |
|---|---|
| identification sharpens with interaction | |δ̂−δ|: 0.140 (K=5) → 0.022 (K=40) → 0.015 (K=80) |
| **control: nominal vs identified+corrected** across |δ| | nominal **100→92→58→20→0%**; corrected **100%** throughout |
| re-encounter, TRUE reach | recall-only 83% · re-probe-every-time 100% · no-memory 42% · **GATED(recall+verify) 100% at 23% of the probe cost** |
| two-confidence gate | recognize AUROC 0.984; posterior width K=5 0.089→"probe", K=40 0.027→"act"; aliasing 20/20 stale recalls caught → re-probe, 0 poison |
| price · determinism | recall 8.2 kFLOP, 0 probes on re-encounter · bit-exact ✓ |

The GATED number is the thesis in one line: **100% reach at 23% probe cost — memory pays where the energy is
confident, interaction pays where it isn't.** Remember / verify / adapt / *price*, all from one energy object,
now including the object's physics. Honest scope: 1-DOF sensor offset as the single parameter (mass/friction/
multi-param = the extension); stage-4 perceptual stand-in; one seed.

## The staged program from here — mechanism program COMPLETE (stages 1–5)
1. ~~Recall + certificate~~ 2. ~~Sequences~~ 3. ~~Certified consolidation~~ 4. ~~Perceptual front-end~~
5. ~~Hidden-property attractors~~ — **all done, all measured on the shipped artifact.**
- **The public stake** (next): DAM-as-Modulator on a π0.5-class backbone = the missing 15th variant on RoboMME's
  leaderboard (CVPR 2026 challenge, ManiSkill observations + learned encoders), + the position paper for CoRL Nov 9.
