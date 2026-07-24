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
| `ebm_ebt_true.rs` | **the true Energy-Based Transformer** — train THROUGH the unrolled energy descent (2nd-order autograd); 100% in-dist, thinks (K=1→6: 22→100%). ⚠ its "feedforward=0%" claim was unmeasured & wrong — see `ebm_edge.rs` |
| `ebm_edge.rs` | **honest correction** — measures the feedforward baseline the EBT write-ups asserted: a fair feedforward solves the multivalued task at **100% on 354 params** (all sizes). The EBT needs 2641 params + K≥6 thinking to match. The toy was the *wrong* demonstration of the edge thesis (a discrete-token puzzle can't test the *architectural* mismatch that defeats transformers in robots) |
| `ebm_ebt_scale.rs` | **scaling the true EBT** — chain of D coupled multivalued links; confirms the paper's stabilizers (rand K/α + Langevin) and prices the ceiling as **capacity** (D=6 plateau 13→25→42% with width 128→256→512) |
| `ebm_ebt_struct.rs` | **structure vs scale** (honest negative) — a weight-shared *local* energy is **worse**, not better (D=4: 8% vs the generic net's 78%); locality alone doesn't buy generalization, the capacity route is what scaled |
| `grad2_test.rs` | second-order autograd validation — `d²/dx² Σx³=6x`, `Σeˣ`, and HNN input-gradient training |

## Energy-based AI-for-science
| file | what it demonstrates |
|---|---|
| `ebm_hamiltonian.rs` | Hamiltonian NN — learn the energy, conserve it (5.5× lower drift) |
| `ebm_phnn.rs` | **port-Hamiltonian** — energy-accounted model of a damped, *driven* body (a joint + motor); conservation-only HNN fails 130× on rollout, PHNN ties the black box AND recovers friction c/m=0.30 exactly — the bridge to embodied control |
| `ebm_control.rs` | **energy-based control — architectural match** — a *learned* energy (HNN, RMSE 0.003) drives underactuated pendulum swing-up + stabilization; a naive position controller with the same torque cannot reach upright. Energy is the load-bearing control object — the edge is architectural match to physics, not toy capability |
| `ebm_plan.rs` | **control by descent (no hand law)** — MPPI plans the swing-up through a *learned* model; the energy pump *emerges* from planning, and capability to reach upright scales with planning **horizon** (compute), 2.44→0.33 rad. Directional — reaches the top but doesn't cleanly stabilize (honest limit) |
| `ebm_watts.rs` | **the metric = joules/task, not tokens** — exact FLOP accounting: energy-shaping swing-up = 110 kFLOP (microcontroller-scale) vs a single 7B-LLM token = 14 GFLOP (~127,000× more, datacenter). The architecture is edge-compute-viable; the *capability* is still nano (honest caveats in-file) |
| `ebm_percept.rs` | **scale the body + close the perception loop** — cart-pole (4-D, underactuated) balanced from *noisy position-only* obs. Energy-based state inference (perception = inference to a low-energy explanation) balances (RMS 0.081) where naive finite-diff velocity fails catastrophically (RMS 73.5). The embodied loop closed on a scaled body |
| `ebm_dropout.rs` | **richer perception** — noise + observation dropout; the energy observer coasts on its dynamics prior through 0–50% occlusion where naive fails from noise (honest limit at 70%) |
| `ebm_dpend.rs` | **harder body — built properly (HNN-canonical)** ✅ — recovers the conserved energy of a *chaotic* double pendulum at \|corr\|=**0.998** (never told the formula) by matching Hamilton's equations in canonical coordinates. Three ad-hoc invariant objectives had failed (≈0.03–0.11); the right method works. Architectural match under coupling + chaos |
| `ebm_cartfull.rs` | **full regulation — built properly (LQR)** ✅ — real Riccati-solved gains regulate cart *and* pole (true state RMS 0.009/0.048; energy observer 0.044/0.235) from noisy position-only obs. The wall grid-search couldn't clear, cleared by the right tool |
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
| `ebm_garden.rs` / `ebm_garden3.rs` | **garden-path reanalysis** — v1 (`ebm_garden`) was inconclusive by design (circular metric, no commitment); the **bistable-prior** model (`ebm_garden3`) is the real, non-circular result: reversing an early commitment is a **thinking-resolvable compute cost** (garden-path K=1→8: 64→100%; matched controls stay ~93%) |

## The physics of intelligence (Krakauer grounding)
| file | what it demonstrates |
|---|---|
| `ebm_expertise.rs` | **intelligence = economy of effort** — as the descent EBT learns, the descent steps to solve *fall* (K*: 8→4; K=4 accuracy 11→100%). Expertise recodes the landscape so the hard problem becomes cheap — fewer watts for the same capability |
| `ebm_curiosity.rs` | **curiosity ↔ expertise — two halves of one arc, one scalar** — bridges OIST's curiosity-driven free-energy robot (Tinker–Doya–Tani, *Science Advances* 2026) to the economy-of-effort result. Curiosity = train on the highest-**surprise** (post-descent residual) contexts, a sibling of OIST's KL info-gain reward. **Economy half — ROBUST:** surprise falls 1.38→0.12 and K*→4 as skill forms — the same scalar curiosity acts on is what mastery drives down. **Speed half — HONEST NEGATIVE here:** on the diffuse multivalued task curious ≈ control (both hit 85% at step 300); a companion localized-region bench (PAI-101 *Curiosity* lesson) shows curiosity ~halves steps (2.25×). The speed win scales with how concentrated the informative experience is — it is task-dependent, not universal |
| `ebm_materiality.rs` | **materiality does computation** — disc-packing: abstract random search collapses (59%→0% valid as density rises) while descent on a repulsion energy solves 100% in a few steps. Physics drops the search clauses |

## The flagship — one structured energy, four jobs, on a body (the empty center of the triangle)
| file | what it demonstrates |
|---|---|
| `ebm_oneenergy.rs` | **one goal-conditioned energy, four jobs** — the SAME learned E(state,goal) controls (89%), verifies (76%), remembers (goal=attractor, 0.155 rad), certifies stability (100% Lyapunov) on a pendulum. Score-first, no partition function. The empty center of the physics↔language↔control triangle, occupied |
| `ebm_lang.rs` | **the language edge fused** — the goal is a symbolic INSTRUCTION the energy *decodes* from a learned embedding (not handed the goal). One E(state,instruction) over 6 commands: decodes the language cleanly (each command's energy-min at its goal, 0.069 rad), certifies (95%), verifies (75%); control 61% (honest weak spot — off-goal landscape). Full physics↔language↔control centre |
| `ebm_ternary_cert.rs` | **the SMT-certified ternary certificate, on the fabric** — the exact weights dReal (δ-complete SMT) certified as a Lyapunov energy on the reversed Van der Pol's non-convex ROA (annulus to R=1.3, where the best *quadratic* is refuted at 1.2): +18% certified radius beyond any provable quadratic, with T∈{−1,0,+1}, 8/16 nonzeros — the hidden layer is 2 multiplies + selects/adds. Pure f64 Rust: cross-verified to ~5e-13 against the certified reference, 100% in-annulus descent measured live, compiles unchanged to wasm32. Synthesis lineage Chang/Gao (Neural Lyapunov Control, dReal); our legs are the ternary weights, the quadratic-anchored init, and train-stricter-than-verify margins |
| `ebm_cert_verify.rs` | **the device-side certificate RE-VERIFIER** (Ferrite gate's teeth) — dependency-free f64 Rust, wasm-clean (75 KB), NO solver: re-proves the deployed ternary energy still carries a valid Lyapunov certificate over the whole continuous region (2nd-order Taylor + per-box CROWN \|tanh″\|, adaptive box refinement, all 6 free/contact×saturation hybrid cases) before a pack is trusted. Verifies *correctness*, not just Ferrite's bit-exact *reproduction*. Cross-verified 4.8e-13 to the certified artifact. **Demonstrates both gate decisions on one verifier:** the deployed ternary energy is ACCEPTED (worst bound converges +0.026→−0.000, 1486 boxes); with the learned head disabled the bare quadratic is REJECTED at R=1.2 (worst bound *plateaus* at +0.25, failing boxes multiply under refinement — the signature of a real §5-law violation, not a loose bound), naming the offending box exactly as a drifted pack would 400. Runs browser→Jetson→edge — the device-side path the Ferrite plan specifies |

> Note: these are nano-to-small-scale mechanism-and-method proofs, not deployed systems. See the whitepaper §4 for what is and is not claimed.
