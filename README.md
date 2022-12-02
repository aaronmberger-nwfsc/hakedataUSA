
<!-- README.md is generated from README.Rmd. Please edit that file -->

# hakedataUSA

<!-- badges: start -->
<!-- badges: end -->

The goal of {hakedataUSA} is to provide code to extract and workup the
U.S. data for the assessment of Pacific Hake.

## Installation

You can install the development version of {hakedataUSA} from
[GitHub](https://github.com/) with:

``` r
# install.packages("pak")
pak::pak("pacific-hake/hakedataUSA")
# or if getwd() is a clone of this repository
# pak::local_install()
library(hakedataUSA)
```

## Instructions

``` r
# Customize the next two lines
local.assess <- file.path("c:", "stockAssessment", "hake-assessment")
local.model <- "test"

current_year <- as.numeric(format(Sys.Date(), "%Y"))

hakedata <- pulldatabase()

norpaccatches(hakedata[["ncatch"]])
pacfincatches(hakedata[["pcatch"]])

age_norpac <- atseacomps(hakedata[["atsea.ages"]], hakedata[["ncatch"]])
age_shore <- shorecomps(hakedata[["page"]], verbose = TRUE)

plot_rawmeasure(
  hakedata[["atsea.ages"]],
  hakedata[["page"]],
  years = current_year:(current_year - 4)
)

# Send catches to hake-assessment/data
datatoassessment_catch(file.path(local.assess, "data"))
# fix end year and catches
new_catch(
  dirout = file.path(local.assess, "data"), year = hakedata_year(),
  filedat = file.path(local.assess, "models", local.model, "hake_data.ss")
)
# Send compositions to hake-assessment/data
datatoassessment_comp(file.path(local.assess, "data"))
compdata <- datatocomps(
  dirdata = file.path(local.assess, "data"),
  dirmod = file.path(local.assess, "models", local.model),
  cohorts = current_year - c(2014, 2010)
)

wtatageinfo <- wtatage_collate()
# Move the wt-at-age for this year to the hake-assessment directory
file.copy(
  from = wtatageinfo[["file"]],
  to = file.path(
    local.assess,
    "data", "LengthWeightAge",
    basename(wtatageinfo[["file"]])
  ),
  overwrite = TRUE
)
data_wtatage(dir = file.path(local.assess, "data", "LengthWeightAge"))
```

``` r
render("inst/extdata/beamer/hake_jtc_data_us.Rmd")
```

## Issues

Please contact <kelli.johnson@noaa.gov> if there are issues with the
code. Note that the databases will only be accessible to members of the
U.S. JTC.
