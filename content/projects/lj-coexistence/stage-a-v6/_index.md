---
title: "Stage A v6 — Density Grid Scan"
weight: 2
bookFlatSection: false
bookCollapseSection: false
---

{{< katex />}}

# Stage A v6 — Density Grid Scan

Extension of the Stage A hysteresis diagnostic across a grid of densities.
Instead of a single density $\rho^* = 0.08$, we scan six densities from the
gas branch ($\rho^* = 0.02$) through the onset of the two-phase region
($\rho^* = 0.12$), each at 16 temperatures from $T^* = 0.35$ to $0.50$.

This maps out how the dew-point temperature $T^*_\text{dew}$ depends on
density, tracing the low-density side of the coexistence curve.

**Parameters:** $N = 128$ particles, 6 densities
($\rho^* = 0.02, 0.04, 0.06, 0.08, 0.10, 0.12$), 16 temperatures per density
($T^* = 0.35$–$0.50$, step $0.01$), 5 independent chains per initialisation
type, 100,000 sweeps per chain.

---

## Interactive Viewer

Select a density and temperature to view the energy time series and particle
snapshots. The **timeseries** mode shows $E/N$ vs sweep (blue = gas-init,
red = droplet-init) with hover-linked snapshots. The **snapshot** mode shows
a GIF slider of the chain evolution.

<iframe src="/viewers/stage-a-v6/index.html" width="100%" height="700px"
  scrolling="no" style="border: 1px solid #333; border-radius: 6px; background: #1a1a2e; overflow: hidden;"></iframe>

[Open full-page viewer](/viewers/stage-a-v6/index.html)

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

## Coexistence Summary

Six-panel overview showing the hysteresis gap $\Delta E / N$ (gas-init minus
droplet-init mean energy) as a function of temperature for each density.
The temperature where $\Delta E$ vanishes is the dew-point estimate.

![Coexistence Summary](/viewers/stage-a-v6/coexistence_summary.png)

---

## Per-Density Plots

<details><summary>$\rho^* = 0.02$</summary>

![Hysteresis](/viewers/stage-a-v6/rho0.02/N128_hysteresis_rho0.02_10K_50K.png)

**Energy time series $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.02/N128_energy_T035_T042.png)

**Energy time series $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.02/N128_energy_T043_T050.png)

**Cluster OP $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.02/N128_clusterop_T035_T042.png)

**Cluster OP $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.02/N128_clusterop_T043_T050.png)

</details>

<details><summary>$\rho^* = 0.04$</summary>

![Hysteresis](/viewers/stage-a-v6/rho0.04/N128_hysteresis_rho0.04_10K_50K.png)

**Energy time series $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.04/N128_energy_T035_T042.png)

**Energy time series $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.04/N128_energy_T043_T050.png)

**Cluster OP $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.04/N128_clusterop_T035_T042.png)

**Cluster OP $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.04/N128_clusterop_T043_T050.png)

</details>

<details><summary>$\rho^* = 0.06$</summary>

![Hysteresis](/viewers/stage-a-v6/rho0.06/N128_hysteresis_rho0.06_10K_50K.png)

**Energy time series $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.06/N128_energy_T035_T042.png)

**Energy time series $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.06/N128_energy_T043_T050.png)

**Cluster OP $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.06/N128_clusterop_T035_T042.png)

**Cluster OP $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.06/N128_clusterop_T043_T050.png)

</details>

<details><summary>$\rho^* = 0.08$</summary>

![Hysteresis](/viewers/stage-a-v6/rho0.08/N128_hysteresis_rho0.08_10K_50K.png)

**Energy time series $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.08/N128_energy_T035_T042.png)

**Energy time series $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.08/N128_energy_T043_T050.png)

**Cluster OP $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.08/N128_clusterop_T035_T042.png)

**Cluster OP $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.08/N128_clusterop_T043_T050.png)

</details>

<details><summary>$\rho^* = 0.10$</summary>

![Hysteresis](/viewers/stage-a-v6/rho0.10/N128_hysteresis_rho0.10_10K_50K.png)

**Energy time series $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.10/N128_energy_T035_T042.png)

**Energy time series $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.10/N128_energy_T043_T050.png)

**Cluster OP $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.10/N128_clusterop_T035_T042.png)

**Cluster OP $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.10/N128_clusterop_T043_T050.png)

</details>

<details><summary>$\rho^* = 0.12$</summary>

![Hysteresis](/viewers/stage-a-v6/rho0.12/N128_hysteresis_rho0.12_10K_50K.png)

**Energy time series $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.12/N128_energy_T035_T042.png)

**Energy time series $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.12/N128_energy_T043_T050.png)

**Cluster OP $T^* = 0.35$–$0.42$**
![](/viewers/stage-a-v6/rho0.12/N128_clusterop_T035_T042.png)

**Cluster OP $T^* = 0.43$–$0.50$**
![](/viewers/stage-a-v6/rho0.12/N128_clusterop_T043_T050.png)

</details>
