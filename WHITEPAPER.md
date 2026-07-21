# Energy First Architecture (EFA)

**A post-transformer, energy-based architecture for Physical AI — one learned scalar energy that predicts, plans, remembers, verifies, and discovers.**

Charlot Lab · Institute for Physical AI @ Bailey Military Institute
Author: David J. Charlot, Dean of Physical AI
Status: research whitepaper (v1). Every result below is measured; every limit is priced.

---

## Abstract

The dominant AI stack — a point-neuron, dense matrix multiply, frozen weights, a finite context window, a black box, running in a cloud datacenter — was built for machines that *talk*. Physical AI is for machines that *act*, and acting is fundamentally an **energy** problem: physics is written in Hamiltonians, Lagrangians, least-action and variational principles; control is descent on a value; perception is inference to a low-energy explanation.

**The mission is data-center-grade AI capability at the edge** — and the concrete fact that motivates it is that a frontier LLM cannot run in a robot. Claude, Gemini, DeepSeek all fail there, for two independent reasons. **First, energy:** they are too large, too power-hungry, and cloud-tethered; the honest currency for edge intelligence is *joules per task*, not tokens per second (a unit that isn't even defined for an embodied controller). **Second, and deeper, architecture:** a discrete-token, autoregressive, attention-over-context model is the wrong *shape* for a world that is continuous, parallel, real-time, and governed by energy and force — and no efficiency optimization fixes a shape mismatch. This is not a race to win on the incumbent's benchmark; that race is a dead technique. It is a different architecture whose substrate matches the physical world it must act in. **Energy First Architecture (EFA)** takes energy as the native object rather than a bolted-on loss. A single learned scalar energy, defined over a sparse-positive, monosemantic latent, does the jobs that the incumbent stack assembles from separate subsystems: it is at once a **world model** (predict by descending energy), a **planner** (act by descending energy toward a goal), a **memory** (a Hebbian fast-weight matrix written at inference, no gradient), a **verifier** (low energy = valid), and — pointed at scientific data — a **discoverer** (the governing law is the sparse energy that explains the observations).

This document records the honest construction of EFA: from proving every mechanism at nano scale, to training and acting on a real pure-Rust cross-fabric GPU stack, to aligning with the 2024–2026 training-free / test-time ecosystem, to a program of energy-based AI-for-science. The through-line is a discipline: **every claim is measured, and every limit is priced.** EFA is not presented as beating frontier systems at scale — it is presented as a specific, defensible architecture whose edge is real on the axes where a native energy is the right representation: out-of-distribution generalization, compositional zero-shot, native verification, test-time thinking, energy conservation, and scientific-law discovery.

---

## 1. The Thesis: Energy First

The incumbent stack is a sequence of choices, each reasonable for language and each questionable for embodied intelligence:

| layer | incumbent | EFA inversion |
|---|---|---|
| unit | point neuron (scalar × weight → nonlinearity) | brain-shaped, sparse-positive, monosemantic activation |
| memory | frozen weights + a finite KV context | Hebbian fast-weight matrix written *at inference* |
| legibility | black box, post-hoc interpretability | monosemantic / steerable *by construction* |
| world model | autoregressive next-token | predict *consequences* in a latent by energy descent |
| compute | dense matmul, one fixed forward pass | sparse, local, and *variable* test-time thinking |
| substrate | cloud GPU | on-device, energy-first, in a browser tab |

The claim is not that any single inversion is novel — each has a research lineage (below). The claim is **architectural unification**: these are not six modules but *three mechanisms over one representation*, because a sparse-positive activation space can simultaneously be the readable feature space, the associative memory space, and the world-model prediction space. Interpretability, memory, and prediction become three readouts of one latent, not three subsystems.

EFA is the Institute's working answer to the question: *what is the right architecture for intelligence that acts in the physical world, runs anywhere, and pays for its intelligence in joules rather than data-center scale?*

---

## 2. Architecture: The Mechanisms

EFA is a small number of coupled mechanisms over a shared latent `x ∈ ℝⁿ₊` (sparse, non-negative, unit-normalized).

### 2.1 Sparse-positive monosemantic latent
An encoder maps observations to `x` — a ReLU, ~5–10% active, unit-normalized code. Sparsity + non-negativity make individual dimensions tend toward single, readable meanings (monosemanticity by construction), and make the same code usable as both a feature vector and an associative key. *Lineage: BDH / "The Dragon Hatchling" (Kosowski et al., 2025); sparse autoencoders.*

### 2.2 Hebbian fast-weight memory `Z` (training-free)
Working memory is a fast-weight matrix written at inference by an outer product — `Z += α·x·xᵀ` — with **no gradient**. Recall is an associative read. This is the training-free heart of EFA: the model learns *during* inference without touching its slow weights. *Lineage: fast-weight programmers (Schmidhuber; Schlag/Irie 2021); BDH.* EFA adopts the modern upgrades from the test-time-memory literature — a **delta-rule** corrective write (`Z += β(x − Zx)xᵀ`), a **decay/forget** gate, and **surprise-gating** that reuses the world model's own prediction error — all still gradient-free.

### 2.3 Dendritic continual gate
A dendritic, k-winner gate routes different contexts (tasks/worlds) to disjoint sub-networks, giving continual learning without catastrophic forgetting — a differentiator, since world models are not usually continual. *Lineage: Active Dendrites / continual RL.*

### 2.4 JEPA world model + energy planning
The world model predicts the *next latent* given an action (a JEPA-style latent predictor), with the energy defined as latent distance to a goal. **Prediction is energy descent; planning is energy descent toward a goal latent.** Acting uses value-guided / population planning over the learned model at test time — the MuZero / TD-MPC2 / V-JEPA-2-AC family. *Lineage: JEPA (LeCun); MuZero; TD-MPC2; V-JEPA 2-AC.*

### 2.5 The keystone identity
The load-bearing bet: **the sparse-positive activation space is simultaneously the world-model prediction space, the associative-memory space, and the readable feature space.** One state `x` is the object; the world model, the memory, and the interpretation are three readouts of it. This is what makes EFA one architecture rather than a portfolio of modules.

---

## 3. The Validation Program

EFA was built the way it should be judged: prove each mechanism in isolation, then compose, then scale, then push toward hard tasks and real data — pricing every limit found along the way.

### 3.1 Nano program — every mechanism, proven
Before integration, each mechanism was demonstrated as a self-contained, on-device (in-browser) artifact:

- **Memory:** the sparse latent holds a Hebbian fast-weight memory — 100% recall on a clean loop.
- **World model + plan:** the same latent is a plannable JEPA world model (next-latent cosine ≈ 0.99; ~70% of latent goals reached vs ~8% chance).
- **Continual gate:** three physics-worlds learned in sequence with no forgetting (all three retained vs a baseline that keeps only the last).
- **Learn-in-imagination:** a policy trained *entirely inside* the learned world model reaches 71% vs 40% model-free at matched real experience.
- **Hebbian train==live:** memory is load-bearing *only if trained with the loop on* (chance → 99.5%; off-distribution training gives zero lift) — a necessary-condition result.
- **POMDP fusion:** a hidden goal, held only in memory, is reached 39% vs 5% blind — `Z` demonstrably load-bearing.

### 3.2 On the fabric — trains, acts, scales
EFA leaves the toybox: it trains and acts natively on **Ferric**, a pure-Rust cross-fabric GPU stack (the same code runs on datacenter GPU, laptop, and browser via wgpu/WebGPU/Metal).

- **Trains:** the JEPA world model trains via Ferric autograd + Adam, batched on GPU — 8-step latent fidelity 0.92–0.95.
- **Acts:** the full perceive → plan → act loop runs on the fabric; value-guided planning reaches goals on a maze where naive energy-descent is provably trapped.
- **The value-RL last mile — corrected under test.** Monte-Carlo value learning plateaued at ~23%; this was first (wrongly) diagnosed as a tooling limit. **Fitted Value Iteration with a target network** — a Bellman-*optimality* backup, fully batched, no new ops — broke it to **39%**. The limit was the *algorithm*, not the tooling.
- **Scaling — honestly.** Value-net width scales cleanly (23→27→39→45%). On a harder task the story is noisier: reach is high-variance in size and gated by value-RL *convergence*, not parameter count — so "scale is the lever" survives only as "capacity floor, then compute." Measured against the *exact optimal* (BFS ceiling), EFA closes to **87%** of optimal on hard cross-wall goals, and an early-reward planner lifts it to **96%** while tightening paths.

### 3.3 Training-free alignment — the test-time ecosystem
EFA is, by construction, a training-free / test-time model. Placed against the 2024–2026 landscape (Energy-Based Transformers, Titans, DeltaNet, modern Hopfield, TD-MPC2, V-JEPA 2-AC), its mechanisms are the frontier's mechanisms — and the adopted upgrades were validated on the fabric:

- **Acting (MPPI/CEM latent planning):** 39% → **69%** reach on the *same* value net, purely from more test-time search — the o1 test-time-compute substitution law, for control.
- **Memory (delta-rule + modern-Hopfield readout):** near-perfect episodic recall where the additive write already interferes; priced by an honest rank limit past the memory's capacity.
- **Composition:** the whole stack — sparse latent → Hebbian `Z` → JEPA world model → value → MPPI — runs as *one model* on the fabric, with `Z` load-bearing (goal hidden after t=0; reach 39/69% with memory vs 0/5% blind).

### 3.4 Energy-based zero-shot — the narrow, real edge
A dedicated program tested where an energy-based model beats a feedforward baseline, on the axes the literature says it should:

- **Zero-shot composition by energy summation:** independently-trained concept energies sum to satisfy conjunctions *never seen jointly* (73–74% on 2–3-way), with a clean thinking curve — a thing feedforward nets structurally cannot do; honestly fragile under naive sampling.
- **Energy-as-verifier (best-of-N):** the same energies, used to *select* rather than *descend*, reach **100%** and fix every case naive sampling collapsed on — the robust route to System-2. Demonstrated end-to-end on a real autoregressive sequence model (residual-energy verifier, EDLM recipe), tracking the oracle to 100%.
- **The true Energy-Based Transformer — train *through* the descent.** With second-order autograd now on the fabric (§4), the flagship architecture is the real thing, not a workaround: the model predicts by *K* unrolled steps of energy descent (`ŷ ← ŷ − α·∂E/∂ŷ`) and is trained by backpropagating *through* the whole unrolled descent to the weights. On a multivalued nonlinear system, the descent-trained EBT reaches **100%** in-distribution, with accuracy that *climbs with thinking* (K=1→6: 22→100%) and generalizes OOD (64–71%). **Honest correction (caught under test):** an earlier version of this section claimed feedforward is "structurally 0%" here — that was **asserted, not measured, and it is wrong.** A fairly-supervised feedforward (asked for *one* consistent solution) solves this task at **100% on ~350 parameters** (measured, `ebm_edge.rs`). The EBT's genuine, *narrower* edge on this task is representing the **multivalued solution set** — different descent inits land on different valid solutions, which a single forward pass cannot produce — plus refinement by thinking; it is **not** a capability a feedforward lacks. The deeper reason the transformer stack fails at embodied intelligence is architectural (§5), not a toy like this.
- **Scaling the true EBT — the ceiling priced, not assumed.** Generalized to a *chain of D coupled multivalued links*, thinking still lifts every case, and the EBT paper's headline stabilizers (randomized descent step-count/size + Langevin noise) confirm on Ferric — the D=2 case converges in K=3 instead of K=6, and low-budget accuracy rises across the board. Difficulty past D≈4 hits a ceiling more thinking can't cross; a width sweep shows that ceiling is **capacity, not fundamental** — the D=6 plateau rises monotonically **13→25→42%** from width 128→256→512. Honestly: even at 512 the 5-link chain is only ~42% solved, so the limit is *measured and priced* ("capacity floor, then compute"), not crossed. And a tempting shortcut fails under test: a **weight-shared *local* energy** (E = Σᵢ g(ŷᵢ,ŷᵢ₊₁,aᵢ,bᵢ), structure-matched, tiny) is *worse*, not better — 8% at D=4 vs the generic net's 78%. We treated this negative with the suspicion it deserves — it could be a *design* artifact, since local descent needs on the order of *D* steps to propagate constraints along the chain — and re-ran with the budget scaled to problem size (**K∝D, up to 32×D**). It still collapses (D6=1%, D8=0%), so the failure is not a budget artifact: a naive sum-of-local-energies has no global coordinator, and locality alone does not buy generalization. The capacity route is the one that scaled. *(One confound remains untested — annealed sampling to escape frustration minima — and is flagged rather than claimed away.)*
- **OOD generalization:** a *goal-agnostic* distance energy generalizes to out-of-distribution goals (37→41%) where a learned value (51→1%) and a behavior-cloned policy (100→7%) both collapse — with the crucial nuance that the edge requires the energy to be goal-agnostic.
- **Metropolis-corrected sampling:** makes composed *generation* robust across a wide step-size band where uncorrected Langevin is knife-edge — and it corrected an earlier overclaim (the "fragility" was largely a step-size artifact).

### 3.5 Energy-based AI-for-science — a physics discovery suite
Pointed at scientific data, EFA's energy-minimization becomes law discovery. The suite is comprehensive across equation types and validated on **real historical data**:

| capability | systems | result |
|---|---|---|
| conserve dynamics | Hamiltonian NN | energy drift 5.5× lower than a naive net |
| model an actuated, dissipative body | **port-Hamiltonian NN** (learns dissipation + input port) | conservation-only HNN fails 130× on a damped, driven rollout; PHNN ties the black box **and** recovers friction *c/m*=0.30 exactly + an energy budget |
| discover the ODE | 2D oscillator · **Lorenz (chaos)** | exact, from noisy data |
| discover the PDE | **Burgers** (advection–diffusion) · **Fisher–KPP** (reaction–diffusion) | exact |
| discover the invariant | nonlinear pendulum | conservation law, correlation 0.99 |
| discover from **real data** | **Hudson Bay lynx–hare, 1900–1920** | Lotka–Volterra recovered — correct structure + coefficients |

And an honest boundary: an *energy-conserving surrogate* at field scale did **not** beat a naive force net — and this held even after redoing it with exact second-order gradients (6.8% vs 1.6% drift), so the negative is structural, not a tooling artifact. The AI-for-math verifier is mechanism-sound but gated on a Lean-task-trained encoder — general embeddings sit at chance, because tactic↔goal compatibility is a *formal*, not a surface-semantic, property.

### 3.6 Cognitive-science bridges — predictive coding & the global workspace
EFA's mechanism — *predict by descending an energy* — is the computational form of two leading cortical theories: **predictive coding** (inference as prediction-error / free-energy minimization) and **Global Workspace Theory** (a sparse, broadcast set of representations that flexible cognition routes through). Two controlled nano-probes test whether EFA actually exhibits their signatures — with one strong claim *falsified* and kept honest:

- **Predictive coding, in language.** An energy-based LM on a synthetic language with a *known* per-context surprisal gradient learns the true distribution (KL≈0.0006) and its **prediction energy encodes surprisal** (ρ = +0.71) — the "energy = prediction error" reading. But the stronger, pre-registered claim — that the model spends *more inference steps* on harder tokens, like human reading-time — is **falsified here**: step-count is flat (ρ=−0.12) and descent-"work" runs the *other* way (ρ=−0.71). The reconciliation with §3.4: energy-based inference allocates compute to **reasoning-depth** difficulty (longer chains genuinely needed more thinking), **not** to output-**uncertainty**. Surprisal-as-entropy ≠ surprisal-as-reasoning-load.
- **The global workspace, measured with a Ferric J-lens.** Following the 2026 interpretability result that an LM's privileged representations form a sparse, non-negative *workspace* (verifiable via an averaged-Jacobian "J-lens"), we build the J-lens on Ferric's autograd (∂output/∂latent, averaged) and probe a sparse-positive EFA latent. It shows the workspace's **selectivity** signature: ablating the top causal (J-lens) directions collapses **flexible/multi-hop** reasoning (100→31%) but spares **automatic** report (100→98%), and the causal ranking beats a variance ranking. Honestly, the frontier's *extreme low-variance* signature does **not** reproduce at nano (our J-space holds ~35% of variance, ≈proportional) — the selectivity is real, the low-variance property looks like a scale phenomenon.

- **Reanalysis is a thinking-resolvable compute cost (the garden-path, done right).** The surprisal result predicted that energy-based inference should spend compute on *reasoning depth*, not uncertainty. Testing this cleanly took three tries — a first version was inconclusive (a circular effort metric over a model that learned a smooth integrator with no commitment), which is itself the lesson that a *positive* deserves the same scrutiny as a negative. The working design gives the model an **architectural commitment**: a decision coordinate in an explicit double well, tilted by learned evidence, with thermal (Langevin) escape. On minimal pairs matched in final answer *and* total evidence but differing in whether an early interpretation must be **reversed**, garden-path items are answered far worse than controls at minimal thinking (64% vs 93%) and the gap **fully closes with more descent steps** (K=1→8: 64→100%), while controls need none. Reversing a commitment is a real, thinking-resolvable reasoning-depth cost — the computational signature of garden-path processing difficulty. *(Nano synthetic task; 81% base accuracy; the bistable prior is imposed, the evidence coupling learned.)*

The value of this section is the same discipline as the rest: a falsified strong claim, real narrow ones kept, two inconclusive tests discarded rather than dressed up, and a concrete tool (the J-lens) that our own tooling made buildable.

---

## 4. Honest Limits & What Is Not Claimed

This section is load-bearing; the credibility of the wins depends on it.

- **Scale.** Everything above is nano-to-small. EFA is **not** claimed to beat frontier transformers at in-distribution capability or at frontier scale. Its demonstrated edge is *narrow* — OOD, composition, verification, thinking, conservation, discovery — not raw capability. Where we pushed difficulty (the true-EBT coupled-chain, §3.4), the ceiling is capacity-bound and only *partially* lifted by the widths we could afford (D=6 → 42% at width 512) — a measured floor, not a solved task.
- **Sampling.** Energy *descent* (Langevin/generation) is step-size sensitive; the robust route is *verification* (best-of-N) or Metropolis correction. This is priced, not hidden.
- **Exact gradients — now shipped, and it settled one negative.** Ferric now has **second-order autograd** (differentiable gradients: `grad()` returns Var graph nodes that backprop again). This *unlocked* the true Energy-Based Transformer (§3.4). It did **not** rescue the energy-conserving field surrogate: redone with *exact* gradients it improved (8.3%→6.8% drift) but a naive force net (1.6%) still won — confirming the negative was structural, not a finite-difference artifact. Capability gained; one honest boundary made definitive.
- **AI-for-math.** The verifier mechanism works; real Lean competitiveness is gated on a domain-trained proof-state encoder — a multi-quarter program, not a demo.
- **Method vs. system.** These are mechanism-and-method proofs on canonical benchmarks, not deployed systems that top leaderboards.

The map this produces is the point: it says precisely *where* an energy-based program can win and *what each next rung costs*.

---

## 5. Goals & Relation to IPAI @ BMI and Physical AI

EFA is a Charlot Lab research effort inside the **Institute for Physical AI @ Bailey Military Institute (IPAI @ BMI)**. It advances the Institute's thesis in three concrete ways:

1. **The architecture for embodied intelligence that runs anywhere.** Physical AI must act on-device, at the edge, at low energy. EFA is on-device and energy-first by construction — the whole stack runs in a browser tab on WebGPU via the Institute's pure-Rust **Ferric** compute layer. This is the substrate inversion made real.

2. **Energy as the native language of the physical world — architectural match, not just efficiency.** The second reason the transformer stack fails at embodied intelligence (Abstract) is that its *shape* is wrong for physical reality; EFA's claim is that an energy architecture is the *right shape*, because physics already is energy. The sharpest evidence is the **port-Hamiltonian** result (§3.5): on a damped, driven body (a joint + motor), an energy-*conserving*-only architecture — the wrong physical prior — fails **130×** on rollout, while the port-Hamiltonian architecture (energy + dissipation + input port) ties a black box **and** recovers the physical friction coefficient exactly. That is architecture matching physics: the model's structure carries the body's energy budget (loss to friction, injection through the port), which a token model has no way to represent. The physics-discovery suite (§3.5) is the same point — the governing law *is* an energy, recovered natively. And the match pays off in *control*: on an **underactuated pendulum swing-up** (torque too weak to lift directly), a **learned** energy Ê (HNN, gradient-field RMSE 0.003 vs the true energy, never told the formula) drives energy-shaping control that swings the body up and stabilizes it, while a naive position controller with the *same* actuator cannot reach upright — it has no energy to pump. Energy is the load-bearing object for embodied control, and an energy architecture has it natively.

The loop closes on the sensing side too, and on a harder body. On a **cart-pole** (4-D, underactuated) the controller is given only **noisy position observations** — no velocities — so it must *infer* the state, which is the EFA reading of perception: inference to a low-energy explanation. An energy observer (the minimizer of observation-mismatch + a learned-dynamics prior — trust the smooth model prediction, gently correct with the position innovation) recovers velocities cleanly and **balances** the pole, while the obvious baseline — velocities by differencing the noisy positions — **fails catastrophically** at the same noise (its velocity estimate is pure amplified noise). Same body, same controller, same noise; only the perception front-end differs. And the loop is *complete*: with a proper LQR (Riccati-solved gains on a finite-difference linearization) the same energy-observer state drives **full regulation** of the underactuated cart-pole — pole balanced *and* cart held at centre (true-state pole/cart RMS 0.009/0.048 m; from noisy position-only obs, 0.044/0.235 m). Perception, control, and dynamics are all the same energy object — which is why the embodied loop closes on-device. *(Honest boundary from the same push: recovering a **chaotic double pendulum's** conserved energy purely from trajectories resisted three learning objectives — an ill-posed inverse problem whose proper fix, an HNN in canonical coordinates, is identified but unbuilt.)* *(Contrast: a discrete-token puzzle like the multivalued-algebra toy in §3.4 does **not** test this — a plain feedforward solves it; the architectural edge lives in continuous, force-governed dynamics, not toy capability.)*

3. **A teachable, legible research spine.** Every mechanism is a small, driveable, on-device artifact. EFA is not only a research bet but a curriculum surface: the Institute teaches the ideas by letting students *run* them. Legibility-by-construction is both a research property and a pedagogical one.

**What EFA is for:** to be the Institute's coherent, honest, runnable position on how intelligence that *acts* should be built — and to establish, with measured results, the narrow-but-real seams where an energy-based approach is genuinely the right tool.

---

## 6. Roadmap

- **Physics-discovery, deepened:** harder PDEs (KdV/Navier–Stokes), more real measured datasets, systematic noise/sparsity robustness — the robustly-winnable near-term seam.
- **Exact-gradient tooling on Ferric:** ✅ *done* — second-order autograd shipped and validated; it unlocked the true Energy-Based Transformer (§3.4, train-through-optimization) and settled the field-surrogate negative (§4). Scaling probed: past the 2-var system the ceiling is **capacity** (width lifts the D=6 chain 13→42%); the open rung is width/depth to fully solve longer coupled chains.
- **AI-for-math, the real path:** a Lean-task-trained proof-state encoder to feed the (proven) energy verifier — best-of-N proof/answer selection and joint premise-set selection by energy summation.
- **Scale the unified model** on a benchmark with published comparables, where "capacity floor, then compute" can be measured head-to-head.
- **Consolidation:** this whitepaper, the public repository, and the `efa.physicalai-bmi.org` ecosystem as the living record.

---

## 7. Selected References

Energy-Based Transformers (Gladstone, Nagarajan, Du et al., 2025, arXiv:2507.02092) · BDH "The Dragon Hatchling" (Kosowski et al., 2025, arXiv:2509.26507) · JEPA / V-JEPA 2-AC (LeCun; Meta, 2025, arXiv:2506.09985) · MuZero (Schrittwieser et al., 2020) · TD-MPC2 (Hansen et al., 2024, arXiv:2310.16828) · Titans (Behrouz et al., 2025, arXiv:2501.00663) · Gated DeltaNet (Yang et al., 2025, arXiv:2412.06464) · Modern Hopfield (Ramsauer et al., 2021, arXiv:2008.02217; Krotov & Hopfield, 2016) · Compositional EBMs (Du, Li & Mordatch, 2020; "Reduce Reuse Recycle," 2023) · Hamiltonian NN (Greydanus et al., 2019, arXiv:1906.01563) · SINDy / PDE-FIND (Brunton, Proctor & Kutz, 2016; Rudy et al., 2017) · AI-Poincaré (Liu & Tegmark, 2020).

---

*Energy First Architecture · Charlot Lab · Institute for Physical AI @ Bailey Military Institute. Every figure in this document is a measured result, not an estimate.*
