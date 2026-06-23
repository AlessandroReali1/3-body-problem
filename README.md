# Simulating the Three-Body Problem

**Course:** 30561 – Stochastic Processes and Simulation in Natural Sciences

**Authors:** Lorenzo Calda, Alessandro Reali, Luca Ricci

---

## Overview

The three-body problem asks a deceptively simple question: given three masses interacting only through Newtonian gravity, how do they move? Unlike the two-body case, it has no general closed-form solution — the system is famously chaotic, with tiny changes in initial conditions producing wildly different long-term trajectories.

This project builds a 2D numerical simulator for the three-body problem from scratch, then uses it to explore two questions:

1. **How much does the choice of numerical integrator matter** when the underlying dynamics are chaotic?
2. **How does the ratio between the three masses shape the qualitative behavior** of the system — chaotic scattering, temporary binaries, or stable hierarchical structures?

Everything — the integrators, the energy/momentum/angular-momentum diagnostics, the Lyapunov exponent estimator, and the animated trajectory plots — is implemented directly with NumPy and Matplotlib, with no external ODE or physics libraries.

## Physical & Mathematical Background

For three bodies with masses $m_1, m_2, m_3$ and position vectors $\vec r_1(t), \vec r_2(t), \vec r_3(t)$, the gravitational force on body $i$ from body $j$ is:

$$
\vec F_{ij} = G \frac{m_i m_j}{|\vec r_j - \vec r_i|^3}(\vec r_j - \vec r_i)
$$

giving the equation of motion:

$$
m_i \frac{d^2 \vec r_i}{dt^2} = \sum_{j \neq i} \vec F_{ij}
$$

The simulation tracks three conserved quantities throughout each run as a sanity check on numerical accuracy:

- **Energy:** $E = \sum_i \frac12 m_i |\vec v_i|^2 - \sum_{i<j} \dfrac{G m_i m_j}{|\vec r_i - \vec r_j|}$
- **Linear momentum:** $\sum_i m_i \vec v_i$
- **Angular momentum:** $\sum_i m_i (\vec r_i \times \vec v_i)$

## Numerical Methods

Three integrators of increasing order are implemented and compared:

| Method   | Order | Local Truncation Error | Function Evaluations / Step | Global Error |
|----------|-------|------------------------|------------------------------|---------------|
| Euler    | 1st   | $O(h^2)$               | 1                            | $O(h)$        |
| Midpoint | 2nd   | $O(h^3)$               | 2                            | $O(h^2)$      |
| RK4      | 4th   | $O(h^5)$               | 4                            | $O(h^4)$      |

**Key finding:** over long simulations, the Euler method visibly diverges from the other two, while Midpoint and RK4 stay close to one another — Midpoint offering a good accuracy/speed trade-off, RK4 the most precise reference solution.

## Repository Contents

```
.
└── 30561_Calda_Reali_Ricci.ipynb   # Full simulation, analysis, and write-up
```

The notebook is organized into the following sections:

1. **Mathematical framework** — equations of motion and conservation laws
2. **Core dynamics functions** — initial condition generators, acceleration computation, energy calculation
3. **Integrators** — Euler, Midpoint, and RK4 implementations
4. **Trajectory computation & animation** — `compute_trajectory`, `trajectory_plot`
5. **Initial exploration** — comparing integrators on a fixed setup; ten randomized trials over mass/position/velocity to scan the space of qualitative behaviors
6. **In-depth analysis tools** — per-body kinetic/potential energy, angular momentum, inter-body distance, Lyapunov exponent estimation, and position–velocity phase-space plots
7. **Mass-ratio experiments** — three controlled runs (Balanced, Moderate Imbalance, Extreme Imbalance) with full diagnostics for each
8. **Results, conclusions, and future directions**

## Key Experiments

Three mass configurations are simulated with the RK4 integrator and analyzed in depth:

| Experiment | Masses | Behavior |
|---|---|---|
| Balanced | (1, 1, 1) | Fully chaotic, non-hierarchical: no dominant attractor, wildly fluctuating inter-body distances, dense and non-repeating phase space |
| Moderate Imbalance | (1, 10, 1) | A quasi-hierarchical pattern emerges: the heavy body acts as a temporary gravitational center, with semi-regular oscillations punctuated by instability |
| Extreme Imbalance | (1, 10, 100) | The two heaviest bodies form a stable binary; the lightest body acts as a chaotic, mostly decoupled perturber undergoing intermittent close encounters |

A clear trend emerges from the Lyapunov exponent estimates: **the more imbalanced the masses, the smaller the Lyapunov exponent** — a dominant mass stabilizes the system, while comparable masses produce stronger chaos.

## Requirements

- Python 3.x
- `numpy`
- `matplotlib`
- Jupyter Notebook / JupyterLab (for `IPython.display`)


## Future Directions

- **Smaller time steps** ($dt = 0.0001$) for higher precision, especially benefiting RK4 over Midpoint
- **Longer integration horizons** to capture late-stage phenomena such as ejections or permanent binary formation
- **Adaptive or symplectic integrators** to better conserve energy and angular momentum during close encounters
- **Broader sampling** over mass ratios and initial conditions to map stability regions statistically
- **Additional physics**, such as drag, tidal forces, or relativistic corrections

## License

This project was developed for academic purposes as part of the course *30561 – Stochastic Processes and Simulation in Natural Sciences*.
