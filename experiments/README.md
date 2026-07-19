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

> Note: these are nano-to-small-scale mechanism-and-method proofs, not deployed systems. See the whitepaper §4 for what is and is not claimed.
