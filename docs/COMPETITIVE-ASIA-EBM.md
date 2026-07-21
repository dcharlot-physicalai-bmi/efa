# EFA Competitive Intelligence — Cosine, Asia, and the Empty Center

_Prepared for the Dean, Institute for Physical AI (2026-07-21). Method: an 8-agent parallel web sweep (Cosine AI;
China post-transformer / neuromorphic-EBM; Japan Ising/thermodynamic; Korea/SG/Taiwan/India; global EBM frontier) +
a completeness critic + de-inflation pass. Confidence varies; items marked_[gap-fill]_/_[verify]_ are lower-confidence
and should be checked against primary sources before external use._

Scope: does "Cosine AI" compete, and where does the Asian (and adjacent global) landscape sit relative to EFA's
four-axis slot — **learned scalar energy ∩ physics-structured dynamics (port-Hamiltonian/metriplectic + Lyapunov) ∩
embodied real-time control ∩ edge / perf-per-watt**. "adjacent" = one or two axes; "occupies-slot" = all four.

---

## 1) Cosine AI — ORTHOGONAL. A foil, not a competitor.

**cosine.sh (Genie / Lumen)** — UK/SF applied-AI lab, YC W23 (Pullen / Stenner / Li). Genie = a **fine-tuned GPT-4o**
with a plan/retrieve/write/run/test agent scaffold; moat = proprietary SWE-trace data, not architecture. In 2026 they
moved up-market to **Lumen Sovereign**, a from-scratch **frontier transformer** on the Isambard-AI supercomputer under
the UK's £500M Sovereign AI programme. On all four EFA axes: **zero energy/EBM, zero post-transformer (heaviest possible
transformer _consumer_), zero embodied, anti-edge**. Success metric = SWE-bench points + data residency — the exact
leaderboard/token frame EFA rejects.

**Use as an inversion foil:** their bet is maximal centralization (sovereign frontier scale); EFA is maximal
decentralization (datacenter-grade capability at the edge, measured in watts). Name-collisions to ignore: Cosine Robots
(Bengaluru, ~$4k humanoid — mechatronics, no energy-first brain), Cosine Additive (Houston, 3D printing). **There is no
energy-based company named "Cosine."**

---

## 2) The Asian landscape on EFA's four axes

**The load-bearing fact: Asia has assembled all four EFA ingredients — across different actors — but no one player fuses them.**

**Axis 1 — Energy-based (learned scalar energy, inference = descent).** Almost empty among _models_; present in _hardware_ + one EBM lab.
- **BIGAI (Song-Chun Zhu, Beijing)** — the genuine learned-energy EBM lab in China (latent energy priors, diffusion-amortized MCMC, M2Diffuser mobile manipulation). Real scalar energy, **MCMC-sampled** (compute-heavy, anti-edge).
- **Ising / thermodynamic HARDWARE** — "literally minimize an energy in silicon," but every one minimizes a **fixed, hand-specified, discrete-spin Hamiltonian for combinatorial optimization**, not a _learned_ energy over a _continuous latent_ for _control_:
  - **Toshiba SBM (SQBM+)** — mechanically closest: spins = continuous position+momentum, integrates Hamiltonian ODEs to the min (10M vars). Right mechanism, wrong problem (fixed QUBO).
  - **NTT Coherent Ising Machine (100k DOPO)** — purest "physics does the computation" device; EFA-aligned on _materiality_, least practical (room-scale optics, ~2030s).
  - **Fujitsu Digital Annealer**, **Hitachi CMOS Annealing** (Hitachi most edge-plausible).
  - **Tohoku spintronic p-bits / g-bits (Fukami/Ohno/Kanai + NIST/UCSB)** — the deepest _genuine EBM hardware_: p-bits ARE hardware Boltzmann machines sampling a **learned** energy; g-bits (2024) add continuous Gaussian variables + GB-Boltzmann + diffusion; **first CMOS p-bit May 2026.** Clearest Asian energy-native hardware line.
  - **QBoson/Bose Quantum** (photonic CIM), **Tsinghua memristor CIM (Wu Huaqiang/Gao Bin)** (analog Hopfield at ~3% ASIC energy); _[gap-fill]_ **Tsinghua Taichi/ACCEL** photonics (~160 TOPS/W).

**Axis 2 — Post-transformer.** Crowded, no longer a moat by itself.
- **RWKV-7 "Goose" (BlinkDL/Peng Bo)** — pure attention-free RNN, no KV-cache, constant memory; markets "test-time-training its state via in-context gradient descent."
- **Kimi Linear/K3 (Moonshot, KDA)**, **Qwen3-Next (Gated DeltaNet)**, **MiniMax-01 (Lightning Attention 7:1)**, **Hunyuan-TurboS (Mamba/SSM hybrid)** — real departures.
- **SpikingBrain-7B/76B (CASIA, Bo Xu)** — linear/hybrid attention + adaptive spiking (69% sparsity), trained entirely on domestic **MetaX** silicon (sovereign compute).
- _[gap-fill]_ **RockAI "Yan" (Shanghai)** — genuinely non-attention (MCSD) LLM shipping on Raspberry Pi/robots/UAVs/phones — post-transformer + edge + embodied-adjacent.
- **Not post-transformer** (do not miscount): DeepSeek MLA/NSA/DSA, GLM, StepFun MFA — efficient _attention_.

