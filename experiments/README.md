# EFA — Reference Implementations

These are the actual experiments behind [the whitepaper](../WHITEPAPER.md) and [the results ledger](../docs/RESULTS.md) — self-contained Rust programs that train, act, and discover on the [**Ferric**](https://ferric.physicalai-bmi.org) pure-Rust cross-fabric GPU stack (wgpu → Metal / WebGPU / Vulkan). They are included as **readable reference**: they depend on `ferric-tensor` (the Institute's autograd + GPU tensor library) and are run there as `cargo run --release --example <name>`.

Each file's header comment states its purpose, method, and the paper/lineage it builds on. Every printed number is a measured result.

## The core EFA model on the fabric
| file | what it demonstrates |
|---|---|
| `efa_maze.rs` | world model + FVI value + value-guided planning on a maze (the value-RL last mile) |
| `efa_maze_mppi.rs` | MPPI/CEM latent planning — 39% → 69% reach from test-time search alone |
| `efa_pomdp.rs` | the full stack as one model: sparse latent → Hebbian `Z` → world model → value → MPPI, `Z` load-bearing |
| `efa_benchmark.rs` | reach + path-optimality measured against the exact BFS optimal (87%) |

## Energy-based zero-shot (the narrow, real edge)
| file | what it demonstrates |
|---|---|
| `ebm_compose.rs` | zero-shot compositional generation by energy summation |
| `ebm_verify.rs` | energy-as-verifier best-of-N → 100% selection |
| `ebm_edlm.rs` | residual-energy verifier on a **real autoregressive sequence model** (EDLM) |
| `ebm_ood_goal.rs` | OOD-goal generalization — goal-agnostic energy generalizes where learned maps collapse |
| `ebm_mala.rs` | Metropolis-corrected sampling — robust composed generation (and a corrected overclaim) |
| `ebm_ebt_true.rs` | **the true Energy-Based Transformer** — train THROUGH the unrolled energy descent (2nd-order autograd); 100% in-dist where feedforward is 0%, thinks (K=1→6: 22→100%) |
| `ebm_ebt_scale.rs` | **scaling the true EBT** — chain of D coupled multivalued links; confirms the paper's stabilizers (rand K/α + Langevin) and prices the ceiling as **capacity** (D=6 plateau 13→25→42% with width 128→256→512) |
| `ebm_ebt_struct.rs` | **structure vs scale** (honest negative) — a weight-shared *local* energy is **worse**, not better (D=4: 8% vs the generic net's 78%); locality alone doesn't buy generalization, the capacity route is what scaled |
| `grad2_test.rs` | second-order autograd validation — `d²/dx² Σx³=6x`, `Σeˣ`, and HNN input-gradient training |

## Energy-based AI-for-science
| file | what it demonstrates |
|---|---|
| `ebm_hamiltonian.rs` | Hamiltonian NN — learn the energy, conserve it (5.5× lower drift) |
| `ebm_conserve.rs` | discover a conservation law (nonlinear pendulum invariant, corr 0.99) |
| `ebm_discover.rs` | discover a governing ODE by sparse energy-min (SINDy-as-EFA) |
| `ebm_lorenz.rs` | discover the **Lorenz** system (chaos) from noisy data |
| `ebm_pde.rs` | discover **Burgers'** PDE from spatiotemporal field data (PDE-FIND) |
| `ebm_reaction.rs` | discover **Fisher–KPP** reaction–diffusion |
| `ebm_lotka.rs` | discover **Lotka–Volterra** from **real** Hudson Bay lynx–hare data (1900–1920) |
| `ebm_field.rs` / `ebm_field2.rs` | energy-conserving field surrogate — an **honest negative**: naive force net wins, even with exact 2nd-order gradients |

## Cognitive-science bridges (predictive coding & global workspace)
| file | what it demonstrates |
|---|---|
| `ebm_lm_surprisal.rs` | **predictive-coding language twin** — an energy-based LM's prediction energy encodes surprisal (ρ=+0.71), but inference *effort* does **not** track output-entropy difficulty (strong claim falsified); compute scales with reasoning depth, not uncertainty |
| `ebm_jlens.rs` | **a Ferric J-lens** (averaged ∂output/∂latent, via autograd) — a sparse-positive latent shows the *selectivity* of a global workspace (ablating causal dirs collapses flexible reasoning 100→31%, spares automatic report 100→98%); the frontier low-variance signature does not reproduce at nano |

> Note: these are nano-to-small-scale mechanism-and-method proofs, not deployed systems. See the whitepaper §4 for what is and is not claimed.
