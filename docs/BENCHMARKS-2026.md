# Physical-AI Benchmarks 2026 — the landscape and the championing plan

_2026-07-23 · Charlot Lab, Institute for Physical AI @ BMI. Three-agent global sweep (manipulation/VLA · simulators &
locomotion · memory/enablement), verified July 2026. The directive this serves: pursue physical-AI **enablement, not
control**, through the brain-inspired energy-first architecture — and champion the commonly-accepted benchmarks via
test harness and simulation._

## I. The landscape, compressed

**The harness layer consolidated in one week.** July 7, 2026: LeRobot v0.6.0 ships nine Dockerized benchmark families
under one CLI (`lerobot-eval`) with a documented "Adding a New Benchmark" path; the same day, AllenAI's vla-eval
harness v0.4.0 ships 18 benchmarks × every major model server — the lm-eval-harness moment for VLA (one `predict()`
integrates a model; 2,456 models × 18 benchmarks aggregated). These two are the eval spine of the field now.

**Must-report set (table stakes for a manipulation/VLA lab):** LIBERO (~98% handshake; saturated) + **LIBERO-plus**
(robustness under 10k perturbations — where reviewers actually look; SOTA ~79%), **SimplerEnv** (real-to-sim proxy),
**RoboCasa365** (the new scale benchmark, unsaturated), **one real-robot number** (RoboChallenge Table30 accepts
policy-server submissions — no hardware needed), CALVIN (final year of relevance).

**The simulator war is over: MuJoCo won.** Newton 1.0 (NVIDIA+DeepMind+Disney, Linux Foundation) uses MuJoCo-Warp as
primary solver; Isaac Lab 3.0's headline feature is running *without Isaac Sim* on Newton/MuJoCo physics. MuJoCo now
has four fronts: C/CPU, MJX/JAX, Warp/CUDA, and — new — **official in-tree WASM bindings (`@mujoco/mujoco` on npm)**.
ManiSkill/SAPIEN holds the manipulation-benchmark substrate niche (SimplerEnv, RoboTwin, **RoboMME** run on it).

