# EFA — SOTA Ingestion Roadmap (ingest into Rust, don't be rigid)

_2026-07-21. Mandate (the Dean): do NOT be rigid; ingest the frontier into Rust and implement it to improve EFA's
ecosystem, architecture, and thesis. Designed by an 8-agent workflow (six Rust/Ferric ingestion specs + a thesis-openness
pass + a judge), grounded in `ebm_oneenergy.rs`, `ebm_compose5.rs`, `ebm_ebt_true.rs`, `grad2_test.rs`, and
[COMPETITIVE-ASIA-EBM.md](COMPETITIVE-ASIA-EBM.md). Every step is buildable now on the goal-conditioned pendulum/cart-pole
with exact repo idioms, then scales. Priced-honesty ledger at the end — never overclaim._

## The two thesis revisions (non-rigidity, as a badge of rigor)

**ADOPT NOW — the inference primitive (impact 9).** EFA's "control by descent" is currently **not descent**: it is a
1-step `argmin` over 5 fixed discrete torques of a Bellman value (`ebm_oneenergy.rs::greedy`). Replace it with **multi-step
gradient descent on E over a CONTINUOUS action**, trained *through* the unrolled descent using the second-order autograd
EFA already has (`ebm_ebt_true.rs::solve` pointed at the body instead of an algebra toy). This is the exact mechanism
EBT-Policy proves in EFA's slot; it dissolves the named "1-step greedy over 5 torques" weakness; and it makes **K (descent
steps) the literal joules-per-task knob** the thesis already markets as economy-of-effort — so "inference = energy descent"
finally means what it says. Greedy-discrete-argmin is demoted to a coarse fallback.

**ADOPT AS IT MATURES — one energy → a coordinated family (impact 8).** Reframe "ONE scalar energy for everything" to
**"one shared sparse-positive LATENT + a small COORDINATED FAMILY of coupled energies"** (observer / value-control /
feasibility-verify / Lyapunov / decode). Every EFA experiment already split the single energy under load (compose5/6's
decode-vs-control tension; VERIFY stuck at 76% because a value is a poor validity classifier), and the frontier's own
objects are separate potentials (EBT action-energy, neural-Lyapunov V, port-Hamiltonian H+R, metriplectic's two
potentials). More honest **and** more capable. Reposition: **"the empty center is occupied by a coordinated energy family,
not a lone scalar."** Revision is a badge of rigor, not retreat — both revisions strengthen the moat rather than concede it.

## Build order

**1. `ebm_actdescent` — continuous multi-step energy descent over actions (ingest EBT-Policy). REVISES the inference primitive.**
Phase 0 de-risk spike (minimal retrain): put `step(θ,ω,u)` in-graph (energy is periodic in θ via cos/sin, so no `wrap`
needed for the gradient), control by K-step descent `u ← clamp(u − α·∂E(step(s,u),g)/∂u)` via `grad()`; measure reach% for
K∈{0,1,2,4,8,16} vs the discrete-greedy baseline. Phase 1: FVI-train a dedicated continuous-action energy Q(state,u,goal)
over structured columns `[cos(θ−g),sin(θ−g),ω,cosθ,sinθ,u,u²]`. **Win:** the accuracy-vs-joules (perf-per-watt) curve on a
body — reach% vs K overlaid on the discrete baseline; action smoothness (continuous ≪ bang-bang); reach-from-bad-warm-start
= emergent retry. **Honest cost:** on a 1-D torque the continuous win over 5 discrete is **modest**; the exponential payoff
is multi-joint only (not shown here yet).

