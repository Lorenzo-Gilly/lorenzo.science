---
title: "Experimental Setup & Plot Guide"
weight: 2
---

{{< katex />}}

# Experimental Setup & Plot Guide

This page documents how the Stage A v5 diagnostic was run and how every
plot and viewer was generated. If something on a graph looks odd, the
answer is probably here.

---

## 1. Physical Model

| Parameter | Value |
|-----------|-------|
| Model | 2D Lennard-Jones |
| $\varepsilon$ | 1 |
| $\sigma$ | 1 |
| Potential cutoff | $r_\text{cut} = 2.5\sigma$ |
| Potential shift | Yes (continuous at cutoff) |
| Long-range corrections | No |
| Number density | $\rho^* = 0.08$ |
| Box side | $L = \sqrt{N / \rho^*}$ |

The pair potential is the standard LJ form, shifted to zero at
$r_\text{cut}$:

$$U(r) = \begin{cases} 4\varepsilon\left[\left(\frac{\sigma}{r}\right)^{12} - \left(\frac{\sigma}{r}\right)^{6}\right] - U(r_\text{cut}) & r < r_\text{cut} \\\\ 0 & r \geq r_\text{cut}\end{cases}$$

The density $\rho^* = 0.08$ is in the low-density gas branch of the
coexistence curve. The goal is to find $T^*_\text{dew}$ — the temperature
below which a metastable droplet phase becomes thermodynamically
competitive with the gas.

---

## 2. System Sizes

Three system sizes were run:

| $N$ | Box side $L$ | Particles |
|-----|-------------|-----------|
| 32  | 20.0        | 32        |
| 64  | 28.3        | 64        |
| 128 | 40.0        | 128       |

---

## 3. MCMC Details

### Algorithm

Single-particle Metropolis Monte Carlo. Each "move attempt" picks one
particle uniformly at random and proposes a Gaussian displacement:

$$\mathbf{r}_i' = \mathbf{r}_i + \delta \cdot \boldsymbol{\xi}, \quad \boldsymbol{\xi} \sim \mathcal{N}(0, I_2)$$

where $\delta$ is the step size. The move is accepted with probability
$\min(1, e^{-\beta \Delta U})$. Periodic boundary conditions are applied
via minimum image convention.

One **sweep** = $N$ move attempts (every particle is attempted once on
average).

### Step-Size Calibration

The step size $\delta$ is calibrated independently for each
$(T^*, \text{init type})$ combination before production. The procedure:

1. Start with $\delta = 1.0$
2. Run 2000 calibration sweeps
3. Measure the acceptance rate across all chains
4. If acceptance is outside 30–40%, rescale: $\delta \leftarrow \delta \times (\text{acc} / 0.35)$
5. Repeat up to 20 rounds until acceptance is in [0.30, 0.40]
6. Clamp $\delta \in [0.01, 5.0]$

Gas-init and drop-init chains have separate $\delta$ values because the
local energy landscape differs.

### Production Run Parameters

| Parameter | Value |
|-----------|-------|
| Total sweeps | 100,000 |
| Equilibration | **None** (this is a diagnostic) |
| Energy sampling interval | Every 10 sweeps |
| Cluster OP sampling interval | Every 1000 sweeps (= 1 chunk) |
| Snapshot interval | Every 5000 sweeps |
| Independent chains per init type | 5 |
| Temperatures | $T^* = 0.350$ to $0.450$, step $0.005$ (21 values) |
| Total energy samples per chain | 10,000 |
| Total cluster OP samples per chain | 100 |
| Total snapshots per chain | 21 (including initial config) |

**No equilibration is discarded.** This is deliberate — the point of this
diagnostic is to watch the chains relax from their initial configurations
and observe where gas-init and drop-init trajectories merge (indicating
the system has equilibrated and the two basins are indistinguishable).

### Chunking

Production is divided into 100 chunks of 1000 sweeps each. Within each
chunk, a `jax.lax.scan` loop executes $N \times 1000$ individual move
attempts and returns 100 energy samples (one every 10 sweeps). Cluster
OP is measured once at the end of each chunk from the final configuration.

### Random Seeds

Base seed = 99. Each temperature and chain gets a deterministic sub-key
via `jax.random.split`.

---

## 4. Initial Configurations

### Gas Init

Random uniform placement with excluded volume. Particles are placed
sequentially; if a new particle overlaps with any existing particle
(distance $< 0.8\sigma$), the position is resampled. This produces a
spatially uniform gas with no clustering.

### Drop Init (Hexagonal Lattice)

A compact 2D hexagonal lattice with nearest-neighbour spacing
$a = 2^{1/6}\sigma \approx 1.122\sigma$ (the LJ potential minimum).
The cluster is centred in the box. This represents a well-formed liquid
droplet — all particles are within bonding distance of their neighbours
and the cluster OP is close to 1.

---

## 5. Cluster Order Parameter

The cluster order parameter (OP) is the fraction of particles in the
largest connected cluster:

$$\text{OP} = \frac{n_\text{largest}}{N}$$

Two particles are "bonded" if their minimum-image distance is less than
$r_\text{cluster} = 1.5\sigma$.

> **Important:** $r_\text{cluster} = 1.5\sigma$ is completely independent
> of the LJ potential cutoff $r_\text{cut} = 2.5\sigma$. The cluster
> cutoff is a geometric criterion for what counts as a "connected"
> particle; the potential cutoff determines the energy calculation.

Connected components are found using `scipy.sparse.csgraph.connected_components`.

- OP $\approx 1$: most particles form a single cluster (droplet phase)
- OP $\approx 1/N$: all particles are isolated (dilute gas phase)

---

## 6. How to Read the Plots

### Interactive Viewers

Each temperature has an interactive HTML viewer with two linked panels:

