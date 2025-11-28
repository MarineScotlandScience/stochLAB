# Summarized flight height profiles from Johnston et al (2014)

A dataframe containing flight height profiles for all species in
Johnston et al. (2014). Values are expressed as the proportion of birds
in 1 metre height intervals.

## Usage

``` r
Johnston_Flight_heights_SOSS
```

## Format

A data frame object with 3 columns containing the maximum likelihood,
the median, and Upper/Lower confidence limits of flight height
distributions. Flight height bands go from 1 - 300m ASL.

- height:

  Height above sea level, in metres. First element represents the 0-1
  meters height band, and height interval is 1 metre.

- variable:

  The species name and variable from Johnston et al 2014 estimates.
  E.G., ArcticSkua.est is the maximum likelihood estimate from those
  models. .est = maximum likelihood, .lcl and .ucl are the lower and
  upper 95% CLs, and .med is the median estimate.

- prop:

  The proportion of birds within the 1m flight height bands.

## Source

<https://www.bto.org/our-work/science/research-areas/wetland-and-marine/soss>
