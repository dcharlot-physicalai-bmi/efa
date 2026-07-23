# EFA-1 — the first real EFA model

_Spec v0 · 2026-07-22 · Charlot Lab, Institute for Physical AI @ BMI. The goal: a genuinely impactful, bleeding-edge
post-transformer model for Physical AI — the class of artifact a Liquid-AI-ilk lab would ship, built on EFA's own
identity. Positioning is being verified against a live mid-2026 sweep (Liquid, Poolside, Logical Intelligence,
Adaptive, genetic/deterministic AI); this spec states what we build and how it is measured._

## v1 result — the multi-body trunk works (`experiments/ebm_efa1.rs`)

**One 39,318-param trunk + one body-embedding table controls three morphologies (1-, 2-, 3-joint chains) — swap the
embedding, control a different body.** Stage A: 3 per-body FVI demonstrators. Stage B: one shared flow net + one shared
potential net + a learned body embedding, distilled from a mixed-body stream (masked action dims). Identity card:

| body | reach (flow, K=1) | verify | FLOPs/decision | vs discrete Gᵈ | determinism |
|---|---|---|---|---|---|
| 1-DOF | **100%** | 97.4% | 39,168 | 7× | bit-exact ✓ |
| 2-DOF | **100%** | 98.0% | 39,168 | 31× | bit-exact ✓ |
| 3-DOF | **100%** | 99.3% | 39,168 | **140×** | bit-exact ✓ |

100% reach on all three at a single forward pass; the shared potential verifies per-body (99%+); the FLOP-per-decision
edge over the discrete planner **grows with DOF** (7→31→140×) — perf-per-joule compounding on a multi-body model. Bit-exact
determinism per body (Ferric extends it cross-fabric). Honest scope: reachable-goal sets, our simulated chain family, one
seed; the *architecture identity* is the claim (multi-body-per-weights + energy-verified + deterministic + joules-metered),
not manipulation breadth — EFA-2 targets a standard external body. Underactuated/cart-pole stays the known open boundary.

### Stage C done — the shipped closed loop is certified (`experiments/ebm_efa1cert.rs`)

Computed **on the released safetensors** (harness validated first: the certifying reconstruction reproduces the card
100/100/100 before any number was trusted). Findings, now shipped inside `config.json`:

| body | attractor residual (4 goals, max) | ρ(A) at x* | P-metric certified basin | certified ball (P-norm) | empirical from inside |
|---|---|---|---|---|---|
| 1-DOF | 0.24 rad | **0.890** | 31.6% of box | r = 0.76 | 100% |
| 2-DOF | 0.23 rad | **0.952** | 9.8% | r = 0.42 | 100% |
| 3-DOF | 0.32 rad | **0.956** | 2.6% | r = 0.64 | 100% |

Every (body, goal) loop converges to a **true fixed point** (‖f(x*)−x*‖ ≤ 1e-8) inside the card's 0.35-rad criterion;
local exponential stability holds at every attractor. The recorded negative that forced the right lens: identity-metric
one-step contraction fails (24.5/2.6/0.1%) — the loop contracts in the **Lyapunov metric of its own linearization**
(σmax(P^½JP^-½)<1, AᵀPA−P=−I), the same correction ebm_contract found on the single body. Limits stated: grid-sampled
(rigorous = interval/CROWN), one goal certified per body, constant P (a state-dependent metric would certify more).

### Stage D done — the agency loop, measured on the shipped artifact (`experiments/ebm_efa1agency.rs`)

The spec's ladder is now in the artifact (`config.json` → `agency`): **L1 flow K=1 → (E>τ) L2 flow K=4 → L3 planner
tool (argmin over the model's own potential) → L4 seeded ES; execute argmin-E; full ladder bit-exact deterministic
(measured, tools included).** τ per body = 95th pct of validation energy, calibrated from the artifact alone
(2.38/1.88/1.55). Measured:

| eval | body | reach K1 / agency | escalation L2/L3/L4 (% of decisions) | mean kFLOP/decision |
|---|---|---|---|---|
| in-dist | 1/2/3-DOF | 100/100/100 = same | ≤ 0.2 / 0.2 / 0.0 | 77.9–90.7 |
| OOD goals (|g|∈[1.05,1.35], outside ±1.0 training band) | 1/2/3-DOF | 100/93/98 = same | up to **3.4 / 1.3** / 0.0 | up to **161.1** |

Two findings, both stated plainly: (1) **the energy is a calibrated difficulty detector** — escalation is ~0 where the
model is competent and rises 17× on out-of-band 3-DOF goals, pricing the extra thought honestly (78→161 kFLOP);
(2) **the tools bought no additional reach at this scale** — plain K=1 already generalizes to 93–100% *outside its
training band* (a notable measured fact on its own), so the remaining failures are ones the in-band-trained potential
cannot rescue either. The ladder's demonstrated value = adaptive compute + honest pricing + determinism through tools;
rescue-by-tools awaits a regime where K=1 actually fails (EFA-2's harder bodies).

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
card + Ferric loader + both browser demos upgraded to EFA-1. **All shipped**: the pure-JS page
([physicalai-bmi.org/assets/sims/efa-weights.html](https://physicalai-bmi.org/assets/sims/efa-weights.html)) and the
Ferric-WASM WebGPU page ([ferric.physicalai-bmi.org/efa](https://ferric.physicalai-bmi.org/efa)) both default to the
EFA-1 tab — one trunk, three switchable bodies, the **agency gate live in the HUD** (E vs the τ shipped in
config.json, per decision; on the WebGPU page the gate runs inside the WASM call). Both headless-verified: gate reads
per-body τ (2.38/1.88/1.55), verify 91–96% at random probes, at-goal on body switch, zero console errors.

## Honest scope (stated before building)

- v1 bodies are **our simulated suite** — real and coupled and underactuated, but small. The claim is the
  **architecture identity** (energy-verified, certified, deterministic, tool-using, multi-body, joules-measured),
  not manipulation breadth. **EFA-2 targets a standard external body** (MuJoCo task suite / SO-101-LeRobot) so the
  comparison to GR00T/π-class evaluations becomes external and reproducible.
- The underactuated boundary is known (greedy values can't pump a passive joint); EFA-1 includes cart-pole via the
  LQR-grade demonstrator lineage, and the passive-joint case stays on the ledger as open until the energy-shaping
  demonstrator lands.
- Every number on the card comes from the gated, round-trip-verified artifact — the release pipeline already built.
