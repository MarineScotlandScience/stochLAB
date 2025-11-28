# Number of collisions under model option 1

Wrapper function to run CRM calculations under option 1:

- Basic model, i.e. flights across collision risk height are uniformly
  distributed.

- Proportion at collision risk height derived from site survey.

## Usage

``` r
crm_opt1(
  flux_factor,
  prop_crh_surv,
  avg_prob_coll,
  mth_prop_oper,
  avoidance_rate,
  lac_factor
)
```

## Arguments

- flux_factor:

  a vector containing the flux factor for each month

- prop_crh_surv:

  The proportion of flights at collision risk height derived from site
  survey (\\Q_2R\\). Only required for model Option 1.

- avg_prob_coll:

  A numeric value, the average probability of collision for a single
  bird transit through a rotor, assuming no avoidance action
  (\\p\_{average}\\).

- mth_prop_oper:

  A numeric vector, the proportion of time during which turbines are
  operational per month.

- avoidance_rate:

  A numeric value within the interval \\\[0, 1\]\\. The avoidance rate,
  expressing the probability that a bird flying on a collision course
  with a turbine will take evading action to avoid collision.

- lac_factor:

  A numerical value, the large array correction factor. Defaults to 1,
  meaning large array correction is not applicable.

## Value

A numeric vector, the expected number of collisions per month based on
model option 1

## See also

[`get_flux_factor()`](https://marinescotlandscience.github.io/stochLAB/reference/get_flux_factor.md)
for calculating the flux factor

## Examples

``` r
 flux_fct <- get_flux_factor(
      n_turbines = 100,
      rotor_radius = 120,
      flight_speed = 13.1,
      bird_dens = c(1.19,0.85,1.05,1.45,1.41,1.45,1.12,1.45,0.93,0.902,1.06,1.23),
      daynight_hrs = Day_Length(52),
      noct_activity = 0.5
      )

turb_oper <- data.frame(
   month = month.abb,
   prop_oper = runif(12,0.5,0.8)
   )
turb_oper_month <- turb_oper$prop_oper

crm_opt1(
 flux_factor = flux_fct,
 prop_crh_surv = 0.13,
 avg_prob_coll = 0.1494609,
 mth_prop_oper = turb_oper_month,
 avoidance_rate = 0.989,
 lac_factor = 0.9998287)
#>  [1]  57.69569  43.48938  65.38279 108.07415 105.95939 105.71663  94.41790
#>  [8] 129.51333  53.91690  52.08592  69.91428  74.93073
```
