---
title: "Group 5 — Heart failure and Asia"
subtitle: "Studio wall"
author: "Group names here"
format:
  html:
    toc: true
    toc-depth: 2
---

# Spine

> Who dies after heart failure, who grew up poor in the GSS, and where in Asia is life short?

This sentence stays at the top. Every panel must attach to it. If a plot cannot be introduced in a clause that hangs off this line, it is off the wall.

# Introduction — the tables we own

This folder is ours for the ten days. Three tables, three shapes.

- **Panel A** (`panel-A.csv`) — heart-failure clinic file. 0/1 flags have been recoded to yes/no. Quants: age, ejection fraction, serum measures, time.
- **Panel B** (`panel-B.csv`) — GSS 1974–2002: ethnicity, city at 16, low income at 16, immigrant, plus years of education (Quant).
- **Panel C** (`panel-C.csv`) — gapminder 2007, **Asia only**. Stand-in for an NFHS / malaria map. India is one country row, not districts.

C is not a Bangalore street map. If we want feet-on-ground geography, that is a different brick (OSM), not this file.

We load from this folder:


::: {.cell}

```{.r .cell-code}
library(tidyverse)
```

::: {.cell-output .cell-output-stderr}

```
── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
✔ dplyr     1.2.1     ✔ readr     2.2.0
✔ forcats   1.0.1     ✔ stringr   1.6.0
✔ ggplot2   4.0.3     ✔ tibble    3.3.1
✔ lubridate 1.9.5     ✔ tidyr     1.3.2
✔ purrr     1.2.2     
── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```


:::

```{.r .cell-code}
library(usethis)
library(skimr)
library(mosaic)
```

::: {.cell-output .cell-output-stderr}

```
Registered S3 method overwritten by 'mosaic':
  method                           from   
  fortify.SpatialPolygonsDataFrame ggplot2

The 'mosaic' package masks several functions from core packages in order to add 
additional features.  The original behavior of these functions should not be affected by this.

Attaching package: 'mosaic'

The following object is masked from 'package:Matrix':

    mean

The following object is masked from 'package:skimr':

    n_missing

The following objects are masked from 'package:dplyr':

    count, do, tally

The following object is masked from 'package:purrr':

    cross

The following object is masked from 'package:ggplot2':

    stat

The following objects are masked from 'package:stats':

    binom.test, cor, cor.test, cov, fivenum, IQR, median, prop.test,
    quantile, sd, t.test, var

The following objects are masked from 'package:base':

    max, mean, min, prod, range, sample, sum
```


:::

