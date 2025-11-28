# Sampling function for a single turbine in the mCRM

Samples and aggregates appropriate data for a single wind turbine

## Usage

``` r
sample_turbine_mCRM(
  rtn_speed_pars,
  bld_pitch_pars,
  rtr_radius_pars,
  bld_width_pars,
  season_specs,
  n_iter = 10,
  trb_wind_avbl,
  trb_downtime_pars
)
```

## Arguments

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

- season_specs:

  A data frame defining the seasons for aggregating over collision
  estimates. It must comprise the following columns:

  - `season_id`, (unique) season identifier,

  - `start_month`, name of the season's first month,

  - `end_month`, name of the season's last month.

- n_iter:

  An integer value. The number of samples to generate

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

## Value

A data frame of all the information sampled for the turbine with nrow =
n_iter

## Examples

``` r
season_specs <- data.frame(
  season_id = c("PrBMigration", "PoBMigration","OMigration"),
  start_month = c("Mar", "May", "Oct"), end_month = c("Apr", "Sep", "Feb")
  )

  windavb <- data.frame(
    month = month.abb,
    pctg = runif(12, 85, 98)
  )

  dwntm <- data.frame(
    month = month.abb,
    mean = runif(12, 6, 10),
    sd = rep(2, 12))

  sample_turbine_mCRM(rtn_speed_pars = data.frame(mean = 13.1, sd = 4),
                      bld_pitch_pars = data.frame(mean = 3, sd = 0.3),
                      rtr_radius_pars = data.frame(mean = 80, sd = 0),
                      bld_width_pars = data.frame(mean = 8, sd = 0),
                      season_specs = season_specs,
                      n_iter = 10,
                      trb_wind_avbl = windavb,
                      trb_downtime_pars = dwntm)
#>    RotorRadius BladeWidth RotorSpeed      Pitch PrBMigration_DT PrBMigration_OT
#> 1           80          8   9.654915 0.05613066      0.06721754       0.9446499
#> 2           80          8   5.849815 0.05870977      0.10811834       0.9446499
#> 3           80          8  21.483483 0.05538532      0.06530582       0.9446499
#> 4           80          8  17.289497 0.06055223      0.11093565       0.9446499
#> 5           80          8  12.881980 0.05536382      0.03754597       0.9446499
#> 6           80          8  19.795382 0.05153243      0.10249496       0.9446499
#> 7           80          8  19.979274 0.05841543      0.07929669       0.9446499
#> 8           80          8  13.996232 0.05320465      0.07498462       0.9446499
#> 9           80          8  10.218037 0.05143919      0.05353443       0.9446499
#> 10          80          8   7.748047 0.05428239      0.07866270       0.9446499
#>    PoBMigration_DT PoBMigration_OT OMigration_DT OMigration_OT
#> 1       0.05638221       0.9145276    0.04347249     0.8999504
#> 2       0.08399153       0.9145276    0.07447152     0.8999504
#> 3       0.05552873       0.9145276    0.13103293     0.8999504
#> 4       0.09954564       0.9145276    0.04400414     0.8999504
#> 5       0.05459148       0.9145276    0.05524419     0.8999504
#> 6       0.06155858       0.9145276    0.06113508     0.8999504
#> 7       0.09166357       0.9145276    0.07713489     0.8999504
#> 8       0.09968483       0.9145276    0.05828077     0.8999504
#> 9       0.05842914       0.9145276    0.10334259     0.8999504
#> 10      0.02993909       0.9145276    0.04226250     0.8999504
```
