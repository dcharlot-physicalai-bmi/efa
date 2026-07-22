# EFA-1 — the first real EFA model

_Spec v0 · 2026-07-22 · Charlot Lab, Institute for Physical AI @ BMI. The goal: a genuinely impactful, bleeding-edge
post-transformer model for Physical AI — the class of artifact a Liquid-AI-ilk lab would ship, built on EFA's own
identity. Positioning is being verified against a live mid-2026 sweep (Liquid, Poolside, Logical Intelligence,
Adaptive, genetic/deterministic AI); this spec states what we build and how it is measured._

## Positioning (mid-2026 sweep, adversarially verified — 23 sources, 14 confirmed findings)

**A fresh landscape check overrode the training-weight framing** (the standing rule after the last frontier check). The
term to lead with is **"Energy-Based Model," not "post-transformer."** At product level in 2026, *no vendor foregrounds
"post-transformer" or "liquid network"* — Liquid AI markets "hybrid architecture"; the one shipping commercial exemplar
of our exact family is **Logical Intelligence** (Kona 1.0, 26 Jun 2026; **Yann LeCun founding board chair**), which
markets **"Energy-Based Model"** and the deterministic frame **"Certainty, Not Probability."** EBM is the banner with
both academic pedigree *and* a shipping exemplar. EFA-1's identity: **an energy-based, certified, deterministic,
multi-body control model** — concrete verifiable qualifiers, not a contested academic banner.

**The comparables, current state (cite these, not my priors):**
- **Liquid AI** — strongest edge comparable. LFM2.5 family (Jan 2026) + LFM2.5-230M (Jun 2026), "hybrid architecture," measures **tok/s + memory, zero watts/joules in any 2026 release.** Physical AI = a language *skill-selection layer* over NVIDIA SONIC on a Unitree G1 — **not a control model.**
- **Logical Intelligence** (the sharpest neighbor) — token-free EBM reasoning that *validates system states beneath AI stacks* ("Certainty, Not Probability"); sells proof/certification for failure-intolerant deployments. **On our certified/energy-gated seam — but as verification infrastructure, not embodied control, and single-purpose, not multi-body.**
- **Cosine** (Lumen Sovereign) — transformer-lineage sovereign code/enterprise LLM; identity = sovereignty & compute provenance. Orthogonal.
- **Adaptive ML** — **acquired by Datadog (30 Jun 2026)**, folded into Datadog AI Research; was an RLOps platform tuning *others'* models, never a foundation model. Cite as historical.
- **"Genetic intelligence"** — not a company; = LLM-as-mutation evolutionary program search (FunSearch → AlphaEvolve → ShinkaEvolve/OpenEvolve; Darwin Gödel Machine, SWE-bench 20→50%). **Critically, that lineage *abandons* formal certificates for empirical benchmark validation** — the opposite of EFA's cert seam. We use seeded ES as a *tool*, not as the identity.
- **Determinism** — a *solved-but-unshipped engineering* problem (Thinking Machines: the cause is batch non-invariance, ~1.6× overhead). **No physical-AI product ships bit-reproducibility.**

