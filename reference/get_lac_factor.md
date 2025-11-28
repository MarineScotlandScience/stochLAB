# Large array correction factor

A correction factor for large windfarms with a large array of turbines,
to take into account the depletion of bird density in later rows of the
site.

## Usage

``` r
get_lac_factor(
  n_turbines,
  rotor_radius,
  avoidance_rate,
  avg_prob_coll,
  avg_prop_operational,
  wf_width
)
```

## Arguments

- n_turbines:

  Integer, the number of turbines on the wind farm (\\T\\).

- rotor_radius:

  Numeric value, the radius of the rotor (\\R\\), in metres.

- avoidance_rate:

  Numeric value within the interval \\\[0, 1\]\\. The species avoidance
  rate, expressing the probability that a bird flying on a collision
  course with a turbine will take evading action to avoid collision.
  (\\A\\).

- avg_prob_coll:

  Numeric value, the average probability of collision for a single bird
  transit through a rotor, assuming no avoidance action (\\p_average\\).

- avg_prop_operational:

  Numeric value, the average proportion of time turbines are operational
  through the year.

- wf_width:

  Numeric value, the approximate longitudinal width of the wind farm, in
  kilometres (\\w\\).

## Value

The large array correction factor to be applied

## Examples

``` r
  get_lac_factor(
     n_turbines = 100,
     rotor_radius = 120,
     avoidance_rate = 0.989,
     avg_prob_coll = 0.1494609,
     avg_prop_operational = 0.6388292,
     wf_width = 52
     )
#> [1] 0.9998287
```
