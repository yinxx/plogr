
<!-- README.md is generated from README.Rmd. Please edit that file -->
plogr
=====

Provides the header files for the [plog](https://github.com/SergiusTheBest/plog) header-only C++ logging library, and a method to log to R's standard error stream.

Installation
------------

You can install `plogr` from GitHub with:

``` r
# install.packages("devtools")
devtools::install_github("krlmlr/plogr")
```

Usage
-----

Add `LinkingTo: plogr` to your `DESCRIPTION`, and add `#include <plogr.h>` to all modules where you want to access the logging. If your package has an univeral header file which you include from all modules, it's probably a good idea to insert the `#include` directive there, so that all of your code has access to logging.

Example
-------

The code shows a small usage example and a demo which we'll call from R below. The `init_r()` function is the only new function added by the R package, and initializes a logger that logs to R's standard error stream.

(Rcpp is *not* necessary to use `plogr`, it is only needed to run the C++ code chunk.) For further details consult the [plog documentation](https://github.com/SergiusTheBest/plog#readme).

``` cpp
#include <Rcpp.h> // not necessary to use plogr

// [[Rcpp::depends(plogr)]]
#include <plogr.h>

// [[Rcpp::export]]
void plogr_demo() {
  LOG_INFO << "test 1";
  plog::init_r(plog::info);
  LOG_INFO << "test 2";
  LOG_DEBUG << "test 3";
}
```

The R code below calls the `plogr_demo()` C++ function defined above. Currently, the messages are printed straight to the standard error stream, so the message capturing mechanisms employed by `knitr` don't work. We use a sink with a text connection to capture the messages, and print the contents of the variable to which the text connection assigns.

``` r
output <- character()
con <- textConnection("output", "a")
withr::with_message_sink(con, plogr_demo())
close(con)
cat(output, sep = "\n")
#> plogr_demo@10: test 2
```

Nothing happens before we actually initialize the logger. Because it is initialized to the `info` level, the debug log message is not shown, and only "test 2" comes through.
