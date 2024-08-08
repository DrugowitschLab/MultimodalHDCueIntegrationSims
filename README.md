# Code for Multimodal Head Direction Cue Integration

This repository contains the code for network simulations and figure generation in

Melanie A. Basnak, Anna Kutschireiter, Tatsuo S. Okubo, Albert Chen, Pavel Gorelik, Jan Drugowitsch, and Rachel I. Wilson (2024). Multimodal cue integration and learning in a neural representation of head direction. *Nature Neuroscience*.

Link to simulated data files on Figshare: https://doi.org/10.6084/m9.figshare.26510239

## Installation: conda environment set-up instructions

This code uses Python 3.9.16. A list of dependencies can be found in `environment.yml`. To recreate the conda environment, run

    conda env create -f environment.yml

To activate this conda environment, run

    conda activate venv-basnak-etal-2022

## Overview of repository

The repository contains the Python script `ra_network.py` and the following six Jupyter notebooks:
* `fig1_ER_to_EPG_weights.ipynb` generates Figure 1c
* `fig2_changing_cue_intensity.ipynb` generates Figure 2h,i
* `fig3_individual_variation_in_weights.ipynb` generates Figure 3d,f
* `fig4_cue_shift.ipynb` generates Figure 4f,g
* `fig5_cue_combination.ipynb` generates Figure 5f,g
* `fig7_inverted_gain_learning.ipynb` generates Figure 7g,h

The file `ra_network.py` contains `class RingAttractorNetwork` for simulating the ring attractor network, including class methods and functions for analyzing its properties and dynamics, as well as helper functions for generating ground truth angular velocity and head direction trajectories and inputs to the ring attractor network. Additional functions specific to simulations and analyses for individual figures can be found in the notebooks corresponding to the figures. With the exception of Figure 3d,f, `ra_network.py` is used in all of the notebooks.

At the top of each notebook (in the second cell), please specify whether you would like to save your simulated data and/or figure(s) locally, your local directory to which you would like to save your simulated data and/or figure(s), and your file names.

## Load data on file vs. simulate new data

For Figures 1c, 2h, 4f,g, 5f, and 7g, you have the option to either load in existing data or simulate new data on which further analyses will be performed. Choose your option by setting `have_data_file` at the top of the notebook (in the second cell) to either `True` (load existing data) or `False` (simulate new data). By default, new data will be simulated from scratch, i.e. `have_data_file = False`. 