```{.r .cell-code}
A <- read_csv("data/panel-A.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 299 Columns: 13
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (6): anaemia, diabetes, high_blood_pressure, sex, smoking, DEATH_EVENT
dbl (7): age, creatinine_phosphokinase, ejection_fraction, platelets, serum_...

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::

```{.r .cell-code}
B <- read_csv("data/panel-B.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 9120 Columns: 6
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (4): ethnicity, city16, lowincome16, immigrant
dbl (2): kids, education

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::

```{.r .cell-code}
C <- read_csv("data/panel-C.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 33 Columns: 6
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (2): country, continent
dbl (4): year, lifeExp, pop, gdpPercap

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::

```{.r .cell-code}
glimpse(A)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 299
Columns: 13
$ age                      <dbl> 75, 55, 65, 50, 65, 90, 75, 60, 65, 80, 75, 6…
$ anaemia                  <chr> "no", "no", "no", "yes", "yes", "yes", "yes",…
$ creatinine_phosphokinase <dbl> 582, 7861, 146, 111, 160, 47, 246, 315, 157, …
$ diabetes                 <chr> "no", "no", "no", "no", "yes", "no", "no", "y…
$ ejection_fraction        <dbl> 20, 38, 20, 20, 20, 40, 15, 60, 65, 35, 38, 2…
$ high_blood_pressure      <chr> "yes", "no", "no", "no", "no", "yes", "no", "…
$ platelets                <dbl> 265000, 263358, 162000, 210000, 327000, 20400…
$ serum_creatinine         <dbl> 1.90, 1.10, 1.30, 1.90, 2.70, 2.10, 1.20, 1.1…
$ serum_sodium             <dbl> 130, 136, 129, 137, 116, 132, 137, 131, 138, …
$ sex                      <chr> "male", "male", "male", "male", "female", "ma…
$ smoking                  <chr> "no", "no", "yes", "no", "no", "yes", "no", "…
$ time                     <dbl> 4, 6, 7, 7, 8, 8, 10, 10, 10, 10, 10, 10, 11,…
$ DEATH_EVENT              <chr> "yes", "yes", "yes", "yes", "yes", "yes", "ye…
```


:::

```{.r .cell-code}
inspect(A)
```

::: {.cell-output .cell-output-stdout}

```

categorical variables:  
                 name     class levels   n missing
1             anaemia character      2 299       0
2            diabetes character      2 299       0
3 high_blood_pressure character      2 299       0
4                 sex character      2 299       0
5             smoking character      2 299       0
6         DEATH_EVENT character      2 299       0
                                   distribution
1 no (56.9%), yes (43.1%)                      
2 no (58.2%), yes (41.8%)                      
3 no (64.9%), yes (35.1%)                      
4 male (64.9%), female (35.1%)                 
5 no (67.9%), yes (32.1%)                      
6 no (67.9%), yes (32.1%)                      

quantitative variables:  
                      name   class     min       Q1   median       Q3      max
1                      age numeric    40.0     51.0     60.0     70.0     95.0
2 creatinine_phosphokinase numeric    23.0    116.5    250.0    582.0   7861.0
3        ejection_fraction numeric    14.0     30.0     38.0     45.0     80.0
4                platelets numeric 25100.0 212500.0 262000.0 303500.0 850000.0
5         serum_creatinine numeric     0.5      0.9      1.1      1.4      9.4
6             serum_sodium numeric   113.0    134.0    137.0    140.0    148.0
7                     time numeric     4.0     73.0    115.0    203.0    285.0
          mean           sd   n missing
1     60.83389    11.894809 299       0
2    581.83946   970.287881 299       0
3     38.08361    11.834841 299       0
4 263358.02926 97804.236869 299       0
5      1.39388     1.034510 299       0
6    136.62542     4.412477 299       0
7    130.26087    77.614208 299       0
```


:::

```{.r .cell-code}
skim(A)
```

::: {.cell-output-display}

Table: Data summary

|                         |     |
|:------------------------|:----|
|Name                     |A    |
|Number of rows           |299  |
|Number of columns        |13   |
|_______________________  |     |
|Column type frequency:   |     |
|character                |6    |
|numeric                  |7    |
|________________________ |     |
|Group variables          |None |


**Variable type: character**

|skim_variable       | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|anaemia             |         0|             1|   2|   3|     0|        2|          0|
|diabetes            |         0|             1|   2|   3|     0|        2|          0|
|high_blood_pressure |         0|             1|   2|   3|     0|        2|          0|
|sex                 |         0|             1|   4|   6|     0|        2|          0|
|smoking             |         0|             1|   2|   3|     0|        2|          0|
|DEATH_EVENT         |         0|             1|   2|   3|     0|        2|          0|


**Variable type: numeric**

|skim_variable            | n_missing| complete_rate|      mean|       sd|      p0|      p25|      p50|      p75|     p100|hist  |
|:------------------------|---------:|-------------:|---------:|--------:|-------:|--------:|--------:|--------:|--------:|:-----|
|age                      |         0|             1|     60.83|    11.89|    40.0|     51.0|     60.0|     70.0|     95.0|▆▇▇▂▁ |
|creatinine_phosphokinase |         0|             1|    581.84|   970.29|    23.0|    116.5|    250.0|    582.0|   7861.0|▇▁▁▁▁ |
|ejection_fraction        |         0|             1|     38.08|    11.83|    14.0|     30.0|     38.0|     45.0|     80.0|▃▇▂▂▁ |
|platelets                |         0|             1| 263358.03| 97804.24| 25100.0| 212500.0| 262000.0| 303500.0| 850000.0|▂▇▂▁▁ |
|serum_creatinine         |         0|             1|      1.39|     1.03|     0.5|      0.9|      1.1|      1.4|      9.4|▇▁▁▁▁ |
|serum_sodium             |         0|             1|    136.63|     4.41|   113.0|    134.0|    137.0|    140.0|    148.0|▁▁▃▇▁ |
|time                     |         0|             1|    130.26|    77.61|     4.0|     73.0|    115.0|    203.0|    285.0|▆▇▃▆▃ |


:::
:::




::: {.cell}

```{.r .cell-code}
A_Mod <- A |> dplyr:: mutate(high_blood_pressure =as.factor(high_blood_pressure),diabetes =as.factor(diabetes), anaemia =as.factor(anaemia), smoking =as.factor(smoking) )
glimpse(A_Mod)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 299
Columns: 13
$ age                      <dbl> 75, 55, 65, 50, 65, 90, 75, 60, 65, 80, 75, 6…
$ anaemia                  <fct> no, no, no, yes, yes, yes, yes, yes, no, yes,…
$ creatinine_phosphokinase <dbl> 582, 7861, 146, 111, 160, 47, 246, 315, 157, …
$ diabetes                 <fct> no, no, no, no, yes, no, no, yes, no, no, no,…
$ ejection_fraction        <dbl> 20, 38, 20, 20, 20, 40, 15, 60, 65, 35, 38, 2…
$ high_blood_pressure      <fct> yes, no, no, no, no, yes, no, no, no, yes, ye…
$ platelets                <dbl> 265000, 263358, 162000, 210000, 327000, 20400…
$ serum_creatinine         <dbl> 1.90, 1.10, 1.30, 1.90, 2.70, 2.10, 1.20, 1.1…
$ serum_sodium             <dbl> 130, 136, 129, 137, 116, 132, 137, 131, 138, …
$ sex                      <chr> "male", "male", "male", "male", "female", "ma…
$ smoking                  <fct> no, no, yes, no, no, yes, no, yes, no, yes, y…
$ time                     <dbl> 4, 6, 7, 7, 8, 8, 10, 10, 10, 10, 10, 10, 11,…
$ DEATH_EVENT              <chr> "yes", "yes", "yes", "yes", "yes", "yes", "ye…
```


:::
:::


Found tables train the mark. A campus Free Hunch, if we collect one, is a **made** panel of the same pair type — new people, old grammar. It does not replace A, B, or C.

# Panel A — body / measure

*ejection_fraction, serum_creatinine, age are Quant. DEATH_EVENT, diabetes, smoking are Quals.*

### 1. See the table

What is a case? Which columns are Quant / Qual? What is missing?

``` r
# glimpse() / inspect() / skim()
```

### 2. Ask
Possibly can answer:
1. Who smokes more, male or female?
2. How does the age distribution differ between patients who survived and patients who died?
3. Is the blood pressure of people who die higer or lower than the people who live 
4. Do patients with anaemia die more often? 
5. Does sodium level in the blood differ between patients who survived and died?
6. Is there a difference in heart-damage between diabetics and non-diabetics?
7. Is High blood pressure affected by age?
8. Does a weaker heart mean patients don't survive as long?

Possibly cannot answer:
1. Does getting older cause death in heart failure patients? (The data does not show that one causes the other)
2. What happened to patients after the study ended? (we don't know about later outcomes.)

```
### 3. Choose a mark
histogram, bargraph, box plot, scatter plots 

### 4. Predict
we predited that due to smoking, as people grow older thier chances od death is higher than younger onces 

### 5. Make
These first few charts are what I want to check out. I may/will not have these charts in the final rendered Quarto, but want to comment on findings if needed. If any chart is surprising, it stays. 
We have decided on choosing graph 2 as our final graph and therefore we did the t-test for it as well.

1.

::: {.cell}

```{.r .cell-code}
ggplot(A, aes(x = sex , fill = sex)) + geom_bar()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-3-1.png){width=672}
:::
:::


2.

::: {.cell}

```{.r .cell-code}
ggplot(A, aes(x = age)) + geom_histogram()
```

::: {.cell-output .cell-output-stderr}

```
`stat_bin()` using `bins = 30`. Pick better value `binwidth`.
```


:::

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-4-1.png){width=672}
:::

```{.r .cell-code}
ggplot(A, aes(x = age, fill = DEATH_EVENT)) +geom_histogram()
```

::: {.cell-output .cell-output-stderr}

```
`stat_bin()` using `bins = 30`. Pick better value `binwidth`.
```


:::

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-4-2.png){width=672}
:::
:::



::: {.cell}

```{.r .cell-code}
A |> gf_histogram(~ age, fill = ~ DEATH_EVENT)
```

::: {.cell-output .cell-output-stderr}

```
`stat_bin()` using `bins = 30`. Pick better value `binwidth`.
```


:::

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-5-1.png){width=672}
:::

```{.r .cell-code}
A |> 
  group_by(DEATH_EVENT) |> 
  summarize(mean_age = mean(age, na.rm = TRUE))
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 2 × 2
  DEATH_EVENT mean_age
  <chr>          <dbl>
1 no              58.8
2 yes             65.2
```


:::

```{.r .cell-code}
mosaic::t_test(age ~ DEATH_EVENT, data = A)
```

::: {.cell-output .cell-output-stdout}

```

	Welch Two Sample t-test

data:  age by DEATH_EVENT
t = -4.1862, df = 155.29, p-value = 4.735e-05
alternative hypothesis: true difference in means between group no and group yes is not equal to 0
95 percent confidence interval:
 -9.498546 -3.408204
sample estimates:
 mean in group no mean in group yes 
         58.76191          65.21528 
```


:::
:::


3.

::: {.cell}

```{.r .cell-code}
A |> ggplot(aes(x = age)) +
  geom_histogram()
```

::: {.cell-output .cell-output-stderr}

```
`stat_bin()` using `bins = 30`. Pick better value `binwidth`.
```


:::

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-6-1.png){width=672}
:::

```{.r .cell-code}
A |> ggplot(aes(x = age, fill = high_blood_pressure)) +
  geom_histogram()
```

::: {.cell-output .cell-output-stderr}

```
`stat_bin()` using `bins = 30`. Pick better value `binwidth`.
```


:::

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-6-2.png){width=672}
:::
:::



::: {.cell}

```{.r .cell-code}
A |> gf_histogram(~ age, fill = ~ high_blood_pressure)
```

::: {.cell-output .cell-output-stderr}

```
`stat_bin()` using `bins = 30`. Pick better value `binwidth`.
```


:::

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-7-1.png){width=672}
:::

```{.r .cell-code}
A |> 
  group_by(high_blood_pressure) |> 
  summarize(mean_age = mean(age, na.rm = TRUE))
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 2 × 2
  high_blood_pressure mean_age
  <chr>                  <dbl>
1 no                      60.0
2 yes                     62.3
```


:::

```{.r .cell-code}
mosaic::t_test(age ~ high_blood_pressure, data = A)
```

::: {.cell-output .cell-output-stdout}

```

	Welch Two Sample t-test

data:  age by high_blood_pressure
t = -1.6366, df = 221.74, p-value = 0.1031
alternative hypothesis: true difference in means between group no and group yes is not equal to 0
95 percent confidence interval:
 -5.1154412  0.4738739
sample estimates:
 mean in group no mean in group yes 
         60.01890          62.33969 
```


:::
:::


4.

::: {.cell}

```{.r .cell-code}
ggplot(A, aes(x = anaemia)) +geom_bar()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-8-1.png){width=672}
:::

