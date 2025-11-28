# Returns the proportion of birds at height bands along the rotor

Derive the expected proportion of bird flights at equidistant height
bands between the lowest and highest points of the rotor. In other
words, returns the flight height distribution at collision risk
(\\d(y)\\).

## Usage

``` r
get_fhd_rotor(hub_height, fhd, rotor_radius, tidal_offset, yinc = 0.05)
```

## Arguments

- hub_height:

  A numeric value, the height of the rotor hub (\\H\\), given by the sum
  of rotor radius and minimum blade clearance above the highest
  astronomical tide (HAT), in metres.

- fhd:

  A numeric vector, the proportion of bird flights per-1 metre height
  bands from sea surface, starting from 0-1 metre band.

- rotor_radius:

  A numeric value, the radius of the rotor (\\R\\), in metres.

- tidal_offset:

  A numeric value, the tidal offset, the difference between HAT and mean
  sea level, in metres.

- yinc:

  A numeric value, the increment for height bands between the lowest and
  highest points of the rotor, expressed as a proportion of rotor radius
  (defaults is 0.05).

## Value

A numeric vector, the flight height distribution at collision risk, i.e.
between minimum and maximum rotor height.

## Examples

``` r
 gen_fhd_dat <- Johnston_Flight_heights_SOSS %>%
       dplyr::filter(variable=="Gannet.est") %>%
       dplyr::select(height,prop)
 if(is.data.frame(gen_fhd_dat)) gen_fhd <- gen_fhd_dat$prop

 get_fhd_rotor(
   hub_height = 150,
   fhd = gen_fhd,
   rotor_radius = 120,
   tidal_offset = 2.5,
   yinc = 0.05)
#>  [1] 4.080875e-01 2.184714e-01 1.166795e-01 6.212004e-02 3.294138e-02
#>  [6] 1.738272e-02 9.120000e-03 4.746000e-03 2.454000e-03 1.252200e-03
#> [11] 6.348000e-04 3.174000e-04 1.560000e-04 7.566000e-05 3.600000e-05
#> [16] 1.680000e-05 7.674000e-06 3.426000e-06 1.488000e-06 6.270000e-07
#> [21] 2.568000e-07 1.015200e-07 3.870000e-08 1.422000e-08 5.004000e-09
#> [26] 1.680000e-09 5.406000e-10 1.650000e-10 4.776000e-11 1.305600e-11
#> [31] 3.348000e-12 8.058000e-13 1.812000e-13 3.804000e-14 7.374000e-15
#> [36] 1.321200e-15 2.184000e-16 3.300000e-17 4.554000e-18 5.706000e-19
#> [41] 6.468000e-20
```
