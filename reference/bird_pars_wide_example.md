# Example of bird parameters stored in wide format

A data frame of (fake) data on biological parameters of three seabird
species.

## Usage

``` r
bird_pars_wide_example
```

## Format

A 3 x 16 data frame, with the biological parameters (columns) for each
of the 3 species (rows). Columns include:

- Species:

  Species name

- AvoidanceBasic:

  Mean of basic avoidance rate

- AvoidanceBasicSD:

  SD of basic avoidance rate

- AvoidanceExtended:

  Mean of extended avoidance rate

- AvoidanceExtendedSD:

  SD of extended avoidance rate

- Body_Length:

  Mean body length

- Body_LengthSD:

  SD of body length

## Details

Intended to illustrate the application of `stoch_scrm()` to a multiple
scenario setting, where parameter data is available from tables in wide
format.
