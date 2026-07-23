---
license: apache-2.0
library_name: ferric
pipeline_tag: robotics
tags:
- energy-based-model
- control
- physical-ai
- deterministic
- acrobot-v1
- underactuated
- agency
- external-benchmark
---

# EFA-2 · Acrobot-v1 — the rescue artifact

**The first EFA body where the energy gate doesn't just price compute — it rescues.**
Gym **Acrobot-v1**, exact published spec: underactuated (torque on the elbow only, actions {−1, 0, +1}), RK4
dynamics, reward −1/step, 500-step cap. Nothing about the task is ours.

Charlot Lab · Institute for Physical AI @ Bailey Military Institute.
Runtime: [Ferric](https://ferric.physicalai-bmi.org). Siblings: [efa-1](https://huggingface.co/physicalai-bmi/efa-1)
(flagship, certified multi-body) · [efa-2-pendulum](https://huggingface.co/physicalai-bmi/efa-2-pendulum).

## Why this body: the principled K=1 failure

From the hanging start, the optimal pumping torque is **symmetric-bimodal (±1)** — and a flow policy's conditional
expectation at `a₀ = 0` averages the modes toward zero. Measured on this artifact: **K=1 rounds to zero torque on 54%
of hanging-region probes**. That is the classically multi-modal regime where one-shot policies fail — and it shows in
the closed loop.

## The rescue, measured (100 spec episodes each; never-solve = −500)

| policy | mean return | solved | extra compute |
|---|---|---|---|
| flow K=1 (no gate) | −109.9 | **96%** | — |
| **AGENCY: E>τ → K=4 → planner** | **−88.0** | **100%** | on **5.3%** of decisions |
| flow K=4 always | −80.9 | 100% | on 100% of decisions (4×) |
| planner tool always (argmin own E) | −84.8 | 100% | 3 E-evals every step |
| DP teacher (near-optimal) | −84.7 | 100% | 106 evals/step |

**The model's own energy objects on ~5% of decisions, and that objection converts into solved episodes**: +21.9 mean
return, 96→100% solved, capturing most of always-K=4's gain at a small fraction of its marginal compute. The same
potential verifies at 94.5% (ranks the demonstrator's action below the alternatives); every path — including both
tools — is bit-exact deterministic. τ ships in `config.json` (95th pct of validation energy, from the artifact alone).

## Honesty (read before citing)

- Distills a **model-based DP demonstrator** (grid value iteration on the known dynamics; teacher −84.7, 100%,
  squarely in the published good-policy band ≈ −80..−100). Not a model-free-RL comparison.
- K=1's failure is partial (96% solved but slow); the rescue Δ decomposes as ≈16 points from eliminating the four
  never-solves + the rest from faster solutions. One seed.
- ω observation components are scaled by their spec bounds (disclosed in `config.json`).
- Gated release: agency return ≥ −150 ∧ verify ≥ 90% ∧ bit-exact ∧ reload-exact — thresholds fixed before the run.
  Provenance: `experiments/ebm_efa2acro.rs` in the [EFA repo](https://github.com/dcharlot-physicalai-bmi/efa)
  (ledger with negatives included — on two easier bodies the gate found *nothing to rescue*, and the cards say so).

## Architecture

Coordinated pair on the env's own observation `[cosθ₁, sinθ₁, cosθ₂, sinθ₂, ω₁/4π, ω₂/9π]`:
flow head (8→96→96→1, CFM; K-step integration, rounded to the 3 legal actions) + potential head (7→96→96→1,
contrastive; the verifier, the gate, and the planner tool's objective — one energy, three roles).

License: Apache-2.0.