**The empty center (EFA-1's honest slot).** Each of EFA-1's four identity metrics is *unclaimed at product level*:
**joules-per-task** — unclaimed by everyone, even Liquid; **certified/verified action** — flanked by Logical Intelligence
but for *verification infra*, not control; **determinism** — available but shipped by no physical-AI product;
**multi-body-per-weights control** — unclaimed by any surviving comparable (the nearest physical-AI datapoint is Liquid's
*language-only* skill-selection). EFA-1's differentiator against the sharpest neighbor is one sentence: **Logical
Intelligence *verifies* beneath the stack; EFA-1 *controls* the body — energy-gated, on many bodies, measured in joules,
reproducible bit-for-bit across fabrics.**

## The identity (what "SOTA" means here)

**Token throughput and parameter count are meaningless in this architecture class, and EFA-1's card refuses them.**
EFA-1 is measured on what matters for machines that act:

| identity axis | metric on the card |
|---|---|
| capability | reach% per body, per task family |
| energy | FLOPs & estimated joules **per decision** and **per task** |
| verification | % of actions scored valid by the model's own energy before execution |
| agency cost | tool-escalation rate (how often it must think harder / call a tool) |
| safety | certified contraction region per body; structural dE/dt≤0 where mechanical |
| determinism | bit-reproducible decisions given (state, goal, seed) — across fabrics (Metal ⇄ WebGPU) |
| generality | **bodies per model** — one weights file, N embodiments |
| footprint | KB on disk, KB in memory (stated as footprint, never as capability) |

## The architecture (the coordinated energy family, productized)

One shared trunk over `[state-encoding ⊕ goal-encoding ⊕ body-embedding]`, with coordinated heads — the corrected
2026-frontier recipe end-to-end:

1. **Flow head** (actuation): velocity field integrated K forward passes — the recipe that beat Gᵈ planners
   (100% @ K=1 on 3-DOF vs 57% @ 152 evals). Masked to each body's action dimension.
2. **Potential head** (verify + hybrid): scalar energy — low = valid action; its action-gradient carries the majority
   of the field where the hybrid configuration is used (the measured 65%-energy-first dial).
3. **Body embedding**: a learned vector per embodiment — one model, many bodies. v1 bodies: pendulum (1-DOF),
   coupled 2-chain, coupled 3-chain, cart-pole (4-D, underactuated). The suite grows without retraining the identity.

## The agency loop (energy-gated, deterministic, tool-using)

The bleeding-edge mechanism — **the energy decides when to think and when to reach for tools**, and every step is
seeded and reproducible:

```
decide(state, goal, body, seed):
  a ← flow(state, goal, body, K=1)                 # 1 forward pass — the cheap path
  if E(state, a, goal, body) > τ_body:             # the model's own verifier objects
    a ← flow(..., K=4)                             # think harder (EBT-style compute escalation)
    if E(...) > τ:  a ← planner_tool(state)        # tool 1: discrete argmin over the value (the expensive Gᵈ tool)
    if E(...) > τ:  a ← genetic_tool(state, seed)  # tool 2: seeded evolution over the action space (gradient-free)
  execute argmin-E among candidates                # deterministic: same inputs + seed ⇒ same action, bit-for-bit
```

- **EBM/EBT**: the potential is the verifier and the thinking dial (K).
- **Tool use**: the planner and the evolutionary searcher are *tools the energy invokes* — agency as escalation, priced
  per call, logged on the card as the escalation rate.
- **Genetic intelligence**: seeded evolution-strategy search as the gradient-free fallback — the robust tool when the
  landscape defeats descent (our own ES lineage from the Forge learning loop).
- **Deterministic AI**: every path is seeded; Ferric's cross-fabric determinism (matmul bit-identical Metal ⇄ WebGPU,
  proven) makes the *same decision on a laptop, a robot, and a browser tab* — a claim none of the comparison labs make.

## Training plan (honest, staged)

- **Stage A** — per-body demonstrators: the proven FVI recipes (with the value-scaling fix; seed-gated).
- **Stage B** — one multi-body distillation: conditional flow matching + contrastive verify into the shared trunk,
  body-embedding-conditioned; gate **per body** (release thresholds fixed first, seed retry disclosed).
- **Stage C** — certificates: contraction region per body (grid → interval-hull note), structural pH for mechanical.
- **Stage D** — the agency loop calibrated (τ per body from validation quantiles), determinism test
  (native ⇄ WASM bit-comparison), card written from measurements only.

Release: `physicalai-bmi/efa-1` — safetensors + config (env specs, τ, certificates, escalation policy) + the honest
card + Ferric loader + both browser demos upgraded to EFA-1.

## Honest scope (stated before building)

- v1 bodies are **our simulated suite** — real and coupled and underactuated, but small. The claim is the
  **architecture identity** (energy-verified, certified, deterministic, tool-using, multi-body, joules-measured),
  not manipulation breadth. **EFA-2 targets a standard external body** (MuJoCo task suite / SO-101-LeRobot) so the
  comparison to GR00T/π-class evaluations becomes external and reproducible.
- The underactuated boundary is known (greedy values can't pump a passive joint); EFA-1 includes cart-pole via the
  LQR-grade demonstrator lineage, and the passive-joint case stays on the ledger as open until the energy-shaping
  demonstrator lands.
- Every number on the card comes from the gated, round-trip-verified artifact — the release pipeline already built.