**Top panel — Energy time series (Canvas plot):**
- **Blue line:** mean $E/N$ across the 5 gas-init chains
- **Red line:** mean $E/N$ across the 5 drop-init chains
- **Shaded bands:** $\pm 1$ standard deviation across chains
- **x-axis:** sweep number (0 to 100K)
- **y-axis:** energy per particle $E/N$
- The data is downsampled by a factor of 5 (from 10,000 to 2,000 points)
  for rendering performance

**Bottom panel — Particle snapshots:**
- Shows configuration snapshots linked to the cursor position on the
  energy plot
- **Top row:** gas-init chains (5 panels, one per chain)
- **Bottom row:** drop-init chains (5 panels)
- **Red particles:** members of the largest cluster
- **Grey particles:** not in the largest cluster
- **Number in corner:** cluster OP value for that snapshot
- Snapshots are taken every 5000 sweeps (21 frames total including init)

**Controls:**
- Hover over the energy plot to scrub through snapshots
- Click to lock the cursor at a sweep position
- Arrow keys step the locked cursor
- Click again to unlock

### Static Energy Plots (`N{N}_energy_T0xx_T0xx.png`)

One subplot per temperature. Each subplot shows:
- **Blue line + band:** gas-init chain mean $E/N$ $\pm\,1\sigma$ (across 5 chains)
- **Red line + band:** drop-init chain mean $E/N$ $\pm\,1\sigma$ (across 5 chains)
- The data is smoothed with a running mean of window 20 samples
  (= 200 sweeps)
- x-axis: sweep number
- Annotated with calibrated step sizes ($\delta_\text{gas}$,
  $\delta_\text{drop}$)

### Static Cluster OP Plots (`N{N}_clusterop_T0xx_T0xx.png`)

Same layout as energy plots but with a dual y-axis:
- **Left axis (solid lines):** smoothed $E/N$ (running mean, window 20)
- **Right axis (dashed lines):** smoothed cluster OP (running mean, window 5 chunks = 5000 sweeps)
- Shaded bands are $\pm\,1\sigma$ across chains in both cases

### Hysteresis Summary (`N{N}_hysteresis_10K_50K.png`)

Three-panel summary computed from sweep 10,000 to 50,000 (discarding the
first 10K sweeps as burn-in and the last 50K to avoid late-time artefacts):

**Panel 1 — Mean energy vs temperature:**
- Blue circles: gas-init $\langle E/N \rangle$
- Red circles: drop-init $\langle E/N \rangle$
- Shaded bands: $\pm\,1$ standard deviation
- The std here is computed over **all samples from all chains** in the
  10K–50K window (i.e. pooled), not the standard error of the mean

**Panel 2 — Droplet fraction vs temperature:**
- $f_\text{drop}$: fraction of cluster OP samples with $\text{OP} > 0.5$
- Blue: gas-init chains
- Red: drop-init chains
- When both curves meet, the system has lost memory of its initial
  configuration at that temperature

**Panel 3 — Log-odds ratio $\log_{10} R_\text{op}$:**
- $R_\text{op} = (1 - f_\text{pool}) / f_\text{pool}$, where
  $f_\text{pool} = (f_\text{drop,gas} + f_\text{drop,drop}) / 2$
- Pooled across gas-init and drop-init chains
- $R_\text{op} = 1$ ($\log_{10} R_\text{op} = 0$) is the coexistence
  criterion
- The horizontal dashed line marks $\log_{10} R_\text{op} = 0$

### Snapshot GIFs

Animated GIFs cycling through the 21 snapshots (init + 20 production
frames at 5K-sweep intervals). Same colouring as the interactive viewers:
red = largest cluster, grey = other particles. OP value shown in the
corner of each panel.

---

## 7. Error Bars and Uncertainties

All shaded bands and "$\pm$" ranges in these plots are
**$\pm\,1$ standard deviation across the 5 independent chains**, not
standard errors of the mean. To convert to a standard error:

$$\text{SE} = \frac{\sigma}{\sqrt{n_\text{chains}}} = \frac{\sigma}{\sqrt{5}} \approx 0.45\,\sigma$$

The standard deviation is the more informative quantity here because
it shows the **spread of individual chain trajectories** — which is
the physically interesting signal. A chain that nucleates a droplet
will have markedly different energy from one that remains gaseous,
and we want to see that spread, not suppress it.

For the interactive viewers specifically, the std is computed pointwise
(at each energy sample) across 5 chains, then downsampled by averaging
groups of 5 consecutive points.

For the static energy and cluster OP plots, a running mean (window = 20
energy samples = 200 sweeps for energy; window = 5 chunks = 5000 sweeps
for OP) is applied **per chain first**, then the mean and std are computed
across smoothed chains.

---

## 8. Temperature Grid

21 temperatures from $T^* = 0.350$ to $T^* = 0.450$ in steps of 0.005:

$$T^* \in \{0.350, 0.355, 0.360, \ldots, 0.445, 0.450\}$$

This range brackets the known dew point $T^*_\text{dew} = 0.405 \pm 0.002$.
Below $T^*_\text{dew}$, the droplet phase is thermodynamically favoured;
above it, the gas phase is favoured. The diagnostic shows this as
hysteresis: at cold temperatures, gas-init chains eventually nucleate
droplets and converge to the drop-init energy, while at hot temperatures,
drop-init chains evaporate and converge to the gas-init energy.

---

## 9. Software

- **JAX** for GPU-accelerated MCMC (all pair energies vectorised,
  `jax.lax.scan` for the sweep loop)
- **SciPy** for connected-components analysis (cluster OP)
- **Matplotlib** for static plots
- **PIL/Pillow** for GIF generation
- Interactive viewers are self-contained HTML/JavaScript with embedded
  base64-encoded snapshot images and JSON energy data
