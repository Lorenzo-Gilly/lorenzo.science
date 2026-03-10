---
title: "Experimental Setup & Plot Guide"
weight: 2
---

{{< katex />}}

# Experimental Setup & Plot Guide

This page documents how the Stage A diagnostic was run and how every
plot and viewer was generated. If something on a graph looks odd, the
answer is probably here.

---

## 1. Physical Model

| Parameter | Value |
|-----------|-------|
| Model | 2D Lennard-Jones |
| Energy scale | $\varepsilon = 1$ |
| Length scale | $\sigma = 1$ |
| Potential cutoff | $r_\text{cut} = 2.5\,\sigma$ |
| Potential shift | Yes (continuous at cutoff) |
| Long-range corrections | No |
| Number density | $\rho^* = 0.08$ |
| Box side | $L = \sqrt{N / \rho^*}$ |

The pair potential is the standard Lennard-Jones form, shifted to zero at
the cutoff distance:

$$U(r) = \begin{cases} 4\varepsilon\!\left[\left(\frac{\sigma}{r}\right)^{\!12} - \left(\frac{\sigma}{r}\right)^{\!6}\right] - U(r_\text{cut}) & r < r_\text{cut} \\\\ 0 & r \geq r_\text{cut}\end{cases}$$

The density $\rho^* = 0.08$ places the system in the low-density gas branch
of the coexistence curve. The goal is to find $T^*_\text{dew}$ — the
temperature below which a metastable droplet phase becomes thermodynamically
competitive with the gas.

---

## 2. System Sizes

| Particles $N$ | Box side $L / \sigma$ |
|---------------|-----------------------|
| 32            | 20.0                  |
| 64            | 28.3                  |
| 128           | 40.0                  |

---

## 3. MCMC Details

### Algorithm

Single-particle Metropolis Monte Carlo. Each move attempt picks one particle
uniformly at random and proposes a Gaussian displacement:

$$\mathbf{r}_i' = \mathbf{r}_i + \delta \cdot \boldsymbol{\xi}, \quad \boldsymbol{\xi} \sim \mathcal{N}(0, I_2)$$

where $\delta$ is the step size. The move is accepted with probability
$\min\!\big(1,\, e^{-\beta \Delta U}\big)$ where $\beta = 1/T^*$.
Periodic boundary conditions are applied via the minimum image convention.

One **sweep** = $N$ move attempts (on average, every particle is attempted once).

### Step-Size Calibration

The step size $\delta$ is calibrated independently for each
$(T^*,\, \text{init type})$ combination before production:

1. Start with $\delta = 1.0$
2. Run 2000 calibration sweeps across all 5 chains
3. Measure the acceptance rate (fraction of proposed moves accepted)
4. If acceptance is outside 30–40%, rescale: $\delta \leftarrow \delta \times (\text{acc} / 0.35)$
5. Repeat up to 20 rounds until acceptance falls within [0.30, 0.40]
6. Clamp $\delta \in [0.01, 5.0]$

Gas-initialised and droplet-initialised chains get separate $\delta$ values
because the local energy landscape differs (compact droplet vs dilute gas).

### Production Run Parameters

| Parameter | Value |
|-----------|-------|
| Total sweeps per chain | 100,000 |
| Equilibration discarded | None (this is a diagnostic) |
| Energy sampling interval | Every 10 sweeps |
| Order parameter sampling interval | Every 1000 sweeps |
| Snapshot interval | Every 5000 sweeps |
| Independent chains per init type | 5 |
| Temperature grid | $T^* = 0.350, 0.355, \ldots, 0.450$ (21 values) |
| Total energy samples per chain | 10,000 |
| Total order parameter samples per chain | 100 |
| Total snapshots per chain | 21 (including initial configuration) |

**No equilibration is discarded.** This is deliberate — the purpose of this
diagnostic is to watch the chains relax from their starting configurations
and identify where gas-initialised and droplet-initialised trajectories
merge. This merging indicates that the system has equilibrated and the
initial state is no longer relevant.

### Chunking

