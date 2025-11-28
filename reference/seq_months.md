# Generate sequence of months

Generate sequence of months

## Usage

``` r
seq_months(start_month, end_month)
```

## Arguments

- start_month:

  character string, the three-letter name of the starting month.

- end_month:

  character string, the three-letter name of the finishing month.

## Value

character vector. The list of months that falls in between two months

## Examples

``` r
   seq_months("Jan", "Apr")
#> [1] "Jan" "Feb" "Mar" "Apr"
```