```{.r .cell-code}
ggplot(A, aes(x = anaemia, fill = DEATH_EVENT)) +geom_bar()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-8-2.png){width=672}
:::
:::


5.

::: {.cell}

```{.r .cell-code}
ggplot(A, aes(x = DEATH_EVENT, y = serum_sodium)) +geom_boxplot()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-9-1.png){width=672}
:::

```{.r .cell-code}
ggplot(A, aes(x = DEATH_EVENT, y = serum_sodium, fill = DEATH_EVENT)) +geom_boxplot()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-9-2.png){width=672}
:::
:::


6.

::: {.cell}

```{.r .cell-code}
ggplot(A, aes(x = time)) + geom_histogram()
```

::: {.cell-output .cell-output-stderr}

```
`stat_bin()` using `bins = 30`. Pick better value `binwidth`.
```


:::

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-10-1.png){width=672}
:::
:::


7.

::: {.cell}

```{.r .cell-code}
A |> ggplot(aes(x = high_blood_pressure, y = age)) +
  geom_boxplot()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-11-1.png){width=672}
:::
:::


9.

::: {.cell}

```{.r .cell-code}
A |> ggplot(aes(x = ejection_fraction, y = time)) +geom_point()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-12-1.png){width=672}
:::

```{.r .cell-code}
A |> ggplot(aes(x = ejection_fraction, y = time, color = DEATH_EVENT)) +
  geom_point()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-12-2.png){width=672}
