01\_explore-libraries\_jenny.R
================
tvillavicencio
Wed Jan 31 14:25:29 2018

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

    ## ── Attaching packages ──────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
    ## ✔ tidyr   0.7.2     ✔ stringr 1.2.0
    ## ✔ readr   1.1.1     ✔ forcats 0.2.0

    ## ── Conflicts ─────────────────────── tidyverse_conflicts() ──
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

``` r
##adding library information
devtools::session_info()
```

    ## Session info -------------------------------------------------------------

    ##  setting  value                       
    ##  version  R version 3.4.3 (2017-11-30)
    ##  system   x86_64, darwin15.6.0        
    ##  ui       X11                         
    ##  language (EN)                        
    ##  collate  en_US.UTF-8                 
    ##  tz       America/Los_Angeles         
    ##  date     2018-01-31

    ## Packages -----------------------------------------------------------------

    ##  package    * version date       source        
    ##  assertthat   0.2.0   2017-04-11 CRAN (R 3.4.0)
    ##  backports    1.1.2   2017-12-13 CRAN (R 3.4.3)
    ##  base       * 3.4.3   2017-12-07 local         
    ##  bindr        0.1     2016-11-13 CRAN (R 3.4.0)
    ##  bindrcpp   * 0.2     2017-06-17 CRAN (R 3.4.0)
    ##  broom        0.4.3   2017-11-20 CRAN (R 3.4.3)
    ##  cellranger   1.1.0   2016-07-27 CRAN (R 3.4.0)
    ##  cli          1.0.0   2017-11-05 CRAN (R 3.4.2)
    ##  colorspace   1.3-2   2016-12-14 CRAN (R 3.4.0)
    ##  compiler     3.4.3   2017-12-07 local         
    ##  crayon       1.3.4   2017-09-16 CRAN (R 3.4.1)
    ##  datasets   * 3.4.3   2017-12-07 local         
    ##  devtools     1.13.4  2017-11-09 CRAN (R 3.4.2)
    ##  digest       0.6.14  2018-01-14 CRAN (R 3.4.3)
    ##  dplyr      * 0.7.4   2017-09-28 CRAN (R 3.4.2)
    ##  evaluate     0.10.1  2017-06-24 CRAN (R 3.4.1)
    ##  forcats    * 0.2.0   2017-01-23 CRAN (R 3.4.0)
    ##  foreign      0.8-69  2017-06-22 CRAN (R 3.4.3)
    ##  fs         * 1.1.0   2018-01-26 CRAN (R 3.4.3)
    ##  ggplot2    * 2.2.1   2016-12-30 CRAN (R 3.4.0)
    ##  glue         1.2.0   2017-10-29 CRAN (R 3.4.2)
    ##  graphics   * 3.4.3   2017-12-07 local         
    ##  grDevices  * 3.4.3   2017-12-07 local         
    ##  grid         3.4.3   2017-12-07 local         
    ##  gtable       0.2.0   2016-02-26 CRAN (R 3.4.0)
    ##  haven        1.1.1   2018-01-18 CRAN (R 3.4.3)
    ##  hms          0.4.1   2018-01-24 CRAN (R 3.4.3)
    ##  htmltools    0.3.6   2017-04-28 CRAN (R 3.4.0)
    ##  httr         1.3.1   2017-08-20 CRAN (R 3.4.1)
    ##  jsonlite     1.5     2017-06-01 CRAN (R 3.4.0)
    ##  knitr        1.19    2018-01-29 CRAN (R 3.4.3)
    ##  lattice      0.20-35 2017-03-25 CRAN (R 3.4.3)
    ##  lazyeval     0.2.1   2017-10-29 CRAN (R 3.4.2)
    ##  lubridate    1.7.1   2017-11-03 CRAN (R 3.4.2)
    ##  magrittr     1.5     2014-11-22 CRAN (R 3.4.0)
    ##  memoise      1.1.0   2017-04-21 CRAN (R 3.4.0)
    ##  methods    * 3.4.3   2017-12-07 local         
    ##  mnormt       1.5-5   2016-10-15 CRAN (R 3.4.0)
    ##  modelr       0.1.1   2017-07-24 CRAN (R 3.4.1)
    ##  munsell      0.4.3   2016-02-13 CRAN (R 3.4.0)
    ##  nlme         3.1-131 2017-02-06 CRAN (R 3.4.3)
    ##  parallel     3.4.3   2017-12-07 local         
    ##  pillar       1.1.0   2018-01-14 CRAN (R 3.4.3)
    ##  pkgconfig    2.0.1   2017-03-21 CRAN (R 3.4.0)
    ##  plyr         1.8.4   2016-06-08 CRAN (R 3.4.0)
    ##  psych        1.7.8   2017-09-09 CRAN (R 3.4.3)
    ##  purrr      * 0.2.4   2017-10-18 CRAN (R 3.4.2)
    ##  R6           2.2.2   2017-06-17 CRAN (R 3.4.0)
    ##  Rcpp         0.12.15 2018-01-20 CRAN (R 3.4.3)
    ##  readr      * 1.1.1   2017-05-16 CRAN (R 3.4.0)
    ##  readxl       1.0.0   2017-04-18 CRAN (R 3.4.0)
    ##  reshape2     1.4.3   2017-12-11 CRAN (R 3.4.3)
    ##  rlang        0.1.6   2017-12-21 CRAN (R 3.4.3)
    ##  rmarkdown    1.8     2017-11-17 CRAN (R 3.4.2)
    ##  rprojroot    1.3-2   2018-01-03 CRAN (R 3.4.3)
    ##  rstudioapi   0.7     2017-09-07 CRAN (R 3.4.1)
    ##  rvest        0.3.2   2016-06-17 CRAN (R 3.4.0)
    ##  scales       0.5.0   2017-08-24 CRAN (R 3.4.1)
    ##  stats      * 3.4.3   2017-12-07 local         
    ##  stringi      1.1.6   2017-11-17 CRAN (R 3.4.2)
    ##  stringr    * 1.2.0   2017-02-18 CRAN (R 3.4.0)
    ##  tibble     * 1.4.2   2018-01-22 CRAN (R 3.4.3)
    ##  tidyr      * 0.7.2   2017-10-16 CRAN (R 3.4.2)
    ##  tidyverse  * 1.2.1   2017-11-14 CRAN (R 3.4.2)
    ##  tools        3.4.3   2017-12-07 local         
    ##  utf8         1.1.3   2018-01-03 CRAN (R 3.4.3)
    ##  utils      * 3.4.3   2017-12-07 local         
    ##  withr        2.1.1   2017-12-19 CRAN (R 3.4.3)
    ##  xml2         1.2.0   2018-01-24 CRAN (R 3.4.3)
    ##  yaml         2.1.16  2017-12-12 CRAN (R 3.4.3)

