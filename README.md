
<!-- README.md is generated from README.Rmd. Please edit that file -->

# catfacts

<!-- badges: start -->

<!-- badges: end -->

The goal of catfacts is to demonstrate an opinionated approach to
structuring a shiny app. Primary motivation for catfacts is to avoid
mistakes I have made in the past while developing. Key topics are:

  - Handling and organizing data
  - Documenting and controlling dependencies
  - Testing with `expect_` functions from `testthat`

## Structure

    .
    ├── R
    ├── README.Rmd
    ├── README.md
    ├── catfacts.Rproj
    ├── data
    │   ├── README.md
    │   └── contact.rds
    ├── data-raw
    │   ├── README.md
    │   └── contact.R
    ├── global.R
    ├── inst
    │   └── extdata
    │       ├── README.md
    │       └── contact.csv
    ├── renv
    │   ├── activate.R
    │   ├── library
    │   │   └── R-3.6
    │   └── settings.dcf
    ├── renv.lock
    ├── server.R
    ├── tests
    │   ├── testthat
    │   │   ├── test-cat-fact.R
    │   │   └── test-contact.R
    │   └── testthat.R
    ├── ui.R
    └── www
        └── custom.css

## Handling and organizing data

  - **Problem:** I have a vector of 10 choices and that vector is
    copied/pasted throughout the app and the apps tests. Any changes to
    that dropdowns choices will require numerous, redundant changes.

  - **Resolution:** Consider any static, reusable data and document it’s
    creation in `data-raw`, save it in `data`. If the data comes from an
    external source, store the original in `inst/extdata`, document any
    manipulation in `data-raw` and save it in `data`. Reuse that data
    throughout the app and the apps tests.

## Documenting and controlling dependencies

  - **Problem:** I just updated a package to use a development version
    which in turn broke a different app that depended on the CRAN
    version.

  - **Solution:** Use `renv` to control project specific dependencies.

Note: I have never actually run into the problem I described but the
thought of project specific dependencies makes me happy and less nervous
about things breaking.

## Testing with `expect_` functions from `testthat`

  - **Problem:** My data is dynamic and I want my tests to introduce
    randomness when possible, the snapshot approach using `shinytest`
    will not work.

  - **Solution:** Use `shinytest` for automating the app operations and
    `testthat` for testing the apps output.

Note: `shinytest` does have a solution for handling dynamic data which
is to use dummy data while in test mode, see
[here](https://rstudio.github.io/shinytest/articles/in-depth.html#dealing-with-dynamic-data).
However, to my knowledge `shinytest` cannot introduce randomness by
design, only control it, see
[here](https://rstudio.github.io/shinytest/articles/in-depth.html#controlling-randomness).

For my particular use case, a user can generate many different outputs
by selecting values from numerous dropdowns. My main concern is that
regardless of what they pick, the output makes sense. As a result, I
prefer a set of tests that randomly pick certain values from dropdowns
and compare the actual result to the expected result with `expect_`
functions from `testthat`.

If you decide to write tests that introduce randomness, make sure you
have a method of retrieving the random inputs that cause a test to fail.
I have use `assign()` to assign random inputs to the global environment.