:::
:::


# human chunk

# ## Prompt we actually used
# ai_draft
# ours
```
### 6. Say
Patients who died were older on average (about 65 years) than those who survived (about 59). The difference of roughly 6.5 years is statistically significant (t = -4.19, p= 4.735e-05 (0.00004735), p < 0.001) 

### 7. Limit
We can see patterns in who died, but we cannot say what caused the deaths or whether these patterns apply to everyone with heart failure.



# Panel B — category / talk

*immigrant × lowincome16, ethnicity × city16. education is years — Quant. Do not average ethnicity.*

::: {.cell}

```{.r .cell-code}
glimpse(B)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 9,120
Columns: 6
$ kids        <dbl> 0, 1, 1, 2, 2, 0, 0, 0, 1, 3, 6, 2, 2, 4, 0, 1, 0, 1, 0, 0…
$ education   <dbl> 14, 13, 2, 16, 12, 13, 12, 12, 12, 12, 13, 16, 12, 14, 17,…
$ ethnicity   <chr> "cauc", "cauc", "cauc", "cauc", "cauc", "other", "cauc", "…
$ city16      <chr> "no", "yes", "no", "no", "yes", "yes", "no", "no", "yes", …
$ lowincome16 <chr> "no", "no", "no", "no", "no", "no", "no", "no", "no", "no"…
$ immigrant   <chr> "no", "no", "yes", "no", "no", "no", "no", "yes", "yes", "…
```


:::
:::


::: {.cell}

```{.r .cell-code}
skim(B)
```

::: {.cell-output-display}

Table: Data summary

|                         |     |
|:------------------------|:----|
|Name                     |B    |
|Number of rows           |9120 |
|Number of columns        |6    |
|_______________________  |     |
|Column type frequency:   |     |
|character                |4    |
|numeric                  |2    |
|________________________ |     |
|Group variables          |None |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|ethnicity     |         0|             1|   4|   5|     0|        2|          0|
|city16        |         0|             1|   2|   3|     0|        2|          0|
|lowincome16   |         0|             1|   2|   3|     0|        2|          0|
|immigrant     |         0|             1|   2|   3|     0|        2|          0|


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|  mean|   sd| p0| p25| p50| p75| p100|hist  |
|:-------------|---------:|-------------:|-----:|----:|--:|---:|---:|---:|----:|:-----|
|kids          |         0|             1|  2.08| 1.81|  0|   1|   2|   3|    8|▇▇▂▁▁ |
|education     |         0|             1| 12.64| 2.96|  0|  12|  12|  14|   20|▁▁▇▆▂ |


:::
:::


### 1. See the table

What is a case? Which columns are Quant / Qual? What is missing?
                   
### 2. Ask
1. Were immigrants more likely to grow up in a low-income family?
Immigrants and low income at 16 (shown through education) 
2. Does education differ between people who grew up low-income and those who didn't?
Education and low income at 16
3. Does growing up low-income relate to number of kids? (Main Character)
4. Do people with more education have fewer kids?
5. Do immigrants have a different number of kids? 
6. How many years of education do people have?


### 5. Make
We have decided on choosing graph 1 and 5 as our final graph and therefore we did the mosaic graph for it as well.

1.

::: {.cell}

```{.r .cell-code}
B |> gf_bar(~ immigrant, fill = ~ lowincome16, position = "fill") |> gf_labs(title = "Share who grew up low-income, by immigrant status", x = "Immigrant", y = "Proportion", fill = "Low income at 16")
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-15-1.png){width=672}
:::
:::


