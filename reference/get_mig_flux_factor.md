# Migration Flux factor

Returns the migratory flux factor, expressing the total number of bird
flights through the rotors of the wind farm per month, if all flights
occur within the rotor's circle area of all turbines, and assuming birds
take no avoiding action.

## Usage

``` r
get_mig_flux_factor(n_turbines, rotor_radius, wf_width, popn_est)
```

## Arguments

- n_turbines:

  An integer. The number of turbines on the wind farm (\\T\\).

- rotor_radius:

  An integer. The radius of the rotor (\\R\\), in metres

- wf_width:

  An integer. The width (in km) of the

- popn_est:

  An integer. The population estimate from the spatial line sampling
  technique

## Value

The number of bird flights potentially transiting through rotors at each
time period (assuming no avoidance), if all flights occur within the
rotor's circular area

## Details

The flux factor is used for other model calculations. Methodology and
assumptions underpinning `get_mig_flux_factor` are described in "Stage
B" of [Band
(2012)](https://www.bto.org/sites/default/files/u28/downloads/Projects/Final_Report_SOSS02_Band1ModelGuidance.pdf)

## Examples

``` r
get_mig_flux_factor(
     n_turbines = 100,
     rotor_radius = 120,
     wf_width = 51,
     popn_est = 10000
)
#> [1] 3695.991
```
