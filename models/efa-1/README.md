# EFA-1

**An energy-based, verified, deterministic, multi-body control model** — one body-embedding-conditioned trunk that
controls a *family* of bodies from a single weights file. Swap the body embedding, control a different body.

Charlot Lab · Institute for Physical AI @ Bailey Military Institute.
Runtime: [Ferric](https://ferric.physicalai-bmi.org) (pure-Rust, cross-fabric: Metal / WebGPU / Vulkan / browser).

## Identity — measured in what matters for machines that act

**This card refuses tokens and parameter-count-as-capability.** In a post-transformer control model those numbers carry
no meaning; the identity axes are:

| axis | EFA-1 (gated, round-trip-verified; exact numbers in `config.json`) |
|---|---|
| capability | reach% per body at **K=1 forward pass** (flagship run: 100% on all three bodies) |
| verification | the model's **own potential** ranks good actions below bad, per body (97–99%) |
| energy | ~39 kFLOP **per decision** — vs a discrete Gᵈ planner's 7× / 31× / **140×** more as DOF grows |
| determinism | same (state, goal) ⇒ same action, **bit-for-bit**; Ferric extends this cross-fabric (Metal ⇄ WebGPU) |
| generality | **3 bodies per weights file** (1-, 2-, 3-joint coupled chains), one learned embedding row each |
| footprint | ~39k params ≈ 160 KB — stated as *footprint*, never as capability |

## Architecture

The **coordinated energy family on one latent** (the corrected 2026 recipe, end-to-end):
- **Shared trunk inputs**: a body-agnostic 12-wide joint encoding (4 features per joint: cos(θ−g), sin(θ−g), ω, sinθ;
  inactive joints zero) ⊕ a learned **body embedding** (one row per body).
- **Flow head** (actuation): 3-wide velocity field, masked to the body's DOF, integrated at K=1 — no iterative energy
  descent over actions (the recipe the field's own evidence retired), no BPTT.
- **Potential head** (verify): a scalar energy over (state, action, body) — **low = valid**; trained contrastively;
  this is the model checking its own actions.

Inference (from `config.json`): `u = clamp(flow(feat, a=0, t=0, emb[body])[:dof])`; verify any candidate action by
`potential(feat, a, emb[body])`.

## Lineage & honesty (read before using)

- Built by the EFA program's gated release pipeline: **train → gate (every body reach ≥95% AND verify ≥90% AND
  bit-exact) → save → reload from disk → re-verify** — only verified weights ship. Provenance: `experiments/ebm_efa1.rs`;
  the 69-experiment [validation ledger](https://github.com/dcharlot-physicalai-bmi/efa/blob/main/docs/RESULTS.md)
  (negatives included), the [2026 frontier check](https://github.com/dcharlot-physicalai-bmi/efa/blob/main/docs/FRONTIER-CHECK-2026.md)
  that corrected the recipe, and the [EFA-1 spec](https://github.com/dcharlot-physicalai-bmi/efa/blob/main/docs/EFA-1.md)
  with the verified mid-2026 positioning.
- **Simulated bodies** (coupled-pendulum-chain family, dynamics in `config.json`), reachable-goal sets, distilled from
  per-body fitted-value demonstrators, one gated seed. The claim is the **architecture identity** —
  multi-body-per-weights + energy-verified + deterministic + joules-metered — not manipulation breadth.
- **Certificates**: the program has demonstrated contraction-region and structural port-Hamiltonian certificates on
  single-body controllers (ledger §VII); computing them for the EFA-1 closed loop is the named next step — *not claimed
  for this artifact*.
- Underactuated bodies remain a measured open boundary (ledger). EFA-2 targets a standard external body
  (MuJoCo / SO-101-LeRobot) so comparisons become externally reproducible.

## Positioning (verified mid-2026, cited in the spec)

Each of EFA-1's identity axes is unclaimed at product level by the current comparables: the leading edge lab measures
tok/s + memory (no joules); the nearest energy-based neighbor verifies *beneath* AI stacks but does not control bodies;
no physical-AI product ships bit-reproducibility; no surviving comparable ships multi-body-per-weights control.
**They verify beneath the stack; EFA-1 controls the body.**

License: Apache-2.0.