::: {.cell}

```{.r .cell-code}
vcd::structable( lowincome16 ~ immigrant, data=B) |> vcd:: mosaic()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-16-1.png){width=672}
:::

```{.r .cell-code}
vcd::structable(lowincome16 ~ immigrant, data = B) |> vcd::mosaic(type = "expected")
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-16-2.png){width=672}
:::

```{.r .cell-code}
mosaic::xchisq.test(lowincome16~ immigrant, data = B)
```

::: {.cell-output .cell-output-stdout}

```

	Pearson's Chi-squared test with Yates' continuity correction

data:  x
X-squared = 0.016678, df = 1, p-value = 0.8972

  6394      788  
(6396.07) ( 785.92)
[0.00039] [0.00316]
<-0.026> < 0.074>
   
  1728      210  
(1725.92) ( 212.07)
[0.00144] [0.01170]
< 0.050> <-0.142>
   
key:
	observed
	(expected)
	[contribution to X-squared]
	<Pearson residual>
```


:::
:::


2. 

::: {.cell}

```{.r .cell-code}
B |>
  group_by(lowincome16) |>
  summarize(mean_edu = mean(education)) |>
  gf_col(mean_edu ~ lowincome16, fill = ~ lowincome16) |>
  gf_labs(title = "Average years of education by low income at 16",
          x = "Low income at 16", y = "Mean years of education")
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-17-1.png){width=672}
:::
:::

3.

::: {.cell}

```{.r .cell-code}
ggplot(B, aes(x = kids, fill = lowincome16)) +
  geom_bar(position = "dodge")
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-18-1.png){width=672}
:::
:::


