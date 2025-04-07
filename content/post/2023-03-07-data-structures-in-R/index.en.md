---
title: "Data Structures in R"
author: ["Stefano Mezzini"]
date: 2023-03-07 11:00:00 -0800
categories: ["R", "R_Data"]
tags: ["R", "data-structures", "wrangling"] # tags always lowercase
math: true # to enable math (not on by default)
---

\usepackage{amsmath}

There are many different data structure types in `R`, each with varying levels of complexity and uses. The simplest data structure in `R` is a vector. Vectors are one-dimensional arrays (i.e., sets of values) of a single class (such as characters, numbers, dates, etc.), and they have a similar structure to the mathematical concept of vectors. For example, the vector $\vec v$ with values 1, 4, 6, 2 would be:

$$\vec v = \begin{bmatrix}1 \\ 4 \\ 6 \\ 2\end{bmatrix}.$$

In `R` we can create vectors using the `c()` function, as follows:


``` r
v <- c(1, 4, 6, 2)
v
```

```
## [1] 1 4 6 2
```

(`R` prints vectors in a line rather than as columns to improve readability.)

Since vectors can only contain elements of a common type, `c()` will force all elements to be of a single class. In the example below, I create a vector with the number 1, the letter "a", today's date, and the value `TRUE` (a boolean value), but `c()` coerces all values to be characters:


``` r
c(1, 'a', Sys.Date(), TRUE)
```

```
## [1] "1"     "a"     "20185" "TRUE"
```

If we place two vectors (of the same class) side by side, we can create a matrix. Matrices are thus two-dimensional arrays 



``` r
# 2D arrays: matrices
matrix(data = 1:8, nrow = 2)
```

```
##      [,1] [,2] [,3] [,4]
## [1,]    1    3    5    7
## [2,]    2    4    6    8
```

``` r
matrix(data = 1:8, ncol = 2)
```

```
##      [,1] [,2]
## [1,]    1    5
## [2,]    2    6
## [3,]    3    7
## [4,]    4    8
```

``` r
matrix(data = 1:8, ncol = 2, byrow = TRUE)
```

```
##      [,1] [,2]
## [1,]    1    2
## [2,]    3    4
## [3,]    5    6
## [4,]    7    8
```

``` r
# matrix operations
matrix(1:9, ncol = 3)
```

```
##      [,1] [,2] [,3]
## [1,]    1    4    7
## [2,]    2    5    8
## [3,]    3    6    9
```

``` r
matrix(1:9, ncol = 3) + 3
```

```
##      [,1] [,2] [,3]
## [1,]    4    7   10
## [2,]    5    8   11
## [3,]    6    9   12
```

``` r
matrix(1:9, ncol = 3) * 2 # NOT matrix multiplication
```

```
##      [,1] [,2] [,3]
## [1,]    2    8   14
## [2,]    4   10   16
## [3,]    6   12   18
```

``` r
matrix(1:9, ncol = 3) %*% 6:8 # matrix multiplication
```

```
##      [,1]
## [1,]   90
## [2,]  111
## [3,]  132
```

``` r
matrix(1:9, ncol = 3) %*% matrix(1:6, ncol = 2)
```

```
##      [,1] [,2]
## [1,]   30   66
## [2,]   36   81
## [3,]   42   96
```

If we want to create an object that has different data types, a matrix or vector won't work because `R` will force all items to be of the same type.


``` r
class(c(0, 2))
```

```
## [1] "numeric"
```

``` r
class(c(0, 2, 'a')) # coerces all items to text
```

```
## [1] "character"
```

Instead, we need to use a list.


``` r
# grouping objects with different and types: lists
l <- list(letters = c('a', 'b', 'c'),
          numbers = 1:10,
          today = Sys.Date())
l
```

```
## $letters
## [1] "a" "b" "c"
## 
## $numbers
##  [1]  1  2  3  4  5  6  7  8  9 10
## 
## $today
## [1] "2025-04-07"
```

``` r
l$letters
```

```
## [1] "a" "b" "c"
```

``` r
l$today
```

```
## [1] "2025-04-07"
```

If we want a list that has a table-like structure (which will likely be the case for a lot of the data you use in `R`), we can use a data frame.


``` r
data.frame(num = 1:10,
           abc = LETTERS[1:10])
```

```
##    num abc
## 1    1   A
## 2    2   B
## 3    3   C
## 4    4   D
## 5    5   E
## 6    6   F
## 7    7   G
## 8    8   H
## 9    9   I
## 10  10   J
```

``` r
data.frame(num = 1:5,
           abc = LETTERS[1:10])
```

```
##    num abc
## 1    1   A
## 2    2   B
## 3    3   C
## 4    4   D
## 5    5   E
## 6    1   F
## 7    2   G
## 8    3   H
## 9    4   I
## 10   5   J
```

``` r
data.frame(num = 1:10,
           abc = LETTERS[1:9])
```

```
## Error in data.frame(num = 1:10, abc = LETTERS[1:9]): arguments imply differing number of rows: 10, 9
```

The tidyverse set of packages provides a "fancy data frame" that does not recycle elements (unless they are a single value), and allows you to reference other columns you previously created:


``` r
library('tibble')
tibble(num = 1:10,
       abc = LETTERS[num])
```

```
## # A tibble: 10 × 2
##      num abc  
##    <int> <chr>
##  1     1 A    
##  2     2 B    
##  3     3 C    
##  4     4 D    
##  5     5 E    
##  6     6 F    
##  7     7 G    
##  8     8 H    
##  9     9 I    
## 10    10 J
```

``` r
tibble(num = 1:5,
       abc = LETTERS[1:10])
```

```
## Error in `tibble()`:
## ! Tibble columns must have compatible sizes.
## • Size 5: Existing data.
## • Size 10: Column `abc`.
## ℹ Only values of size one are recycled.
```

