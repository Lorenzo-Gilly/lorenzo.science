---
title: "Stage A — Hysteresis Diagnostic"
weight: 1
bookFlatSection: false
bookCollapseSection: false
---

{{< katex />}}

# Stage A — Hysteresis Diagnostic

No-equilibration MCMC diagnostic comparing gas-initialised and droplet-initialised
chains across temperatures $T^* = 0.350$–$0.450$ (step 0.005). 5 independent
chains per initialisation type, 100K sweeps, $N = 32, 64, 128$.

See the **[Experimental Setup & Plot Guide](methods/)** for full details on MCMC
parameters, initialisation protocols, what the shaded bands mean, and how
to read every plot on this page.

---

## Interactive Viewers

Each viewer shows the energy time series (gas vs drop init) with a
click-to-lock cursor linked to particle configuration snapshots below.
Hover over the energy plot to scrub through snapshots in real time.

### N = 32

<iframe src="/lorenzo.science/viewers/stage-a-v5/N32/index.html" width="100%" height="700px"
  style="border: 1px solid #333; border-radius: 6px; background: #1a1a2e;"></iframe>

[Open full-page N=32 viewer](/lorenzo.science/viewers/stage-a-v5/N32/index.html)

---

### N = 64

<iframe src="/lorenzo.science/viewers/stage-a-v5/N64/index.html" width="100%" height="700px"
  style="border: 1px solid #333; border-radius: 6px; background: #1a1a2e;"></iframe>

[Open full-page N=64 viewer](/lorenzo.science/viewers/stage-a-v5/N64/index.html)

---

### N = 128

<iframe src="/lorenzo.science/viewers/stage-a-v5/N128/index.html" width="100%" height="700px"
  style="border: 1px solid #333; border-radius: 6px; background: #1a1a2e;"></iframe>

[Open full-page N=128 viewer](/lorenzo.science/viewers/stage-a-v5/N128/index.html)

---

## Static Plots

### Hysteresis Summary

The hysteresis plots show the mean energy gap $\Delta E = E_\text{gas} - E_\text{drop}$
as a function of $T^*$, averaged over sweeps 10K–50K. The gap vanishes at
$T^* \approx 0.405$, consistent with $T^*_\text{dew} = 0.405 \pm 0.002$.

#### N = 32
![N=32 Hysteresis](/lorenzo.science/viewers/stage-a-v5/N32/N32_hysteresis_10K_50K.png)

| Energy (cold) | Energy (hot) |
|---------------|-------------|
| ![](/lorenzo.science/viewers/stage-a-v5/N32/N32_energy_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N32/N32_energy_T040_T045.png) |

| Cluster OP (cold) | Cluster OP (hot) |
|-------------------|-----------------|
| ![](/lorenzo.science/viewers/stage-a-v5/N32/N32_clusterop_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N32/N32_clusterop_T040_T045.png) |

#### N = 64
![N=64 Hysteresis](/lorenzo.science/viewers/stage-a-v5/N64/N64_hysteresis_10K_50K.png)

| Energy (cold) | Energy (hot) |
|---------------|-------------|
| ![](/lorenzo.science/viewers/stage-a-v5/N64/N64_energy_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N64/N64_energy_T040_T045.png) |

| Cluster OP (cold) | Cluster OP (hot) |
|-------------------|-----------------|
| ![](/lorenzo.science/viewers/stage-a-v5/N64/N64_clusterop_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N64/N64_clusterop_T040_T045.png) |

#### N = 128
![N=128 Hysteresis](/lorenzo.science/viewers/stage-a-v5/N128/N128_hysteresis_10K_50K.png)

| Energy (cold) | Energy (hot) |
|---------------|-------------|
| ![](/lorenzo.science/viewers/stage-a-v5/N128/N128_energy_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N128/N128_energy_T040_T045.png) |

| Cluster OP (cold) | Cluster OP (hot) |
|-------------------|-----------------|
| ![](/lorenzo.science/viewers/stage-a-v5/N128/N128_clusterop_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N128/N128_clusterop_T040_T045.png) |