4.

::: {.cell}

```{.r .cell-code}
ggplot(B, aes(x = education, fill = factor(kids))) +
  geom_bar()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-19-1.png){width=672}
:::
:::


5.

::: {.cell}

```{.r .cell-code}
ggplot(B, aes(x = kids, fill = immigrant)) +
  geom_bar(position = "dodge")
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-20-1.png){width=672}
:::
:::


::: {.cell}

```{.r .cell-code}
vcd::structable(kids ~ immigrant, data=B) |> vcd:: mosaic()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-21-1.png){width=672}
:::
:::


::: {.cell}

```{.r .cell-code}
vcd::structable(kids ~ immigrant, data = B) |> vcd::mosaic(type = "expected")
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-22-1.png){width=672}
:::

```{.r .cell-code}
mosaic::xchisq.test(kids~ immigrant, data = B)
```

::: {.cell-output .cell-output-stdout}

```

	Pearson's Chi-squared test

data:  x
X-squared = 8.658, df = 8, p-value = 0.372

  1890      237  
(1894.24) ( 232.76)
[0.0095] [0.0773]
<-0.097> < 0.278>
   
  1367      177  
(1375.04) ( 168.96)
[0.0470] [0.3826]
<-0.217> < 0.619>
   
  2091      247  
(2082.15) ( 255.85)
[0.0376] [0.3059]
< 0.194> <-0.553>
   
  1304      170  
(1312.70) ( 161.30)
[0.0577] [0.4693]
<-0.240> < 0.685>
   
   705       85  
( 703.55) (  86.45)
[0.0030] [0.0243]
< 0.055> <-0.156>
   
   334       42  
( 334.85) (  41.15)
[0.0022] [0.0177]
<-0.047> < 0.133>
   
   189       19  
( 185.24) (  22.76)
[0.0764] [0.6216]
< 0.276> <-0.788>
   
    87       13  
(  89.06) (  10.94)
[0.0475] [0.3867]
<-0.218> < 0.622>
   
   155        8  
( 145.16) (  17.84)
[0.6666] [5.4251]
< 0.816> <-2.329>
   
key:
	observed
	(expected)
	[contribution to X-squared]
	<Pearson residual>
```


