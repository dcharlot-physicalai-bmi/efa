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

## The staged program from here
1. ~~Sequence attractors~~ — **done above.**
2. **Certified consolidation**: Hebbian fast-weight writes with a Lyapunov-style convergence guarantee — what
   RoboTTT's TTT does, but verified (our certificate machinery pointed at the memory write).
3. **Perceptual front-end**: contexts from real embeddings (start: our own sim observations; then RoboMME's
   ManiSkill observations).
4. **The public stake**: DAM-as-Modulator on a π0.5-class backbone = the missing 15th variant on RoboMME's
   leaderboard (CVPR 2026 challenge), + the position paper for CoRL Nov 9.
5. **Hidden-property attractors**: basins over latent physical parameters updated by interaction — memory and
   system identification unified in one energy.
