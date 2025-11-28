# Total day and night hours per month

Taken from Forsythe et al.(1995) A model comparison for daylength as a
function of latitude and day of year. Ecological Modelling. 80: 87 - 95

## Usage

``` r
Day_Length(wf_latitude)
```

## Arguments

- wf_latitude:

  A decimal value. The latitude of the centroid of the windfarm, in
  degrees.

## Value

data frame with total number of daylight hours and night hours in each
month at the specified latitude.

## Examples

``` r
    x <- Day_Length(54.6)
```
