01\_explore-libraries\_jenny.R
================
tvillavicencio
Wed Jan 31 14:12:14 2018

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "/Users/tvillavicencio/Library/R/3.4/library"                   
    ## [2] "/Library/Frameworks/R.framework/Versions/3.4/Resources/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "/Library/Frameworks/R.framework/Resources/library"

``` r
library(fs)
path_real(.Library)
```

    ## /Library/Frameworks/R.framework/Versions/3.4/Resources/library

Installed packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ──────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
    ## ✔ tidyr   0.7.2     ✔ stringr 1.2.0
    ## ✔ readr   1.1.1     ✔ forcats 0.2.0

    ## ── Conflicts ─────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 155

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 4 x 3
    ##   LibPath                                                 Priority       n
    ##   <chr>                                                   <chr>      <int>
    ## 1 /Library/Frameworks/R.framework/Versions/3.4/Resources… base          14
    ## 2 /Library/Frameworks/R.framework/Versions/3.4/Resources… recommend…    15
    ## 3 /Library/Frameworks/R.framework/Versions/3.4/Resources… <NA>          92
    ## 4 /Users/tvillavicencio/Library/R/3.4/library             <NA>          34

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  67 0.432 
    ## 2 yes                 82 0.529 
    ## 3 <NA>                 6 0.0387

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 4 x 3
    ##   Built     n  prop
    ##   <chr> <int> <dbl>
    ## 1 3.4.0    57 0.368
    ## 2 3.4.1    16 0.103
    ## 3 3.4.2    21 0.135
    ## 4 3.4.3    61 0.394

Reflections

``` r
## reflect on ^^ and make a few notes to yourself; inspiration
##   * does the number of base + recommended packages make sense to you?
##   * how does the result of .libPaths() relate to the result of .Library?
```

Going further

``` r
## if you have time to do more ...

## is every package in .Library either base or recommended?
all_default_pkgs <- list.files(.Library)
all_br_pkgs <- ipt %>%
  filter(Priority %in% c("base", "recommended")) %>%
  pull(Package)
setdiff(all_default_pkgs, all_br_pkgs)
```

    ##  [1] "anytime"        "assertthat"     "backports"      "base64enc"     
    ##  [5] "BH"             "bindr"          "bindrcpp"       "bit"           
    ##  [9] "bit64"          "blob"           "broom"          "callr"         
    ## [13] "cellranger"     "cli"            "colorspace"     "covr"          
    ## [17] "crayon"         "curl"           "data.table"     "DBI"           
    ## [21] "dbplyr"         "debugme"        "dichromat"      "digest"        
    ## [25] "dplyr"          "dtplyr"         "evaluate"       "forcats"       
    ## [29] "ggplot2"        "glue"           "gtable"         "haven"         
    ## [33] "highr"          "hms"            "htmltools"      "httr"          
    ## [37] "jsonlite"       "knitr"          "labeling"       "Lahman"        
    ## [41] "lazyeval"       "lubridate"      "magrittr"       "markdown"      
    ## [45] "memoise"        "microbenchmark" "mime"           "mnormt"        
    ## [49] "modelr"         "munsell"        "nycflights13"   "openssl"       
    ## [53] "pillar"         "pkgconfig"      "plogr"          "plyr"          
    ## [57] "praise"         "psych"          "purrr"          "R6"            
    ## [61] "RApiDatetime"   "RColorBrewer"   "Rcpp"           "readr"         
    ## [65] "readxl"         "rematch"        "reprex"         "reshape2"      
    ## [69] "rex"            "rlang"          "rmarkdown"      "RMySQL"        
    ## [73] "RPostgreSQL"    "rprojroot"      "RSQLite"        "rstudioapi"    
    ## [77] "rvest"          "scales"         "selectr"        "stringi"       
    ## [81] "stringr"        "testthat"       "tibble"         "tidyr"         
    ## [85] "tidyselect"     "tidyverse"      "translations"   "utf8"          
    ## [89] "viridisLite"    "whisker"        "withr"          "xml2"          
    ## [93] "yaml"

``` r
## study package naming style (all lower case, contains '.', etc

## use `fields` argument to installed.packages() to get more info and use it!
ipt2 <- installed.packages(fields = "URL") %>%
  as_tibble()
ipt2 %>%
  mutate(github = grepl("github", URL)) %>%
  count(github) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   github     n  prop
    ##   <lgl>  <int> <dbl>
    ## 1 F         70 0.452
    ## 2 T         85 0.548
