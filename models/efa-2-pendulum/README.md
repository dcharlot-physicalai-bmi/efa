---
license: apache-2.0
library_name: ferric
pipeline_tag: robotics
tags:
- energy-based-model
- control
- physical-ai
- deterministic
- pendulum-v1
- external-benchmark
---

# EFA-2 · Pendulum-v1 (v0 of the external-body program)

**The EFA recipe on the first body the world defines, measured on the metric the world publishes.**
Gym **Pendulum-v1**, exact published spec — dynamics, ±2 torque limit (swing-up regime), reward function, start
distribution, 200-step episodes. Nothing about the task is ours; every number below is externally reproducible
against the same spec.

Charlot Lab · Institute for Physical AI @ Bailey Military Institute.
Runtime: [Ferric](https://ferric.physicalai-bmi.org) (pure-Rust, cross-fabric). Sibling flagship:
[physicalai-bmi/efa-1](https://huggingface.co/physicalai-bmi/efa-1) (multi-body, certified, agency-gated).

## The external card

| policy | mean return (100 spec episodes) | upright at end | cost per decision |
|---|---|---|---|
| random | ≈ −1200 | — | — |
| **published anchor: SB3 SAC** | **≈ −150** | — | 1 fwd pass (256-wide ×2) |
| **efa-2-pendulum, flow K=1** | **−142.6** | **100%** | **1 forward pass** (~21k FLOPs) |
| flow K=2 | −127.9 | 100% | 2 passes |
| flow K=4 | −125.8 | 100% | 4 passes |
| DP teacher (near-optimal) | −124.5 | 100% | 106 evals/decision |

The **thinking dial is real on the external metric**: K=1 → K=4 climbs −142.6 → −125.8, converging toward the
near-optimal teacher. The same potential **verifies** at 98.1% (ranks the demonstrator's action below random), and
decisions are **bit-exact deterministic**. Swing-up — a discontinuous, energy-pumping optimal policy — is solved at
K=1 closed-loop (100% upright from every spec start).

## Honesty (read before citing)

- **This distills a model-based DP demonstrator** (known dynamics, grid value iteration). The claim is *SOTA-level
  control on the published metric at one forward pass, with verification and determinism* — **not** "beats SAC at
  model-free RL." SAC learns from reward alone; this artifact does not.
- One seed, 2-D body. The external-body program continues toward MuJoCo / SO-101-LeRobot.
- The energy gate (τ in `config.json`) escalated on ~0.1% of decisions — K=1 already succeeds closed-loop here, so
  the gate prices compute; it has nothing to rescue. Stated plainly, as on every EFA card.
- Gated release: thresholds fixed before the run (return@K1 ≥ −160 ∧ upright 100% ∧ verify ≥ 90% ∧ bit-exact ∧
  reload-exact); train → gate → save → reload from disk → re-verify. Provenance: `experiments/ebm_efa2pend.rs` in the
  [EFA repo](https://github.com/dcharlot-physicalai-bmi/efa) (69+-experiment ledger, negatives included).

## Architecture

The coordinated pair on the env's **own observation vector** `[cosθ, sinθ, θ̇]`:
- **Flow head** (5→96→96→1): conditional-flow-matched velocity field; `u = clamp(flow(obs, a=0, t=0), ±2)` at K=1;
  K-step integration for the accuracy-vs-compute dial.
- **Potential head** (4→96→96→1): contrastive energy — low = valid action; the model checking its own actions.

License: Apache-2.0.
