# Stochastic migration collision risk model

Run migration stochastic collision risk model for a single species and
one turbine scenario

## Usage

``` r
mig_stoch_crm(
  wing_span_pars,
  flt_speed_pars,
  body_lt_pars,
  prop_crh_pars,
  avoid_bsc_pars,
  n_turbines,
  n_blades = 3,
  rtn_speed_pars,
  bld_pitch_pars,
  rtr_radius_pars,
  bld_width_pars,
  wf_width,
  wf_latitude,
  flight_type,
  prop_upwind = 0.5,
  popn_estim_pars,
  season_specs,
  chord_profile = chord_prof_5MW,
  trb_wind_avbl,
  trb_downtime_pars,
  n_iter = 10,
  LargeArrayCorrection = TRUE,
  log_file = NULL,
  seed = NULL,
  verbose = TRUE
)
```

## Arguments

- wing_span_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the species wingspan, in metres. Assumed to
  follow a *tnorm-lw0* distribution.

- flt_speed_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the species flying speed, in metres/sec. Assumed
  to follow a Truncated Normal with lower bound at 0 (*tnorm-lw0*).

- body_lt_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the species body length, in metres. Assumed to
  follow a *tnorm-lw0* distribution.

- prop_crh_pars:

  A single row data frame with columns `mean` and `sd`. The mean and
  standard deviation of the proportion of flights at collision risk
  height.

- avoid_bsc_pars:

  Single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the species avoidance rate to be used. Avoidance
  rate expresses the probability that a bird flying on a collision
  course with a turbine will take evading action to avoid collision, and
  it is assumed to follow a Beta distribution.

- n_turbines:

  An integer. The number of turbines expected to be installed

- n_blades:

  An integer. The number of blades per turbine (defaults to 3)

- rtn_speed_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the rotation speed.

- bld_pitch_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the blade pitch angle, i.e. the angle between
  the blade surface and the rotor plane, in degrees. Assumed to follow a
  *tnorm-lw0* distribution.

- rtr_radius_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the radius of the rotor, in metres. Assumed to
  follow a *tnorm-lw0* distribution.

- bld_width_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the maximum blade width, in metres. Assumed to
  be *tnorm-lw0* distribution.

- wf_width:

  Numeric value, the approximate longitudinal width of the wind farm, in
  kilometres (\\w\\).

- wf_latitude:

  A decimal value. The latitude of the centroid of the windfarm, in
  degrees.

- flight_type:

  A character. Either "flying" or "gliding" representing the type of
  flight most commonly used by the species

- prop_upwind:

  Numeric value between 0-1 giving the proportion of flights upwind -
  defaults to 0.5.

- popn_estim_pars:

  A single row data frame with columns `mean` and `sd`. The population
  estimate of the species expected to fly through the wind farm area.

- season_specs:

  A data frame defining the seasons for aggregating over collision
  estimates. It must comprise the following columns:

  - `season_id`, (unique) season identifier,

  - `start_month`, name of the season's first month,

  - `end_month`, name of the season's last month.