**2. `ebm_lyapunov` — earn the Lyapunov certificate on the same energy (neural-Lyapunov, Chang & Sicun Gao). EXTENDS + de-overclaims.**
First rename today's result to **"empirical Lyapunov MONITOR (100% on-policy)"**, *not* a certificate. Then earn a real one:
a Lyapunov-risk objective (`V=E(x,g)−E(g,g)`, penalize `relu(margin−V)` and `relu(dV+κ‖x−g‖²)`), CEGIS counterexample mining
(input-grad ascent toward violators + a δ-grid backstop), and a **Lipschitz-sound** certified region (softplus is
1-Lipschitz; report conservative upper bounds). **Win:** certified region-of-attraction (0 today → measured %); 0 descent
violations inside `{V≤c}`; certificate at **zero marginal inference joules** (V *is* E). **Honest cost:** a Lipschitz bound
is **not** an SMT/dReal proof — the region is conservative; needs known differentiable dynamics.

**3. `ebm_phcontrol` — port-Hamiltonian / IDA-PBC energy shaping. EXTENDS (physics structure).**
Constrain the energy to the mechanical form `E(θ,ω;g)=V_φ(θ;g)+½ω²`; fix canonical `J=[[0,1],[-1,0]]`, PSD damping
`R=diag(0,r)`; closed-form energy-shaping controller `u = sinθ − dV/dθ + (0.05−r)ω`. **Win:** CERTIFY goes to **~100%
structural** (dE/dt = −r·ω² ≤ 0 *by construction*, a certificate not a check); energy-evals/step drop 5→~2. After step 3
EFA's certified controller and EBT-Policy's descent controller are the **same object** — EFA's being the only one carrying
the certificate. **Honest cost:** collocated/mechanical bodies only; negative-*semi*-definite; underactuated needs the
matching PDE (scale-next).

**4. `ebm_eqmatch` — companion validity energy (Equilibrium/Energy Matching, score-first, no Z). EXTENDS → coordinated family.**
Train a genuine compatibility energy by matching the score (∇E), not a Bellman value. **Win:** attacks the pinning weakness
(‖∇E‖ inside the tolerance annulus goes ~0→c₀, so it keeps pushing to the exact center) and lifts **VERIFY from 76%** (a
value is a poor validity classifier; this trains low-E-on-valid directly). **Honest cost:** dynamics-blind until a build-2
rollout-path match.

**5. `ebm_ttt` — Test-Time-Training latent-state observer (Yu Sun). EXTENDS → completes the loop.**
The same descent primitive over the LATENT STATE: a state estimate refined by descending a self-supervised prediction-error
energy at inference. **Win:** matches the repo's hand-tuned fixed-gain observer with **zero hand-tuning** on the linear case
and **beats** it (fixed gain diverges) on a nonlinear tip-position sensor; RMS-vs-K perf-per-watt curve; occlusion-coast
horizon. Completes sense→think→act→remember under **one primitive, one K**. **Honest cost:** only *ties* on the linear case
— lead with the nonlinear sensor.

_Deferred / parallel:_ `ebm_hopfield` (modern continuous Hopfield as certified O(1) associative recall) — deferred because
it chains a *second* energy rather than sharing the one latent, in tension with the steps-4/5 reframe; and a continuous-time
energy **flow** (RK4/adjoint neural-ODE) — the heaviest lift, reframing K-step descent as the fixed-step special case of a
continuous flow (worth it once the ODE-solver+adjoint tooling justifies it).

## Dependencies & discipline
2,3 depend on 1 (they certify/structure the continuous controller); 3 sharpens 2; 4 is a largely independent training-recipe
swap; 5 completes the coordinated-family reframe. Every step names a one-afternoon zero/minimal-retrain **spike** answering
its load-bearing question before the full build. **Priced-honesty ledger:** step 1's 1-D continuous win is modest
(exponential is multi-joint only); step 2's certificate is conservative, not SMT-proven, needs known dynamics; step 3's free
certificate is collocated-only and negative-semi-definite; step 4 is dynamics-blind until build-2; step 5 only ties on the
linear case. Each full-force payoff (continuous descent, region certificate, matching PDE) reaches strength on
multi-joint/underactuated bodies — the honest "scale next" frontier.
