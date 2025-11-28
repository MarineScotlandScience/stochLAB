# Input validator

Input validator

## Usage

``` r
validate_inputs(
  model_options,
  n_iter = NULL,
  flt_speed_pars = NULL,
  flight_speed = NULL,
  body_lt_pars = NULL,
  body_lt = NULL,
  wing_span_pars = NULL,
  wing_span = NULL,
  avoid_bsc_pars = NULL,
  avoid_rt_basic = NULL,
  avoid_ext_pars = NULL,
  avoid_rt_ext = NULL,
  noct_act_pars = NULL,
  noct_activity = NULL,
  prop_crh_pars = NULL,
  bird_dens_opt = NULL,
  bird_dens_dt = NULL,
  chord_prof = NULL,
  dens_month = NULL,
  turb_oper_month = NULL,
  flight_type = NULL,
  prop_upwind = NULL,
  gen_fhd_boots = NULL,
  site_fhd_boots = NULL,
  n_blades = NULL,
  air_gap_pars = NULL,
  rtr_radius_pars = NULL,
  rotor_radius = NULL,
  blade_width = NULL,
  blade_pitch = NULL,
  hub_height = NULL,
  bld_width_pars = NULL,
  rtn_pitch_opt = NULL,
  bld_pitch_pars = NULL,
  rtn_speed_pars = NULL,
  rotor_speed = NULL,
  n_turbines = NULL,
  windspd_pars = NULL,
  rtn_pitch_windspd_dt = NULL,
  trb_wind_avbl = NULL,
  trb_downtime_pars = NULL,
  wf_n_trbs = NULL,
  wf_width = NULL,
  wf_latitude = NULL,
  tidal_offset = NULL,
  gen_fhd = NULL,
  site_fhd = NULL,
  lrg_arr_corr = NULL,
  xinc = NULL,
  yinc = NULL,
  seed = NULL,
  verbose = NULL,
  out_format = NULL,
  out_sampled_pars = NULL,
  out_period = NULL,
  season_specs = NULL,
  popn_estim_pars = NULL,
  fn = "scrm"
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

- flight_speed:

  Numeric value. The bird flying speed (\\v\\), in metres/sec.

- body_lt_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the species body length, in metres. Assumed to
  follow a *tnorm-lw0* distribution.

- body_lt:

  Numeric value. The length of the bird (\\L\\), in metres.

- wing_span_pars:

  A single row data frame with columns `mean` and `sd`, the mean and
  standard deviation of the species wingspan, in metres. Assumed to
  follow a *tnorm-lw0* distribution.

- wing_span:

  Numeric value. The wingspan of the bird (\\W\\), in metres.

- avoid_bsc_pars, avoid_ext_pars:

  Single row data frames with columns `mean` and `sd`, the mean and
  standard deviation of the species avoidance rate to be used in the
  basic model (Options 1 and 2) and extended model (Options 3 and 4)
  calculations (see **Details** section). Avoidance rate expresses the
  probability that a bird flying on a collision course with a turbine
  will take evading action to avoid collision, and it is assumed to
  follow a Beta distribution.

- avoid_rt_basic, avoid_rt_ext:

  Numeric values. The avoidance rate for, respectively, the basic model
  (i.e. required for model Options 1 and 2) and the extended model (i.e.
  required for Options 3 and 4). Avoidance rate expresses the
  probability that a bird flying on a collision course with a turbine
  will take evading action to avoid collision.

- noct_act_pars:

  A single row data frame with columns `mean` and `sd`, The mean and
  standard deviation of the species nocturnal flight activity level,
  expressed as a proportion of daytime activity levels, and assumed to
  be Beta distributed.

- noct_activity:

  A numeric value. The nocturnal flight activity level, expressed as a
  proportion of daytime activity levels (\\f_night\\).

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

- chord_prof:

  A data frame with the chord taper profile of the rotor blade. Function
  expects two named columns:

  - `pp_radius`, equidistant intervals of radius at bird passage point,
    as a proportion of `rotor_radius`, within the range \\\[0, 1\]\\.

  - `chord`, the chord width at `pp_radius`, as a proportion of
    `blade_width`.

  Defaults to a generic profile for a typical modern 5MW turbine. See
  [`chord_prof_5MW()`](https://marinescotlandscience.github.io/stochLAB/reference/chord_prof_5MW.md)
  for details.

- dens_month:

  Data frame, containing estimates of daytime in-flight bird densities
  per month within the windfarm footprint, in birds/km^2. It must
  contain the following named columns:

  - `month`, the month names.

  - `dens`, the number of birds in flight at any height per square
    kilometre in each month.

- turb_oper_month:

  Data frame, holding the proportion of time during which turbines are
  operational per month. The following named column are expected:

  - `month`, the month names.

  - `prop_oper`, the proportion of time operating, per month.

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

- rotor_radius:

  Numeric value. The radius of the rotor (\\R\\), in metres.

- blade_width:

  Numeric value, giving the maximum blade width, in metres.

- blade_pitch:

  Numeric value. The average blade pitch angle, the angle between the
  blade surface and the rotor plane (\\\gamma\\), in radians.

- hub_height:

  A numeric value, the height of the rotor hub (\\H\\), given by the sum
  of rotor radius and minimum blade clearance above the highest
  astronomical tide (HAT), in metres.

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

- rotor_speed:

  Numeric value. The operational rotation speed, in revolutions/min.

- n_turbines:

  Integer, the number of turbines on the wind farm (\\T\\).

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

- gen_fhd, site_fhd:

  Data frame objects, with flight height distributions (fhd) of the
  species - the relative frequency distribution of bird flights at
  1-metre height intervals from sea surface. Specifically:

  - `gen_fhd`, Data frame with the species' generic fhd derived from
    combining wider survey data. Only required for model Options 2 and 3

  - `site_fhd`, Data frame with the species' site-specific fhd derived
    from local survey data. Only required for model Option 4

  Data frames must contain the following named columns:

  - `height`, integers representing height bands from sea surface, in
    metres. Function expects 0 as the first value, representing the 0-1m
    band.

  - `prop`, the proportion of flights at each height band.

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

- seed:

  Integer, the random seed for [random number
  generation](https://rdrr.io/r/base/Random.html), for analysis
  reproducibility.

- verbose:

  Logical, print model run progress on the console?

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

- popn_estim_pars:

  A single row data frame with columns `mean` and `sd`. The population
  estimate of the species expected to fly through the wind farm area.

- fn:

  a character string specifying the parent function whose inputs are
  being checked:

  - `"scrm"`: checks
    [`stoch_crm()`](https://marinescotlandscience.github.io/stochLAB/reference/stoch_crm.md)
    inputs

  - `"crm"`: checks
    [`band_crm()`](https://marinescotlandscience.github.io/stochLAB/reference/band_crm.md)
    inputs

  - `"mcrm"`: checks
    [`mig_stoch_crm()`](https://marinescotlandscience.github.io/stochLAB/reference/mig_stoch_crm.md)
    inputs

## Value

Nothing returned from this function

## Examples

``` r
  validate_inputs(model_options=c(1),
               avoid_bsc_pars=data.frame(mean=0.99,sd=0.001),
               prop_crh_pars=data.frame(mean=0.01,sd=0.01),
               air_gap_pars = data.frame(mean=21,sd=0),
               rtr_radius_pars = data.frame(mean=100,sd=0),
               bld_pitch_pars = data.frame(mean=15,sd=0),
               rtn_pitch_opt = "probDist",
               rtn_speed_pars = data.frame(mean=14,sd=5),
               out_period = "months",
               lrg_arr_corr = TRUE,
               fn="scrm")
```