**The enablement axis the field itself names: MEMORY — genuinely unsolved.** RoboMME (ICML 2026 **Oral**, CVPR 2026
live challenge, LeRobot + vla-eval ingested) is the accepted benchmark: 16 tasks × 4 cognitive memory types; best
non-oracle result **44.5% vs 90.5% human**; finding: *"no single representation or integration strategy dominates."*
The frontier is moving here fast: NVIDIA RoboTTT (July 2026 — TTT fast weights in GR00T, "context length as a new
scaling axis"), Physical Intelligence MEM, a dozen keyframe/memory papers, and a **CoRL 2026 workshop "Memory for
Robot Foundation Models" (Nov 9, organizer: Yilun Du)** whose open problem #6 is *"evaluation metrics distinguishing
genuine memory use from shortcuts."*

**Two facts that define our openings:**
1. **The Hopfield/energy-based memory slot is EMPTY** — verified three ways: no papers combine attractor/DAM memory
   with robot policies; the community reading list has zero energy-based entries; RoboMME's own 14-variant grid
   (symbolic/perceptual/recurrent) contains **no associative-memory family at all**. Its results read as a spec for
   one: perceptual representation wins, light modulation wins, recurrent write-mechanisms fail, symbolic needs oracle
   grounding — attractor memory is simultaneously perceptual, content-addressable, and naturally modulatory.
2. **No leaderboard anywhere scores efficiency or determinism of a policy.** Joules-of-compute-per-decision,
   closed-loop, on a leaderboard: does not exist as of July 2026. Determinism became a simulator *feature* THIS MONTH
   (Newton 1.4 "bit-exact rollouts") but is nowhere a *scored policy metric*; π's model cards measure only
   throughput. The axis we've run internally since EFA-1 is unclaimed publicly — and the determinism half of the
   window has started closing at the simulator layer.

**How outside harnesses become "commonly accepted" (the proven recipe):** Gymnasium wrapper + one-line pip; land when
a model class needs a new yardstick; baselines + checkpoints on HF; get aggregated (lerobot-eval integration or a
CVPR/NeurIPS challenge slot); and — fastest attention on-ramp — **audit an incumbent** (LIBERO-PRO's perturbation
audit made it the successor to the benchmark it audited).

**Frontier-lab reporting pattern:** π reports throughput on self-defined real tasks; NVIDIA is benchmark-forward
(RoboCasa-GR1, SimplerEnv, DROID); Figure reports operational records — but co-authored RoboMME. Academic credibility
routes through the real-world layer (RoboArena, RoboChallenge) + the scale sims (BEHAVIOR 2026 challenge: launched
July 2, deadline **Oct 16**, π0.5 + GR00T N1.7 as official baselines).

## II. The plan — three moves, in priority order

### Move 1 (flagship): the ATTRACTOR MEMORY program — "EFA-M"
The brain-inspired energy-first thesis lands on the field's named unsolved axis. Energy landscapes ARE memory — the
Hopfield lineage is the origin of EBMs, and the slot is empty with a dated window (CVPR 2026 challenge open now;
CoRL memory workshop **Nov 9, 2026** — after that, Yilun Du's orbit floods the space).

The program (each stage measured, per the house discipline):
1. **The mechanism, in Ferric first**: a Dense Associative Memory module whose *retrieval is descent on a learned
   energy* — content-addressable recall from partial cues, integrated as light modulation (the winning integration).
   Nano proof on a purpose-built memory-critical probe (counting / permanence / procedural analogs of RoboMME's
   suites on our bodies).
2. **The EFA differentiators nobody else can claim:**
   - **Retrieval energy = memory-confidence certificate** — the model knows when its memory is trustworthy; this is
     literally workshop open problem #6, and it is our verify seam applied to memory.
   - **Verified consolidation** — Hebbian fast-weight writes with a Lyapunov-style convergence guarantee: what
     RoboTTT's TTT does, but *certified* (our certificate machinery pointed at the memory write).
   - **Sequence attractors / limit cycles** for counting & procedural memory (Long-Sequence Hopfield theory) —
     attacking the suites where perceptual memory loses to symbolic, without a 3–5×-compute VLM.
   - **Joules-per-recall**: O(1) attractor settlement vs VLM-symbolic's 3–5× compute — performance-per-watt doctrine
     applied to memory.
   - **Hidden-property attractors**: basins over latent physical parameters (mass/friction posteriors updated by
     interaction) — unifying hidden-property inference with memory; no one is adjacent to this combination.
3. **The public stake**: the missing 15th variant on RoboMME's own leaderboard (π0.5 + DAM-as-Modulator, target
   >44.5%), entered via the CVPR 2026 challenge and/or vla-eval; position paper for the CoRL Nov 9 workshop.

### Move 2: the EFFICIENCY + DETERMINISM CARD — champion the unclaimed axis
Every leaderboard measures success; none price it. We already run this discipline internally (FLOPs/decision,
bit-exact cross-fabric, escalation pricing). Make it public infrastructure:
1. **The spec**: an "efficiency card" per policy per benchmark — FLOPs & joules per decision (closed loop), decisions
   per second, escalation/adaptive-compute rate, **bit-exact determinism verified** (same obs ⇒ same action,
   cross-run and cross-platform), footprint. One JSON schema; ships with weights (our releases already do this).
2. **The harness**: a `lerobot-eval` / vla-eval-compatible wrapper that annotates EXISTING benchmarks with the card —
   we champion accepted benchmarks rather than invent competing ones (the Dean's directive verbatim).
3. **The on-ramp (LIBERO-PRO precedent)**: publish an efficiency/determinism AUDIT of the released open policies
   (OpenVLA, π0, GR00T checkpoints via vla-eval): how many joules per LIBERO success? Are any two runs bit-identical?
   Nobody has ever printed that table. Our EFA releases sit in the same table as the existence proof that the axes
   are achievable.
4. **The browser witness**: nobody runs scored, reproducible policy eval in-browser; official MuJoCo WASM just
   landed. Our Ferric-WASM + WebGPU stack turns the card into a *live, verifiable artifact* — click, run the exact
   released weights, watch the determinism check pass on your own device. The Institute signature move, applied to
   evaluation itself.

### Move 3: standard-body integration (feeds both moves + EFA-2)
- **Eval spine**: adopt vla-eval + LeRobot as the lab's external reporting spine (one `predict()` server for EFA
  models; SO-101 flow already LeRobot-native).
- **EFA-2 v1 target = ManiSkill** — one substrate serves external comparability AND the RoboMME entry (same sim).
  MuJoCo (incl. official WASM) for the browser harness and Playground-class bodies.
- **A real number** when ready: RoboChallenge Table30 submission (policy server, no hardware needed).
- **Calendar**: RoboMME CVPR challenge (open now) · BEHAVIOR deadline Oct 16 (observe; π0.5/GR00T baselines) ·
  **CoRL memory workshop Nov 9 (the clock for Move 1)**.

## III. Positioning: "enablement, not control"
Verified unclaimed: nobody frames physical AI this way. The claim in one line: **control is table stakes; the missing
layer is what lets a policy remember, verify, adapt, and price its own thinking — and an energy-first, brain-inspired
architecture provides all four from one object.** EFA-1/-2 demonstrated verify + price + certify on control; EFA-M
extends the same energy to memory (recall = descent, confidence = energy, consolidation = certified write). That is
the brain-inspired story told with measurements instead of metaphors.

_Every claim above traces to the three sweep reports (July 2026, agent transcripts in session records); key URLs:
robomme.github.io · corl2026-memory.github.io · huggingface.co/blog/lerobot-release-v060 ·
github.com/allenai/vla-evaluation-harness · github.com/google-deepmind/mujoco/tree/main/wasm ·
github.com/newton-physics/newton/releases · robochallenge.ai · behavior.stanford.edu/challenge._
