# Energy First Architecture (EFA)

> One learned scalar **energy** — over a sparse-positive, monosemantic latent — that predicts, plans, remembers, verifies, and discovers. A post-transformer, on-device, energy-first architecture for **Physical AI**.

**Charlot Lab · [Institute for Physical AI @ BMI](https://physicalai-bmi.org) · [efa.physicalai-bmi.org](https://efa.physicalai-bmi.org)**

---

## What this is

The dominant AI stack — point neuron, dense matmul, frozen weights, finite context, black box, cloud GPU — was built for machines that *talk*. Physical AI is for machines that *act*, and acting is an **energy** problem (Hamiltonians, least-action, value descent). **EFA** takes energy as the native object. A single learned scalar energy does what the incumbent stack assembles from separate subsystems:

- **World model** — predict by descending energy in a latent
- **Planner** — act by descending energy toward a goal
- **Memory** — a Hebbian fast-weight matrix written *at inference*, no gradient
- **Verifier** — low energy = valid (best-of-N selection, native)
- **Discoverer** — the governing law is the sparse energy that explains the data

The bet is **unification**: not six modules, but *three mechanisms over one representation* — because a sparse-positive activation space is at once the readable feature space, the associative-memory space, and the world-model prediction space.

## Read this first

- **[WHITEPAPER.md](WHITEPAPER.md)** — the thesis, architecture, full validation program, honest limits, and roadmap. Start here.
- **[docs/RESULTS.md](docs/RESULTS.md)** — the complete validation ledger: every experiment, every number, every priced limit.
- **[experiments/](experiments/)** — reference implementations (Rust, run on the [Ferric](https://ferric.physicalai-bmi.org) pure-Rust cross-fabric GPU stack).

## The through-line: measured, and priced

This project is built on one discipline — **every claim is measured, and every limit is priced.** EFA is *not* presented as beating frontier systems at scale. It is a specific architecture whose edge is real on the axes where a native energy is the right representation:

| axis | representative result |
|---|---|
| test-time thinking (acting) | MPPI latent planning **39% → 69%** reach, same value net |
| native verification | energy best-of-N → **100%** selection; on real AR sequences too |
| zero-shot composition | concept conjunctions never trained jointly, by energy summation |
| OOD generalization | goal-agnostic energy **37→41%** OOD where learned maps collapse |
| energy conservation | Hamiltonian NN, **5.5×** lower energy drift |
| scientific discovery | **Lorenz (chaos), Burgers, Fisher–KPP, and real lynx–hare data** — governing laws recovered exactly |

And the honest boundaries that make the wins credible: energy-*descent* is step-size-sensitive (verification is the robust route); an energy-conserving *surrogate* at field scale did **not** beat a naive net (needs exact gradients); AI-for-math is verifier-ready but gated on a Lean-trained encoder. See the whitepaper §4.

## How it relates to IPAI @ BMI and Physical AI

EFA is the Institute's coherent, runnable position on how intelligence that *acts* should be built: **on-device** (the whole stack runs in a browser tab on WebGPU via the Institute's pure-Rust **Ferric** compute layer), **energy-first** (physics is already energy-based — no bolted-on loss), and **legible by construction** (every mechanism is a small, driveable artifact — a research property *and* a curriculum surface). See the whitepaper §5.

## Status

Research whitepaper v1. Nano-to-small scale; mechanism-and-method proofs on canonical benchmarks and real data — not deployed systems at frontier scale. The point is a precise map of *where* an energy-based program can win and *what each next rung costs*.

---

*Institute for Physical AI @ Bailey Military Institute. Every figure in this repository is a measured result, not an estimate.*
