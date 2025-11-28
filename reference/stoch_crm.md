# Stochastic collision risk model for a single species and one wind farm scenario

Runs a Stochastic Collision Risk Model (SCRM) for estimating the number
of in-flight collisions with offshore windfarm turbines, for given
species and windfarm scenario. Core calculations follow the work
developed by [Masden
(2015)](https://data.marine.gov.scot/dataset/developing-avian-collision-risk-model-incorporate-variability-and-uncertainty).
See **Background and Updates** section below for more details.

## Usage

``` r
stoch_crm(
  model_options = c("1", "2", "3", "4"),
  n_iter = 1000,
  flt_speed_pars,
  body_lt_pars,
  wing_span_pars,
  avoid_bsc_pars = NULL,
  avoid_ext_pars = NULL,
  noct_act_pars,
  prop_crh_pars = NULL,
  bird_dens_opt = c("tnorm", "resample", "qtiles"),
  bird_dens_dt,
  flight_type,
  prop_upwind,
  gen_fhd_boots = NULL,
  site_fhd_boots = NULL,
  n_blades,
  air_gap_pars,
  rtr_radius_pars,
  bld_width_pars,
  bld_chord_prf = chord_prof_5MW,
  rtn_pitch_opt = c("probDist", "windSpeedReltn"),
  bld_pitch_pars = NULL,
  rtn_speed_pars = NULL,
  windspd_pars = NULL,
  rtn_pitch_windspd_dt = NULL,
  trb_wind_avbl,
  trb_downtime_pars,
  wf_n_trbs,
  wf_width,
  wf_latitude,
  tidal_offset,
  lrg_arr_corr = TRUE,
  xinc = 0.05,
  yinc = 0.05,
  out_format = c("draws", "summaries"),
  out_sampled_pars = FALSE,
  out_period = c("months", "seasons", "annum"),
  season_specs = NULL,
  verbose = TRUE,
  log_file = NULL,
  seed = NULL
)
```

## Arguments

- model_options:

  Character vector, the model options for calculating collision risk
  (see **Details** section below).

- n_iter:

  An integer. The number of iterations for the model simulation.

- flt_speed_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the species flying speed, in metres/sec. Assumed
  to follow a Truncated Normal with lower bound at 0 (*tnorm-lw0*).

- body_lt_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the species body length, in metres. Assumed to
  follow a *tnorm-lw0* distribution.

- wing_span_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the species wingspan, in metres. Assumed to
  follow a *tnorm-lw0* distribution.

- avoid_bsc_pars, avoid_ext_pars:

  Single row data frames with columns `mean` and `sd`, the mean and
  standard deviation of the species avoidance rate to be used in the
  basic model (Options 1 and 2) and extended model (Options 3 and 4)
  calculations (see **Details** section). Avoidance rate expresses the
  probability that a bird flying on a collision course with a turbine
  will take evading action to avoid collision, and it is assumed to
  follow a Beta distribution.

- noct_act_pars:

  A single row data frame with columns `mean` and `sd`, The mean and
  standard deviation of the species nocturnal flight activity level,
  expressed as a proportion of daytime activity levels, and assumed to
  be Beta distributed.

- prop_crh_pars:

  Required only for model Option 1, a single row data frame with columns
  `mean` and `sd`. The mean and standard deviation of the proportion of
  flights at collision risk height derived from site survey, assumed to
  be Beta distributed.

- bird_dens_opt:

  Option for specifying the random sampling mechanism for bird
  densities:

  - `"tnorm"`: Sampling of density estimates from a *tnorm-lw0*
    distribution (default value),

  - `"resample"`: Re-sample draws of bird density estimates (e.g.
    bootstrap samples),

  - `"qtiles"`: Sampling from a set of quantile estimates of bird
    densities.

- bird_dens_dt:

  A data frame with monthly estimates of bird density within the
  windfarm footprint, expressed as the number of daytime in-flight
  birds/km^2 per month. Data frame format requirements:

  - If `bird_dens_opt = "tnorm"`, `bird_dens_dt` must contain the
    following columns:

    - `month`, (unique) month names,

    - `mean`, the mean number of birds in flight at any height per
      square kilometre in each month,

    - `sd`, idem, for standard deviation.

  - If `bird_dens_opt = "resample"`, `bird_dens_dt` columns must be
    named as months (i.e. `Jan`, `Feb`, ...), each containing random
    samples of monthly density estimates.

  - If `bird_dens_opt = "qtiles"`, `bird_dens_dt` must comply with:

    - First column named as `p`, giving reference probabilities,

    - Remaining columns named as months (i.e. `Jan`, `Feb`, ...), each
      giving the quantile estimates of bird density in a given month,
      for the reference probabilities in column `p`.

- flight_type:

  A character string, either 'flapping' or 'gliding', indicating the
  species' characteristic flight type.

- prop_upwind:

  Numeric value between 0-1 giving the proportion of flights upwind -
  defaults to 0.5.

- gen_fhd_boots:

  Required only for model Options 2 and 3, a data frame with bootstrap
  samples of flight height distributions (FHD) of the species derived
  from general (country/regional level) data. FHD provides relative
  frequency distribution of bird flights at 1-+ -metre height bands,
  starting from sea surface. The first column must be named as `height`,
  expressing the lower bound of the height band (thus it's first element
  must be 0). Each remaining column should provide a bootstrap sample of
  the proportion of bird flights at each height band, with no column
  naming requirements.

  **NOTE:**
  [generic_fhd_bootstraps](https://marinescotlandscience.github.io/stochLAB/reference/generic_fhd_bootstraps.md)
  is a list object with generic FHD bootstrap estimates for 25 seabird
  species from Johnson et al (2014)
  [doi:10.1111/1365-2664.12191](https://doi.org/10.1111/1365-2664.12191)
  (see usage in Example Section below).

- site_fhd_boots:

  Required only for model Option 4, a data frame similar to
  `gen_fhd_boots`, but for FHD estimates derived from site-specific
  data.

- n_blades:

  An integer, the number of blades in rotor (\\b\\).

- air_gap_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the tip clearance gap, in metres, i.e. the
  distance between the minimum rotor tip height and the highest
  astronomical tide (HAT). Assumed to follow a *tnorm-lw0* distribution.

- rtr_radius_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the radius of the rotor, in metres. Assumed to
  follow a *tnorm-lw0* distribution.

- bld_width_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the maximum blade width, in metres. Assumed to
  be *tnorm-lw0* distribution.

- bld_chord_prf:

  A data frame with the chord taper profile of the rotor blade. It must
  contain the columns:

  - `pp_radius`, equidistant intervals of radius at bird passage point,
    as a proportion of `rotor_radius`, within the range \\\[0, 1\]\\.

  - `chord`, the chord width at `pp_radius`, as a proportion of
    `blade_width`.

  Defaults to a generic profile for a typical modern 5MW turbine. See
  [`chord_prof_5MW()`](https://marinescotlandscience.github.io/stochLAB/reference/chord_prof_5MW.md)
  for details.

- rtn_pitch_opt:

  a character string, the option for specifying the sampling mechanism
  for rotation speed and blade pitch:

  - `"probDist"`: sample rotation speed and blade pitch values from a
    *tnorm-lw0* distribution (default value).

  - `"windSpeedReltn"`: generate rotation speed and blade pitch values
    as a function of wind speed intensity.

- bld_pitch_pars:

  Only required if `rtn_pitch_opt = "probDist"`, a single row data frame
  with columns `mean` and `sd`, the mean and standard deviation of the
  blade pitch angle, i.e. the angle between the blade surface and the
  rotor plane, in degrees. Assumed to follow a *tnorm-lw0* distribution.

- rtn_speed_pars:

  Only required if `rtn_pitch_opt = "probDist"`, a single row data frame
  with columns `mean` and `sd`, the mean and standard deviation of the
  operational rotation speed, in revolutions per minute. Assumed to
  follow a *tnorm-lw0* distribution.

- windspd_pars:

  Only required if `rtn_pitch_opt = "windSpeedReltn"`, a single row data
  frame with columns `mean` and `sd`, the mean and the standard
  deviation of wind speed at the windfarm site, in metres/sec. Assumed
  to follow a *tnorm-lw0* distribution.

- rtn_pitch_windspd_dt:

  Only required if `rtn_pitch_opt = "windSpeedReltn"`, a data frame
  giving the relationship between wind speed, rotation speed and blade
  pitch values. It must contain the columns:

  - `wind_speed`, wind speed in m/s,

  - `rtn_speed`, rotation speed in rpm,

  - `bld_pitch`, blade pitch values in degrees.

- trb_wind_avbl:

  A data frame with the monthly estimates of operational wind
  availability. It must contain the columns:

  - `month`, (unique) month names,

  - `pctg`, the percentage of time wind conditions allow for turbine
    operation per month.

- trb_downtime_pars:

  A data frame with monthly estimates of maintenance downtime, assumed
  to follow a *tnorm-lw0* distribution. It must contain the following
  columns:

  - `month`, (unique) month names,

  - `mean`, numeric, the mean percentage of time in each month when
    turbines are not operating due to maintenance,

  - `sd`, the standard deviation of monthly maintenance downtime.

- wf_n_trbs:

  Integer, the number of turbines on the windfarm.

- wf_width:

  Numeric value, the approximate longitudinal width of the wind farm, in
  kilometres (\\w\\).

- wf_latitude:

  A decimal value. The latitude of the centroid of the windfarm, in
  degrees.

- tidal_offset:

  A numeric value, the tidal offset, the difference between HAT and mean
  sea level, in metres.

- lrg_arr_corr:

  Boolean value. If TRUE, the large array correction will be applied.
  This is a correction factor to account for the decay in bird density
  at later rows in wind farms with a large array of turbines.

- yinc, xinc:

  numeric values, the increments along the y-axis and x-axis for
  numerical integration across segments of the rotor circle. Chosen
  values express proportion of rotor radius. By default these are set to
  0.05, i.e. integration will be performed at a resolution of one
  twentieth of the rotor radius.

- out_format:

  Output format specification. Possible values are:

  - `"draws"`: returns stochastic draws of collisions estimates (default
    value),

  - `"summaries"`: returns summary statistics of collisions estimates.

- out_sampled_pars:

  Logical, whether to output summary statistics of values sampled for
  each stochastic model parameter.

- out_period:

  Controls level of temporal aggregation of collision outputs. Possible
  values are:

  - `"months"`: monthly collisions (default value),

  - `"seasons"`: collisions per user-defined season,

  - `"annum"`: total collisions over 12 months.

- season_specs:

  Only required if `out_period = "seasons"`, a data frame defining the
  seasons for aggregating over collision estimates. It must comprise the
  following columns:

  - `season_id`, (unique) season identifier,

  - `start_month`, name of the season's first month,

  - `end_month`, name of the season's last month.

- verbose:

  Logical, print model run progress on the console?

- log_file:

  Path to log file to store session info and main model run options. If
  set to NULL (default value), log file is not created.

- seed:

  Integer, the random seed for [random number
  generation](https://rdrr.io/r/base/Random.html), for analysis
  reproducibility.

## Value

If `out_sampled_pars = FALSE`, returns a list with estimates of number
of collisions per chosen time periods, with elements containing the
outputs for each CRM Option.

If `out_sampled_pars = TRUE`, returns a list object with two top-level
elements:

- `collisions`, a list comprising collision estimates for each CRM
  Option,

- `sampled_pars`, a list with summary statistics of values sampled for
  stochastic model parameters.

## Details

Collision risk can be calculated under 4 options, specified by
`model_options`:

- **Option 1** - Basic model with proportion at collision risk height
  derived from site survey (`prop_crh_surv`).

- **Option 2** - Basic model with proportion at collision risk height
  derived from a generic flight height distribution (`gen_fhd`).

- **Option 3** - Extended model using a generic flight height
  distribution (`gen_fhd`).

- **Option 4** - Extended model using a site-specific flight height
  distribution (`site_fhd`).

Where,

- Basic model - assumes a uniform distribution of bird flights at
  collision risk height (i.e. above the minimum and below the maximum
  height of the rotor blade).

- Extended model - takes into account the distribution of bird flight
  heights at collision risk height.

## Examples

``` r
# ------------------------------------------------------
# Run with arbitrary parameter values, for illustration
# ------------------------------------------------------

# ------------------------------------------------------
# Setting some of the required inputs upfront
b_dens <- data.frame(
  month = month.abb,
  mean = runif(12, 0.8, 1.5),
  sd = runif(12, 0.2, 0.3)
)
head(b_dens)
#>   month      mean        sd
#> 1   Jan 0.8144011 0.2188043
#> 2   Feb 1.4395130 0.2838642
#> 3   Mar 1.2261714 0.2167280
#> 4   Apr 1.4711761 0.2711816
#> 5   May 1.0310451 0.2209844
#> 6   Jun 1.4167246 0.2222708

# Generic FHD bootstraps from Johnson et al (2014)
fhd_boots <- generic_fhd_bootstraps[[1]]
head(fhd_boots)
#> # A tibble: 6 × 201
#>   height bootId_1 bootId_2 bootId_3 bootId_4 bootId_5 bootId_6 bootId_7 bootId_8
#>    <dbl>    <dbl>    <dbl>    <dbl>    <dbl>    <dbl>    <dbl>    <dbl>    <dbl>
#> 1      0   0.160    0.166    0.174    0.152    0.174    0.168    0.177    0.172 
#> 2      1   0.134    0.138    0.144    0.129    0.144    0.140    0.145    0.142 
#> 3      2   0.113    0.115    0.119    0.109    0.119    0.116    0.120    0.118 
#> 4      3   0.0948   0.0963   0.0981   0.0927   0.0981   0.0967   0.0986   0.0976
#> 5      4   0.0796   0.0803   0.0810   0.0786   0.0810   0.0805   0.0812   0.0808
#> 6      5   0.0669   0.0670   0.0669   0.0667   0.0669   0.0670   0.0669   0.0670
#> # ℹ 192 more variables: bootId_9 <dbl>, bootId_10 <dbl>, bootId_11 <dbl>,
#> #   bootId_12 <dbl>, bootId_13 <dbl>, bootId_14 <dbl>, bootId_15 <dbl>,
#> #   bootId_16 <dbl>, bootId_17 <dbl>, bootId_18 <dbl>, bootId_19 <dbl>,
#> #   bootId_20 <dbl>, bootId_21 <dbl>, bootId_22 <dbl>, bootId_23 <dbl>,
#> #   bootId_24 <dbl>, bootId_25 <dbl>, bootId_26 <dbl>, bootId_27 <dbl>,
#> #   bootId_28 <dbl>, bootId_29 <dbl>, bootId_30 <dbl>, bootId_31 <dbl>,
#> #   bootId_32 <dbl>, bootId_33 <dbl>, bootId_34 <dbl>, bootId_35 <dbl>, …

# wind speed vs rotation speed vs blade pitch
wind_rtn_ptch <- data.frame(
  wind_speed = seq_len(30),
  rtn_speed = 10/(30:1),
  bld_pitch = c(rep(90, 4), rep(0, 8), 5:22)
)
head(wind_rtn_ptch)
#>   wind_speed rtn_speed bld_pitch
#> 1          1 0.3333333        90
#> 2          2 0.3448276        90
#> 3          3 0.3571429        90
#> 4          4 0.3703704        90
#> 5          5 0.3846154         0
#> 6          6 0.4000000         0

# wind availability
windavb <- data.frame(
  month = month.abb,
  pctg = runif(12, 85, 98)
)
head(windavb)
#>   month     pctg
#> 1   Jan 88.60699
#> 2   Feb 93.82388
#> 3   Mar 95.51683
#> 4   Apr 86.26232
#> 5   May 89.28475
#> 6   Jun 96.80974

# maintenance downtime
dwntm <- data.frame(
  month = month.abb,
  mean = runif(12, 6, 10),
  sd = rep(2, 12))
head(dwntm)
#>   month     mean sd
#> 1   Jan 8.400462  2
#> 2   Feb 7.667378  2
#> 3   Mar 9.108983  2
#> 4   Apr 6.124940  2
#> 5   May 7.874024  2
#> 6   Jun 8.178536  2

# seasons specification
seas_dt <- data.frame(
  season_id = c("a", "b", "c"),
  start_month = c("Jan", "May", "Oct"), end_month = c("Apr", "Sep", "Dec")
  )
head(seas_dt)
#>   season_id start_month end_month
#> 1         a         Jan       Apr
#> 2         b         May       Sep
#> 3         c         Oct       Dec

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
  log_file = file.path(getwd(), "scrm_example.log")
)
#> 
#> ── Stochastic CRM ──
#> 
#> ℹ Checking inputs
#> ✔ Checking inputs [14ms]
#> 
#> ℹ Preparing data
#> ✔ Preparing data [28ms]
#> 
#> ℹ Sampling parameters
#> ✔ Sampling parameters [122ms]
#> 
#> ⠙ Calculating collisions | 3/1000 iterations
#> ⠹ Calculating collisions | 309/1000 iterations
#> ✔ Calculating collisions | 1000/1000 iterations [1.4s]
#> 
#> ℹ Sorting outputs
#> ✔ Sorting outputs [644ms]
#> 
#> ✔ Job done!
#> $collisions
#> $collisions$opt1
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 a         Jan_A…  36.6 16.4    37.5     9.72    24.1    47.9      68.7    84.5
#> 2 b         May_S…  67.0 30.0    69.5    18.2     46.0    87.6     126.    151. 
#> 3 c         Oct_D…  18.2  8.55   18.6     4.52    11.8    24.0      34.9    47.5
#> 
#> $collisions$opt2
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 a         Jan_A… 0.981 1.91   0.504   0.0950   0.290   0.875      6.89   16.8 
#> 2 b         May_S… 1.80  3.49   0.924   0.170    0.534   1.63      11.1    29.7 
#> 3 c         Oct_D… 0.491 0.987  0.247   0.0448   0.141   0.436      3.56    9.49
#> 
#> $collisions$opt3
#> # A tibble: 3 × 10
#>   season_id period  mean    sd median pctl_2.5 pctl_25 pctl_75 pctl_97.5 pctl_99
#>   <chr>     <chr>  <dbl> <dbl>  <dbl>    <dbl>   <dbl>   <dbl>     <dbl>   <dbl>
#> 1 a         Jan_A… 0.480 1.21  0.183    0.0408  0.118    0.327      3.82   11.0 
#> 2 b         May_S… 0.880 2.22  0.339    0.0752  0.213    0.615      7.68   18.9 
#> 3 c         Oct_D… 0.239 0.615 0.0916   0.0209  0.0555   0.169      2.09    5.91
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
#>  1 Jan    0.807 0.215  0.805    0.405      1.24
#>  2 Feb    1.45  0.274  1.44     0.938      1.99
#>  3 Mar    1.23  0.218  1.23     0.787      1.63
#>  4 Apr    1.48  0.268  1.48     0.970      2.02
#>  5 May    1.03  0.215  1.03     0.622      1.45
#>  6 Jun    1.41  0.218  1.41     0.946      1.85
#>  7 Jul    1.14  0.218  1.14     0.726      1.57
#>  8 Aug    1.42  0.225  1.42     0.972      1.88
#>  9 Sep    1.39  0.214  1.39     0.981      1.80
#> 10 Oct    1.25  0.241  1.25     0.789      1.72
#> 11 Nov    0.827 0.294  0.820    0.264      1.42
#> 12 Dec    1.03  0.288  1.03     0.458      1.59
#> 
#> $sampled_pars$prop_oper_mth
#> # A tibble: 12 × 6
#>    period  mean     sd median pctl_2.5 pctl_97.5
#>    <chr>  <dbl>  <dbl>  <dbl>    <dbl>     <dbl>
#>  1 Jan    0.803 0.0207  0.802    0.763     0.840
#>  2 Feb    0.862 0.0206  0.861    0.822     0.900
#>  3 Mar    0.864 0.0205  0.864    0.821     0.904
#>  4 Apr    0.801 0.0195  0.801    0.764     0.839
#>  5 May    0.814 0.0195  0.814    0.775     0.851
#>  6 Jun    0.886 0.0198  0.886    0.846     0.924
#>  7 Jul    0.854 0.0199  0.854    0.814     0.895
#>  8 Aug    0.774 0.0194  0.774    0.736     0.812
#>  9 Sep    0.829 0.0206  0.829    0.788     0.867
#> 10 Oct    0.789 0.0200  0.789    0.748     0.827
#> 11 Nov    0.804 0.0201  0.804    0.765     0.844
#> 12 Dec    0.847 0.0201  0.848    0.809     0.884
#> 
#> $sampled_pars$downtime
#> # A tibble: 12 × 6
#>    period  mean    sd median pctl_2.5 pctl_97.5
#>    <chr>  <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#>  1 Jan     8.32  2.07   8.36     4.62     12.3 
#>  2 Feb     7.67  2.06   7.68     3.81     11.6 
#>  3 Mar     9.11  2.05   9.11     5.09     13.4 
#>  4 Apr     6.12  1.95   6.16     2.38      9.91
#>  5 May     7.89  1.95   7.87     4.18     11.7 
#>  6 Jun     8.26  1.98   8.24     4.40     12.2 
#>  7 Jul     7.09  1.99   7.08     3.04     11.1 
#>  8 Aug     9.00  1.94   8.96     5.19     12.8 
#>  9 Sep     8.39  2.06   8.37     4.54     12.5 
#> 10 Oct     7.81  2.00   7.82     4.01     11.9 
#> 11 Nov     7.05  2.01   7.03     3.08     10.9 
#> 12 Dec     8.03  2.01   7.94     4.32     11.8 
#> 
#> $sampled_pars$wind_speed
#> # A tibble: 1 × 5
#>    mean    sd median pctl_2.5 pctl_97.5
#>   <dbl> <dbl>  <dbl>    <dbl>     <dbl>
#> 1  7.74  3.00   7.76     2.15      13.7
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
#> 1 0.327 0.634      0        0      1.57
#> 
#> $sampled_pars$avoid_bsc
#> # A tibble: 1 × 5
#>    mean       sd median pctl_2.5 pctl_97.5
#>   <dbl>    <dbl>  <dbl>    <dbl>     <dbl>
#> 1 0.990 0.000986  0.990    0.988     0.992
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
#> 1 0.0605 0.00909 0.0602   0.0441    0.0801
#> 
#> $sampled_pars$gen_fhd
#> # A tibble: 500 × 6
#>    height   mean      sd median pctl_2.5 pctl_97.5
#>     <dbl>  <dbl>   <dbl>  <dbl>    <dbl>     <dbl>
#>  1      0 0.163  0.0187  0.166    0.109     0.187 
#>  2      1 0.136  0.0129  0.138    0.0967    0.152 
#>  3      2 0.114  0.00851 0.115    0.0863    0.124 
#>  4      3 0.0950 0.00527 0.0963   0.0769    0.100 
#>  5      4 0.0794 0.00294 0.0803   0.0686    0.0816
#>  6      5 0.0664 0.00146 0.0669   0.0606    0.0670
#>  7      6 0.0556 0.00116 0.0558   0.0530    0.0567
#>  8      7 0.0465 0.00166 0.0466   0.0439    0.0490
#>  9      8 0.0390 0.00215 0.0389   0.0357    0.0431
#> 10      9 0.0327 0.00249 0.0324   0.0290    0.0386
#> # ℹ 490 more rows
#> 
#> 
```
