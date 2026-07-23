# EFA-2 — the external-body program

_2026-07-23 · Charlot Lab, Institute for Physical AI @ BMI. EFA-1 proved the architecture identity on our own
simulated chain family; the honest criticism of every number so far is "internal bodies, internal evals." EFA-2
retires it: the EFA recipe on bodies the WORLD defines, measured on metrics the world publishes, so every comparison
is externally reproducible._

## v0 SHIPPED — Gym Pendulum-v1, exact spec (`experiments/ebm_efa2pend.rs`)

**Why this body first:** the canonical published control spec (dynamics, ±2 torque limit, reward, start distribution,
200-step episodes all defined by gymnasium, not us) with published baselines — and a real test, not a lap of honor:
|u| ≤ 2 < mgl makes it a **swing-up** problem whose optimal policy pumps energy, bang-bang, discontinuous at the
bottom — the classically multi-modal regime.

| policy | mean return (100 spec episodes) | upright | cost/decision |
|---|---|---|---|
| random | ≈ −1200 | — | — |
| published anchor: SB3 SAC | ≈ −150 | — | — |
| **efa-2-pendulum, flow K=1** | **−142.6** | **100%** | **1 forward pass** |
| flow K=2 / K=4 | −127.9 / −125.8 | 100% | 2 / 4 passes |
| DP teacher (near-optimal) | −124.5 | 100% | 106 evals |

Verify 98.1% · bit-exact deterministic · gated release (thresholds fixed pre-run), reload-exact ·
release: [physicalai-bmi/efa-2-pendulum](https://huggingface.co/physicalai-bmi/efa-2-pendulum) (19,874 params).

**What v0 established:**
1. **The recipe transfers off our bodies** — flow + contrastive potential on the env's *own* observation vector,
   externally comparable end to end.
2. **The thinking dial is real on an external metric** — K=1→4 climbs −142.6→−125.8 toward the teacher; the first
   accuracy-vs-compute curve EFA can show an outsider on their own scale.
3. **Flow robustness reconfirmed (my prediction was wrong, recorded):** K=1 did *not* fail on the discontinuous
   swing-up policy — imperfect per-state fidelity (mean |a−u*| = 0.276), yet 100% upright closed-loop.
4. **Agency = pricing, still not rescue** (esc ~0.1%; two bodies now agree). Rescue needs a body where K=1 fails.
5. **Train-time determinism**: the release re-run reproduced the entire training and card bit-identically on Ferric.

**Honesty:** distills a model-based DP demonstrator (known dynamics). The claim is *SOTA-level control on the
published metric at one forward pass, verified + deterministic* — **not** "beats SAC at model-free RL." One seed, 2-D.

## The ladder from here

- **v1 — MuJoCo body** (Reacher/standard arm task): 3-D+ external dynamics we don't hand-code; demonstrator via the
  proven FVI-with-value-scaling recipe or MPC; same card discipline.
- **v2 — SO-101-LeRobot**: the maker-standard arm (the Institute's reference platform); LeRobot dataset lineage; the
  bridge from external sim to external *hardware* data.
- **The rescue question stays open and named**: find the external body where K=1 genuinely fails (contact-rich or
  long-horizon), and measure whether the energy-gated ladder converts escalation into reach — the one agency claim
  not yet earned.