**Axis 3 — Embodied.** "LLM-as-brain" on someone else's controller, not energy-based control.
- **RLWRLD (Seoul)** VLA/transformer on leaderboards; **Tianjic/TianjicX (Tsinghua, Luping Shi)** hybrid ANN+SNN, self-balancing bicycle — but **fixed SNN inference**; **SynSense (Chengdu)** mW event-driven vision in robots. _[gap-fill]_ **Lynxi** (Tianjic's commercial vehicle), **BrainChip Akida (AU)**, **CASIA BrainCog / Yi Zeng**, **Zhejiang "Darwin Monkey"** (960 Darwin3, ~2B spiking neurons).
- **Preferred Networks (Japan)** — Green500 pedigree, MN-Core L inference silicon, on-robot with Toyota from 2027. **Most credible embodied-edge program in Asia — but makes the transformer/diffusion-VLA stack efficient.** Competes for EFA's _market_, not its _architecture_.

**Axis 4 — Edge / perf-per-watt.** Overwhelmingly **inference silicon for the transformer stack.**
- Korea's "K-Nvidia": **FuriosaAI** RNGD (2.25–3× perf/W), **Rebellions**, **DeepX** DX-M1 (sub-5W, robots/drones — _the hardware EFA runs ON_). _[gap-fill]_ **Samsung HBM-PIM / SK Hynix AiM** (compute-in-memory), **Huawei Ascend / Cambricon**.
- **India** — **IISc NeuRonICS (ARYABHAT + molecular memristor)** world-class analog CIM; frugal-LLM camp (Sarvam/Krutrim) never meets the hardware camp. **KAIST self-learning memristor** (on-chip learning for robots/AVs).

---

## 3) Closest to EFA's slot — and exactly what each is MISSING

1. **EBT-Policy (arXiv 2510.27545) — OCCUPIES THE SLOT. Sharpest competitive signal by a wide margin.** Chinese-led (Tsinghua/Peking/ZhiCheng + UIUC). Visuomotor policy as an Energy-Based Transformer: single scalar energy over actions, **inference = energy descent (2 steps vs Diffusion Policy ~100, ~50×)**, better real-robot success, **emergent zero-shot retry with no retry data**, uncertainty from the scalar energy. **Missing (= EFA's entire differentiation window):** (1) physics-structured energy (no port-Hamiltonian/metriplectic), (2) no Lyapunov stability/safety certificate, (3) FLOPs/steps framing, not measured joules-per-task, no microcontroller target.
2. **Tohoku p-bits/g-bits** — energy + edge (hardware); missing physics-structured control + embodiment. EFA's substrate physically instantiated, but aimed at sampling/optimization; nobody pointed it at closed-loop control.
3. **BIGAI** — energy + embodied; missing physics structure + edge (generative/vision prior sampled by MCMC).
4. **Toshiba SBM** — energy-descent mechanics + edge-capable; missing learned energy, continuous-state semantics, embodiment.
5. **SpikingBrain (CASIA)** — post-transformer + edge-intent + sovereign silicon; missing energy + embodiment. **Fusion risk is organizational:** BrainCog/embodied siblings in the same institute.

---

## 4) Real threats vs noise

**TIER 1 — genuine architectural threat (energy + control):** **EBT-Policy and the Yilun Du EBM cluster.** The
inference-as-energy-descent-for-control idea is proven, published, improving under a coherent community. Out-execute, don't dismiss.

**TIER 2 — substrate threats/allies (energy hardware EFA may run ON):** **Tohoku p-bits, Extropic (X-0/Z-1), Normal
Computing (CN101)** — 1,000–10,000× by sampling an energy distribution in silicon; complementary unless one ships an
energy-based _control_ stack. **Tianjic/Lynxi, SynSense, Akida, KAIST/IISc memristors** — own the deployment surface, fixed SNN inference.

**TIER 3 — market threats (compete for embodied-edge customers, not architecture):** **Preferred Networks (+Toyota),
RLWRLD, DeepX.** They win the narrative slot ("who owns the robot's brain") with efficient transformers/VLAs. Beat on joules-per-task + provable stability.

**NOISE — do NOT inflate into energy-first threats:**
- **All delta-rule / post-transformer LLMs** (RWKV-7, Kimi KDA, Qwen Gated DeltaNet, MiniMax, Hunyuan). The delta rule minimizes a **fixed associative-recall L2 loss**, not a learned scalar energy — "optimization-at-inference" ≠ "energy-based." Adjacent on one axis.
- **Efficient/sovereign LLMs + inference chips as _architecture_ threats** (Furiosa/Rebellions, DeepSeek, StepFun, Naver, Sarvam, ternary/BitNet). They attack cost while _keeping the transformer_ — win joules-per-_token_, not joules-per-_physical-task_.
- **Trida-7B (Trillion), LLaDA (Renmin/Ant)** — parallel/discrete text-diffusion = iterative masked-token prediction, **not energy-based.** Reclassify "post-AR, not EBM."
- **JEPA / V-JEPA** — "energy" is conceptual scaffolding over a regression-trained ViT with ~no runtime descent; effectively orthogonal on the energy axis.
- **QBoson / NTT CIM / Fujitsu DA** — validate physics-does-energy but solve buses/finance/drugs. Orthogonal to control.

---

## 5) EFA's unclaimed slot, restated in light of Asia

Every ingredient exists and is well-funded, **in different buildings**: post-transformer-at-scale (SpikingBrain, RWKV,
Qwen/Kimi/MiniMax/Hunyuan); learned energy (BIGAI; hardware Tohoku p-bits/g-bits); physics-does-the-minimization
(Toshiba/NTT/Hitachi/Fujitsu, Tsinghua memristor+photonics, QBoson); embodied+edge+watts (PFN, Tianjic/Lynxi/SynSense/Akida, DeepX, KAIST/IISc).

**Nobody has ASSEMBLED them into a single artifact that is: a _learned_ scalar energy (not a hand-specified Ising
Hamiltonian, not spiking sparsity, not conceptual scaffolding) that is _physics-structured_ (port-Hamiltonian /
metriplectic — conservative + dissipative), _descended in real time for embodied control_, carrying a _Lyapunov
stability/safety certificate_, and _measured in joules-per-task on edge silicon_.** The Asian energy-hardware giants are
all on the "minimize a _given_ energy" side (fixed QUBO, offline optimization). The "_learn_ the energy, structure it by
physics, descend it for control" half — EFA's half — is commercially and academically unclaimed in Asia. The one line
that reached the intersection (EBT-Policy) has no physics structure and no certificate.

**⚠ The field EFA's moat lives in — neural-Lyapunov / port-Hamiltonian / Hamiltonian-Lagrangian control (HNN Greydanus,
LNN Cranmer, DeLaN Lutter, neural-Lyapunov Ya-Chien Chang & Sicun Gao @ UCSD)** — the sweep first dismissed as
"US/European" but it is active and partly Asian-diaspora. **It is the real competitor for EFA's differentiator. Own it
before someone bolts it onto EBT-Policy.**

**One-line positioning:** _EFA = EBT-Policy's energy-descent controller + a port-Hamiltonian physics prior + a Lyapunov
certificate + measured watts on the edge. Everyone in Asia has one or two of these; no one has all four._

---

## 6) Watch list

**Architecture (Tier-1):** EBT-Policy (2510.27545); **Yilun Du (Harvard/Kempner)** — the hub of the modern EBM revival,
_the_ lab to watch; Energy-Based Transformers (2507.02092, note: argues _more_ inference compute — anti-watts, the emphasis
EFA must invert); Equilibrium Matching (2510.02300) + Energy Matching (2504.10612) — inference-as-energy-descent becoming consensus.

**Truer inference-as-optimization cousins (sweep under-weighted):** **TTT layers (Yu Sun, Xinlei Chen, 2407.04620)** — "hidden
state = a model updated by gradient descent at test time," a truer EFA cousin than the delta-rule LMs; **Liquid AI / LNNs
(Hasani, MIT)** — continuous-time ODE nets proven for edge drone control, closest existing overlap with EFA's _physics_ axis.

**EFA's own moat to dominate:** neural-Lyapunov (Chang & Sicun Gao, UCSD); port-Hamiltonian/Lagrangian nets (Greydanus HNN,
Cranmer LNN, Lutter DeLaN); latent EBMs (Ying Nian Wu / Jianwen Xie) + score-based (Yang Song) — name them as prior art EFA _extends_.

**Hardware substrates (Tier-2 ally/target):** Tohoku p-bits/g-bits (first CMOS p-bit May 2026); Extropic (Z-1, ~250k p-bits)
+ Normal Computing; DeepX DX-M1 (sub-5W, robots); _[verify]_ Tsinghua Taichi/ACCEL, Samsung HBM-PIM / SK Hynix AiM, Lynxi, Akida.

**Organizational fusion risks:** **CASIA (Bo Xu)** — SpikingBrain + BrainCog/Yi Zeng embodied cognition under one roof
(highest-probability Asian slot-occupier); **Preferred Networks + Toyota** (silicon + robots + funding to adopt an energy controller wholesale).

**Attribution hygiene:** SpikeGPT is **UCSC-led (Rui-Jie Zhu)**, not China; never list Cosine, JEPA, or delta-rule LLMs as "energy players."
