# Collision risk model, for a single species and one turbine scenario

Core function of the Collision Risk Model (CRM). Calculates the expected
number of in-flight collisions per month of a seabird species on a given
offshore windfarm, for a choice of model options.

Calculations are equivalent to those performed on the original CRM
[spreadsheet](https://www.bto.org/sites/default/files/u28/downloads/Projects/Final_Report_SOSS02_Band2Tool.xlsm),
as per [Band
(2012)](https://www.bto.org/sites/default/files/u28/downloads/Projects/Final_Report_SOSS02_Band1ModelGuidance.pdf),
providing backward compatibility with the original outputs.

## Usage

``` r
band_crm(
  model_options = c("1", "2", "3", "4"),
  flight_speed,
  body_lt,
  wing_span,
  flight_type,
  avoid_rt_basic = NULL,
  avoid_rt_ext = NULL,
  noct_activity,
  prop_crh_surv = NULL,
  dens_month,
  prop_upwind,
  site_fhd = NULL,
  gen_fhd = NULL,
  rotor_speed,
  rotor_radius,
  blade_width,
  blade_pitch,
  n_blades,
  hub_height,
  chord_prof = chord_prof_5MW,
  n_turbines,
  turb_oper_month,
  wf_width,
  wf_latitude,
  tidal_offset,
  lrg_arr_corr = TRUE,
  xinc = 0.05,
  yinc = 0.05,
  ...
)
```

## Arguments

- model_options:

  Character vector, the model options for calculating collision risk
  (see **Details** section below).

- flight_speed:

  Numeric value. The bird flying speed (\\v\\), in metres/sec.

- body_lt:

  Numeric value. The length of the bird (\\L\\), in metres.

- wing_span:

  Numeric value. The wingspan of the bird (\\W\\), in metres.

- flight_type:

  A character string, either 'flapping' or 'gliding', indicating the
  species' characteristic flight type.

- avoid_rt_basic, avoid_rt_ext:

  Numeric values. The avoidance rate for, respectively, the basic model
  (i.e. required for model Options 1 and 2) and the extended model (i.e.
  required for Options 3 and 4). Avoidance rate expresses the
  probability that a bird flying on a collision course with a turbine
  will take evading action to avoid collision.

- noct_activity:

  A numeric value. The nocturnal flight activity level, expressed as a
  proportion of daytime activity levels (\\f_night\\).

- prop_crh_surv:

  The proportion of flights at collision risk height derived from site
  survey (\\Q_2R\\). Only required for model Option 1.

- dens_month:

  Data frame, containing estimates of daytime in-flight bird densities
  per month within the windfarm footprint, in birds/km^2. It must
  contain the following named columns:

  - `month`, the month names.

  - `dens`, the number of birds in flight at any height per square
    kilometre in each month.

- prop_upwind:

  Numeric value between 0-1 giving the proportion of flights upwind -
  defaults to 0.5.

- gen_fhd, site_fhd:

  Data frame objects, with flight height distributions (fhd) of the
  species - the relative frequency distribution of bird flights at
  1-metre height intervals from sea surface. Specifically:

  - `gen_fhd`, Data frame with the species' generic fhd derived from
    combining wider survey data. Only required for model Options 2 and 3

  - `site_fhd`, Data frame with the species' site-specific fhd derived
    from local survey data. Only required for model Option 4

  Data frames must contain the following named columns:

  - `height`, integers representing height bands from sea surface, in
    metres. Function expects 0 as the first value, representing the 0-1m
    band.

  - `prop`, the proportion of flights at each height band.

- rotor_speed:

  Numeric value. The operational rotation speed, in revolutions/min.

- rotor_radius:

  Numeric value. The radius of the rotor (\\R\\), in metres.

- blade_width:

  Numeric value, giving the maximum blade width, in metres.

- blade_pitch:

  Numeric value. The average blade pitch angle, the angle between the
  blade surface and the rotor plane (\\\gamma\\), in radians.

- n_blades:

  An integer, the number of blades in rotor (\\b\\).

- hub_height:

  A numeric value, the height of the rotor hub (\\H\\), given by the sum
  of rotor radius and minimum blade clearance above the highest
  astronomical tide (HAT), in metres.

- chord_prof:

  A data frame with the chord taper profile of the rotor blade. Function
  expects two named columns:

  - `pp_radius`, equidistant intervals of radius at bird passage point,
    as a proportion of `rotor_radius`, within the range \\\[0, 1\]\\.

  - `chord`, the chord width at `pp_radius`, as a proportion of
    `blade_width`.

  Defaults to a generic profile for a typical modern 5MW turbine. See
  [`chord_prof_5MW()`](https://marinescotlandscience.github.io/stochLAB/reference/chord_prof_5MW.md)
  for details.

- n_turbines:

  Integer, the number of turbines on the wind farm (\\T\\).

- turb_oper_month:

  Data frame, holding the proportion of time during which turbines are
  operational per month. The following named column are expected:

  - `month`, the month names.

  - `prop_oper`, the proportion of time operating, per month.

- wf_width:

  Numeric value, the approximate longitudinal width of the wind farm, in
  kilometres (\\w\\).

- wf_latitude:

  A decimal value. The latitude of the centroid of the windfarm, in
  degrees.

- tidal_offset:

  A numeric value, the tidal offset, the difference between HAT and mean
  sea level, in metres.

- lrg_arr_corr:

  Boolean value. If TRUE, the large array correction will be applied.
  This is a correction factor to account for the decay in bird density
  at later rows in wind farms with a large array of turbines.

- yinc, xinc:

  numeric values, the increments along the y-axis and x-axis for
  numerical integration across segments of the rotor circle. Chosen
  values express proportion of rotor radius. By default these are set to
  0.05, i.e. integration will be performed at a resolution of one
  twentieth of the rotor radius.

- ...:

  Additional arguments to pass on when function is called in
  [`stoch_crm()`](https://marinescotlandscience.github.io/stochLAB/reference/stoch_crm.md),
  namely `rotor_grids` and `wf_daynight_hrs_month`.

## Value

Returns the expected number of bird collisions per month, for each of
the chosen CRM Options. Returned months are those shared between
`dens_month` and `turb_oper_month`. Output format is specific to how the
function is called:

- data frame object, if called as a stand-alone function.

- list object, if called inside
  [`stoch_crm()`](https://marinescotlandscience.github.io/stochLAB/reference/stoch_crm.md).

## Details

Collision risk can be calculated under 4 options, specified by
`model_options`:

- **Option 1** - Basic model with proportion at collision risk height
  derived from site survey (`prop_crh_surv`).

- **Option 2** - Basic model with proportion at collision risk height
  derived from a generic flight height distribution (`gen_fhd`).

- **Option 3** - Extended model using a generic flight height
  distribution (`gen_fhd`).

- **Option 4** - Extended model using a site-specific flight height
  distribution (`site_fhd`).

Where,

- Basic model - assumes a uniform distribution of bird flights at
  collision risk height (i.e. above the minimum and below the maximum
  height of the rotor blade).

- Extended model - takes into account the distribution of bird flight
  heights at collision risk height.

## Validation and pre-processing of inputs

`band_crm()` requirements and behaviour are dependent on how it is
called:

- As a stand-alone function:

  - All arguments are expected to be specified as describe above

  - Input validation and pre-processing are carried out.

- Inside
  [`stoch_crm()`](https://marinescotlandscience.github.io/stochLAB/reference/stoch_crm.md):

  - Assumes inputs have already been pre-processed and validated, and
    thence it skips those steps.

  - Additional arguments `rotor_grids` and `wf_daynight_hrs_month` need
    to be passed to the function. Under the stochastic context, these
    quantities can be calculated ahead of the simulation loop to
    maximize performance.

  - Furthermore, `gen_fhd`, `site_fhd`, `dens_month` and
    `turb_oper_month` can be provided as numeric vectors

## Code revision and optimization

Core code performing Band calculations in [Masden
(2015)](https://www.gov.scot/publications/scottish-marine-freshwater-science-vol-6-14-developing-avian-collision/)
implementation was substantially re-factored, re-structured and
streamlined to conform with conventional R packages requirements.

Furthermore, previous code underpinning key calculations for the
extended model was replaced by an alternative approach, resulting in
significant gains in computational speed over Masden's approach. This
optimization is particularly beneficial under a stochastic context, when
Band calculations are called repeatedly, potentially thousands of times.
See
[`generate_rotor_grids()`](https://marinescotlandscience.github.io/stochLAB/reference/generate_rotor_grids.md)
for further details.

## Examples

``` r
# ------------------------------------------------------
# Run with arbitrary parameter values, for illustration
# ------------------------------------------------------

# Setting a dataframe of parameters to draw from
params <- data.frame(
  flight_speed = 13.1,         # Flight speed in m/s
  body_lt = 0.85,              # Body length in m
  wing_span = 1.01,            # Wing span in m
  flight_type = "flapping",    # flapping or gliding flight
  avoid_rt_basic = 0.989,      # avoidance rate for option 1 and 2
  avoid_rt_ext = 0.981,        # extended avoidance rate for option 3 and 4
  noct_activity = 0.5,         # proportion of day birds are inactive
  prop_crh_surv = 0.13,        # proportion of birds at collision risk height (option 1 only)
  prop_upwind = 0.5,           # proportion of flights that are upwind
  rotor_speed = 15,            # rotor speed in revolutions/minute
  rotor_radius = 120,          # radius of turbine in m
  blade_width = 5,             # width of turbine blades at thickest point in m
  blade_pitch = 15,            # mean radius pitch in Radians
  n_blades = 3,                # total number of blades per turbine
  hub_height = 150,            # height of hub in m above HAT
  n_turbines = 100,            # number of turbines in the wind farm
  wf_width = 52,               # width across longest section of wind farm
  wf_latitude = 56,            # latitude of centroid of wind farm
  tidal_offset = 2.5,          # mean tidal offset from HAT of the wind farm
  lrg_arr_corr = TRUE          # apply a large array correction?
)

# Monthly bird densities
b_dens <- data.frame(
  month = month.abb,
  dens = runif(12, 0.8, 1.5)
)

# flight height distribution from Johnston et al
gen_fhd_dat <- Johnston_Flight_heights_SOSS %>%
  dplyr::filter(variable=="Gannet.est") %>%
  dplyr::select(height,prop)


# monthly operational time of the wind farm
turb_oper <- data.frame(
  month = month.abb,
  prop_oper = runif(12,0.5,0.8)
)


band_crm(
  model_options = c(1,2,3),
  flight_speed = params$flight_speed,
  body_lt = params$body_lt,
  wing_span = params$wing_span,
  flight_type = params$flight_type,
  avoid_rt_basic = params$avoid_rt_basic,
  avoid_rt_ext = params$avoid_rt_ext,
  noct_activity = params$noct_activity,
  prop_crh_surv = params$prop_crh_surv,
  dens_month = b_dens,
  prop_upwind = params$prop_upwind,
  gen_fhd = gen_fhd_dat,
  site_fhd = NULL,  # Option 4 only
  rotor_speed = params$rotor_speed,
  rotor_radius = params$rotor_radius,
  blade_width = params$blade_width,
  blade_pitch = params$blade_pitch,
  n_blades = params$n_blades,
  hub_height = params$hub_height,
  chord_prof = chord_prof_5MW,
  n_turbines = params$n_turbines,
  turb_oper_month = turb_oper,
  wf_width = params$wf_width,
  wf_latitude = params$wf_latitude,
  tidal_offset = params$tidal_offset,
  lrg_arr_corr = params$lrg_arr_corr
  )
#> # A tibble: 12 × 4
#>    month  opt1  opt2  opt3
#>    <chr> <dbl> <dbl> <dbl>
#>  1 Jan    46.6  12.0  4.92
#>  2 Feb    52.1  13.4  5.50
#>  3 Mar    74.6  19.2  7.87
#>  4 Apr    86.4  22.3  9.12
#>  5 May    91.4  23.6  9.64
#>  6 Jun   112.   28.8 11.8 
#>  7 Jul   123.   31.7 13.0 
#>  8 Aug    59.3  15.3  6.26
#>  9 Sep    68.7  17.7  7.24
#> 10 Oct    75.1  19.4  7.93
#> 11 Nov    51.4  13.2  5.42
#> 12 Dec    56.5  14.6  5.96
```