- chord_profile:

  A data frame with the chord taper profile of the rotor blade. It must
  contain the columns:

  - `pp_radius`, equidistant intervals of radius at bird passage point,
    as a proportion of `rotor_radius`, within the range \\\[0, 1\]\\.

  - `chord`, the chord width at `pp_radius`, as a proportion of
    `blade_width`. Defaults to a generic profile for a typical modern
    5MW turbine. See
    [`chord_prof_5MW()`](https://marinescotlandscience.github.io/stochLAB/reference/chord_prof_5MW.md)
    for details.

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

- n_iter:

  An integer \> 0. The number of iterations for the model simulation.

- LargeArrayCorrection:

  A boolean. Should the large array correction be calculated

- log_file:

  Path to log file to store session info and main model run options. If
  set to NULL (default value), log file is not created.

- seed:

  Integer, the random seed for [random number
  generation](https://rdrr.io/r/base/Random.html), for analysis
  reproducibility.

- verbose:

  boolean. TRUE for a verbose output

## Value

Estimates of number of collisions per migratory season for the n number
of iterations specified

## Details

This function is an adaption of code from Masden(2015) used for
estimating the collision risk of seabirds in offshore windfarm sites and
is a further adaptation from Band(2012). It is a further adaptation of
the stoch_crm function.

The collision risk model evaluates risk for each defined migratory
period where flux rate is simply the number of birds travelling through
the windfarm.

Changes in relation to previous top-line function `stoch_crm`

- function will run only option 1 for migratory species

## Examples

``` r
# ------------------------------------------------------
# Run with arbitrary parameter values, for illustration
# ------------------------------------------------------
season_specs <- data.frame(
  season_id = c("PrBMigration", "PoBMigration","OMigration"),
  start_month = c("Mar", "May", "Oct"), end_month = c("Apr", "Sep", "Feb")
)

# wind availability
windavb <- data.frame(
  month = month.abb,
  pctg = runif(12, 85, 98)
)
head(windavb)
#>   month     pctg
#> 1   Jan 85.91886
#> 2   Feb 97.95959
#> 3   Mar 92.95408
#> 4   Apr 87.24326
#> 5   May 96.82273
#> 6   Jun 85.48687

# maintenance downtime
dwntm <- data.frame(
  month = month.abb,
  mean = runif(12, 6, 10),
  sd = rep(2, 12))
head(dwntm)
#>   month     mean sd
#> 1   Jan 8.911964  2
#> 2   Feb 6.868338  2
#> 3   Mar 7.824921  2
#> 4   Apr 7.331199  2
#> 5   May 8.273411  2
#> 6   Jun 7.008823  2


mig_stoch_crm(
  # Wing span in m,
  wing_span_pars = data.frame(mean = 1.08, sd = 0.04),
  # Flight speed in m/s
  flt_speed_pars = data.frame(mean = 7.26, sd = 1.5),
  # Body length in m,
  body_lt_pars = data.frame(mean = 0.39, sd = 0.005),
  # Proportion of birds at CRH
  prop_crh_pars = data.frame(mean = 0.06, sd = 0.009),
  # avoidance rate
  avoid_bsc_pars = data.frame(mean = 0.99, sd = 0.001),
  n_turbines = 150,
  n_blades = 3,
  # rotation speed in m/s of turbine blades
  rtn_speed_pars = data.frame(mean = 13.1, sd = 4),
  # pitch in degrees of turbine blades
  bld_pitch_pars = data.frame(mean = 3, sd = 0.3),
  # sd = 0, rotor radius is fixed
  rtr_radius_pars = data.frame(mean = 80, sd = 0),
  # sd = 0, blade width is fixed
  bld_width_pars = data.frame(mean = 8, sd = 0),
  wf_width = 100,
  wf_latitude = 54.1,
  prop_upwind = 0.5,
  flight_type = "flapping",
  # population flying through windfarm,
  popn_estim_pars = data.frame(mean = 21584, sd = 2023),
  season_specs = season_specs,
  chord_profile = chord_prof_5MW,
  trb_wind_avbl = windavb,
  trb_downtime_pars = dwntm,
  n_iter = 1000,
  LargeArrayCorrection = TRUE,
  log_file = NULL,
  seed = 1234,
  verbose = TRUE)
#> 
#> ── Stochastic CRM for Migratory birds ──
#> 
#> ℹ Checking for manditory arguments
#> ✔ Checking for manditory arguments [7ms]
#> 
#> ℹ Data validation
#> ✔ Data validation [16ms]
#> 
#> ℹ Sampling data
#> ✔ Sampling data [27ms]
#> 
#> ℹ Running simulation...
#> ✔ Running simulation... [1.3s]
#> 
#> ℹ Creating outputs
#> ✔ Creating outputs [14ms]
#> 
#> $collisions
#> # A tibble: 1,000 × 3
#>    PrBMigration PoBMigration OMigration
#>           <dbl>        <dbl>      <dbl>
#>  1        0.196        0.200      0.200
#>  2        0.202        0.207      0.207
#>  3        0.225        0.230      0.231
#>  4        0.190        0.193      0.194
#>  5        0.207        0.211      0.212
#>  6        0.272        0.277      0.278
#>  7        0.225        0.229      0.230
#>  8        0.175        0.178      0.179
#>  9        0.246        0.251      0.252
#> 10        0.381        0.388      0.390
#> # ℹ 990 more rows
#> 
#> $flux_rates
#> # A tibble: 1,000 × 3
#>    PrBMigration PoBMigration OMigration
#>           <dbl>        <dbl>      <dbl>
#>  1        4015.        4015.      4015.
#>  2        3959.        3959.      3959.
#>  3        4240.        4240.      4240.
#>  4        3946.        3946.      3946.
#>  5        4080.        4080.      4080.
#>  6        4028.        4028.      4028.
#>  7        4269.        4269.      4269.
#>  8        3899.        3899.      3899.
#>  9        3915.        3915.      3915.
#> 10        4145.        4145.      4145.
#> # ℹ 990 more rows
#> 
```
