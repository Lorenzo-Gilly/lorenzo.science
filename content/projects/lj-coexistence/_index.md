---
title: "2D LJ Coexistence"
weight: 1
bookFlatSection: false
bookCollapseSection: false
---

{{< katex />}}

# 2D Lennard-Jones Gas-Liquid Coexistence

Calibration of the coexistence parameters for a 2D Lennard-Jones system
($\varepsilon = 1$, $\sigma = 1$, $r_\text{cut} = 2.5\sigma$, shifted potential,
no long-range corrections).

**Goal:** determine $T^*_\text{dew}(\rho^* = 0.08)$ — the dew-point temperature
at low gas density. This is the entry point for the full coexistence calibration
of a normalizing flow model.

## Key Result

$$T^*_\text{dew}(\rho^* = 0.08) = 0.405 \pm 0.002$$

Established by Stage B equal-weight long runs ($N=64$, $N=128$). The finite-size
shift from $N=64$ to $N=128$ is only 0.001 — negligible.

## Experiments

- [**Stage A** — Hysteresis diagnostic with interactive viewers](stage-a-v5/)
- [**Stage B** — Equal-weight long runs for $T^*_\text{dew}$ determination](stage-b/)
