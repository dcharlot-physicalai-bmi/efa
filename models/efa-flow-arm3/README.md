# efa-flow-arm3

**EFA (Energy First Architecture) open-weight release #2** — the flow-matching actuation policy for a **3-joint coupled
chain** (6-D state, 3 torques): a velocity field `v(s,a,t)` integrated **K forward passes** from `a=0`. The corrected
2026 recipe — no iterative energy descent over actions, no BPTT — on the body where the discrete Gᵈ approach degrades.

Charlot Lab · Institute for Physical AI @ Bailey Military Institute.
Runtime: [Ferric](https://ferric.physicalai-bmi.org) (pure-Rust, cross-fabric: Metal / WebGPU / Vulkan / browser).

**▶ Live demo — steer these exact weights in your browser (fetched from this repo, run on-device, no GPU):** https://physicalai-bmi.org/assets/sims/efa-weights · **WebGPU via Ferric-WASM:** https://ferric.physicalai-bmi.org/efa

## Why this model matters

The eval-budget scaling result, as weights: a discrete argmin controller on this body costs **125–152 action-evaluations
per decision** (exponential in DOF) and, in the flagship run, reaches **57%**; this flow policy reaches **~100% at a
single forward pass** (constant in DOF) — cheaper *and* better as DOF grows. The "student beats teacher" effect
(distillation exceeding its demonstrator's closed loop) was confirmed across three teacher variants in the ledger.

## Architecture & inference

- Velocity field `v(s,a,t)`: relu/linear MLP over `[cos(θᵢ−gᵢ), sin(θᵢ−gᵢ), ωᵢ for i=1..3, sin θ₁..₃, a₁, a₂, a₃, t]` → 3-D velocity.
- **Inference** (K=1 sufficient): `a←0; for k in 0..K { t=k/K; a += v(s,a,t)/K }` — clamp to ±UMAX and apply.
- Trained by conditional flow matching to a two-stage argmin demonstrator over a fitted value (HV=128).

## Metrics (this artifact — gated, round-trip-verified; exact numbers in `config.json`)

Release gate: **reach ≥95% at K=1**, best-of-≤3 seeds; shipped weights **reloaded from this file and re-evaluated**
before release. Context from the ledger: flagship run flow 100% @ K=1 vs teacher 57% @ 152 evals; **multi-seed study:
2/3 lightened-recipe seeds reached 100%, one seed's failed value-training capped its flow at 25%** — the variance source
is FVI value-training stability, and the gate exists precisely because of it.

## Honest limits (read before using)

- **Simulated toy body** (3-link chain, dynamics in `config.json`); fully-actuated — the underactuated variant is a
  *measured open boundary* (ledger: both teacher and flow ~22%; a greedy 1-step value has no plan for pumping a passive joint).
- **Distilled**: quality is bounded below by nothing but bounded *in kind* by the demonstrator's coverage; the flow
  exceeds its teacher's closed-loop but cannot exceed its plan.
- Not energy-first per se: this is the plain velocity net (the *hybrid* sibling, `efa-hybrid-arm2`, carries the
  potential); a gradient-structured 3-DOF hybrid is future work.
- Trained & evaluated entirely on-device via Ferric; no external data.

## Provenance

Built by `experiments/efa_release3.rs` (train → gate → save → **reload → re-verify**). Program record: `WHITEPAPER.md`
§3.9, `docs/RESULTS.md` §VII, `docs/FRONTIER-CHECK-2026.md` (the 2026 frontier check that corrected the recipe).

License: Apache-2.0.
