# Generate random draws based on empirical c.d.f.

Generate random draws from a set of quantiles, based on the empirical
cumulative density function

## Usage

``` r
sample_qtls(n, probs, qtls)
```

## Arguments

- n:

  integer, the number of draws to generate

- probs:

  numeric vector, the probabilities

- qtls:

  numeric vector, the quantiles for the probabilities specified in
  `probs`

## Value

a numeric vector, with random draws of the approximated distribution
underpinning the provided quantiles

## Details

Based on the Inverse Transform Sampling technique, by sampling random
probabilities from a uniform distribution and interpolate (cubic) the
count samples from the percentiles provided by the user (taken as the
empirical cumulative density function)

## Examples

``` r
 sample_qtls(10,c(0.1,0.2,0.3),qtls=c(0.05,0.1,0.95))
#>  [1] 0.06563417 0.60837315 0.09127136 0.07021602 0.66582035 0.08566147
#>  [7] 0.11109371 0.06252278 0.15186973 0.09753652
```
