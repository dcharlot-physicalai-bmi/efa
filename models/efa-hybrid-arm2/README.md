# efa-hybrid-arm2

**The first open-weight EFA (Energy First Architecture) model** — the *hybrid* actuation architecture
`v = −κ∇ₐE + w` for a coupled 2-link arm: **one scalar potential Eθ(s,a,t) that actuates (descend its action-gradient)
and verifies (Eθ(·,1): low = valid action)**, plus a small ℓ2-penalized correction net `w` that absorbs the residual a
scalar fit cannot express.

Charlot Lab · Institute for Physical AI @ Bailey Military Institute.
Runtime: [Ferric](https://ferric.physicalai-bmi.org) (pure-Rust, cross-fabric: Metal / WebGPU / Vulkan / browser).

## What this is (and is not)

This is a **proof-point checkpoint**, not a capability release: a tiny (~50k-param) goal-conditioned controller for a
simulated 2-torque coupled pendulum chain, distilled from a fitted-value-iteration demonstrator. It exists to ship the
**EFA artifact class** — the "coordinated family over one latent" as downloadable weights with a verified round-trip —
and the release pipeline behind it. The architecture, not the body, is the point.

## Architecture

- **Potential** `Eθ(s,a,t)`: relu/linear MLP over `[cos(θ₁−g₁), sin(θ₁−g₁), ω₁, cos(θ₂−g₂), sin(θ₂−g₂), ω₂, sin θ₁, sin θ₂, a₁, a₂, t]` → scalar.
- **Correction** `wφ(s,a,t)`: same inputs → 2-D velocity residual, trained jointly with an ℓ2 penalty (λ in `config.json`) so the potential must carry all the field it can.
- **Inference** (K=2 recommended): `a←0; for k in 0..K { t=k/K; a += (−κ∇ₐEθ(s,a,t) + wφ(s,a,t))/K }` — the action *is* energy descent plus a small correction. `∇ₐEθ` is exact (analytic backprop through the relu net).
- **Verify readout**: rank candidate actions by `Eθ(s, a, t=1)` — lower is more valid.

## Metrics (this artifact — gated, round-trip-verified; exact numbers in `config.json`)

Release gate: **actuate ≥95% (K=2) AND verify ≥90%**, best-of-≤3 seeds. The shipped weights were **reloaded from this
file and re-evaluated** before release. Flagship-run context (see the [validation ledger](../../docs/RESULTS.md)):
actuate 100% @ K=1–4, verify 94.3%, potential carrying 65% of the field; λ-sweep frontier: ~57–65% energy-first at
100% control, knee between λ=0.1–0.3.

## Honest limits (read before using)

- **Simulated toy body** (2-link chain, known dynamics in `config.json`); the policy is meaningless without that env.
- **Distilled** from a discrete FVI demonstrator — the win is the architecture and eval-budget (K forward passes vs Gᵈ argmin evals), not new capability.
- **FVI seed variance is real** (multi-seed study in the ledger); this artifact passed the gate, and the gate policy is disclosed in `config.json`.
- The potential carries the *majority*, not all, of the field — the measured energy-firstness is in `config.json`; a pure-potential variant costs actuation (ledger §VII).
- Trained & evaluated entirely on-device via Ferric; no external data.

## Provenance

Built by `experiments/efa_release.rs` (train → gate → save → **reload → re-verify**) from the EFA validation program
(65 measured experiments, negatives included): see `WHITEPAPER.md` §3.9 and `docs/RESULTS.md` §VII, including the 2026
frontier check that produced this architecture (`docs/FRONTIER-CHECK-2026.md`).

License: Apache-2.0.
