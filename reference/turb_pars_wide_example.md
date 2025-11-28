# Example of turbine and windfarm parameters stored in wide format

A data frame of (fake) data on turbine and wind farm features for 3
scenarios.

## Usage

``` r
turb_pars_wide_example
```

## Format

A 3 x 51 data frame, with the turbine and windfarm parameters (columns)
for each of the 3 development scenarios (rows). Columns include:

- TurbineModel :

  The turbine/windfarm scenario ID

- Blades:

  Nr of blades

- RotorRadius:

  Mean of rotor radius

- RotorRadiusSD:

  SD of rotor radius

- HubHeightAdd:

  Mean of air gap, the distance between sea surface and lowest tip
  height.

- HubHeightAddSD:

  SD of air gap, as explained above.

## Details

Intended to illustrate the application of `stoch_scrm()` to a multiple
scenario setting, where parameter data is available from tables in wide
format.
