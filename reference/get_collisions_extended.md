# Number of collisions based on the extended model

Calculates the expected number of collisions under the extended model,
i.e. taking into account the distribution of bird flight heights at risk
height (above the minimum and below the maximum height of the rotor
blade)

## Usage

``` r
get_collisions_extended(
  rotor_grids,
  d_y,
  rotor_radius,
  blade_width,
  rotor_speed,
  blade_pitch,
  flight_type,
  wing_span,
  flight_speed,
  body_lt,
  n_blades,
  prop_upwind,
  avoidance_rate,
  flux_factor,
  mth_prop_oper,
  lac_factor
)
```

## Arguments

- rotor_grids:

  A list object containing geometric attributes of the rotor at
  equidistant points within its unit circle. This object should be built
  via the function
  [`generate_rotor_grids()`](https://marinescotlandscience.github.io/stochLAB/reference/generate_rotor_grids.md).

- d_y:

  a vector with the proportion of birds at height bands within the rotor
  disc

- rotor_radius:

  Numeric value. The radius of the rotor (\\R\\), in metres.

- blade_width:

  Numeric value, giving the maximum blade width, in metres.

- rotor_speed:

  Numeric value. The operational rotation speed, in revolutions/min.

- blade_pitch:

  Numeric value. The average blade pitch angle, the angle between the
  blade surface and the rotor plane (\\\gamma\\), in radians.

- flight_type:

  A character string, either 'flapping' or 'gliding', indicating the
  species' characteristic flight type.

- wing_span:

  Numeric value. The wingspan of the bird (\\W\\), in metres.

- flight_speed:

  Numeric value. The bird flying speed (\\v\\), in metres/sec.

- body_lt:

  Numeric value. The length of the bird (\\L\\), in metres.

- n_blades:

  An integer, the number of blades in rotor (\\b\\).

- prop_upwind:

  Numeric value between 0-1 giving the proportion of flights upwind -
  defaults to 0.5.

- avoidance_rate:

  a numeric value within the interval \\\[0, 1\]\\. The avoidance rate,
  expressing the probability that a bird flying on a collision course
  with a turbine will take evading action to avoid collision.

- flux_factor:

  a vector containing the flux factor for each month

- mth_prop_oper:

  A numeric vector, the proportion of time during which turbines are
  operational per month.

- lac_factor:

  A numerical value, the large array correction factor. Defaults to 1,
  meaning large array correction is not applicable.

## Value

Numeric vector with the expected number of collisions per month based on
the extended model

## Note

The original Masden code for extendend model (Option 3) included the
calculation of the "Flux Integral" and "Average collision risk for
single rotor transit". These quantities are not required for the
calculation of collisions under the extended model, nor are used
anywhere else in the package. Therefore, those calculations were dropped
from the current implementation to keep computations to a minimum
required.

## See also

[`generate_rotor_grids()`](https://marinescotlandscience.github.io/stochLAB/reference/generate_rotor_grids.md)

## Examples

``` r
 rotor_grids <- generate_rotor_grids(yinc = 0.05, xinc = 0.05, chord_prof_5MW)

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

get_collisions_extended(
  rotor_grids = rotor_grids,
  d_y = gen_fhd_at_rotor, # ... with generic FHD = Option 3, with site FHD = Option 4
  rotor_radius = 120,
  blade_width = 5,
  rotor_speed = 15,
  blade_pitch = 15,
  flight_type = "flapping",
  wing_span = 1.01,
  flight_speed = 13.1,
  body_lt = 0.85,
  n_blades = 3,
  prop_upwind = 0.5,
  avoidance_rate = 0.981,
  flux_factor = flux_fct,
  mth_prop_oper = turb_oper_month,
  lac_factor = 0.9998299
  )
#>  [1]  7.794391  5.642063  7.637052 12.386698 12.374993 13.501268  7.043609
#>  [8] 13.347696  8.200821  6.321165  6.366087  8.912458
```