:::
:::


6. 

::: {.cell}

```{.r .cell-code}
ggplot(B, aes(x = education)) +
  geom_bar()
```

::: {.cell-output-display}
![](index_files/figure-html/unnamed-chunk-23-1.png){width=672}
:::
:::


### 6. Say

Caption + one sentence we will stand behind. Title is a sentence, not a column name.

### 7. Limit

What this panel cannot do. Which kit table we refused to abuse.

# Panel C — place

*Asia-only lifeExp / gdpPercap / pop. Join to rnaturalearth; unmatched names are a brick.*


::: {.cell}

```{.r .cell-code}
glimpse(C)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 33
Columns: 6
$ country   <chr> "Afghanistan", "Bahrain", "Bangladesh", "Cambodia", "China",…
$ continent <chr> "Asia", "Asia", "Asia", "Asia", "Asia", "Asia", "Asia", "Asi…
$ year      <dbl> 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, …
$ lifeExp   <dbl> 43.828, 75.635, 64.062, 59.723, 72.961, 82.208, 64.698, 70.6…
$ pop       <dbl> 31889923, 708573, 150448339, 14131858, 1318683096, 6980412, …
$ gdpPercap <dbl> 974.5803, 29796.0483, 1391.2538, 1713.7787, 4959.1149, 39724…
```


:::
:::


::: {.cell}

```{.r .cell-code}
skim(C)
```

::: {.cell-output-display}

Table: Data summary

|                         |     |
|:------------------------|:----|
|Name                     |C    |
|Number of rows           |33   |
|Number of columns        |6    |
|_______________________  |     |
|Column type frequency:   |     |
|character                |2    |
|numeric                  |4    |
|________________________ |     |
|Group variables          |None |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|country       |         0|             1|   4|  18|     0|       33|          0|
|continent     |         0|             1|   4|   4|     0|        1|          0|


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|         mean|           sd|        p0|        p25|         p50|         p75|         p100|hist  |
|:-------------|---------:|-------------:|------------:|------------:|---------:|----------:|-----------:|-----------:|------------:|:-----|
|year          |         0|             1|      2007.00|         0.00|   2007.00|    2007.00|     2007.00|     2007.00| 2.007000e+03|▁▁▇▁▁ |
|lifeExp       |         0|             1|        70.73|         7.96|     43.83|      65.48|       72.40|       75.64| 8.260000e+01|▁▁▅▇▅ |
|pop           |         0|             1| 115513752.33| 289673399.13| 708573.00| 6426679.00| 24821286.00| 69453570.00| 1.318683e+09|▇▁▁▁▁ |
|gdpPercap     |         0|             1|     12473.03|     14154.94|    944.00|    2452.21|     4471.06|    22316.19| 4.730699e+04|▇▁▂▁▁ |


:::
:::



Geometry is not in the CSV. Join in R, then use the same seven headings.

``` r
# library(sf)
# library(rnaturalearth)
# world <- ne_countries(scale = "medium", returnclass = "sf")
# # join on country name; list the unmatched labels
```

### 1. See the table

What is a case? Which columns are Quant / Qual? What is missing?

``` r
# glimpse() / inspect() / skim()
```

### 2. Ask

One question *this* table can answer. One it cannot. Write both.

### 3. Choose a mark

Geom, map mark, or (later) test that matches the pair type. Name it before you code.

### 4. Predict

What do we think we will see? One sentence, before Run.

### 5. Make

Human brick first. Optional: one AI prompt, pasted below, only for what section 3 named.

``` r
# human chunk

# ## Prompt we actually used
# ai_draft
# ours
```

### 6. Say

Caption + one sentence we will stand behind. Title is a sentence, not a column name.

### 7. Limit

What this panel cannot do. Which kit table we refused to abuse.

# Conclusion

What the spine looks like after three panels.

- The question we will defend in ninety seconds
- What we will **not** claim
- Which lines were human, which were machine
- Limits: sample, missingness, the test we did not run
- Gift for the steal-this strip (palette, annotation, join trick)

``` r
# sessionInfo()
```

