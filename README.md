
<!-- README.md is generated from README.Rmd. Please edit that file -->

# stochLAB <img src="man/figures/logo.png" align="right" height="139"/>

<!-- badges: start -->

[![test-coverage](https://github.com/MarineScotlandScience/stochLAB/actions/workflows/test-coverage.yaml/badge.svg)](https://github.com/MarineScotlandScience/stochLAB/actions/workflows/test-coverage.yaml)
[![pkgdown](https://github.com/MarineScotlandScience/stochLAB/actions/workflows/pkgdown.yaml/badge.svg)](https://github.com/MarineScotlandScience/stochLAB/actions/workflows/pkgdown.yaml)
[![R-CMD-check](https://github.com/MarineScotlandScience/stochLAB/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/MarineScotlandScience/stochLAB/actions/workflows/R-CMD-check.yaml)
[![pkgcheck](https://github.com/MarineScotlandScience/stochLAB/actions/workflows/pkgcheck.yaml/badge.svg)](https://github.com/MarineScotlandScience/stochLAB/actions/workflows/pkgcheck.yaml)
[![Status at rOpenSci Software Peer
Review](https://badges.ropensci.org/551_status.svg)](https://github.com/ropensci/software-review/issues/551)

<!-- badges: end -->

`{stochLAB}` is a tool to run Collision Risk Models (CRMs) for seabirds
on offshore wind farms.

## Overview

The `{stochLAB}` package is an adaptation of the [R
code](https://data.marine.gov.scot/dataset/developing-avian-collision-risk-model-incorporate-variability-and-uncertainty-r-code-0)
developed by [Masden
(2015)](https://data.marine.gov.scot/dataset/developing-avian-collision-risk-model-incorporate-variability-and-uncertainty)
to incorporate variability and uncertainty in the avian collision risk
model originally developed by [Band
(2012)](https://www.bto.org/sites/default/files/u28/downloads/Projects/Final_Report_SOSS02_Band1ModelGuidance.pdf).
The package is for use by individuals modelling collision risk of
seabirds at offshore wind farms. The primary functions take input
information on the morphology, behaviour and densities of seabirds as
well data pertaining to the proposed wind farm (i.e., turbine
dimensions, speed and number).

These collision risk models are useful for marine ornithologists who are
working in the offshore wind industry, particularly in UK waters.
However, the package itself relies on generic biological and windfarm
data and can be applied anywhere (i.e., in any marine environment) as
long as the parameters are appropriate for the species and windfarms of
interest.

Code developed under `{stochLAB}` substantially re-factored and
re-structured Masden’s (heavily script-based) implementation into a
user-friendly, streamlined, well documented and easily distributed tool.
Furthermore, the package lays down the code infrastructure for easier
incorporation of new functionality, e.g. extra parameter sampling
features, model expansions, etc.

In addition, previous code underpinning core calculations for the
extended model has been replaced by an alternative approach, resulting
in significant gains in computational speed over Masden’s code. This
optimization is particularly beneficial under a stochastic context, when
core calculations are called repeatedly during simulations.

For a more detailed overview type `?stochLAB`, once installed!

## Installation

You can install the released version of stochLAB from
[CRAN](https://CRAN.R-project.org) with:

``` r
install.packages("stochLAB")
```

You can install the development version with:

``` r
# install.packages("devtools")
devtools::install_github("MarineScotlandScience/stochLAB")
```

This package depends on the following packages, which should be
installed automatically:

`cli dplyr glue logr magrittr msm pracma purrr rlang stats tibble tidyr`

## Bug reports

To report any bugs, please log an
[ISSUE](https://github.com/MarineScotlandScience/stochLAB/issues)

## Input parameters

Many of the input parameters for the `stoch_crm()` function need to be
obtained from developers (e.g., blade pitch, rotor radius, wind speed,
etc…). However, there are many parameters around the morphology and
biology of birds that are built into the `sCRM` package for UK seabirds,
which can be found [HERE](https://github.com/dmpstats/sCRM). `sCRM` is
an R Shiny application that wraps up the `stoch_crm()` and `band_crm()`
functions. These biological parameters can be accessed by installing the
`sCRM` package and running `sCRM::spp_dflts`, which will bring up a
tibble object with all the relevant information.

If performing a collision risk assessment in UK waters, default
biological data for the following parameters can be obtained from the
`sCRM` package:

`flt_speed_pars, body_lt_pars, wing_span_pars, avoid_bsc_pars, avoid_ext_pars, noct_act_pars, flight_type, gen_fhd_boots`

Other parameters around the species of interest need to be derived from
site-based surveys:

`prop_crh_pars, bird_dens_dt,` and `site_fhd_boots`

All wind farm parameters need to be obtained by the wind farm
developers:

`n_blades, air_gap_pars, rtr_radius_pars, bld_width_pars, bld_pitch_pars, rtn_speed_pars, windspd_pars, trb_wind_avbl, trb_downtime_pars, wf_n_trbs, wf_width, wf_latitude, tidal_offset, season_specs, bld_chord_prf, lrg_arr_corr`

The following parameters refer to the outputs:

`out_format, out_sampled_pars, out_period, verbose, log_file`

More information on input parameter specifics can be found in the
vignettes for `stoch_crm` and `band_crm`.

## Outputs

Once the collision risk model is run, the key outputs are presented as a
table which contains the mean, standard deviation and median number of
collisions summarised by month, season, or year. Quantiles of the
bootstrapped collisions are also presented in the tables. These tables
are accessed through calling from the model object. Run the
[Examples](#examples) to view exemplar outputs.

``` r
stochOUT <- stochLAB::stoch_crm(...)

stochOUT$collisions$opt1 #For outputs from option 1 of the stochastic collision risk model 
stochOUT$collisions$opt2 #For outputs from option 2 of the stochastic collision risk model 
stochOUT$collisions$opt3 #For outputs from option 3 of the stochastic collision risk model 
```

## Examples

### Simple example

This is a basic example of running the stochastic collision model for
one seabird species and one turbine/wind-farm scenario, with fictional
input parameter data.

``` r
library(stochLAB)

# ------------------------------------------------------
# Setting some of the required inputs upfront

b_dens <- data.frame(
  month = month.abb,
  mean = runif(12, 0.8, 1.5),
  sd = runif(12, 0.2, 0.3))

# Generic FHD bootstraps for one species, from Johnson et al (2014)
fhd_boots <- generic_fhd_bootstraps[[1]]

# wind speed vs rotation speed vs pitch
wind_rtn_ptch <- data.frame(
  wind_speed = seq_len(30),
  rtn_speed = 10/(30:1),
  bld_pitch = c(rep(90, 4), rep(0, 8), 5:22))

# wind availability
windavb <- data.frame(
  month = month.abb,
  pctg = runif(12, 85, 98))

# maintenance downtime
dwntm <- data.frame(
  month = month.abb,
  mean = runif(12, 6, 10),
  sd = rep(2, 12))

# seasons specification
seas_dt <- data.frame(
  season_id = c("a", "b", "c"),
  start_month = c("Jan", "May", "Oct"), end_month = c("Apr", "Sep", "Dec"))

# ----------------------------------------------------------
# Run stochastic CRM, treating rotor radius, air gap and
# blade width as fixed parameters (i.e. not stochastic)

stoch_crm(
  model_options = c(1, 2, 3),
  n_iter = 1000,
  flt_speed_pars = data.frame(mean = 7.26, sd = 1.5),
  body_lt_pars = data.frame(mean = 0.39, sd = 0.005),
  wing_span_pars = data.frame(mean = 1.08, sd = 0.04),
  avoid_bsc_pars = data.frame(mean = 0.99, sd = 0.001),
  avoid_ext_pars = data.frame(mean = 0.96, sd = 0.002),
  noct_act_pars = data.frame(mean = 0.033, sd = 0.005),
  prop_crh_pars = data.frame(mean = 0.06, sd = 0.009),
  bird_dens_opt = "tnorm",
  bird_dens_dt = b_dens,
  flight_type = "flapping",
  prop_upwind = 0.5,
  gen_fhd_boots = fhd_boots,
  n_blades = 3,
  rtr_radius_pars = data.frame(mean = 80, sd = 0), # sd = 0, rotor radius is fixed
  air_gap_pars = data.frame(mean = 36, sd = 0),    # sd = 0, air gap is fixed
  bld_width_pars = data.frame(mean = 8, sd = 0),   # sd = 0, blade width is fixed
  rtn_pitch_opt = "windSpeedReltn",
  windspd_pars = data.frame(mean = 7.74, sd = 3),
  rtn_pitch_windspd_dt = wind_rtn_ptch,
  trb_wind_avbl = windavb,
  trb_downtime_pars = dwntm,
  wf_n_trbs = 200,
  wf_width = 15,
  wf_latitude = 56.9,
  tidal_offset = 2.5,
  lrg_arr_corr = TRUE,
  verbose = TRUE,
  seed = 1234,
  out_format = "summaries",
  out_sampled_pars = TRUE,
  out_period = "seasons",
  season_specs = seas_dt,
  log_file = paste0(getwd(), "scrm_example.log")
)
#> 
#> ── Stochastic CRM ──
#> 
#> ℹ Checking inputs✔ Checking inputs [119ms]
#> ℹ Preparing data✔ Preparing data [225ms]
#> ℹ Sampling parameters✔ Sampling parameters [374ms]
#> ⠙ Calculating collisions | 3/1000 iterations⠹ Calculating collisions | 59/1000 iterations⠸ Calculating collisions | 173/1000 iterations⠼ Calculating collisions | 292/1000 iterations⠴ Calculating collisions | 396/1000 iterations⠦ Calculating collisions | 519/1000 iterations⠧ Calculating collisions | 625/1000 iterations⠇ Calculating collisions | 746/1000 iterations⠏ Calculating collisions | 864/1000 iterations⠋ Calculating collisions | 980/1000 iterations✔ Calculating collisions | 1000/1000 iterations [2s]
#> ℹ Sorting outputs✔ Sorting outputs [701ms]
#> ✔ Job done!
#> $collisions
#> $collisions$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 a         Jan_A…  39.0  17.5   40.4    10.4     25.6    50.6      71.6    94.7
#> 2 b         May_S…  57.9  26.1   58.7    15.9     39.1    76.0     108.    144. 
#> 3 c         Oct_D…  22.1  10.2   22.4     5.89    14.0    29.2      41.8    52.0
#> 
#> $collisions$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 a         Jan_A… 1.02   1.79  0.550   0.0922   0.324   0.910      7.08   16.0 
#> 2 b         May_S… 1.51   2.61  0.836   0.142    0.480   1.37       9.82   21.2 
#> 3 c         Oct_D… 0.572  1.02  0.315   0.0515   0.181   0.509      3.91    9.22
#> 
#> $collisions$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 a         Jan_A… 0.489 1.10   0.199   0.0478  0.127    0.353      4.19    9.11
#> 2 b         May_S… 0.723 1.59   0.303   0.0672  0.187    0.514      5.87   12.9 
#> 3 c         Oct_D… 0.275 0.625  0.114   0.0256  0.0730   0.203      2.22    5.40
#> 
#> 
#> $sampled_pars
#> $sampled_pars$air_gap
#> # A tibble: 1 × 5
#>    mean    sd median pctl_2.5 pctl_97.5
#>   <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#> 1    36     0     36       36        36
#> 
#> $sampled_pars$bld_width
#> # A tibble: 1 × 5
#>    mean    sd median pctl_2.5 pctl_97.5
#>   <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#> 1     8     0      8        8         8
#> 
#> $sampled_pars$body_lt
#> # A tibble: 1 × 5
#>    mean      sd median pctl_2.5 pctl_97.5
#>   <dbl>   <dbl>  <dbl>    <dbl>     <dbl>
#> 1 0.390 0.00499  0.390    0.380     0.400
#> 
#> $sampled_pars$flt_speed
#> # A tibble: 1 × 5
#>    mean    sd median pctl_2.5 pctl_97.5
#>   <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#> 1  7.28  1.47   7.28     4.30      10.0
#> 
#> $sampled_pars$noct_actv
#> # A tibble: 1 × 5
#>     mean      sd median pctl_2.5 pctl_97.5
#>    <dbl>   <dbl>  <dbl>    <dbl>     <dbl>
#> 1 0.0333 0.00498 0.0333   0.0241    0.0436
#> 
#> $sampled_pars$rtr_radius
#> # A tibble: 1 × 5
#>    mean    sd median pctl_2.5 pctl_97.5
#>   <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#> 1    80     0     80       80        80
#> 
#> $sampled_pars$wing_span
#> # A tibble: 1 × 5
#>    mean     sd median pctl_2.5 pctl_97.5
#>   <dbl>  <dbl>  <dbl>    <dbl>     <dbl>
#> 1  1.08 0.0398   1.08     1.00      1.16
#> 
#> $sampled_pars$hub_height
#> # A tibble: 1 × 5
#>    mean    sd median pctl_2.5 pctl_97.5
#>   <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#> 1   116     0    116      116       116
#> 
#> $sampled_pars$dens_mth
#> # A tibble: 12 × 6
#>    period  mean    sd median pctl_2.5 pctl_97.5
#>    <chr>  <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#>  1 Jan    1.45  0.278  1.44     0.926      2.00
#>  2 Feb    0.970 0.280  0.961    0.448      1.52
#>  3 Mar    1.38  0.214  1.38     0.949      1.77
#>  4 Apr    1.49  0.208  1.50     1.10       1.91
#>  5 May    1.34  0.224  1.33     0.910      1.78
#>  6 Jun    1.05  0.272  1.05     0.469      1.60
#>  7 Jul    1.35  0.291  1.36     0.803      1.93
#>  8 Aug    0.872 0.291  0.875    0.303      1.47
#>  9 Sep    0.902 0.211  0.903    0.501      1.31
#> 10 Oct    1.31  0.225  1.31     0.876      1.75
#> 11 Nov    1.13  0.295  1.12     0.569      1.72
#> 12 Dec    1.18  0.239  1.18     0.708      1.64
#> 
#> $sampled_pars$prop_oper_mth
#> # A tibble: 12 × 6
#>    period  mean     sd median pctl_2.5 pctl_97.5
#>    <chr>  <dbl>  <dbl>  <dbl>    <dbl>     <dbl>
#>  1 Jan    0.802 0.0207  0.802    0.762     0.839
#>  2 Feb    0.833 0.0207  0.833    0.793     0.871
#>  3 Mar    0.869 0.0204  0.869    0.827     0.910
#>  4 Apr    0.862 0.0196  0.861    0.824     0.899
#>  5 May    0.775 0.0195  0.775    0.737     0.812
#>  6 Jun    0.888 0.0198  0.888    0.848     0.926
#>  7 Jul    0.845 0.0199  0.845    0.804     0.885
#>  8 Aug    0.758 0.0194  0.759    0.721     0.796
#>  9 Sep    0.860 0.0205  0.860    0.818     0.898
#> 10 Oct    0.877 0.0200  0.877    0.836     0.915
#> 11 Nov    0.864 0.0201  0.865    0.826     0.904
#> 12 Dec    0.834 0.0201  0.835    0.796     0.871
#> 
#> $sampled_pars$downtime
#> # A tibble: 12 × 6
#>    period  mean    sd median pctl_2.5 pctl_97.5
#>    <chr>  <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#>  1 Jan     7.47  2.07   7.51     3.78      11.5
#>  2 Feb     9.98  2.07   9.99     6.13      13.9
#>  3 Mar     9.20  2.04   9.20     5.18      13.5
#>  4 Apr     9.39  1.96   9.43     5.61      13.2
#>  5 May     8.28  1.95   8.25     4.57      12.1
#>  6 Jun     8.44  1.98   8.41     4.58      12.4
#>  7 Jul     7.00  1.99   6.99     2.95      11.0
#>  8 Aug     9.22  1.94   9.18     5.41      13.0
#>  9 Sep     7.53  2.05   7.51     3.68      11.6
#> 10 Oct     7.11  2.00   7.13     3.31      11.2
#> 11 Nov     8.92  2.01   8.90     4.94      12.8
#> 12 Dec     9.60  2.01   9.50     5.89      13.4
#> 
#> $sampled_pars$wind_speed
#> # A tibble: 1 × 5
#>    mean    sd median pctl_2.5 pctl_97.5
#>   <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#> 1  7.73  3.00   7.73     2.15      13.7
#> 
#> $sampled_pars$rtn_speed
#> # A tibble: 1 × 5
#>    mean     sd median pctl_2.5 pctl_97.5
#>   <dbl>  <dbl>  <dbl>    <dbl>     <dbl>
#> 1 0.428 0.0574  0.417    0.345     0.556
#> 
#> $sampled_pars$bld_pitch
#> # A tibble: 1 × 5
#>    mean    sd median pctl_2.5 pctl_97.5
#>   <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#> 1 0.329 0.635      0        0      1.57
#> 
#> $sampled_pars$avoid_bsc
#> # A tibble: 1 × 5
#>    mean       sd median pctl_2.5 pctl_97.5
#>   <dbl>    <dbl>  <dbl>    <dbl>     <dbl>
#> 1 0.990 0.000985  0.990    0.988     0.992
#> 
#> $sampled_pars$avoid_ext
#> # A tibble: 1 × 5
#>    mean      sd median pctl_2.5 pctl_97.5
#>   <dbl>   <dbl>  <dbl>    <dbl>     <dbl>
#> 1 0.960 0.00203  0.960    0.956     0.964
#> 
#> $sampled_pars$prop_crh
#> # A tibble: 1 × 5
#>     mean      sd median pctl_2.5 pctl_97.5
#>    <dbl>   <dbl>  <dbl>    <dbl>     <dbl>
#> 1 0.0605 0.00904 0.0601   0.0441    0.0800
#> 
#> $sampled_pars$gen_fhd
#> # A tibble: 500 × 6
#>    height   mean      sd median pctl_2.5 pctl_97.5
#>     <dbl>  <dbl>   <dbl>  <dbl>    <dbl>     <dbl>
#>  1      0 0.163  0.0188  0.166    0.109     0.187 
#>  2      1 0.136  0.0129  0.138    0.0967    0.152 
#>  3      2 0.114  0.00855 0.115    0.0863    0.124 
#>  4      3 0.0950 0.00530 0.0963   0.0769    0.100 
#>  5      4 0.0794 0.00296 0.0803   0.0686    0.0816
#>  6      5 0.0664 0.00146 0.0669   0.0606    0.0670
#>  7      6 0.0556 0.00116 0.0558   0.0530    0.0567
#>  8      7 0.0465 0.00166 0.0466   0.0439    0.0490
#>  9      8 0.0390 0.00216 0.0389   0.0357    0.0432
#> 10      9 0.0327 0.00250 0.0324   0.0290    0.0386
#> # ℹ 490 more rows
```

### Multiscenario example

This is an example usage of `stoch_crm()` for multiple scenarios. The
aim is two-fold:

1.  Suggest how input parameter datasets used in the previous
    implementation can be reshaped to fit `stoch_crm()`’s interface.
    Suggested code is also relevant in the context of multiple scenarios
    applications, since the wide tabular structure of these datasets is
    likely the favoured format for users to compile input parameters
    under different scenarios.

2.  Propose a functional programming framework to run `stoch_crm()` for
    multiple species and wind-farm/turbines features.

Please note the example runs on fictional data.

``` r
library(stochLAB)

# --------------------------------------------------------- #
# ----      Reshaping into list-column data frames       ----
# --------------------------------------------------------- #
#
# --- bird features
bird_pars <- bird_pars_wide_example %>%
  dplyr::relocate(Flight, .after = dplyr::last_col()) %>%
  tidyr::pivot_longer(AvoidanceBasic:Prop_CRH_ObsSD) %>%
  dplyr::mutate(
    par = dplyr::if_else(grepl("SD|sd|Sd", name), "sd", "mean"),
    feature = gsub("SD|sd|Sd","", name)) %>%
  dplyr::select(-name) %>%
  tidyr::pivot_wider(names_from = par, values_from = value) %>%
  tidyr::nest(pars = c(mean, sd)) %>%
  tidyr::pivot_wider(names_from = feature, values_from = pars) %>%
  tibble::add_column(prop_upwind = 0.5)

# --- bird densities: provided as mean and sd Parameters for Truncated Normal lower
# bounded at 0
dens_pars <- dens_tnorm_wide_example %>%
  tibble::add_column(
    dens_opt = rep("tnorm", nrow(.)),
    .after = 1) %>%
  tidyr::pivot_longer(Jan:DecSD) %>%
  dplyr::mutate(
    par = dplyr::if_else(grepl("SD|sd|Sd", name), "sd", "mean"),
    month = gsub("SD|sd|Sd","", name)) %>%
  dplyr::select(-name) %>%
  tidyr::pivot_wider(names_from = par, values_from = value) %>%
  tidyr::nest(mth_dens = c(month, mean, sd))

# --- FHD data from Johnson et al (2014) for the species under analysis
gen_fhd_boots <- generic_fhd_bootstraps[bird_pars$Species]

# --- seasons definitions (made up)
season_dt <- list(
  Arctic_Tern = data.frame(
    season_id = c("breeding", "feeding", "migrating"),
    start_month = c("May", "Sep", "Jan"),
    end_month = c("Aug", "Dec", "Apr")),
  Black_headed_Gull = data.frame(
    season_id = c("breeding", "feeding", "migrating"),
    start_month = c("Jan", "May", "Oct"),
    end_month = c("Apr", "Sep", "Dec")),
  Black_legged_Kittiwake = data.frame(
    season_id = c("breeding", "feeding", "migrating"),
    start_month = c("Dec", "Mar", "Sep"),
    end_month = c("Feb", "Aug", "Nov")))

# --- turbine parameters
## address operation parameters first
trb_opr_pars <- turb_pars_wide_example %>%
  dplyr::select(TurbineModel, JanOp:DecOpSD) %>%
  tidyr::pivot_longer(JanOp:DecOpSD) %>%
  dplyr::mutate(
    month = substr(name, 1, 3),
    par = dplyr::case_when(
      grepl("SD|sd|Sd", name) ~ "sd",
      grepl("Mean|MEAN|mean", name) ~ "mean",
      TRUE ~ "pctg"
    )) %>%
  dplyr::select(-name) %>%
  tidyr::pivot_wider(names_from = par, values_from = value) %>%
  tidyr::nest(
    wind_avbl = c(month, pctg),
    trb_dwntm = c(month, mean, sd))

## address turbine features and subsequently merge operation parameters
trb_pars <- turb_pars_wide_example %>%
  dplyr::select(TurbineModel:windSpeedSD ) %>%
  dplyr::relocate(RotorSpeedAndPitch_SimOption, .after = 1) %>%
  tidyr::pivot_longer(RotorRadius:windSpeedSD) %>%
  dplyr::mutate(
    par = dplyr::if_else(grepl("SD|sd|Sd", name), "sd", "mean"),
    feature = gsub("(SD|sd|Sd)|(Mean|MEAN|mean)","", name)
  ) %>%
  dplyr::select(-name) %>%
  tidyr::pivot_wider(names_from = par, values_from = value) %>%
  tidyr::nest(pars = c(mean, sd)) %>%
  tidyr::pivot_wider(names_from = feature, values_from = pars) %>%
  dplyr::left_join(., trb_opr_pars)
#> Joining with `by = join_by(TurbineModel)`

# --- windspeed, rotation speed and blade pitch relationship
wndspd_rtn_ptch_example
#>    wind_speed rtn_speed bld_pitch
#> 1           0       0.0        90
#> 2           1       0.0        90
#> 3           2       0.0        90
#> 4           3       6.8         0
#> 5           4       6.8         0
#> 6           5       6.8         0
#> 7           6       6.8         0
#> 8           7       6.8         0
#> 9           8       8.1         0
#> 10          9       9.1         0
#> 11         10       9.3         0
#> 12         11       9.4         4
#> 13         12       9.5         7
#> 14         13       9.7         9
#> 15         14       9.7        11
#> 16         15       9.9        13
#> 17         16      10.2        15
#> 18         17      10.2        16
#> 19         18      10.2        18
#> 20         19      10.2        19
#> 21         20      10.2        20
#> 22         21      10.2        22
#> 23         22      10.2        23
#> 24         23      10.2        24
#> 25         24      10.2        25
#> 26         25      10.2        26
#> 27         26      10.2        27
#> 28         27      10.2        28
#> 29         28      10.2        29
#> 30         29      10.2        30

# --- windfarm parameters
wf_pars <- data.frame(
  wf_id = c("wf_1", "wf_2"),
  n_turbs = c(200, 400),
  wf_width = c(4, 10),
  wf_lat = c(55.8, 55.0),
  td_off = c(2.5, 2),
  large_array_corr = c(FALSE, TRUE)
)


# -------------------------------------------------------------- #
# ----      Run stoch_crm() for multiple scenarios           ----
# -------------------------------------------------------------- #

# --- Set up scenario combinations
scenarios_specs <- tidyr::expand_grid(
  spp = bird_pars$Species,
  turb_id = trb_pars$TurbineModel,
  wf_id = wf_pars$wf_id) %>%
  tibble::add_column(
    scenario_id = paste0("scenario_", 1:nrow(.)),
    .before = 1)

# --- Set up progress bar for the upcoming iterative mapping step
pb <- progress::progress_bar$new(
  format = "Running Scenario: :what [:bar] :percent eta: :eta",
  width = 100,
  total = nrow(scenarios_specs))

# --- Map stoch_crm() to each scenario specification via purrr::pmap
outputs <- scenarios_specs %>%
  purrr::pmap(function(scenario_id, spp, turb_id, wf_id, ...){

    pb$tick(tokens = list(what = scenario_id))

    # params for current species
    c_spec <- bird_pars %>%
      dplyr::filter(Species == {{spp}}) 

    # density for current species
    c_dens <- dens_pars %>%
      dplyr::filter(Species == {{spp}})

    # params for current turbine scenario
    c_turb <- trb_pars %>%
      dplyr::filter(TurbineModel == {{turb_id}})

    # params for current windfarm scenario
    c_wf <- wf_pars %>%
      dplyr::filter(wf_id == {{wf_id}})

    # inputs in list-columns need to be unlisted, either via `unlist()` or
    # indexing `[[1]]`
    # switching off `verbose`, otherwise console will be 
    # cramped with log messages
    
    stoch_crm(
      model_options = c(1, 2, 3),
      n_iter = 1000,
      flt_speed_pars = c_spec$Flight_Speed[[1]],
      body_lt_pars = c_spec$Body_Length[[1]],
      wing_span_pars = c_spec$Wingspan[[1]],
      avoid_bsc_pars = c_spec$AvoidanceBasic[[1]],
      avoid_ext_pars = c_spec$AvoidanceExtended[[1]],
      noct_act_pars = c_spec$Nocturnal_Activity[[1]],
      prop_crh_pars = c_spec$Prop_CRH_Obs[[1]],
      bird_dens_opt = c_dens$dens_opt,
      bird_dens_dt = c_dens$mth_dens[[1]],
      flight_type = c_spec$Flight,
      prop_upwind = c_spec$prop_upwind,
      gen_fhd_boots = gen_fhd_boots[[spp]],
      n_blades = c_turb$Blades,
      rtr_radius_pars = c_turb$RotorRadius[[1]],
      air_gap_pars = c_turb$HubHeightAdd[[1]],
      bld_width_pars = c_turb$BladeWidth[[1]],
      rtn_pitch_opt = c_turb$RotorSpeedAndPitch_SimOption,
      bld_pitch_pars = c_turb$Pitch[[1]],
      rtn_speed_pars = c_turb$RotationSpeed[[1]],
      windspd_pars = c_turb$windSpeed[[1]],
      rtn_pitch_windspd_dt = wndspd_rtn_ptch_example,
      trb_wind_avbl = c_turb$wind_avbl[[1]],
      trb_downtime_pars = c_turb$trb_dwntm[[1]],
      wf_n_trbs = c_wf$n_turbs,
      wf_width = c_wf$wf_width,
      wf_latitude = c_wf$wf_lat,
      tidal_offset = c_wf$td_off,
      lrg_arr_corr = c_wf$large_array_corr,
      verbose = FALSE,
      seed = 1234,
      out_format = "summaries",
      out_sampled_pars = FALSE,
      out_period = "seasons",
      season_specs = season_dt[[spp]],
      log_file = NULL
    )
  })

# --- close progress bar
pb$terminate()

# --- identify elements of output list
names(outputs) <- scenarios_specs$scenario_id

outputs
#> $scenario_1
#> $scenario_1$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A…  984.  75.9   982.     847.    933.   1031.     1130.   1241.
#> 2 feeding   Sep_D…  552.  41.6   551.     478.    523.    579.      634.    683.
#> 3 migrating Jan_A…  626.  47.7   623.     541.    591.    658.      722.    780.
#> 
#> $scenario_1$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A…  60.7  67.3   41.9     2.41    25.3    65.7      293.    389.
#> 2 feeding   Sep_D…  34.0  37.7   23.5     1.35    14.3    37.0      162.    217.
#> 3 migrating Jan_A…  38.6  42.8   26.8     1.55    16.2    42.4      186.    249.
#> 
#> $scenario_1$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A…  17.9  24.8  10.5     0.394    5.92    17.9     106.    151. 
#> 2 feeding   Sep_D…  10.1  13.9   5.86    0.221    3.35    10.0      59.0    84.5
#> 3 migrating Jan_A…  11.4  15.8   6.77    0.251    3.79    11.5      66.9    93.8
#> 
#> 
#> $scenario_2
#> $scenario_2$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 1836. 137.   1831.    1586.   1743.   1921.     2098.   2301.
#> 2 feeding   Sep_D… 1045.  76.4  1044.     909.    991.   1096.     1195.   1288.
#> 3 migrating Jan_A… 1181.  87.6  1177.    1025.   1118.   1241.     1356.   1463.
#> 
#> $scenario_2$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 122.  132.    85.2     5.20    52.1   133.       574.    759.
#> 2 feeding   Sep_D…  69.2  75.0   48.4     2.95    29.9    75.9      323.    430.
#> 3 migrating Jan_A…  78.3  84.8   55.1     3.37    33.7    86.5      369.    491.
#> 
#> $scenario_2$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A…  35.9  48.7   21.4    0.848   12.2     36.1      208.    294.
#> 2 feeding   Sep_D…  20.4  27.7   12.1    0.482    7.00    20.6      118.    167.
#> 3 migrating Jan_A…  23.1  31.3   13.9    0.547    7.91    23.5      133.    185.
#> 
#> 
#> $scenario_3
#> $scenario_3$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 2131. 143.   2126.    1875.   2034.   2223.     2415.   2690.
#> 2 feeding   Sep_D… 1149.  76.8  1143.    1011.   1095.   1197.     1302.   1446.
#> 3 migrating Jan_A… 1300.  89.6  1292.    1131.   1239.   1357.     1491.   1638.
#> 
#> $scenario_3$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A…  53.7  77.6   30.1    0.894   17.0     53.9      347.    477.
#> 2 feeding   Sep_D…  28.9  41.8   16.1    0.496    9.16    29.0      185.    257.
#> 3 migrating Jan_A…  32.7  47.1   18.3    0.546   10.3     32.5      205.    291.
#> 
#> $scenario_3$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 14.4   25.7   6.47   0.124     3.37   12.7      109.    162. 
#> 2 feeding   Sep_D…  7.74  13.8   3.48   0.0685    1.81    6.84      59.8    86.3
#> 3 migrating Jan_A…  8.74  15.6   3.93   0.0756    2.05    7.65      66.9    96.0
#> 
#> 
#> $scenario_4
#> $scenario_4$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 3720.  240.  3713.    3290.   3560.   3872.     4188.   4654.
#> 2 feeding   Sep_D… 2037.  131.  2025.    1801.   1944.   2122.     2297.   2540.
#> 3 migrating Jan_A… 2297.  152.  2284.    2012.   2193.   2393.     2626.   2854.
#> 
#> $scenario_4$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 100.  142.    57.0     1.79    32.6   101.       633.    867.
#> 2 feeding   Sep_D…  54.8  77.7   31.0     1.01    17.8    55.5      342.    474.
#> 3 migrating Jan_A…  61.7  87.3   35.1     1.11    20.1    62.2      382.    531.
#> 
#> $scenario_4$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A…  26.8  47.1  12.3     0.249    6.47    23.9      199.    295.
#> 2 feeding   Sep_D…  14.7  25.7   6.69    0.140    3.53    13.1      111.    160.
#> 3 migrating Jan_A…  16.5  28.9   7.57    0.153    3.98    14.7      124.    177.
#> 
#> 
#> $scenario_5
#> $scenario_5$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 1176.  341.  1053.     825.    985.   1219.     2220.   2634.
#> 2 feeding   Sep_D…  615.  176.   551.     430.    516.    640.     1136.   1338.
#> 3 migrating Jan_A…  703.  202.   630.     489.    591.    731.     1323.   1551.
#> 
#> $scenario_5$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 0.837  2.75 0.131  0.000231  0.0341   0.390      8.68    31.9
#> 2 feeding   Sep_D… 0.438  1.45 0.0687 0.000118  0.0180   0.202      4.66    17.0
#> 3 migrating Jan_A… 0.500  1.63 0.0782 0.000136  0.0209   0.230      5.20    18.4
#> 
#> $scenario_5$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 0.221 0.795 0.0245  2.61e-5 0.00602  0.0786      2.41    8.88
#> 2 feeding   Sep_D… 0.116 0.418 0.0129  1.29e-5 0.00319  0.0408      1.29    4.73
#> 3 migrating Jan_A… 0.132 0.472 0.0147  1.51e-5 0.00365  0.0462      1.44    5.12
#> 
#> 
#> $scenario_6
#> $scenario_6$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 2164.  568.  1961.    1557.   1840.   2263.     3874.   4500.
#> 2 feeding   Sep_D… 1148.  298.  1043.     822.    977.   1210.     2021.   2323.
#> 3 migrating Jan_A… 1310.  341.  1188.     934.   1117.   1370.     2325.   2688.
#> 
#> $scenario_6$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 1.64   5.28  0.266 0.000499  0.0711   0.779     17.1     59.5
#> 2 feeding   Sep_D… 0.872  2.82  0.142 0.000254  0.0379   0.415      9.33    32.2
#> 3 migrating Jan_A… 0.991  3.18  0.162 0.000295  0.0437   0.472     10.4     34.7
#> 
#> $scenario_6$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  May_A… 0.433 1.53  0.0503  5.79e-5 0.0126   0.157       4.77   16.6 
#> 2 feeding   Sep_D… 0.230 0.817 0.0271  2.91e-5 0.00672  0.0832      2.59    8.97
#> 3 migrating Jan_A… 0.261 0.921 0.0304  3.37e-5 0.00768  0.0947      2.88    9.68
#> 
#> 
#> $scenario_7
#> $scenario_7$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  429.  93.1   423.     270.    360.    490.      626.    767.
#> 2 feeding   May_S…  697. 151.    685.     442.    588.    791.     1006.   1252.
#> 3 migrating Oct_D…  266.  59.2   262.     164.    223.    303.      390.    498.
#> 
#> $scenario_7$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  257. 150.    223.     62.9   153.     320.      603.   1071.
#> 2 feeding   May_S…  417. 244.    366.    100.    251.     517.      985.   1797.
#> 3 migrating Oct_D…  159.  93.0   137.     37.8    94.0    199.      377.    649.
#> 
#> $scenario_7$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  28.2  25.9   20.9     4.02   12.5     34.1      86.4    239.
#> 2 feeding   May_S…  45.9  42.4   34.3     6.51   20.3     55.1     145.     409.
#> 3 migrating Oct_D…  17.5  16.0   12.8     2.48    7.78    21.8      54.6    144.
#> 
#> 
#> $scenario_8
#> $scenario_8$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  855.  186.   844.     539.    719.    977.     1248.   1531.
#> 2 feeding   May_S… 1376.  299.  1352.     872.   1160.   1560.     1985.   2472.
#> 3 migrating Oct_D…  534.  119.   526.     329.    448.    608.      782.    999.
#> 
#> $scenario_8$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  536.  307.   468.    135.     324.    668.     1246.   2183.
#> 2 feeding   May_S…  862.  494.   760.    213.     524.   1067.     2013.   3624.
#> 3 migrating Oct_D…  334.  192.   290.     81.5    200.    418.      783.   1330.
#> 
#> $scenario_8$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  58.6  52.8   43.8     8.62    26.4    71.2      178.    485.
#> 2 feeding   May_S…  94.4  85.7   71.2    14.0     42.5   114.       295.    820.
#> 3 migrating Oct_D…  36.6  32.8   27.1     5.36    16.5    45.6      113.    293.
#> 
#> 
#> $scenario_9
#> $scenario_9$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  571. 123.    563.     360.    484.    643.      818.   1015.
#> 2 feeding   May_S…  965. 210.    953.     615.    815.   1089.     1428.   1738.
#> 3 migrating Oct_D…  354.  78.1   349.     223.    299.    401.      511.    646.
#> 
#> $scenario_9$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  194. 140.   157.      32.9   100.     245.      539.   1050.
#> 2 feeding   May_S…  329. 238.   265.      55.1   169.     414.      905.   1788.
#> 3 migrating Oct_D…  121.  87.9   97.6     20.1    62.0    153.      332.    682.
#> 
#> $scenario_9$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  23.0  25.6  15.7      2.36    8.83    27.4      79.6    265.
#> 2 feeding   May_S…  38.9  43.5  26.3      3.96   15.0     47.4     137.     472.
#> 3 migrating Oct_D…  14.3  16.2   9.75     1.46    5.51    17.2      48.9    183.
#> 
#> 
#> $scenario_10
#> $scenario_10$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A… 1136.  245.  1120.     717.    963.   1279.     1628.   2019.
#> 2 feeding   May_S… 1901.  413.  1875.    1212.   1605.   2145.     2811.   3422.
#> 3 migrating Oct_D…  709.  156.   698.     446.    598.    802.     1024.   1294.
#> 
#> $scenario_10$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  404.  286.   328.     70.2    211.    509.     1101.   2131.
#> 2 feeding   May_S…  676.  481.   547.    116.     351.    852.     1833.   3592.
#> 3 migrating Oct_D…  252.  181.   205.     43.2    131.    320.      686.   1393.
#> 
#> $scenario_10$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  47.6  52.0   32.8     5.05    18.6    56.9      164.    535.
#> 2 feeding   May_S…  79.8  87.6   54.3     8.36    31.2    95.8      279.    944.
#> 3 migrating Oct_D…  29.8  33.2   20.5     3.15    11.7    35.6      101.    373.
#> 
#> 
#> $scenario_11
#> $scenario_11$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  570. 121.    558.     366.    484.    647.      826.    979.
#> 2 feeding   May_S…  981. 211.    957.     624.    830.   1115.     1431.   1682.
#> 3 migrating Oct_D…  347.  76.0   344.     221.    293.    395.      515.    606.
#> 
#> $scenario_11$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  24.7  40.3  11.5     0.787    4.98    29.1     111.     440.
#> 2 feeding   May_S…  42.5  69.2  19.8     1.35     8.56    48.3     189.     768.
#> 3 migrating Oct_D…  15.0  24.2   7.02    0.486    3.02    17.4      67.2    263.
#> 
#> $scenario_11$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  3.52 11.1   0.933   0.0455   0.337    2.66      21.6   133. 
#> 2 feeding   May_S…  6.04 19.0   1.57    0.0814   0.584    4.48      36.6   233. 
#> 3 migrating Oct_D…  2.13  6.60  0.568   0.0283   0.207    1.57      13.3    75.9
#> 
#> 
#> $scenario_12
#> $scenario_12$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A… 1135.  241.  1111.     728.    964.   1287.     1645.   1949.
#> 2 feeding   May_S… 1932.  415.  1885.    1229.   1635.   2197.     2819.   3315.
#> 3 migrating Oct_D…  696.  152.   689.     442.    587.    791.     1032.   1212.
#> 
#> $scenario_12$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  50.9  81.9   24.0     1.68   10.5     60.3      229.    887.
#> 2 feeding   May_S…  86.6 139.    41.0     2.85   17.8     99.1      384.   1534.
#> 3 migrating Oct_D…  31.2  49.5   14.8     1.04    6.37    36.3      139.    535.
#> 
#> $scenario_12$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Jan_A…  7.17  22.2   1.95   0.0973   0.707    5.54      43.2    268.
#> 2 feeding   May_S… 12.2   37.7   3.25   0.172    1.22     9.21      73.2    464.
#> 3 migrating Oct_D…  4.38  13.4   1.19   0.0606   0.439    3.28      27.1    154.
#> 
#> 
#> $scenario_13
#> $scenario_13$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  21.5  4.95   21.0     13.1    18.0    24.4      32.0    39.7
#> 2 feeding   Mar_A…  60.9 13.9    59.8     37.2    50.8    69.7      91.1   112. 
#> 3 migrating Sep_N…  24.4  5.70   24.0     14.9    20.3    27.8      37.0    45.4
#> 
#> $scenario_13$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  21.6  4.72   21.3     13.2    18.4    24.6      31.5    36.8
#> 2 feeding   Mar_A…  61.3 13.3    60.9     37.6    52.2    69.8      89.0   106. 
#> 3 migrating Sep_N…  24.6  5.48   24.3     14.8    20.9    27.9      36.1    42.8
#> 
#> $scenario_13$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  18.6  3.99   18.7     11.4    15.9    21.1      27.2    35.6
#> 2 feeding   Mar_A…  52.9 11.3    52.9     32.7    45.1    59.8      77.3   100. 
#> 3 migrating Sep_N…  21.2  4.68   21.1     13.1    18.0    24.0      31.4    41.3
#> 
#> 
#> $scenario_14
#> $scenario_14$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  43.5  10.0   42.6     26.6    36.5    49.5      64.8    80.5
#> 2 feeding   Mar_A… 121.   27.6  119.      73.7   101.    138.      181.    223. 
#> 3 migrating Sep_N…  49.0  11.4   48.1     30.0    40.8    55.7      74.3    91.2
#> 
#> $scenario_14$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  46.0  9.96   45.4     28.2    39.2    52.4      66.7    78.3
#> 2 feeding   Mar_A… 128.  27.6   127.      78.7   109.    145.      185.    219. 
#> 3 migrating Sep_N…  51.8 11.5    51.3     31.3    44.2    58.8      75.8    89.4
#> 
#> $scenario_14$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  39.6  8.40   39.7     24.3    33.9    45.0      57.7    75.1
#> 2 feeding   Mar_A… 110.  23.3   110.      68.7    94.2   125.      160.    206. 
#> 3 migrating Sep_N…  44.7  9.76   44.6     27.7    38.1    50.5      65.7    85.9
#> 
#> 
#> $scenario_15
#> $scenario_15$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  30.8  6.55   30.3     20.0    26.2    34.7      45.5    53.8
#> 2 feeding   Mar_A…  88.3 18.8    86.9     57.3    75.0    99.5     130.    157. 
#> 3 migrating Sep_N…  34.5  7.50   33.9     22.1    29.1    38.9      51.6    60.5
#> 
#> $scenario_15$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  16.7  3.64   16.5     10.1    14.0    19.1      23.8    29.4
#> 2 feeding   Mar_A…  47.8 10.4    47.6     29.1    40.2    54.7      68.6    83.1
#> 3 migrating Sep_N…  18.6  4.16   18.6     11.2    15.7    21.3      27.0    33.1
#> 
#> $scenario_15$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  15.4  3.59   15.4     9.16    12.9    17.5      22.1    31.7
#> 2 feeding   Mar_A…  44.1 10.3    44.1    26.2     37.0    50.2      62.9    91.6
#> 3 migrating Sep_N…  17.2  4.09   17.3    10.2     14.5    19.5      25.1    37.2
#> 
#> 
#> $scenario_16
#> $scenario_16$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  62.3  13.3   61.3     40.4    53.0    70.3      92.2    109.
#> 2 feeding   Mar_A… 175.   37.3  172.     114.    149.    197.      258.     311.
#> 3 migrating Sep_N…  69.1  15.0   68.0     44.2    58.3    78.1     104.     121.
#> 
#> $scenario_16$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  35.4  7.67   35.0     21.5    29.9    40.6      50.5    62.3
#> 2 feeding   Mar_A…  99.4 21.5    98.9     60.8    83.8   114.      143.    173. 
#> 3 migrating Sep_N…  39.2  8.67   39.1     23.6    33.1    44.8      56.8    69.4
#> 
#> $scenario_16$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  32.7  7.54   32.7     19.6    27.6    37.1      46.9    66.3
#> 2 feeding   Mar_A…  91.9 21.1    91.8     54.9    77.1   104.      131.    188. 
#> 3 migrating Sep_N…  36.3  8.51   36.3     21.5    30.6    40.9      52.7    77.3
#> 
#> 
#> $scenario_17
#> $scenario_17$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  27.1  6.46   26.8     16.4    22.5    31.3      40.4    51.6
#> 2 feeding   Mar_A…  81.3 19.3    80.0     49.3    67.2    93.3     122.    157. 
#> 3 migrating Sep_N…  31.0  7.53   30.4     18.5    25.4    35.7      47.4    58.1
#> 
#> $scenario_17$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  1.10 0.470   1.04    0.417   0.761    1.34      2.18    3.68
#> 2 feeding   Mar_A…  3.28 1.40    3.12    1.24    2.27     4.02      6.51   11.0 
#> 3 migrating Sep_N…  1.25 0.541   1.19    0.471   0.869    1.54      2.51    4.42
#> 
#> $scenario_17$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F… 0.774 0.408  0.725    0.283   0.520   0.957      1.56    4.85
#> 2 feeding   Mar_A… 2.32  1.21   2.18     0.855   1.56    2.88       4.64   14.1 
#> 3 migrating Sep_N… 0.884 0.467  0.840    0.333   0.593   1.10       1.78    5.34
#> 
#> 
#> $scenario_18
#> $scenario_18$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  54.9  13.1   54.2     33.2    45.5    63.3      81.7    104.
#> 2 feeding   Mar_A… 161.   38.2  159.      97.8   133.    185.      242.     310.
#> 3 migrating Sep_N…  62.2  15.1   61.0     37.2    51.0    71.7      95.0    117.
#> 
#> $scenario_18$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  2.33 0.992   2.22    0.889    1.62    2.84      4.63    7.73
#> 2 feeding   Mar_A…  6.84 2.90    6.50    2.61     4.73    8.38     13.6    22.7 
#> 3 migrating Sep_N…  2.64 1.13    2.52    0.998    1.83    3.24      5.29    9.20
#> 
#> $scenario_18$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 breeding  Dec_F…  1.65 0.855   1.54    0.606    1.11    2.03      3.31    10.1
#> 2 feeding   Mar_A…  4.83 2.49    4.55    1.79     3.26    6.00      9.54    28.7
#> 3 migrating Sep_N…  1.86 0.969   1.77    0.707    1.25    2.31      3.68    11.0
```

### Band model example

This is an example usage of `band_crm()`. This is for a single species
and single set of turbine parameters. This replicates the Band (2012)
worksheet. The `stoch_crm()` function wraps around this function, where
`band_crm()` acts in essence as a single draw of `stoch_crm()`.

Please note the example runs on fictional data.

``` r
library(stochLAB)
# ------------------------------------------------------
# Run with arbitrary parameter values, for illustration
# ------------------------------------------------------

# Setting a dataframe of parameters to draw from
params <- data.frame(
  flight_speed = 13.1,         # Flight speed in m/s
  body_lt = 0.85,              # Body length in m
  wing_span = 1.01,            # Wing span in m
  flight_type = "flapping",    # flapping or gliding flight
  avoid_rt_basic = 0.989,      # avoidance rate for option 1 and 2
  avoid_rt_ext = 0.981,        # extended avoidance rate for option 3 and 4
  noct_activity = 0.5,         # proportion of day birds are inactive
  prop_crh_surv = 0.13,        # proportion of birds at collision risk height (option 1 only)
  prop_upwind = 0.5,           # proportion of flights that are upwind
  rotor_speed = 15,            # rotor speed in m/s
  rotor_radius = 120,          # radius of turbine in m
  blade_width = 5,             # width of turbine blades at thickest point in m
  blade_pitch = 15,            # mean radius pitch in Radians
  n_blades = 3,                # total number of blades per turbine
  hub_height = 150,            # height of hub in m above HAT
  n_turbines = 100,            # number of turbines in the wind farm
  wf_width = 52,               # width across longest section of wind farm
  wf_latitude = 56,            # latitude of centroid of wind farm
  tidal_offset = 2.5,          # mean tidal offset from HAT of the wind farm
  lrg_arr_corr = TRUE          # apply a large array correction?
)

# Monthly bird densities
b_dens <- data.frame(
  month = month.abb,
  dens = runif(12, 0.8, 1.5)
)

# flight height distribution from Johnston et al
gen_fhd_dat <- Johnston_Flight_heights_SOSS %>%
  dplyr::filter(variable=="Gannet.est") %>%
  dplyr::select(height,prop)

# monthly operational time of the wind farm
turb_oper <- data.frame(
  month = month.abb,
  prop_oper = runif(12,0.5,0.8)
)


stochLAB::band_crm(
  model_options = c(1,2,3),
  flight_speed = params$flight_speed,
  body_lt = params$body_lt,
  wing_span = params$wing_span,
  flight_type = params$flight_type,
  avoid_rt_basic = params$avoid_rt_basic,
  avoid_rt_ext = params$avoid_rt_ext,
  noct_activity = params$noct_activity,
  prop_crh_surv = params$prop_crh_surv,
  dens_month = b_dens,
  prop_upwind = params$prop_upwind,
  gen_fhd = gen_fhd_dat,
  site_fhd = NULL,  # Option 4 only
  rotor_speed = params$rotor_speed,
  rotor_radius = params$rotor_radius,
  blade_width = params$blade_width,
  blade_pitch = params$blade_pitch,
  n_blades = params$n_blades,
  hub_height = params$hub_height,
  chord_prof = chord_prof_5MW,
  n_turbines = params$n_turbines,
  turb_oper_month = turb_oper,
  wf_width = params$wf_width,
  wf_latitude = params$wf_latitude,
  tidal_offset = params$tidal_offset,
  lrg_arr_corr = params$lrg_arr_corr
  )
#> # A tibble: 12 × 4
#>    month  opt1  opt2  opt3
#>    <chr> <dbl> <dbl> <dbl>
#>  1 Jan    98.6  25.4 10.4 
#>  2 Feb    53.2  13.7  5.61
#>  3 Mar    83.7  21.6  8.83
#>  4 Apr    61.4  15.8  6.48
#>  5 May   117.   30.2 12.4 
#>  6 Jun    91.7  23.6  9.67
#>  7 Jul   114.   29.3 12.0 
#>  8 Aug    91.1  23.5  9.61
#>  9 Sep   106.   27.3 11.2 
#> 10 Oct    64.1  16.5  6.77
#> 11 Nov    60.7  15.7  6.40
#> 12 Dec    73.4  18.9  7.75
```
