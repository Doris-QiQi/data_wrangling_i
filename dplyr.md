Data manipulation with `dplyr`
================
Jingyi Yao
2022-09-23

## Preparation Work

### `options()` the global printing settings

#### `options(tibble.print_min = number)` controls how many rows it will show

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
options(tibble.print_min = 3) # it will show at least 3 rows of data
```

### load the litters and puppy data set

#### `clean_names()` delete the blanks and substitute all symbols with `_`

#### `read_csv(col_types="cdi")`controls the variable types

-   `c` for character
-   `d` for doubles
-   `i` for integer

``` r
litters_data = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")      # control the types of variables
litters_data = janitor::clean_names(litters_data)

pups_data = read_csv("./data/FAS_pups.csv",
  col_types = "ciiiii")
pups_data = janitor::clean_names(pups_data) # clean names with _ and eliminate other symbols
```

################################################################################################### 

## I. `select` columns

#### only need a subset of the columns in a data table

### 1. `select()` fucntion

#### specify the columns you want to keep by **naming** all of them:

#### you need to type the column names

#### the first arugument is the **df name**

``` r
select(litters_data, group, litter_number, gd0_weight, pups_born_alive)
```

    ## # A tibble: 49 × 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <int>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # … with 46 more rows

### 2. specify a *range of columns*

#### do not need to type all the names

#### `group:gd_of_birth` means from `group` to `gd_of_birth`

``` r
select(litters_data, group:gd_of_birth) 
```

    ## # A tibble: 49 × 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <int>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # … with 46 more rows

### 3. *remove* columns:

#### put a minus sign `-` before the column name

``` r
select(litters_data, -pups_survive)
```

    ## # A tibble: 49 × 7
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_…¹
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20               3       4
    ## 2 Con7  #1/2/95/2           27          42            19               8       0
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6       0
    ## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth

### 4. `rename(new_col_name = old_col_name)` the variable *while selecting* variables

#### format: `new_name = old name`

``` r
select(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 × 2
    ##   GROUP LiTtEr_NuMbEr
    ##   <chr> <chr>        
    ## 1 Con7  #85          
    ## 2 Con7  #1/2/95/2    
    ## 3 Con7  #5/5/3/83/3-3
    ## # … with 46 more rows

### 5. `remame()` function

#### rename but will return all the variables *without selecting*

``` r
rename(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)  # a function in tidyverse
```

    ## # A tibble: 49 × 8
    ##   GROUP LiTtEr_NuMbEr gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20       3       4       3
    ## 2 Con7  #1/2/95/2           27          42            19       8       0       7
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
    ## # … with 46 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth, ³​pups_survive

### 6. `starts_with()`, `ends_with()`, `contains()` in `select()`

#### using `?select_helpers`

#### when there variables are named with *suffixes* or other *standard patterns*:

``` r
select(litters_data, starts_with("gd"))    # variable starts with gd
```

    ## # A tibble: 49 × 3
    ##   gd0_weight gd18_weight gd_of_birth
    ##        <dbl>       <dbl>       <int>
    ## 1       19.7        34.7          20
    ## 2       27          42            19
    ## 3       26          41.4          19
    ## # … with 46 more rows

``` r
select(litters_data, ends_with("weight"))  # variable ends with weight
```

    ## # A tibble: 49 × 2
    ##   gd0_weight gd18_weight
    ##        <dbl>       <dbl>
    ## 1       19.7        34.7
    ## 2       27          42  
    ## 3       26          41.4
    ## # … with 46 more rows

``` r
select(litters_data, contains("gd"))       # variable contains gd
```

    ## # A tibble: 49 × 3
    ##   gd0_weight gd18_weight gd_of_birth
    ##        <dbl>       <dbl>       <int>
    ## 1       19.7        34.7          20
    ## 2       27          42            19
    ## 3       26          41.4          19
    ## # … with 46 more rows

``` r
?select_helpers
```

    ## Help on topic 'select_helpers' was found in the following packages:
    ## 
    ##   Package               Library
    ##   tidyselect            /Library/Frameworks/R.framework/Versions/4.2-arm64/Resources/library
    ##   vroom                 /Library/Frameworks/R.framework/Versions/4.2-arm64/Resources/library
    ##   dplyr                 /Library/Frameworks/R.framework/Versions/4.2-arm64/Resources/library
    ##   tidyr                 /Library/Frameworks/R.framework/Versions/4.2-arm64/Resources/library
    ## 
    ## 
    ## Using the first match ...

### 7. `everything()` in `select()`

#### put the selected columns before `everything()`

#### the selected columns will be put at the front

#### the columns not selected will be placed after the selected ones

``` r
select(litters_data, litter_number, pups_survive, everything())
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
    ##   <chr>                <int> <chr>      <dbl>      <dbl>   <int>   <int>   <int>
    ## 1 #85                      3 Con7        19.7       34.7      20       3       4
    ## 2 #1/2/95/2                7 Con7        27         42        19       8       0
    ## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

### 8. `relocate()` *NOT* in `select()`

#### similar to everything()

#### but `relocate()` is a function outside of `select`

#### the first argument is the df name

``` r
relocate(litters_data, litter_number, pups_survive)
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
    ##   <chr>                <int> <chr>      <dbl>      <dbl>   <int>   <int>   <int>
    ## 1 #85                      3 Con7        19.7       34.7      20       3       4
    ## 2 #1/2/95/2                7 Con7        27         42        19       8       0
    ## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

################################################################################################### 

## II. `filter()` – filter out *rows*

### comparision: `select()` operates on **columns** and `filter()` operates on **rows**

### 1. important operators:

-   comparison operators `>, >=, <, <=, ==, and !=`.
-   `%in%` to detect if values appear in a set
-   `is.na()` to find missing values.
-   logical operators `&` and `|`or negated using `!`

### 2. exmamples:

-   `gd_of_birth == 20`
-   `pups_born_alive >= 2`
-   `pups_survive != 4` not equal to
-   `!(pups_survive == 4)` exclude
-   `group %in% c("Con7", "Con8")` column name in a vector
-   `group == "Con7" & gd_of_birth == 20`

### 3. omit missing observations.

#### recommend: using `drop_na` from the `tidyr` package:

#### format : `drop_na(df,colname)`means: drop the rows in df where the column has missing val

-   `drop_na(litters_data)` will remove **any row** with a missing value
-   `drop_na(litters_data, wt_increase)` will remove rows for which
    `wt_increase` is missing.

### 4. basic examples

``` r
filter(pups_data, sex == 1)
```

    ## # A tibble: 155 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #85               1       4      13        7      11
    ## 2 #85               1       4      13        7      12
    ## 3 #1/2/95/2         1       5      13        7       9
    ## # … with 152 more rows

``` r
filter(pups_data, sex == 2, pd_walk < 11)
```

    ## # A tibble: 127 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #1/2/95/2         2       4      13        7       9
    ## 2 #1/2/95/2         2       4      13        7      10
    ## 3 #1/2/95/2         2       5      13        8      10
    ## # … with 124 more rows

################################################################################################### 

## III. `mutate()` *create new variables* to the tibble

### 1. `mutate(new_col = function(old_columns))` add new columns using the old columns

-   Your new variables can be functions of old variables
-   New variables appear **at the end** of the df in the order that they
    are created
-   You can overwrite old variables
-   You can create a new variable and immediately refer to (or change)
    it

#### `str_to_lower(col)` change the character all to lower case

``` r
mutate(litters_data,
  wt_gain = gd18_weight - gd0_weight,
  group = str_to_lower(group)          # change capital to lower 
)
```

    ## # A tibble: 49 × 9
    ##   group litter_number gd0_weight gd18_…¹ gd_of…² pups_…³ pups_…⁴ pups_…⁵ wt_gain
    ##   <chr> <chr>              <dbl>   <dbl>   <int>   <int>   <int>   <int>   <dbl>
    ## 1 con7  #85                 19.7    34.7      20       3       4       3    15  
    ## 2 con7  #1/2/95/2           27      42        19       8       0       7    15  
    ## 3 con7  #5/5/3/83/3-3       26      41.4      19       6       0       5    15.4
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth, ⁵​pups_survive

################################################################################################### 

## IV. `arrange()` sort the data

#### format: `arrange(df, col_1,col_2,...)`

#### we may arrange the df by many columns but they have an internla order

#### the default order is ascending, otherwise we need to add `desc(col)` to change it

``` r
head(arrange(litters_data, group, pups_born_alive), 10) # default : ascending
```

    ## # A tibble: 10 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_…¹ pups_…² pups_…³ pups_…⁴
    ##    <chr> <chr>                <dbl>       <dbl>    <int>   <int>   <int>   <int>
    ##  1 Con7  #85                   19.7        34.7       20       3       4       3
    ##  2 Con7  #5/4/2/95/2           28.5        44.1       19       5       1       4
    ##  3 Con7  #5/5/3/83/3-3         26          41.4       19       6       0       5
    ##  4 Con7  #4/2/95/3-3           NA          NA         20       6       0       6
    ##  5 Con7  #2/2/95/3-2           NA          NA         20       6       0       4
    ##  6 Con7  #1/2/95/2             27          42         19       8       0       7
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA         20       9       0       9
    ##  8 Con8  #2/2/95/2             NA          NA         19       5       0       4
    ##  9 Con8  #1/6/2/2/95-2         NA          NA         20       7       0       6
    ## 10 Con8  #3/6/2/2/95-3         NA          NA         20       7       0       7
    ## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
    ## #   ³​pups_dead_birth, ⁴​pups_survive

``` r
head(arrange(litters_data, desc(group), pups_born_alive), 10) # change : desc(col_name)
```

    ## # A tibble: 10 × 8
    ##    group litter_number gd0_weight gd18_weight gd_of_bi…¹ pups_…² pups_…³ pups_…⁴
    ##    <chr> <chr>              <dbl>       <dbl>      <int>   <int>   <int>   <int>
    ##  1 Mod8  #7/82-3-2           26.9        43.2         20       7       0       7
    ##  2 Mod8  #97                 24.5        42.8         20       8       1       8
    ##  3 Mod8  #5/93/2             NA          NA           19       8       0       8
    ##  4 Mod8  #7/110/3-2          27.5        46           19       8       1       8
    ##  5 Mod8  #82/4               33.4        52.7         20       8       0       6
    ##  6 Mod8  #2/95/2             28.5        44.5         20       9       0       9
    ##  7 Mod8  #5/93               NA          41.1         20      11       0       9
    ##  8 Mod7  #3/82/3-2           28          45.9         20       5       0       5
    ##  9 Mod7  #5/3/83/5-2         22.6        37           19       5       0       5
    ## 10 Mod7  #106                21.7        37.8         20       5       0       2
    ## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
    ## #   ³​pups_dead_birth, ⁴​pups_survive

################################################################################################### 

## V. pipes: `%>%` for *multi-step* data manipulation

### Cmd + Shift + M (Mac) to generate `%>%`

``` r
litters_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%
  janitor::clean_names() %>%
  select(-pups_survive) %>%
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)) %>% 
  drop_na(wt_gain)

litters_data
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
    ## 1 con7  #85                 19.7        34.7          20       3       4    15  
    ## 2 con7  #1/2/95/2           27          42            19       8       0    15  
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
    ## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth

#### except select,mutate,arrange,filter, we can use `%>%` for other functions

#### in `lm()`,the argument `data=.` cannot be left out, we use `.` to represent the data

``` r
litters_data %>%
  lm(wt_gain ~ pups_born_alive, data = .) %>%    # data = . must be written
  broom::tidy()
```

    ## # A tibble: 2 × 5
    ##   term            estimate std.error statistic  p.value
    ##   <chr>              <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)       13.1       1.27      10.3  3.39e-11
    ## 2 pups_born_alive    0.605     0.173      3.49 1.55e- 3

################################################################################################### 

## VI. Learning Check:

#### Learning Assessment: Write a chain of commands that:

-   loads the pups data
-   cleans the variable names
-   filters the data to include only pups with sex 1
-   removes the PD ears variable
-   creates a variable that indicates whether PD pivot is 7 or more days

``` r
df <- pups_data %>% janitor::clean_names() %>% filter(sex==1) %>% select(- pd_ears)%>% mutate(PD_status = pd_pivot>7) 
df
```

    ## # A tibble: 155 × 6
    ##   litter_number   sex pd_eyes pd_pivot pd_walk PD_status
    ##   <chr>         <int>   <int>    <int>   <int> <lgl>    
    ## 1 #85               1      13        7      11 FALSE    
    ## 2 #85               1      13        7      12 FALSE    
    ## 3 #1/2/95/2         1      13        7       9 FALSE    
    ## # … with 152 more rows
