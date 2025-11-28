# Number of collisions under model Option 4

Wrapper function to run the CRM calculations under option 4, i.e.:

- Using the extended model, which takes into account the distribution of
  bird flight heights at risk height (above the minimum and below the
  maximum height of the rotor blade)

- Using site-specific flight height distribution of the species obtained
  from site survey data

## Usage

``` r
crm_opt4(
  rotor_grids,
  d_y_surv,
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

- d_y_surv:

  a vector with the proportion of birds at height bands within the rotor
  disc, from a site-specific flight height distribution

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

A numeric vector, the expected number of collisions per month based on
model option 4

## Examples

``` r
 rotor_grids <- generate_rotor_grids(yinc = 0.05, xinc = 0.05, chord_prof_5MW)

 site_fhd_dat <- Johnston_Flight_heights_SOSS %>%
      dplyr::filter(variable=="Gannet.est") %>%
      dplyr::select(height,prop)

 site_fhd <- site_fhd_dat$prop

 surv_fhd_at_rotor <-
    get_fhd_rotor(
      hub_height = 150,
      fhd = site_fhd,
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

crm_opt4(
  rotor_grids = rotor_grids,
  d_y_surv = surv_fhd_at_rotor,
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
#>  [1]  6.075181  5.170792  7.210196  8.349804 10.646414 11.831230 10.592826
#>  [8] 10.717566  6.758279  6.614187  6.059367  7.142571
```
