# Rotor blade chord profile

A data.frame giving the blade's chord width profile, i.e. the chord
width along the length of the blade, provided as a proportion of its
maximum width.

## Usage

``` r
chord_prof_5MW
```

## Format

A dataframe

- pp_radius:

  radius at bird passage point, as a proportion of rotor radius (R)

- chord:

  chord width at pp_radius, as a proportion of the maximum chord width

## Details

This is a generic profile for a typical modern 5MW turbine used for
offshore generation. Due to commercial sensitivities by blade
manufacturers, some of this detailed information may not be readily
available for each make/model of blade and hence generic information may
have to be used.

## Note

`"chord_prof_5MW"` is numerically identical to `"coverC"`. `"coverC"`
should become deprecated in future versions of the code