If you plan to use existing data rather than simulate data from scratch, be sure to download the appropriate data file(s) from [Figshare](https://doi.org/10.6084/m9.figshare.26510239 "Basnak et al., network simulation data"):
* `data-ER-EPG-weights_gamma-decay=17_2022-211-30_02.npz` contains data for Figure 1c
* `data-varying-cue-intensity_2022-11-30.npz` contains data for Figure 2h, 3d,f
* `data-cue-shift_2022-12-07.npz` contains data for Figure 4f,g
* `data-cue-combination_2022-11-30.npz` contains data for Figure 5f
* `data-inverted-gain_2022-11-30.npz` contains data for Figure 7g

 Save the data file(s) to your local directory. In each notebook where you want to use existing data, set `have_data_file = True` and set `data_path` to your local directory where you saved the data file.

Note that Figure 3d,f resamples simulated data for Figure 2h. Therefore, set `data_path` in `fig3_individual_variation_in_weights.ipynb` to your local directory where simulated data (whether it is new data or `data-varying-cue-intensity_2022-11-30.npz`) for Figure 2h is saved.

## Details for individual code files

For each code file below, we refer you to the relevant section in the paper and provide any additional usage information and details about numerical implementation. Note that all lengths of time mentioned below are in simulation time rather than experiment time (24s of experiment time corresponds to 1s of simulation time).

### `ra_network.py`

Relevant section in the paper: Methods - Network model

Note that we numerically implemented the learning rule described in the paper using a different parametrization. Recall that the learning is described as follows in the paper:

$$\frac{dW_{k,nm}}{dt} = \eta|v(t)|f_{n}\Bigl(w_\textrm{max} - W_{k,nm}(t)\Bigr) - \eta|v(t)|f_{n}w_\textrm{max}\frac{g_{k,m}(t)}{g_0}$$

where the first term represents non-associative LTP, and the second term represents associative LTD. We re-expressed this learning rule as

$$\frac{dW_{k,nm}}{dt} = \eta|v(t)|\Bigl(-\gamma_\textrm{Hebb}f_ng_{k,m}(t) + \gamma_\textrm{postboost}f_n - \gamma_\textrm{decay}W_{k,nm}(t)f_n\Bigr)$$

where the first term represents inhibitory Hebbian plasticity, the second term represents a post-synaptically gated activity boost, and the third term represents post-synaptically gated weight decay. The re-parametrization is given by $w_\textrm{max} = \frac{\gamma_\textrm{postboost}}{\gamma_\textrm{decay}}, g_0 = \frac{\gamma_\textrm{decay}}{\gamma_\textrm{Hebb}}$ and absorbing $\frac{1}{\gamma_\textrm{decay}}$ into the learning rate $\eta$. The learning rate in the re-expressed learning rule is $\eta = 0.02$; in the simulations, however, we set `eta = 5e-5` because `eta` implicitly includes the step size `dt = 0.0025`, i.e. $\eta$ $\times$ `dt` $=$ `eta`. The learning rule parameters $\gamma_\textrm{Hebb}, \gamma_\textrm{postboost}, \gamma_\textrm{decay}$ correspond to the variables `gamma_Hebb`, `gamma_postboost`, `gamma_decay`, respectively, in the code files. We set `gamma_Hebb = 1`, `gamma_postboost = 1`, `gamma_decay = 17`.

### `fig1_ER_to_EPG_weights.ipynb`

 This notebook simulates a ring attractor network with two external inputs (visual + wind) that are aligned and equal in strength for 240s, and visualizes the final visual ER $\rightarrow$ EPG weights and wind ER $\rightarrow$ EPG weights as heat maps.

### `fig2_changing_cue_intensity.ipynb`

Relevant section in the paper: "Impact of cue intensity on bump parameters, and across-individual variability (Figs. 2h,i & 3d,f)" under Methods - Network model

For Figure 2i, we simulated a ring attractor network with a single external input with zero, low, or high cue intensity for 150s each. The network was set to the same initial state for each cue intensity and the external inputs were identical (i.e. identical ground truth angular velocity and head direction trajectories) except for the cue intensity. We visualized the final ER activity, EPG activity, and ER $\rightarrow$ EPG weights.

### `fig3_individual_variation_in_weights.ipynb`

Relevant section in the paper: "Impact of cue intensity on bump parameters, and across-individual variability (Figs. 2h,i & 3d,f)" under Methods - Network model

For Figure 3d,f, there is the option to run a subsampling analysis, in which we subsampled the data in Figure 3d,f such that the size of each subsample matches the number of flies in the experiment (`n_flies = 15`). We created `n_subsamples = 1e8` subsamples and, for each subsample, we regressed bump width or amplitude onto HD encoding accuracy. We then calculated the average p-value across all subsamples and the fraction of p-values that are significant (we set the alpha level at 0.05). To rerun this subsampling analysis, set `run_subsampling_analysis = True` in the notebook. By default, this subsampling analysis will not be run, i.e. `run_subsampling_analysis = False`.

### `fig4_cue_shift.ipynb`

Relevant section in the paper: "Impact of individual variations in experienced cue intensity on cue weighting (Fig. 4f,g)" under Methods - Network model

In each simulation in this notebook, the cue of one modality is presented before the cue of the other modality; the ER amplitude for one modality is fixed while the ER amplitude for the other modality is varied. This creates four conditions:
1. Visual cue is presented first; visual ER amplitude is fixed
2. Visual cue is presented first; wind ER amplitude is fixed
3. Wind cue is presented first; visual ER amplitude is fixed
4. Wind cue is presented first; wind ER amplitude is fixed

Therefore, the total number of simulations `n_sims` has to be a multiple of four for the conditions to be balanced across simulations. This is achieved by having the cue with fixed ER amplitude be presented first in the first half of all simulations *and* assigning the cue with varied ER amplitudes to be the wind cue on the simulations indexed by even integers.

### `fig5_cue_combination.ipynb`

Relevant section in the paper: "Temporal evolution of bump parameters during cue combination (Fig. 5f,g)" under Methods - Network model

### `fig7_inverted_gain_learning.ipynb`

Relevant section in the paper: "Impact of variation in ER amplitude on remapping during inverted gain (Fig. 7g,h)" under Methods - Network model

For Figure 7g, either the normalized or the unnormalized HD encoding accuracy across time is visualized in the top panel. The normalized HD encoding accuracy is obtained by dividing the unnormalized HD encoding accuracy by the steady-state HD encoding accuracy. The steady-state HD encoding accuracy is the mean HD encoding accuracy during normal gain. By default, the normalized HD encoding accuracy is plotted, i.e. `plot_normalized_HD_encoding_accuracy = True`. To plot the unnormalized HD encoding accuracy, set `plot_normalized_HD_encoding_accuracy = False`.