``` r
sessionInfo()
```

    ## R version 3.4.3 (2017-11-30)
    ## Platform: x86_64-apple-darwin15.6.0 (64-bit)
    ## Running under: OS X El Capitan 10.11.6
    ## 
    ## Matrix products: default
    ## BLAS: /Library/Frameworks/R.framework/Versions/3.4/Resources/lib/libRblas.0.dylib
    ## LAPACK: /Library/Frameworks/R.framework/Versions/3.4/Resources/lib/libRlapack.dylib
    ## 
    ## locale:
    ## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] bindrcpp_0.2    forcats_0.2.0   stringr_1.2.0   dplyr_0.7.4    
    ##  [5] purrr_0.2.4     readr_1.1.1     tidyr_0.7.2     tibble_1.4.2   
    ##  [9] ggplot2_2.2.1   tidyverse_1.2.1 fs_1.1.0       
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] reshape2_1.4.3   haven_1.1.1      lattice_0.20-35  colorspace_1.3-2
    ##  [5] htmltools_0.3.6  yaml_2.1.16      utf8_1.1.3       rlang_0.1.6     
    ##  [9] pillar_1.1.0     withr_2.1.1      foreign_0.8-69   glue_1.2.0      
    ## [13] modelr_0.1.1     readxl_1.0.0     bindr_0.1        plyr_1.8.4      
    ## [17] munsell_0.4.3    gtable_0.2.0     cellranger_1.1.0 rvest_0.3.2     
    ## [21] devtools_1.13.4  memoise_1.1.0    psych_1.7.8      evaluate_0.10.1 
    ## [25] knitr_1.19       parallel_3.4.3   broom_0.4.3      Rcpp_0.12.15    
    ## [29] backports_1.1.2  scales_0.5.0     jsonlite_1.5     mnormt_1.5-5    
    ## [33] hms_0.4.1        digest_0.6.14    stringi_1.1.6    grid_3.4.3      
    ## [37] rprojroot_1.3-2  cli_1.0.0        tools_3.4.3      magrittr_1.5    
    ## [41] lazyeval_0.2.1   crayon_1.3.4     pkgconfig_2.0.1  xml2_1.2.0      
    ## [45] lubridate_1.7.1  assertthat_0.2.0 rmarkdown_1.8    httr_1.3.1      
    ## [49] rstudioapi_0.7   R6_2.2.2         nlme_3.1-131     compiler_3.4.3
