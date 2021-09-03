# A deep generative model for probabilistic energy forecasting in power systems: normalizing flows
Official implementation of generative models to compute scenario of renewable generation and consumption on the GEFcom2014 open dataset presented in the paper:
> Dumas, Jonathan, et al. "A deep generative model for probabilistic energy forecasting in power systems: normalizing flows." arXiv preprint arXiv:2106.09370 (2021).
> [[arxiv]](https://arxiv.org/abs/2106.09370)

Note: this paper is under review for Applied Energy.

## Cite

If you make use of this code, please cite our arXiv paper:

```
@article{dumas2021deep,
  title={A deep generative model for probabilistic energy forecasting in power systems: normalizing flows},
  author={Dumas, Jonathan and Lanaspeze, Antoine Wehenkel Damien and Corn{\'e}lusse, Bertrand and Sutera, Antonio},
  journal={arXiv preprint arXiv:2106.09370},
  year={2021}
}
```

Note: the reference will be changed if the paper is accepted for publication in Applied Energy

# Framework of the study
![strategy](https://github.com/jonathandumas/generative-models/blob/9549e0c301b448a749660ce716742ff928dc2778/figures/applied-energy-framework.png)

# Numerical experiments of the study
![numerical-experiments](https://github.com/jonathandumas/generative-models/blob/918ba080d82b04f541e2196a803165708f64fb73/figures/numerical-experiments-methodology.png)

# Dependencies
Two libraries are required to implement the normalizing flows models used in the study:
* https://github.com/AWehenkel/Normalizing-Flows -> to access the repositories lib and model
* https://github.com/AWehenkel/UMNN -> to implement the Unconstrained Monotonic Neural Networks normalizing flows

If you make use of the Unconstrained Monotonic Neural Networks code, please cite the paper:

```
@inproceedings{wehenkel2019unconstrained,
  title={Unconstrained monotonic neural networks},
  author={Wehenkel, Antoine and Louppe, Gilles},
  booktitle={Advances in Neural Information Processing Systems},
  pages={1543--1553},
  year={2019}
}
```

Concerning the forecast value assessment: the Python Gurobi library is used to implement the algorithms in Python 3.7, and [Gurobi](https://www.gurobi.com/) 9.0.2 to solve all the optimization problems.


## Data
The GEFcom2014/data folder contains the GEFcom2014 dataset: load, wind, and PV tracks.


## Generative models
The GEFcom2014/models folder contains the generative models in the following folders:
* GAN: generative adversarial networks
* VAE: variational autoencoders
* NFs: normalizing flows (affine and unconstrained monotonic neural networks)
* GC: gaussian copula
* RAND: random

Then, inside each folder, there is a Python file to run the model. For instance, for the NFs:

```bash
nf_autoregressive.py 
```

For each model, the structure of the Python file is similar. 

First, select the track by indicating the track considered:

```bash
tag = 'load'  # pv, wind, load
```

Second, specify the number of scenarios and quantiles to be generated per day: 

```bash
n_s = 100 # number of scenarios
N_q = 99 # number of quantiles
```

The hyper-parameters of the models for each track are already specified but can be modified. By default, they are set to the values of the paper.

Finally, by running the Python file, the model is trained and generates scenarios and quantiles over the learning, validation, and testing set.

Note: the function

```bash
quantiles_and_evaluation(dir_path=dir_path, s_VS=s_VS, s_TEST=s_TEST, N_q=N_q, df_y_VS=df_y_VS, df_y_TEST=df_y_TEST, name=name, ymax_plf=ymax_plf, ylim_crps=ylim_crps, tag=tag, nb_zones=nb_zones)
```
allows computing some metrics over the validation and testing sets.

## Forecast quality
The GEFcom2014/forecast_quality folder contains the Python files to evaluate the forecast quality of the generative models. The scenarios to be evaluated are located in the folder GEFcom2014/forecast_quality/scenarios. This folder contains the scenarios per generative model: GAN, GC, NFs, etc. 

For instance, GEFcom2014/forecast_quality/scenarios/gan comprises scenarios over the load, wind, and PV tracks over the testing set.

The file:
```bash
CRPS_QS_all_tracks.py
```
allows computing the CRPS, QS, and reliability diagram metrics for all tracks and models.

The file:
```bash
CRPS_QS_DM_test.py
```
computes DM-statistical test of the CRPS and QS metrics for all models and tracks.

The file:
```bash
ES_VS_metrics.py 
```
computes the ES, VS multivariate metrics for all tracks and models. It also computes the DM-statistical test.

The file:
```bash
scenario_correlations.py
```
computes the correlation matrices between scenarios for a given day.

The file:
```bash
clf_metric.py
```
contains the classifier-based metric. Note: scenarios over the learning set are required to fit the classifier. Due to the size of the files, they are not included in the GEFcom2014/forecast_quality/scenarios folder. They need to be generated and included in this folder.

## Forecast value
The GEFcom2014/forecast_value/ folder contains the Python files to evaluate the forecast value of the generative models. It uses the retailer energy case study to bid optimally on a day-ahead basis by solving a stochastic optimization problem.

The file:
```bash
bidding_retailer.py 
```
computes the day-ahead bids for all models and the corresponding profits.

The parameters:
```bash
nb_s = 50 # number of scenarios considered per stochastic optimization problem
soc_max = 1 # battery storage capacity
dad_price = 100  # euros /MWh
q_pos = 2
q_neg = 2
pos_imb = q_pos * dad_price  # euros /MWh
neg_imb = q_neg * dad_price  # euros /MWh
```
are set to the default values used in the paper.
