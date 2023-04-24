
<!-- README.md is generated from README.Rmd. Please edit that file -->

# mipreadr

<!-- badges: start -->

[![](https://img.shields.io/badge/devel%20version-0.1.0-blue.svg)](https://github.com/viana-guilherme/mipreadr)
[![Project Status: Active - The project has reached a stable, usable
state and is being actively
developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active)

<!-- badges: end -->

**mipreadr** is a package that provides several functions to importing
and pre-processing microplate reader data into R. Currently it fully
supports output files generated by Perkin Elmer’s Victor x3 microplate
reader, but support for other file formats (including Victor Nivo) will
be implemented in the future.

## Installation

You can install the development version of mipreadr from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("viana-guilherme/mipreadr")
```

## Basic workflow

### Create a plate layout with createLayout()

``` r
createLayout("exp01")
```

This will save an .xlsx sheet into your current working directory, which
you can then edit to indicate the position of your samples and blanks
within your plate. For this, please use the following convention:

-   *Technical replicates* (within a plate) should be named after their
    sample names and an unique experimental condition identifier,
    separated by a single underscore (\_). Eg: KT2440_M9

-   A *Blank* well for a given condition identifier should be indicated
    using a hash sign (#). Eg: \#M9

-   You may indicate different *biological replicates* in the same plate
    using an underscore followed by a number. Eg: KT2440_M9_1 and
    KT2440_M9_2. If there’s only one biological replicate in the plate,
    you may omit this information.

If you wish to edit the plate layout interactively within RStudio, set
the parameter `interactive = TRUE`, this will open a shiny widget that
allows you to directly edit the layout file.

### Import plate files to the R session and collapse technical replicates with addPlate()

The `addPlate()` function requires a plate file (from your microplate
reader, usually a .txt or .csv) and a layout file (the .xlsx created
above) for a single experiment, generating a list with the raw data,
metadata and processed plate data (i.e. Blank values subtracted from
samples, technical replicates averaged, etc.). The
`normalizeFluorescence` parameter indicates whether to create new
column(s) with the fluorescence/luminescence measurements (e.g. GFP,
mCherry, Lux) normalized by absorbance (e.g. OD600)

``` r
exp01 <- addPlate(file = "exp01.txt",
                  layout = "exp01_layout.xlsx",
                  mode = "x3",
                  normalizeFluorescence = TRUE)
exp02 <- addPlate(file = "exp02.txt",
                  layout = "exp02_layout.xlsx",
                  mode = "x3",
                  normalizeFluorescence = TRUE)
```

### Join data from different plates together and calculate biological replicate means and standard deviations with mergePlates()

To use this function, create a list with the experiments you’d like to
join and the biological replicates will be detected automatically from
within the variables. The output from `mergePlates()` is a single tibble
in the long format, ready to be plotted or otherwise analyzed using
available tools.

``` r
analysis <- list(exp01, exp02)
mergePlates(analysis)
```

## Contact

This is a fork of the [mipreadr](https://github.com/viana-guilherme/mipreadr) repository. Please, direct bugs, comments or suggestions to the primary [maintainer](viana.guilherme@proton.me)
