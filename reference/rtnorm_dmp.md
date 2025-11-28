# Customised sampling of Truncated Normal distribution

Wrapper of the msm::rtnorm() function, improving on outputs management
and user feedback on edge cases

## Usage

``` r
rtnorm_dmp(n, mean = 0, sd = 1, lower = -Inf, upper = Inf)
```

## Arguments

- n:

  An integer value. The number of samples to generate

- mean:

  A decimal value. The mean for the truncated normal distribution

- sd:

  A decimal value. The standard deviation of the distribution to
  simulate

- lower:

  A decimal value. The lower limit for the distribution

- upper:

  A decimal value. The upper limit for the distribution

## Value

a vector of samples values from the truncated normal distribution

## Examples

``` r
rtnorm_dmp(n=10,mean=0.4,sd=0.2)
#>  [1] 0.4729449 0.1683407 0.4783579 0.1973194 0.5755679 0.4755823 0.6969956
#>  [8] 0.4172149 0.4816135 0.5931918

```
