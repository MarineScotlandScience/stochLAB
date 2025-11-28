# Get expected collisions based on the basic model

Provides the number of collisions expected to occur by month. The basic
model assumes a uniform distribution of bird flights at risk height
(i.e. between min and max rotor height).

## Usage

``` r
get_collisions_basic(
  n_transits,
  avg_prob_coll,
  mth_prop_oper,
  avoidance_rate,
  lac_factor = 1
)
```

## Arguments

- n_transits:

  A numeric vector, the estimated number of bird flights crossing the
  rotors of the wind farm at each time period.

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

A numeric vector. The expected number of collisions at each time periods

## Examples

``` r
turb_oper <- data.frame(
 month = month.abb,
 prop_oper = runif(12,0.5,0.8)
 )
 mth_oper_month <- turb_oper$prop_oper

 flux_factor <- get_flux_factor(
   n_turbines = 100,
   rotor_radius = 120,
   flight_speed = 13.1,
   bird_dens = c(1.19,0.85,1.05,1.45,1.41,1.45,1.12,1.45,0.93,0.902,1.06,1.23),
   daynight_hrs = Day_Length(52),
   noct_activity = 0.5
 )

 prop_crh_surv <- 0.13

 n_transits_opt1 <- flux_factor * prop_crh_surv

 get_collisions_basic(
   n_transits = n_transits_opt1,
   avg_prob_coll = 0.1494609,
   mth_prop_oper = mth_oper_month,
   avoidance_rate = 0.989,
   lac_factor = 0.9998287
 )
#>  [1]  74.25537  57.75174  79.25489  89.53172 109.31832 125.99678  67.42349
#>  [8] 105.78254  72.17156  68.59884  61.68448  65.22787
```
