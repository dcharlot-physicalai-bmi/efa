# EFA — Validation Ledger

The complete record of experiments behind [the whitepaper](../WHITEPAPER.md). Every row is a measured result on the [Ferric](https://ferric.physicalai-bmi.org) pure-Rust cross-fabric GPU stack (or, where noted, an algorithmic/CPU study or a JS nano spike). Reference implementations are in [`../experiments/`](../experiments/).

Discipline: **every claim measured, every limit priced.** Negatives and self-corrections are included on purpose — they are what make the wins credible.

---

## I. Nano program — every mechanism, proven (in-browser, on-device)

| mechanism | make-or-break test | result |
|---|---|---|
| Memory | sparse latent holds a Hebbian fast-weight memory | 100% recall |
| World model + plan | same latent is a plannable JEPA world model | next-latent cos ≈ 0.99; ~70% goals vs ~8% chance |
| Continual gate | learn 3 physics-worlds in sequence, no forgetting | all 3 retained (baseline keeps only last) |
| Learn-in-imagination | policy trained in the dream vs model-free | 71% vs 40% at matched real steps |
| Hebbian train==live | memory load-bearing only if trained in the loop | chance → 99.5% (off-distribution: zero lift) |
| POMDP fusion | reach a hidden goal held only in memory vs blind | 39% vs 5% |

## II. On the fabric — trains, acts, scales (Ferric)

| result | measurement |
|---|---|
| World model trains | JEPA + autograd + Adam on GPU; 8-step latent fidelity 0.92–0.95 |
| **Second-order autograd** | `grad()` returns differentiable gradients (Var nodes that backprop again); validated exact (`d²/dx² Σx³=6x`, `Σeˣ`, HNN input-grad training); unlocks train-through-optimization |
| Acts by planning | full perceive→plan→act loop; value-guided beats trapped energy-descent |
| Value-RL last mile | MC plateau 23% → **Fitted Value Iteration + target network → 39%** (algorithm, not tooling) |
| Value-net scaling | clean monotone 23→27→39→**45%** |
| Hard-task scaling | high-variance in size; **value-RL convergence-bound**, not parameter-count ("capacity floor, then compute") |
| Benchmark vs exact optimal | **87%** of the BFS optimal on hard cross-wall goals |
| Path efficiency | early-reward planner: **96%** reach, paths 2.48× → 1.93× |

## III. Training-free / test-time alignment

| adopt | result |
|---|---|
| MPPI/CEM latent planning (acting) | **39% → 69%** reach, same value net (o1 substitution law for control) |
| ensemble / uncertainty-guarded planning | null on a well-converged value net (honest boundary) |
| delta-rule + modern-Hopfield memory | near-perfect episodic recall vs additive's interference; rank-limit priced |
| full-stack composition (POMDP, on fabric) | latent→Z→world-model→value→MPPI as one model; Z load-bearing (69% vs 5% blind) |
| multi-item episodic tour | acting + memory upgrades compound; +9 pts at K=16 (delta/Hopfield vs additive) |

## IV. Energy-based zero-shot — the narrow, real edge

| build | result |
|---|---|
| compositional generation (energy summation) | novel conjunctions 73–74%, thinking curve rises; fragile under naive sampling |
| energy-as-verifier (best-of-N, 2D) | **100%** selection; fixes every case Langevin collapsed on |
| OOD-goal generalization | goal-agnostic distance energy **37→41%** OOD; learned value 51→1%; BC policy 100→7% |
| nano energy-based-transformer (via verification) | solves a multivalued system feedforward can't (0%); thinks with N |
| **true EBT — train THROUGH the descent** (2nd-order autograd) | **100%** in-dist on the multivalued system (feedforward 0%); thinks (K=1→6: 22→100%); OOD 64–71%; beats the verification route in accuracy *and* cost |
| MALA (Metropolis-corrected sampling) | robust composed generation across a wide ε band; **corrected an earlier fragility overclaim** |
| residual-energy verifier on a real AR model (EDLM) | best-of-N tracks the oracle to **100%** on real sequences |

## V. Energy-based AI-for-science

| capability | system | result |
|---|---|---|
| conserve dynamics (HNN) | oscillator | energy drift **−0.9% vs +5.0%** (5.5× lower) over a long rollout |
| discover ODE | 2D oscillator | exact governing equation from noisy data |
| discover ODE (chaos) | **Lorenz** | all 7 terms + coefficients exact, from ~11k noisy chaotic samples |
| discover PDE | **Burgers** (advection–diffusion) | `u_t = −u·u_x + 0.10·u_xx` exact |
| discover PDE | **Fisher–KPP** (reaction–diffusion) | `u_t = u_xx + u − u²` exact |
| discover invariant | nonlinear pendulum | conservation law, correlation **0.99** with the true energy |
| discover from **REAL data** | **Hudson Bay lynx–hare 1900–1920** | Lotka–Volterra recovered — correct signs + sensible magnitudes |

**Honest boundaries (priced):**
- *Energy-conserving surrogate at field scale* — did **not** beat a naive force net (8.3% vs 2.2% drift). Redone with **exact second-order gradients** it improved (6.8% drift) but naive (1.6%) still won — so the negative is *structural*, not a finite-difference artifact. A real negative, made definitive under the better tooling.
- *AI-for-math verifier on real Lean/mathlib data* — the verifier mechanism is sound, but lexical *and* general-semantic (MiniLM) embeddings sit at chance: tactic↔goal compatibility is a **formal**, not surface-semantic, property. The bottleneck is a Lean-task-trained encoder, not the energy verifier.

---

## Reading the negatives

Two self-corrections are worth calling out because they define the method:
1. The value-RL "23% ceiling" was diagnosed as a tooling limit — **wrong**; Fitted Value Iteration + a target network broke it to 39% with no new ops.
2. The composition "fragility" was attributed to a fundamental sampler problem — **largely wrong**; a well-tuned constant-step Langevin composes fine, and MALA makes it robust. The earlier claim was walked back under test.

*Charlot Lab · Institute for Physical AI @ BMI. Every figure is a measured result, not an estimate.*
