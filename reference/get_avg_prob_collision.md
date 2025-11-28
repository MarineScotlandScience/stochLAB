# Average single transit collision risk with no avoidance

Calculate the average probability of collision for a single bird transit
at any point across the rotor, assuming no avoidance action. Required
for the Basic model calculations, where flights at collision risk height
are assumed to be uniformly distributed.

## Usage

``` r
get_avg_prob_collision(
  flight_speed,
  body_lt,
  wing_span,
  prop_upwind = 0.5,
  flap_glide,
  rotor_speed,
  rotor_radius,
  blade_width,
  blade_pitch,
  n_blades,
  chord_prof = chord_prof_5MW
)
```

## Arguments

- flight_speed:

  Numeric value. The bird flying speed (\\v\\), in metres/sec.

- body_lt:

  Numeric value. The length of the bird (\\L\\), in metres.

- wing_span:

  Numeric value. The wingspan of the bird (\\W\\), in metres.

- prop_upwind:

  Numeric value between 0-1 giving the proportion of flights upwind -
  defaults to 0.5.

- flap_glide:

  Numeric value representing the correction for flapping or gliding
  birds. Gliding birds have a value of 2/pi while flapping birds have a
  value of 1. (\\F\\).

- rotor_speed:

  Numeric value. The operational rotation speed, in revolutions/min.

- rotor_radius:

  Numeric value. The radius of the rotor (\\R\\), in metres.

- blade_width:

  Numeric value, giving the maximum blade width, in metres.

- blade_pitch:

  Numeric value. The average blade pitch angle, the angle between the
  blade surface and the rotor plane (\\\gamma\\), in radians.

- n_blades:

  An integer, the number of blades in rotor (\\b\\).

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

## Value

A numeric value. The average collision probability (risk) for a bird
flying through any point of the rotor circle area.

## Details

Methodology and assumptions underpinning `get_avg_prob_collision` are
described in "Stage C" of [Band
(2012)](https://www.bto.org/sites/default/files/u28/downloads/Projects/Final_Report_SOSS02_Band1ModelGuidance.pdf).

## Examples

``` r
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
#> [1] 0.1494609
```