Production is divided into 100 chunks of 1000 sweeps each. Within each
chunk, $N \times 1000$ individual move attempts are executed via a compiled
JAX scan loop, yielding 100 energy samples (one every 10 sweeps). The cluster
order parameter is measured once at the end of each chunk from the final
particle configuration.

### Random Seeds

Base seed = 99. Each temperature and chain receives a deterministic sub-key
via JAX's splittable PRNG.

---

## 4. Initial Configurations

### Gas-Initialised Chains

Particles are placed one by one at random positions in the simulation box.
If a new particle would overlap with any existing particle (centre-to-centre
distance $< 0.8\,\sigma$), the position is resampled. This produces a
spatially uniform gas with no clustering.

### Droplet-Initialised Chains

A compact 2D hexagonal lattice with nearest-neighbour spacing
$a = 2^{1/6}\sigma \approx 1.122\,\sigma$ (the distance at which the LJ
potential has its minimum). The cluster is centred in the simulation box.
This represents a well-formed liquid droplet — all particles are within
bonding distance of their neighbours and the order parameter is close to 1.

---

## 5. Cluster Order Parameter

The **cluster order parameter** measures how much of the system forms a
single connected droplet. It is defined as the fraction of particles in
the largest connected cluster:

$$\text{OP} = \frac{n_\text{largest}}{N}$$

Two particles are considered "bonded" (connected) if their minimum-image
distance is less than $r_\text{cluster} = 1.5\,\sigma$.

> **Note:** The cluster bond cutoff $r_\text{cluster} = 1.5\,\sigma$ is
> completely independent of the LJ potential cutoff $r_\text{cut} = 2.5\,\sigma$.
> The former is a geometric criterion for defining connectivity between
> particles; the latter determines the range of the energy calculation.

Connected components are found via sparse graph analysis
(`scipy.sparse.csgraph.connected_components`).

Interpretation:
- $\text{OP} \approx 1$: most particles form a single cluster (**droplet phase**)
- $\text{OP} \approx 1/N$: all particles are isolated (**dilute gas phase**)
- $\text{OP} = 0.5$: used as the threshold for classifying a configuration
  as "droplet-like" in the hysteresis summary

---

## 6. How to Read the Plots

### Interactive Viewers

Each temperature has an interactive HTML viewer with two linked panels:

