---
title: "Stage A — Hysteresis Diagnostic"
weight: 1
bookFlatSection: false
bookCollapseSection: false
---

{{< katex />}}

# Stage A — Hysteresis Diagnostic

This diagnostic tests whether the system remembers its initial configuration
at each temperature. We run two sets of Markov chain Monte Carlo (MCMC)
simulations starting from opposite states:

- **Gas-initialised** — particles placed randomly (uniform gas)
- **Droplet-initialised** — particles arranged in a compact hexagonal cluster

If both sets of chains converge to the same energy at a given temperature, the
system has equilibrated and the initial state is forgotten. If they remain
separated, the chains are kinetically trapped — there is a free-energy barrier
between the gas and droplet phases.

The temperature where the two sets of chains first merge is an estimate of
$T^*_\text{dew}$, the dew-point temperature.

**Parameters:** 21 temperatures from $T^* = 0.350$ to $0.450$ (step 0.005),
5 independent chains per initialisation type, 100,000 sweeps per chain,
no equilibration discarded. System sizes $N = 32, 64, 128$ particles.

See the **[Experimental Setup & Plot Guide](methods/)** for MCMC parameters,
step-size calibration, order parameter definition, and how to read each plot.

---

## Interactive Viewers

Each viewer shows the energy per particle $E/N$ as a function of sweep number
(blue = gas-initialised, red = droplet-initialised). The shaded band shows
$\pm 1$ standard deviation across the 5 chains. Hover over the energy plot
to scrub through particle configuration snapshots below, where red particles
belong to the largest connected cluster.

### N = 32

<iframe src="/lorenzo.science/viewers/stage-a-v5/N32/index.html" width="100%" height="600px"
  scrolling="no" style="border: 1px solid #333; border-radius: 6px; background: #1a1a2e; overflow: hidden;"></iframe>

[Open full-page N=32 viewer](/lorenzo.science/viewers/stage-a-v5/N32/index.html)

---

### N = 64

<iframe src="/lorenzo.science/viewers/stage-a-v5/N64/index.html" width="100%" height="600px"
  scrolling="no" style="border: 1px solid #333; border-radius: 6px; background: #1a1a2e; overflow: hidden;"></iframe>

[Open full-page N=64 viewer](/lorenzo.science/viewers/stage-a-v5/N64/index.html)

---

### N = 128

<iframe src="/lorenzo.science/viewers/stage-a-v5/N128/index.html" width="100%" height="600px"
  scrolling="no" style="border: 1px solid #333; border-radius: 6px; background: #1a1a2e; overflow: hidden;"></iframe>

[Open full-page N=128 viewer](/lorenzo.science/viewers/stage-a-v5/N128/index.html)

<script>
window.addEventListener("message", function(e) {
  if (e.data && e.data.type === "contentResize") {
    var iframes = document.querySelectorAll("iframe");
    for (var i = 0; i < iframes.length; i++) {
      if (iframes[i].contentWindow === e.source) {
        iframes[i].style.height = e.data.height + "px";
        break;
      }
    }
  }
});
</script>

---

## Static Plots

### Hysteresis Summary

Three-panel summary averaged over sweeps 10K–50K (discarding early relaxation):

1. **Energy per particle** — gas-init (blue) and droplet-init (red) mean
   $\langle E/N \rangle$ vs temperature. Where the two curves merge, the
   energy gap $\Delta E$ vanishes.
2. **Droplet fraction** — fraction of time the cluster order parameter exceeds
   0.5 (i.e. more than half the particles form a single cluster). When the
   gas-init and droplet-init curves meet, both chain types spend the same
   fraction of time in each phase.
3. **Phase ratio** $R_\text{op}$ — log-scale ratio of gas-like to droplet-like
   time. $R_\text{op} = 1$ (log $R_\text{op} = 0$) marks the dew point.

The data shows the energy gap closing and the droplet fractions merging
around $T^* \approx 0.40$–$0.41$.

#### N = 32
![N=32 Hysteresis](/lorenzo.science/viewers/stage-a-v5/N32/N32_hysteresis_10K_50K.png)

| Energy time series ($T^* = 0.35$–$0.395$) | Energy time series ($T^* = 0.40$–$0.45$) |
|---|---|
| ![](/lorenzo.science/viewers/stage-a-v5/N32/N32_energy_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N32/N32_energy_T040_T045.png) |

| Energy + order parameter ($T^* = 0.35$–$0.395$) | Energy + order parameter ($T^* = 0.40$–$0.45$) |
|---|---|
| ![](/lorenzo.science/viewers/stage-a-v5/N32/N32_clusterop_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N32/N32_clusterop_T040_T045.png) |

#### N = 64
![N=64 Hysteresis](/lorenzo.science/viewers/stage-a-v5/N64/N64_hysteresis_10K_50K.png)

| Energy time series ($T^* = 0.35$–$0.395$) | Energy time series ($T^* = 0.40$–$0.45$) |
|---|---|
| ![](/lorenzo.science/viewers/stage-a-v5/N64/N64_energy_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N64/N64_energy_T040_T045.png) |

| Energy + order parameter ($T^* = 0.35$–$0.395$) | Energy + order parameter ($T^* = 0.40$–$0.45$) |
|---|---|
| ![](/lorenzo.science/viewers/stage-a-v5/N64/N64_clusterop_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N64/N64_clusterop_T040_T045.png) |

#### N = 128
![N=128 Hysteresis](/lorenzo.science/viewers/stage-a-v5/N128/N128_hysteresis_10K_50K.png)

| Energy time series ($T^* = 0.35$–$0.395$) | Energy time series ($T^* = 0.40$–$0.45$) |
|---|---|
| ![](/lorenzo.science/viewers/stage-a-v5/N128/N128_energy_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N128/N128_energy_T040_T045.png) |

| Energy + order parameter ($T^* = 0.35$–$0.395$) | Energy + order parameter ($T^* = 0.40$–$0.45$) |
|---|---|
| ![](/lorenzo.science/viewers/stage-a-v5/N128/N128_clusterop_T035_T039.png) | ![](/lorenzo.science/viewers/stage-a-v5/N128/N128_clusterop_T040_T045.png) |
