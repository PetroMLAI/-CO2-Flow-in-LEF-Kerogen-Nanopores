# Nanopore Flow and CO2-EOR Screening in Lower Eagle Ford Shale
A risk quantified pore-network model for CO2-enhanced oil recovery and CO2 storage potential in Lower Eagle Ford kerogen nanopores, built from real FIB-SEM microscopy of a previously characterized sample.

## Background and sample history
The sample studied here (LEF-Sample A) was previously characterized in my own prior work (Cudjoe et al. 2021): TOC of 5.75 wt.%, calcite-rich mineralogy, and a pore diameter range of 13 to 350 nm. In that earlier work, I built a pore network model in PerGeos and computed permeability values of 0.000146 mD. Full citation: Cudjoe et al. 2021. An integrated workflow to characterize lower Eagle Ford shale for hydrocarbon gas huff-n-puff simulation. Fuel, 289, 119854. https://doi.org/10.1016/j.fuel.2020.119854.

PerGeos' flow solver assumes continuum flow physics and does not account for Knudsen diffusion or adsorption, both of which are expected to matter at kerogen nanopore scale. This project revisits the same sample with physics that specifically correct for what that earlier methodology could not capture, and compares the two results directly. Specifically, this work adds:

- A Knudsen diffusion correction (Beskok-Karniadakis) applied at the individual pore throat level
- CO2-specific competitive adsorption modeling, versus the earlier general gas injection framing
- Monte Carlo uncertainty quantification (200 realizations, reported as P10/P50/P90), versus the earlier single deterministic permeability values
- A machine learning surrogate model identifying which structural properties drive the flow result

The PerGeos-derived permeability values above serve as a validation baseline for this project's own apparent permeability estimates.

## The problem
Most reservoir simulators assume continuum flow physics (Darcy's Law), the same math that describes water through a pipe. That assumption breaks down inside organic (kerogen) nanopores, where pore diameters are small enough that gas transport is dominated by wall collisions (Knudsen diffusion) and surface adsorption rather than viscous flow. Ignoring this leads to systematic errors in both production forecasting and CO2 storage capacity estimates for CCUS projects.

## What this project does
- Segment FIB-SEM images into pore space, kerogen, and mineral matrix, using combined backscatter electron (BSE) and secondary electron (SE) channels to separate pore space from kerogen, since the two look nearly identical in BSE alone.
- Extract pore-throat size and connectivity statistics from the available slices (20 of an original 500, most lost)
- Build a statistically equivalent 3D network matched to those measured statistics, since a complete 3D image volume is not available
- Apply a Knudsen-corrected hydraulic conductance model at the pore-throat level
- Simulate CO2 displacing methane via invasion percolation, with pore geometry adjusted for competitive CO2/CH4 adsorption on kerogen
- Run 200 Monte Carlo realizations to report apparent permeability and recovery factor as P10/P50/P90 ranges
- Train an ML surrogate model on the realization ensemble to identify dominant structural controls on permeability

## Results
Run against the real 20-slice FIB-SEM dataset, 200 Monte Carlo realizations:

- **Apparent permeability:** 0.00092 mD most likely (0.00083 conservative to 0.00101 optimistic)
  
- **Physics-only effect:** correcting for nanopore-scale gas behavior moves permeability 1.10x above plain continuum flow (0.00084 mD without the correction), isolated on the exact same pore network so only the physics varies
  
- **CO2-EOR recovery factor at breakthrough:** 27% most likely (19% conservative to 35% optimistic), a wide spread despite every realization sharing identical bulk statistics, explained only by the specific pore arrangement

- **ML feature importance** path length through the network and Knudsen number, not average pore size, were the strongest predictors of recovery, reproduced consistently across independent runs. Permeability could not be predicted from any of the five structural features tried

- **Cross-validated against an independent real PerGeos characterization of the same sample:** coordination number (1.14 measured vs. 1.22 real), specific surface area (51 vs. 61 um^-1), and permeability.

## Tech stack
Python (scikit-image, PoreSpy, OpenPNM, scikit-learn, CoolProp for CO2 thermodynamic properties).






