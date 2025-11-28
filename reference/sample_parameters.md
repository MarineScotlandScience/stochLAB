# Parameter sampling whiz

Generates the random samples of all the stochastic CRM parameters. For
internal use.

## Usage

``` r
sample_parameters(
  model_options,
  mod_mths,
  n_iter = 10,
  flt_speed_pars,
  body_lt_pars,
  wing_span_pars,
  avoid_bsc_pars,
  avoid_ext_pars,
  noct_act_pars,
  prop_crh_pars,
  bird_dens_opt = "tnorm",
  bird_dens_dt,
  gen_fhd_boots = NULL,
  site_fhd_boots = NULL,
  rtr_radius_pars,
  air_gap_pars,
  bld_width_pars,
  rtn_pitch_opt = "probDist",
  bld_pitch_pars,
  rtn_speed_pars,
  windspd_pars,
  rtn_pitch_windspd_dt,
  trb_wind_avbl,
  trb_downtime_pars,
  lrg_arr_corr
)
```

## Arguments

- model_options:

  Character vector, the model options for calculating collision risk
  (see **Details** section below).

- mod_mths:

  character vector, the names of months under modelling

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

- rtr_radius_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the radius of the rotor, in metres. Assumed to
  follow a *tnorm-lw0* distribution.

- air_gap_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the tip clearance gap, in metres, i.e. the
  distance between the minimum rotor tip height and the highest
  astronomical tide (HAT). Assumed to follow a *tnorm-lw0* distribution.

- bld_width_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the maximum blade width, in metres. Assumed to
  be *tnorm-lw0* distribution.

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

- lrg_arr_corr:

  Boolean value. If TRUE, the large array correction will be applied.
  This is a correction factor to account for the decay in bird density
  at later rows in wind farms with a large array of turbines.

## Value

A list object with each element comprising sampled values of given CRM
parameter

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
  bird_dens_dt <- data.frame(
    month = month.abb,
    mean = runif(12, 0.8, 1.5),
    sd = runif(12, 0.2, 0.3)
  )

 # wind availability
  trb_wind_avbl <- data.frame(
    month = month.abb,
    pctg = runif(12, 85, 98)
  )

  # maintenance downtime
  trb_downtime_pars <- data.frame(
    month = month.abb,
    mean = runif(12, 6, 10),
    sd = rep(2, 12))

  # Wind speed relationships
  wind_rtn_ptch <- data.frame(
    wind_speed = seq_len(30),
    rtn_speed = 10/(30:1),
    bld_pitch = c(rep(90, 4), rep(0, 8), 5:22)
    )


  bird_dens_opt <- "tnorm"
  ### extract and standardize month format from monthly data sets
  b_dens_mth <- switch (bird_dens_opt,
                        tnorm = bird_dens_dt$month,
                        resample = names(bird_dens_dt),
                        qtiles = names(bird_dens_dt)[names(bird_dens_dt) != "p"]
  ) %>% format_months()
  dwntm_mth <- format_months(trb_downtime_pars$month)
  windav_mth <- format_months(trb_wind_avbl$month)
  ### Set months to model: only those in common amongst monthly data sets
  mod_mths <- Reduce(intersect, list(b_dens_mth, dwntm_mth, windav_mth))
  ### Order chronologically
  mod_mths <- mod_mths[order(match(mod_mths, month.abb))]

  param_draws <- sample_parameters(
    model_options = c(1,2,3),
    n_iter = 10,
    mod_mths = mod_mths,
    flt_speed_pars = data.frame(mean=7.26,sd=1.5),
    body_lt_pars = data.frame(mean=0.39,sd=0.005),
    wing_span_pars = data.frame(mean=1.08,sd=0.04),
    avoid_bsc_pars = data.frame(mean=0.99,sd=0.001),
    avoid_ext_pars = data.frame(mean=0.96,sd=0.002),
    noct_act_pars = data.frame(mean=0.033,sd=0.005),
    prop_crh_pars = data.frame(mean=0.06,sd=0.009),
    bird_dens_opt = "tnorm",
    bird_dens_dt = bird_dens_dt,
    gen_fhd_boots = generic_fhd_bootstraps[[1]],
    site_fhd_boots = NULL,
    rtr_radius_pars = data.frame(mean=80,sd=0),
    air_gap_pars = data.frame(mean=36,sd=0),
    bld_width_pars = data.frame(mean=8,sd=0),
    rtn_pitch_opt = "windSpeedReltn",
    bld_pitch_pars = NULL,
    rtn_speed_pars = NULL,
    windspd_pars = data.frame(mean=7.74,sd=3),
    rtn_pitch_windspd_dt = wind_rtn_ptch,
    trb_wind_avbl = trb_wind_avbl,
    trb_downtime_pars = trb_downtime_pars,
    lrg_arr_corr = TRUE
    )
```
