Data Manipulation with dplyr
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.0      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
knitr::opts_chunk$set(
  collapse = TRUE,
  fig.width = 6,
  fig.asp = 0.6,
  out.width = "90%"
)
```

``` r
options(tibble.print_min = 3)
litters_data = 
  read_csv("data_import_examples/FAS_litters.csv")
## Rows: 49 Columns: 8
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): Group, Litter Number
## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

litters_data = 
  janitor:: clean_names(litters_data)

pups_data = read.csv("data_import_examples/FAS_pups.csv")
pups_data = janitor::clean_names(pups_data)
```

\##This is where you use select

Select just tells R to select which columns to show a

``` r
select (litters_data, group, litter_number, gd0_weight, pups_born_alive )
## # A tibble: 49 × 4
##   group litter_number gd0_weight pups_born_alive
##   <chr> <chr>              <dbl>           <dbl>
## 1 Con7  #85                 19.7               3
## 2 Con7  #1/2/95/2           27                 8
## 3 Con7  #5/5/3/83/3-3       26                 6
## # … with 46 more rows

select(litters_data, group:gd_of_birth)
## # A tibble: 49 × 5
##   group litter_number gd0_weight gd18_weight gd_of_birth
##   <chr> <chr>              <dbl>       <dbl>       <dbl>
## 1 Con7  #85                 19.7        34.7          20
## 2 Con7  #1/2/95/2           27          42            19
## 3 Con7  #5/5/3/83/3-3       26          41.4          19
## # … with 46 more rows
```

With select, you can also specify which columns you want to remove (put
a - sign)

``` r
select(litters_data, -pups_survive)
## # A tibble: 49 × 7
##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_…¹
##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>   <dbl>
## 1 Con7  #85                 19.7        34.7          20               3       4
## 2 Con7  #1/2/95/2           27          42            19               8       0
## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6       0
## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth
select(litters_data, -pups_survive, -group)
## # A tibble: 49 × 6
##   litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_dead_b…¹
##   <chr>              <dbl>       <dbl>       <dbl>           <dbl>         <dbl>
## 1 #85                 19.7        34.7          20               3             4
## 2 #1/2/95/2           27          42            19               8             0
## 3 #5/5/3/83/3-3       26          41.4          19               6             0
## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth
```

You can rename variables as part of this process

old name is on the right of equal sign and new name is on the left side

``` r
select(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
## # A tibble: 49 × 2
##   GROUP LiTtEr_NuMbEr
##   <chr> <chr>        
## 1 Con7  #85          
## 2 Con7  #1/2/95/2    
## 3 Con7  #5/5/3/83/3-3
## # … with 46 more rows
```

Using “rename” instead of select indicates that you want everything else
to stay the same and keep in the dataframe except the variable ones you
indicate that you want to change

``` r
rename(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number )
## # A tibble: 49 × 8
##   GROUP LiTtEr_NuMbEr gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
##   <chr> <chr>              <dbl>       <dbl>       <dbl>   <dbl>   <dbl>   <dbl>
## 1 Con7  #85                 19.7        34.7          20       3       4       3
## 2 Con7  #1/2/95/2           27          42            19       8       0       7
## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
## # … with 46 more rows, and abbreviated variable names ¹​pups_born_alive,
## #   ²​pups_dead_birth, ³​pups_survive
```

Functions that you can use within select: `starts_with()`,`ends_with()`
and `contains()`.

ex. This selects all the variables that start with gd:

``` r
select(litters_data, starts_with("gd"))
## # A tibble: 49 × 3
##   gd0_weight gd18_weight gd_of_birth
##        <dbl>       <dbl>       <dbl>
## 1       19.7        34.7          20
## 2       27          42            19
## 3       26          41.4          19
## # … with 46 more rows
```

you can also reorganize the dataset without discarding anything using
`everything()`.

``` r
select (litters_data, litter_number, pups_survive, everything())
## # A tibble: 49 × 8
##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
##   <chr>                <dbl> <chr>      <dbl>      <dbl>   <dbl>   <dbl>   <dbl>
## 1 #85                      3 Con7        19.7       34.7      20       3       4
## 2 #1/2/95/2                7 Con7        27         42        19       8       0
## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth
```

Selecting rows (a certain individual): use filter()

normally you filter using comparison measures: (\<,\>=,\>, \<=, ==,!=)
or (& or \|), %N% (Character variables)

`gd_of_birth == 20` `!(pups_survive == 4)`

``` r
filter(litters_data, group == "Con7" & gd_of_birth == 20)
## # A tibble: 4 × 8
##   group litter_number   gd0_weight gd18_weight gd_of_b…¹ pups_…² pups_…³ pups_…⁴
##   <chr> <chr>                <dbl>       <dbl>     <dbl>   <dbl>   <dbl>   <dbl>
## 1 Con7  #85                   19.7        34.7        20       3       4       3
## 2 Con7  #4/2/95/3-3           NA          NA          20       6       0       6
## 3 Con7  #2/2/95/3-2           NA          NA          20       6       0       4
## 4 Con7  #1/5/3/83/3-3/2       NA          NA          20       9       0       9
## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
## #   ³​pups_dead_birth, ⁴​pups_survive
```

A common filtering step is to drop missing data on variables “drop_na”

`drop_na(litters_data) will remove any row with a missing value`

`drop_na(litters_data, wt_increase)` will remove rows for which
`wt_increase` is missing.

“Mutate” is used to change or create new select columns

ex. can look at a new variable that finds the difference between
gd18_weight and gd0_weight.

``` r
#litter_data2 =
mutate(litters_data,
       wt_gain = gd18_weight - gd0_weight,
       group = str_to_lower (group),
       # wt_gain_kg = wt_gain * 2.2
       )
## # A tibble: 49 × 9
##   group litter_number gd0_weight gd18_…¹ gd_of…² pups_…³ pups_…⁴ pups_…⁵ wt_gain
##   <chr> <chr>              <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>
## 1 con7  #85                 19.7    34.7      20       3       4       3    15  
## 2 con7  #1/2/95/2           27      42        19       8       0       7    15  
## 3 con7  #5/5/3/83/3-3       26      41.4      19       6       0       5    15.4
## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth, ⁵​pups_survive
```
