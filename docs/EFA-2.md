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

## v0.5 SHIPPED — Acrobot-v1: THE RESCUE ARTIFACT (`experiments/ebm_efa2acro.rs`)

**The one agency claim not yet earned, now earned — on an external, underactuated spec.** Gym Acrobot-v1, exact
published dynamics (torque on the elbow only, a ∈ {−1,0,+1}, RK4, −1/step, cap 500). The body was chosen because K=1
has a *principled* failure mode here: symmetric-bimodal pumping torque averages toward zero under CFM — and the
diagnostic confirmed it (**K=1 rounds to zero torque on 54% of hanging-region probes**).

| policy | mean return | solved | extra compute on |
|---|---|---|---|
| flow K=1 | −109.9 | 96% | — |
| **AGENCY (E>τ → K=4 → planner)** | **−88.0** | **100%** | **5.3%** of decisions |
| flow K=4 always | −80.9 | 100% | 100% of decisions |
| planner-always (argmin own E) | −84.8 | 100% | every step |
| DP teacher | −84.7 | 100% | 106 evals/step |

**The model's own energy objects on ~5% of decisions and that objection converts into solved episodes** (+21.9 return,
96→100% solve; ≈16 pts of the Δ = the four never-solves eliminated, the rest faster solutions) — capturing most of
always-K=4's gain at a small fraction of its marginal compute. Verify 94.5% (potential ≈ teacher-grade as a 3-action
selector: planner-always −84.8 vs teacher −84.7); full ladder bit-exact; train-time determinism reproduced the entire
measurement bit-identically on the release re-run. Gated release (thresholds pre-fixed), reload-exact:
[physicalai-bmi/efa-2-acrobot](https://huggingface.co/physicalai-bmi/efa-2-acrobot) (20,450 params). One seed; ω obs
scaled by spec bounds (disclosed); model-based demonstrator (not a model-free-RL comparison).

The agency story across three bodies is now complete and honest: **on bodies where K=1 succeeds, the gate stays
silent and near-free (≤0.2% escalation); on the body where K=1 genuinely fails, the same gate converts escalation
into solving.** That is the energy-gated ladder doing exactly what the EFA-1 spec claimed it was for.

## SAPIEN → Rust: the articulated-dynamics core, VERIFIED (`experiments/sim_planar.rs`)

The directive: don't touch Python — port the simulator to Rust. Full SAPIEN is PhysX rigid-body + contact solver +
collision meshes + GPU; its **control-relevant heart** is the articulated forward-dynamics solver, and that is ported
and verified first so every ManiSkill number afterward stands on physics we can prove correct. A general planar
n-link revolute engine with exact Lagrangian dynamics — `M(q)q̈ + C(q,q̇)q̇ + g(q) = τ`, `M` from COM Jacobians,
Christoffel Coriolis from ∂M/∂q, Gaussian-elimination solve, RK4.

**Verification (rigorous, convention-independent):**
- single link reproduces the analytic θ̈ = −sinθ to **machine precision** (0 error in f64);
- τ=0 energy drift falls **~15× per dt-halving** = RK4's 4th order — which *proves* M, C, g are mutually exact
  (a wrong Coriolis leaks energy at O(1); the 4th-order decay is only possible if the model is right);
- work-energy: ∫τ·q̇ dt = ΔE to 3.6e-4.
- Honest precision note: f32 (the deployment precision) leaves a ~1e-3 energy floor from finite-difference-Coriolis
  roundoff — diagnosed by the dt-sweep (it plateaus in f32, converges in f64); analytic Coriolis removes it.

**Named next layers (not built here):** contact/friction impulse solver + collision geometry → then a ManiSkill-class
task (Reacher-style needs no contacts; PushCube-style needs the impulse solver) instantiated on this core, driven by
the EFA flow controller, reported on the world's published metric — the same discipline as Pendulum-v1 / Acrobot-v1,
now on our own verified Rust substrate.

## First task on the port — REACHER (`experiments/efa2_reacher.rs`)

A 2-link planar arm reaching a random target on the verified engine (g=0, no contacts — the Reacher-v4 class). Exact
IK (0.0 fingertip residual), a tanh-soft-saturated PD demonstrator at **98%**, and the EFA flow controller distilled
from it by conditional flow matching.

| K (forward passes/decision) | reach (fingertip < 0.12) | mean final distance |
|---|---|---|
| K=1 | 63% | 0.113 |
| K=2 | 84% | 0.078 |
| K=4 | 88% | 0.073 |
| K=8 | **90%** | 0.073 |
| tanh-PD demonstrator | 98% | 0.009 |

## SAPIEN → Rust step 2 — the CONTACT/FRICTION layer, VERIFIED (`experiments/sim_contact.rs`)

The next piece after the articulation core: a 2D rigid-body world with an impulse-based contact solver (the class
PhysX/Box2D use) + Coulomb friction, so the manipulation tasks that NEED contact (PushCube-class — the majority of
ManiSkill) can run on our own Rust substrate. Sequential-impulse LCP with a friction cone; **verified against four
analytic cases:** restitution (rebound peak = e²·h to ~1%, e∈{0,0.5,1}); Coulomb friction (below μN → no slide;
above → a=(F−μN)/m — both sides of the threshold exact); elastic 1-D collision (momentum & KE conserved to 1e-15);
resting stability (penetration <2e-4, zero drift over 10 s). The recorded bug that shaped it: a Baumgarte velocity
bias injects energy on repeated elastic bounces (e=1 rebounded to 6.2×h) — restitution must be **velocity-only**,
penetration handled by position projection. Next: couple the arm end-effector as the pusher → a PushCube-class task on
the combined verified articulation + contact stack, driven by the EFA flow controller.

## Reacher — the honest read

**The thinking-dial is real on an articulated manipulation task** — reach climbs 63→90% as K grows,
approaching the demonstrator; determinism bit-exact. The **K=1≥90% gate was not met (63%)** — the endpoint-precision
gap at small torques (near the target the required torque is small and the flow's residual dominates) is what the
extra integration steps close. Two findings recorded: (1) a **hard-clamped** PD is un-distillable (near-discontinuous
at the saturation boundary — 55% reach); **tanh soft-saturation** is (distillable, 90% at K=8); (2) the K=1 gate wants
the **hybrid flow+correction** (`v = −κ∇ₐE + w`, ledger-proven 100% on 2-DOF) — the named next build, not chased with
blind capacity. Scope: disclosed params (not a byte-match to MuJoCo Reacher's inertias — the contact-free articulated
task is faithfully the same class); distills a PD demonstrator; one seed.

## The ladder from here

- **v1 — MuJoCo body** (Reacher/standard arm task): 3-D+ external dynamics we don't hand-code; demonstrator via the
  proven FVI-with-value-scaling recipe or MPC; same card discipline.
- **v2 — SO-101-LeRobot**: the maker-standard arm (the Institute's reference platform); LeRobot dataset lineage; the
  bridge from external sim to external *hardware* data.
- **The rescue question stays open and named**: find the external body where K=1 genuinely fails (contact-rich or
  long-horizon), and measure whether the energy-gated ladder converts escalation into reach — the one agency claim
  not yet earned.
