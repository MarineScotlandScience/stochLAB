# Bootstrap samples of generic FHDs of 25 seabird species

A list object comprising bootstrap samples of the generic flight height
distribution (FHD) of 25 seabird species. FHD is expressed as the
proportion of bird flights at 1 metre height intervals.

## Usage

``` r
generic_fhd_bootstraps
```

## Format

A list object with 25 elements, one for each species, containing a data
frame with 500 bootstrap samples of the distribution of bird flights
with height. Each nested data frame contains:

- height:

  Height above sea level, in metres. First element represents the 0-1
  meters height band, and height interval is 1 metre.

- bootId_1:

  First bootstrap sample of the proportion of birds flights within each
  height interval

- bootId_200:

  200th bootstrap sample of the proportion of birds flights within each
  height interval

## Source

<https://besjournals.onlinelibrary.wiley.com/doi/full/10.1111/1365-2664.12191>
