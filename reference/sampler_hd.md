# Customised sampling function wrapper

Samples a dataset based on inputs for either the rtnorm, rbeta or
'rnorm' distributions

## Usage

``` r
sampler_hd(
  dat,
  mode = "rtnorm",
  n = NULL,
  mean = NULL,
  sd = NULL,
  lower = 0,
  upper = NULL
)
```

## Arguments

- dat:

  = A decimal value. The SD value to test (from the UI) - if not
  available in the UI, then do not create a distribution

- mode:

  = A string. Either 'rtnorm', 'rbeta' or 'rnorm' to determine which
  distribution to generate

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

a vector of samples values from the distribution

## Examples

``` r
  sampler_hd(dat=0.1,
       mode='rtnorm',
       n=100,
       mean=9,
       sd=0.1)
#>   [1] 8.909738 9.124842 8.982686 9.029920 9.056487 9.103273 8.991915 9.181838
#>   [9] 8.840868 9.081293 9.027675 8.999912 8.967177 9.168632 8.983260 9.034665
#>  [17] 9.063153 9.007892 9.142822 9.158502 9.034508 9.108253 8.965157 9.005255
#>  [25] 8.936505 8.963286 8.811488 8.905225 9.236296 9.005089 9.097625 8.809241
#>  [33] 9.005111 9.266781 8.860530 9.026057 8.952956 9.020429 8.944322 9.027311
#>  [41] 8.897340 8.909571 8.907136 8.870464 8.935067 8.959291 8.982625 9.067944
#>  [49] 8.882087 8.776863 8.991132 9.095710 8.836743 9.021169 9.220681 9.000107
#>  [57] 9.010952 8.927899 8.990569 9.010051 9.172587 8.933390 8.901488 8.855845
#>  [65] 8.966999 9.033993 8.824172 9.159706 8.991019 9.005898 9.004794 9.115728
#>  [73] 9.056943 8.900337 9.015796 9.211375 9.141908 9.082072 9.056306 8.886654
#>  [81] 8.954851 9.095308 9.068714 8.892164 8.976872 9.148204 9.072152 9.093848
#>  [89] 8.894212 8.971367 9.003562 8.890366 9.072950 9.023802 9.076570 8.986127
#>  [97] 8.994565 9.075196 8.839529 9.063679
```
