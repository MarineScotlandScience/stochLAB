# Number of collisions under model option 2

Wrapper function to run CRM calculations under option 2, i.e.:

- Basic model, i.e. flights across collision risk height are uniformly
  distributed

- Proportion at collision risk height derived from a flight height
  distribution (\\Q'\_2R\\)

## Usage

``` r
crm_opt2(
  d_y,
  flux_factor,
  avg_prob_coll,
  mth_prop_oper,
  avoidance_rate,
  lac_factor
)
```

## Arguments

- d_y:

  a vector with the proportion of bird flights at height bands within
  the rotor disc

- flux_factor:

  a vector containing the flux factor for each month

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
model Option 2.

## See also

[`get_fhd_rotor()`](https://marinescotlandscience.github.io/stochLAB/reference/get_fhd_rotor.md),
[`get_flux_factor()`](https://marinescotlandscience.github.io/stochLAB/reference/get_flux_factor.md)

## Examples

``` r
avg_collision_risk <-
 get_avg_prob_collision(
   flight_speed = 13.1,
   body_lt = 0.85,
   wing_span = 1.01,
   prop_upwind = 0.5,
   flap_glide = 1,
   rotor_speed = 15,
   rotor_radius = 120,
   blade_width = 5,
   blade_pitch = 15,
   n_blades = 3,
   chord_prof = chord_prof_5MW
 )


 gen_fhd_dat <- Johnston_Flight_heights_SOSS %>%
      dplyr::filter(variable=="Gannet.est") %>%
      dplyr::select(height,prop)

 gen_fhd <- gen_fhd_dat$prop

 gen_fhd_at_rotor <-
    get_fhd_rotor(
      hub_height = 150,
      fhd = gen_fhd,
      rotor_radius = 120,
      tidal_offset = 2.5,
      yinc = 0.05)


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

crm_opt2(
    flux_factor = flux_fct,
    d_y = gen_fhd_at_rotor,
    avg_prob_coll = avg_collision_risk,
    mth_prop_oper = turb_oper_month,
    avoidance_rate = 0.989,
   lac_factor = 0.9998299)
#>  [1] 15.44576 14.41523 20.87798 32.15566 33.58259 26.70279 21.83136 25.30970
#>  [9] 13.86093 15.67529 16.59588 21.82491
```