**Top panel — Energy time series:**
- **Blue line:** mean energy per particle $E/N$ across the 5 gas-initialised chains
- **Red line:** mean $E/N$ across the 5 droplet-initialised chains
- **Shaded bands:** $\pm 1$ standard deviation across chains (see [Section 7](#7-error-bars-and-uncertainties))
- **x-axis:** sweep number (0 to 100K)
- **y-axis:** energy per particle $E/N$ (in units of $\varepsilon$)
- The raw data (10,000 samples) is downsampled by a factor of 5 (to 2,000
  points) for rendering performance

**Bottom panel — Particle snapshots:**
- Shows configuration snapshots linked to the cursor position on the
  energy plot above
- **Top row:** gas-initialised chains (5 panels, one per chain)
- **Bottom row:** droplet-initialised chains (5 panels)
- **Red particles:** members of the largest connected cluster
- **Grey particles:** not in the largest cluster
- **Number in corner:** cluster order parameter for that snapshot
- Snapshots are captured every 5000 sweeps (21 frames including the
  initial configuration)

**Controls:**
- Hover over the energy plot to scrub through snapshots
- Click to lock the cursor at a specific sweep
- Arrow keys step the locked cursor forward/backward
- Click again to unlock

### Static Energy Plots

One subplot per temperature. Each subplot shows:
- **Blue line + band:** gas-initialised mean $E/N$ $\pm 1\sigma$ (across 5 chains)
- **Red line + band:** droplet-initialised mean $E/N$ $\pm 1\sigma$
- Data smoothed with a running mean of window 20 samples (= 200 sweeps)
- x-axis: sweep number
- Annotated with calibrated step sizes ($\delta_\text{gas}$,
  $\delta_\text{drop}$)

### Static Energy + Order Parameter Plots

Same layout as the energy plots but with a dual y-axis:
- **Left axis (solid lines):** smoothed $E/N$ (running mean, window = 200 sweeps)
- **Right axis (dashed lines):** smoothed cluster order parameter (running mean, window = 5000 sweeps)
- Shaded bands show $\pm 1\sigma$ across chains in both cases
- Horizontal dotted line at $\text{OP} = 0.5$ marks the droplet classification threshold

### Hysteresis Summary

Three-panel summary computed over the sweep window 10,000–50,000 (discarding
the first 10K sweeps as burn-in):

**Panel 1 — Energy per particle vs temperature:**
- Blue circles: gas-initialised $\langle E/N \rangle$
- Red circles: droplet-initialised $\langle E/N \rangle$
- Shaded bands: $\pm 1$ standard deviation (pooled over all samples and chains)
- Where the two curves merge, the energy gap has closed

**Panel 2 — Droplet fraction vs temperature:**
- The droplet fraction $f_\text{drop}$ is the fraction of order parameter
  samples exceeding 0.5 (i.e. the fraction of time spent in a droplet-like
  state)
- Blue: gas-initialised chains
- Red: droplet-initialised chains
- When both curves meet at the same value, the system has lost memory of its
  initial configuration — this is the transition temperature

**Panel 3 — Phase ratio $R_\text{op}$:**
- Defined as $R_\text{op} = (1 - f_\text{pool}) / f_\text{pool}$
- $f_\text{pool}$ is the droplet fraction averaged across both initialisation
  types
- $R_\text{op} > 1$ (green shading): gas-dominated regime (above $T^*_\text{dew}$)
- $R_\text{op} < 1$ (red shading): droplet-dominated regime (below $T^*_\text{dew}$)
- $R_\text{op} = 1$ ($\log_{10} R_\text{op} = 0$, horizontal line): the
  dew point, where gas and droplet phases are equally probable

### Snapshot GIFs

Animated GIFs cycling through the 21 snapshots (initial configuration + 20
production frames at 5000-sweep intervals). Red particles belong to the
largest cluster; grey particles do not. The cluster order parameter is shown
in the corner of each panel.

---

## 7. Error Bars and Uncertainties

All shaded bands and "$\pm$" ranges in these plots are
**$\pm 1$ standard deviation across the 5 independent chains**, not
standard errors of the mean. To convert to a standard error:

$$\text{SE} = \frac{\sigma}{\sqrt{n_\text{chains}}} = \frac{\sigma}{\sqrt{5}} \approx 0.45\,\sigma$$

The standard deviation is the more informative quantity here because
it shows the **spread of individual chain trajectories** — which is
the physically interesting signal. A chain that nucleates a droplet
will have markedly different energy from one that remains gaseous,
and we want to see that spread rather than suppress it.

**Interactive viewers:** the standard deviation is computed pointwise (at each
energy sample) across 5 chains, then downsampled by averaging groups of 5
consecutive points.

**Static energy and order parameter plots:** a running mean is applied
**per chain first** (window = 200 sweeps for energy; 5000 sweeps for order
parameter), then the mean and std are computed across the smoothed chains.

**Hysteresis summary:** the standard deviation is computed over all samples
from all chains within the 10K–50K sweep window (pooled).

---

## 8. Temperature Grid

21 temperatures from $T^* = 0.350$ to $T^* = 0.450$ in steps of 0.005:

$$T^* \in \{0.350,\; 0.355,\; 0.360,\; \ldots,\; 0.445,\; 0.450\}$$

This range is chosen to bracket the expected dew point $T^*_\text{dew}$.
Below $T^*_\text{dew}$, the droplet phase is thermodynamically favoured;
above it, the gas phase is favoured. The diagnostic shows this as
hysteresis: at cold temperatures, gas-initialised chains eventually nucleate
droplets and converge toward the droplet-initialised energy, while at hot
temperatures, droplet-initialised chains evaporate and converge toward the
gas-initialised energy.

---

## 9. Software

- **JAX** — GPU-accelerated MCMC (vectorised pair energies, compiled scan loops)
- **SciPy** — connected-components analysis for the cluster order parameter
- **Matplotlib** — static plots
- **Pillow** — GIF generation
- Interactive viewers are self-contained HTML/JavaScript with embedded
  base64-encoded snapshot images and JSON energy data
