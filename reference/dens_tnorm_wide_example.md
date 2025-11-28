# Example of Truncated Normal parameters for monthly estimates of bird density

A data frame of (fake) monthly bird density parameters for three seabird
species.

## Usage

``` r
dens_tnorm_wide_example
```

## Format

A 3 x 25 data frame, with the monthly density parameters (columns) for
each of the 3 species (rows). Columns include:

- Species:

  Species name

- Jan:

  January mean density

- JanSD:

  SD of density in January

- Feb:

  February mean density

- FebSD:

  SD of density in February

## Details

Intended to illustrate the application of `stoch_scrm()` to a multiple
scenario setting, where parameter data is available from tables in wide
format.
