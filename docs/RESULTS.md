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
| nano energy-based-transformer (via verification) | represents the *multivalued* solution set + thinks with N (⚠ NOT "feedforward can't" — see correction below) |
| **true EBT — train THROUGH the descent** (2nd-order autograd) | **100%** in-dist on the multivalued system; thinks (K=1→6: 22→100%); OOD 64–71%; beats the verification route in accuracy *and* cost |
| ⚠ **correction to the above** (`ebm_edge.rs`, measured) | the EBT write-ups asserted "feedforward = 0%" on the multivalued task — **unmeasured and wrong.** A fairly-supervised feedforward solves it at **100% on 354 params** (and at 4.5k, 67k). The EBT's real edge on this toy is the *multivalued set* + thinking, not raw capability. The task was the **wrong demonstration** of the edge thesis: a discrete-token algebra puzzle can't test the *architectural* mismatch (continuous, energy/force-governed, dynamical, embodied) that actually defeats the transformer stack in a robot. |
| true-EBT scaling (chain of *D* coupled multivalued links) | thinking lifts every case; the paper's stabilizers (randomized K/α + Langevin noise) confirmed on Ferric (D=2 converges in K=3 not 6); ceiling past D≈4 is **capacity, not fundamental** — D=6 plateau rises monotonically **13→25→42%** at width 128→256→512 (still only 42% solved: priced, not crossed) |
| structure vs scale (weight-shared *local* energy) | **honest negative, stress-tested** — a locality-matched shared energy is *worse*, not better (D=4: **8%** vs the generic net's 78%; solves a single link at 90% then collapses). Suspecting a *design* artifact (too few descent steps to propagate along the chain), we re-ran with a budget scaled to problem size — **K∝D up to 32×D**: it **still** collapses (D6=1%, D8=0%). So budget is ruled out; the negative is real. A naive sum-of-local-energies has no global coordinator; locality alone doesn't buy generalization. *(Remaining untested confound: annealed sampling to escape frustration minima — flagged, not claimed away.)* |
| MALA (Metropolis-corrected sampling) | robust composed generation across a wide ε band; **corrected an earlier fragility overclaim** |
| residual-energy verifier on a real AR model (EDLM) | best-of-N tracks the oracle to **100%** on real sequences |

## V. Energy-based AI-for-science

| capability | system | result |
|---|---|---|
| conserve dynamics (HNN) | oscillator | energy drift **−0.9% vs +5.0%** (5.5× lower) over a long rollout |
| **port-Hamiltonian** (damped + driven body) | mass–spring–damper + motor | conservation-only HNN fails **130×** on rollout (0.79 vs 0.006 MSE); PHNN ties the black box **and** recovers friction **r=0.300 = true c/m** exactly + gives the energy budget — the honest bridge to embodied control |
| discover ODE | 2D oscillator | exact governing equation from noisy data |
| discover ODE (chaos) | **Lorenz** | all 7 terms + coefficients exact, from ~11k noisy chaotic samples |
| discover PDE | **Burgers** (advection–diffusion) | `u_t = −u·u_x + 0.10·u_xx` exact |
| discover PDE | **Fisher–KPP** (reaction–diffusion) | `u_t = u_xx + u − u²` exact |
| discover invariant | nonlinear pendulum | conservation law, correlation **0.99** with the true energy |
| discover from **REAL data** | **Hudson Bay lynx–hare 1900–1920** | Lotka–Volterra recovered — correct signs + sensible magnitudes |

**Honest boundaries (priced):**
- *Port-Hamiltonian* — the PHNN does **not** out-*predict* the black box (0.0061 vs 0.0055 MSE; a hair behind). Its win is *structure at parity accuracy*: exact physical-parameter recovery + an energy budget + decisively beating the wrong prior (conservation-only HNN). Not "more accurate," but "accurate **and** accountable."
- *Energy-conserving surrogate at field scale* — did **not** beat a naive force net (8.3% vs 2.2% drift). Redone with **exact second-order gradients** it improved (6.8% drift) but naive (1.6%) still won — so the negative is *structural*, not a finite-difference artifact. A real negative, made definitive under the better tooling.
- *AI-for-math verifier on real Lean/mathlib data* — the verifier mechanism is sound, but lexical *and* general-semantic (MiniLM) embeddings sit at chance: tactic↔goal compatibility is a **formal**, not surface-semantic, property. The bottleneck is a Lean-task-trained encoder, not the energy verifier.

## VI. Cognitive-science bridges — predictive coding & global workspace

| probe | result |
|---|---|
| predictive-coding language twin | an energy-based LM's **prediction energy encodes surprisal** (ρ = **+0.71** vs true context entropy; KL≈0.0006 so it learned the distribution). But inference *effort* does **not** track output-entropy difficulty — steps-to-settle ρ=−0.12, descent-"work" ρ=−0.71 (easy/peaked contexts have deeper basins, so they descend *more*). |
| Ferric J-lens (averaged ∂output/∂latent) | a sparse-positive latent (13/32 active) shows global-workspace **selectivity** — ablating the top-8 causal directions collapses **flexible/multi-hop** reasoning (100→**31%**) but spares **automatic** report (100→**98%**); causal (J-lens) ranking beats variance ranking (31% vs 41%). |
| garden-path reanalysis (constant surprisal) | **inconclusive by design — a false positive caught by self-scrutiny.** Superficially effort is 1.45× higher at interpretation-flips (uniform tokens ⇒ constant surprisal, so effort *looks* tied to revision not surprisal). But the effort metric (belief displacement ‖Δz‖) is ~circular with the flip condition, and the model learned a smooth *linear integrator* — no commitment, so no real barrier to cross. A genuine test needs a model *forced to commit* (incremental decisions / bistable latent). Recorded as inconclusive, not claimed. |
| emergent commitment — weak pressure | **clean controlled null.** Learnable well depth A on an early-reliable/*weak*-late-noise task: commitment does **not** emerge (cold-start A stays ~0.05); the decisive control: a **warm-started** well (A=0.60) *decays to 0.07* — training throws away an imposed well because it buys nothing (all ~91–93% across K). Not a bootstrap artifact (warm control rules that out). |
| emergent commitment — strong pressure | **pressure-dependent emergence, modest payoff.** With *strong* late noise, the contrast flips: cold-start A **grows** 0.05→**0.28**, warm-start **persists** at 0.46 (vs 0.07 under weak pressure). So commitment *does* self-organize when it earns its keep — a clean weak-vs-strong contrast. **But** scrutinizing the positive: a smooth integrator *also* hits 100% (K≤8), because sequential accumulation gives it **primacy weighting for free** (early tokens integrate over more steps). Commitment's real benefit is narrow — **high-K stability** (bounds the latent, fixing smooth's 94→100% at K=16). Emergence is real; its energetic payoff here is small. |
| garden-path reanalysis — **bistable-prior model** (the design that lets it be tested) | **real, non-circular result.** A decision coordinate in an explicit double-well (architectural commitment) + learned evidence coupling + thermal (Langevin) escape. On minimal pairs matched in final label *and* total evidence, differing only in whether an early commitment must be **reversed**: at minimal thinking budget garden-path accuracy is **64% vs 93%** control, and the gap **fully closes with more descent steps** (K=1→8: **64→100%**) while controls need no extra thinking. Reanalysis is a genuine, **thinking-resolvable reasoning-depth compute cost** — confirming that energy-based inference allocates compute to reasoning depth, not output uncertainty. *(Caveats: nano synthetic task; 81% base accuracy; the bistable prior is architecturally imposed — commitment designed in, evidence coupling learned. Survived the same scrutiny that killed v1/v2.)* |

**Honest boundaries (priced):**
- *The strong "adaptive compute scales with surprisal" claim is **falsified*** in the controlled entropy task. The reconciliation with the EBT-scaling result: energy-based inference allocates compute to **reasoning-depth** difficulty (longer chains needed more thinking), **not** to output-**uncertainty** difficulty. Surprisal-as-entropy ≠ surprisal-as-reasoning-load; the compute story needs the latter (garden-path / multi-hop), which this task lacked.
- *The frontier low-variance-high-causal workspace signature does **not** reproduce at nano* — our J-space holds ~35% of variance (≈proportional), not the ~6–7% seen in a large model. The **selectivity** signature is real; the extreme low-variance one appears to be a scale phenomenon.

---

## Reading the negatives

Two self-corrections are worth calling out because they define the method:
1. The value-RL "23% ceiling" was diagnosed as a tooling limit — **wrong**; Fitted Value Iteration + a target network broke it to 39% with no new ops.
2. The composition "fragility" was attributed to a fundamental sampler problem — **largely wrong**; a well-tuned constant-step Langevin composes fine, and MALA makes it robust. The earlier claim was walked back under test.

*Charlot Lab · Institute for Physical AI @ BMI. Every figure is a measured result, not an estimate.*
