---
license: apache-2.0
library_name: ferric
pipeline_tag: robotics
tags:
- energy-based-model
- control
- physical-ai
- deterministic
- certified
- multi-body
---

# EFA-1

**An energy-based, certified, deterministic, multi-body control model** — one body-embedding-conditioned trunk that
controls a *family* of bodies from a single weights file. Swap the body embedding, control a different body.

Charlot Lab · Institute for Physical AI @ Bailey Military Institute.
Runtime: [Ferric](https://ferric.physicalai-bmi.org) (pure-Rust, cross-fabric: Metal / WebGPU / Vulkan / browser).

## Identity — measured in what matters for machines that act

**This card refuses tokens and parameter-count-as-capability.** In a post-transformer control model those numbers carry
no meaning; the identity axes are:

| axis | EFA-1 (gated, round-trip-verified; exact numbers in `config.json`) |
|---|---|
| capability | reach% per body at **K=1 forward pass** (flagship run: 100% on all three bodies) |
| verification | the model's **own potential** ranks good actions below bad, per body (97–99%) |
| energy | ~39 kFLOP **per decision** — vs a discrete Gᵈ planner's 7× / 31× / **140×** more as DOF grows |
| safety | **certified closed loop**: exponential stability at every measured attractor (ρ(A)<1) + a contraction core (Lyapunov P-metric) + a **funnel basin certificate covering 100% of grid nodes over the full physical domain** per body (limits disclosed below) |
| agency | **energy-gated tool ladder** (K=1 → K=4 → planner tool → seeded ES, all deterministic): escalates on ≤0.2% of in-distribution decisions, **17× more on out-of-band goals** — the model's own energy detects difficulty and prices the extra compute (78→161 kFLOP/decision) |
| determinism | same (state, goal) ⇒ same action, **bit-for-bit**; Ferric extends this cross-fabric (Metal ⇄ WebGPU) |
| generality | **3 bodies per weights file** (1-, 2-, 3-joint coupled chains), one learned embedding row each |
| footprint | ~39k params ≈ 160 KB — stated as *footprint*, never as capability |

## Architecture

The **coordinated energy family on one latent** (the corrected 2026 recipe, end-to-end):
- **Shared trunk inputs**: a body-agnostic 12-wide joint encoding (4 features per joint: cos(θ−g), sin(θ−g), ω, sinθ;
  inactive joints zero) ⊕ a learned **body embedding** (one row per body).
- **Flow head** (actuation): 3-wide velocity field, masked to the body's DOF, integrated at K=1 — no iterative energy
  descent over actions (the recipe the field's own evidence retired), no BPTT.
- **Potential head** (verify): a scalar energy over (state, action, body) — **low = valid**; trained contrastively;
  this is the model checking its own actions.

Inference (from `config.json`): `u = clamp(flow(feat, a=0, t=0, emb[body])[:dof])`; verify any candidate action by
`potential(feat, a, emb[body])`.

## The agency loop (in `config.json` → `agency`)

The model's **own energy decides when to think harder and when to reach for tools** — every path seeded, the full
ladder bit-exact deterministic (measured): `L1` flow K=1 → if E>τ `L2` flow K=4 → `L3` planner tool (discrete argmin
over the model's own potential) → `L4` seeded evolution search; execute the argmin-E candidate. τ per body ships in the
config (95th percentile of validation energy — calibrated from the artifact alone). Measured behavior: in-distribution
the energy is content (≤0.2% escalation, cost ≈ the K=1 baseline); on goals **outside the training band** escalation
rises 17× on the 3-DOF body and mean cost prices honestly (78→161 kFLOP/decision). Stated plainly: at this scale the
tools bought **no additional reach** — K=1 already generalizes to 93–100% out-of-band — so the ladder's demonstrated
value is *calibrated difficulty detection and compute pricing*, not rescue; the L4 genetic tool never fired at natural τ.

## Lineage & honesty (read before using)

- Built by the EFA program's gated release pipeline: **train → gate (every body reach ≥95% AND verify ≥90% AND
  bit-exact) → save → reload from disk → re-verify** — only verified weights ship. Provenance: `experiments/ebm_efa1.rs`;
  the 69-experiment [validation ledger](https://github.com/dcharlot-physicalai-bmi/efa/blob/main/docs/RESULTS.md)
  (negatives included), the [2026 frontier check](https://github.com/dcharlot-physicalai-bmi/efa/blob/main/docs/FRONTIER-CHECK-2026.md)
  that corrected the recipe, and the [EFA-1 spec](https://github.com/dcharlot-physicalai-bmi/efa/blob/main/docs/EFA-1.md)
  with the verified mid-2026 positioning.
- **Simulated bodies** (coupled-pendulum-chain family, dynamics in `config.json`), reachable-goal sets, distilled from
  per-body fitted-value demonstrators, one gated seed. The claim is the **architecture identity** —
  multi-body-per-weights + energy-verified + deterministic + joules-metered — not manipulation breadth.
- **Certificates — computed on this artifact's closed loop** (exact numbers in `config.json`): every (body, goal) loop
  converges to a true fixed point (‖f(x*)−x*‖ ≤ 1e-8) within 0.05–0.32 rad of the goal — inside the card's 0.35
  criterion; **local exponential stability certified** at every attractor (ρ(A) = 0.89 / 0.95 / 0.96 < 1); a
  contraction core in the Lyapunov metric of the closed-loop linearization (certified ball r = 0.76 / 0.42 / 0.64 in
  P-norm; 100% empirical convergence from inside). **Basin certificate (funnel composition, LQR-tree-style): 100.0% of
  grid nodes over the FULL physical domain (θ on the whole circle × ω in the measured transient envelope) provably
  enter that contraction core** — 1,353 / 74,529 / 456,533 nodes per body, median entry 34 / 62 / 66 steps, zero
  no-entries, worst sampled funnel expansion σ_P(Φ) = 117.5 / 18.1 / 59.3.
  **Multi-goal: ALL 12 (body, goal) pairs — every card goal on every body — certify at 100.0% of the full physical
  domain** (per-goal attractors and cores in `certificates_multigoal`; core radii 0.25–1.20, goal-dependent).
  Limits stated plainly: grid-sampled and node-local — no claim between nodes (the measure-zero separatrix lies there);
  not an interval/SMT proof. The continuum gap is *quantified, not hand-waved*: scalar orbit-tube bounds were computed
  and **fail honestly** (`certificates_tube` — the norms-product bound loses the directional cancellation that the
  measured funnel expansion σ_P(Φ) = 18–117 enjoys; full-coverage grids would need infeasible node counts). The
  rigorous continuum route is named: matrix/ellipsoidal tubes, then interval/CROWN bound propagation with
  branch-and-bound — neural-verification tooling, a real project. The recorded negatives that shaped the method:
  identity-metric contraction fails; a full-circle one-step metric field must fail (topological obstruction);
  cell-granular region-growth stalls when the core is smaller than a grid cell. The harness was validated first:
  the certifying reconstruction reproduces the shipped card 100/100/100 before any number was trusted.
  Provenance: `experiments/ebm_efa1cert{,2,3,4,5}.rs`, `experiments/ebm_efa1tube.rs`.
- Underactuated bodies remain a measured open boundary (ledger). EFA-2 targets a standard external body
  (MuJoCo / SO-101-LeRobot) so comparisons become externally reproducible.

## Positioning (verified mid-2026, cited in the spec)

Each of EFA-1's identity axes is unclaimed at product level by the current comparables: the leading edge lab measures
tok/s + memory (no joules); the nearest energy-based neighbor verifies *beneath* AI stacks but does not control bodies;
no physical-AI product ships bit-reproducibility; no surviving comparable ships multi-body-per-weights control.
**They verify beneath the stack; EFA-1 controls the body.**

License: Apache-2.0.
