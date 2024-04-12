General Coding Solutions
================
Veronica Bergstrom
Last updated: 12 April, 2024

- [Install Libraries](#install-libraries)
  - [Time conversion](#time-conversion)
  - [Lonely Integer](#lonely-integer)
  - [Sum Diagonals of a Square](#sum-diagonals-of-a-square)
  - [Counting Sort Function](#counting-sort-function)
- [Print the sum of the minimum 4 values and print the sum of the
  maximum 4 values of an
  array](#print-the-sum-of-the-minimum-4-values-and-print-the-sum-of-the-maximum-4-values-of-an-array)
- [Print the proportion of values that are negative, positive, and
  zero](#print-the-proportion-of-values-that-are-negative-positive-and-zero)
- [Tower breakers](#tower-breakers)
- [Retain unique numbers](#retain-unique-numbers)
- [Rotate array](#rotate-array)
- [Check if a vector or array contains
  duplicates](#check-if-a-vector-or-array-contains-duplicates)
- [Intersection of two arrays](#intersection-of-two-arrays)
- [Add +1 to the last integer in a vector or array and ensure that only
  a single digit represents each
  element](#add-1-to-the-last-integer-in-a-vector-or-array-and-ensure-that-only-a-single-digit-represents-each-element)
- [Move zeros](#move-zeros)

# Install Libraries

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.2     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.2     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.1     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

## Time conversion

Convert 07:05:45PM format to 19:05:45

``` r
timeConversion <- function(s) {
new_time <- format(strptime(s, "%I:%M:%S%p"), format="%H:%M:%S")
return(new_time)
}

timeConversion("07:05:45PM")
```

    ## [1] "19:05:45"

## Lonely Integer

Return the only element that is listed once in an array

``` r
# function

lonelyinteger <- function(a) {
    # Write your code here
    table <- as.data.frame(table(a))
    retain <- table[(table$Freq == 1),]
    retain <- retain[1,1]
    return(retain)
}

# test

a = c(1,2,3,4,3,2,1)

lonelyinteger(a)
```

    ## [1] 4
    ## Levels: 1 2 3 4

## Sum Diagonals of a Square

Sum the two diagonals of a square matrix.

``` r
# function

diagonalDifference <- function(arr) {
    response = abs(sum(diag(arr)) - sum(diag(apply(arr,2,rev))))
    return(response)
}

#test

set.seed(1)
arr <- matrix(floor(runif(16, min = 0, max = 10)), nrow = 4, ncol = 4)
arr
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    2    2    6    6
    ## [2,]    3    8    0    3
    ## [3,]    5    9    2    7
    ## [4,]    9    6    1    4

``` r
diagonalDifference(arr)
```

    ## [1] 8

## Counting Sort Function

Create a vector that shows a count of how many times numbers from 0 to
99 were reported, starting with 0.

``` r
# function

countingSort <- function(arr) {
  array_list <- vector("list",0)
  for (i in 0:99) {
    new_element <- sum(arr==i)
    array_list[[length(array_list) + 1 ]] <- new_element
  }
  array_list <- unlist(array_list)
  return(array_list)
}

# test

array_list <- c(63, 54, 17, 78, 43, 70, 32, 97, 16, 94, 74, 18, 60, 61, 35, 83, 13, 56, 75, 52, 70, 12, 24, 37, 17, 0, 16, 64, 34, 81, 82, 24, 69, 2, 30, 61, 83, 37, 97, 16, 70, 53, 0, 61, 12, 17, 97, 67, 33, 30, 49, 70, 11, 40, 67, 94, 84, 60, 35, 58, 19, 81, 16, 14, 68, 46, 42, 81, 75, 87, 13, 84, 33, 34, 14, 96, 7, 59, 17, 98, 79, 47, 71, 75, 8, 27, 73, 66, 64, 12, 29, 35, 80, 78, 80, 6, 5, 24, 49, 82)

countingSort(array_list)
```

    ##   [1] 2 0 1 0 0 1 1 1 1 0 0 1 3 2 2 0 4 4 1 1 0 0 0 0 3 0 0 1 0 1 2 0 1 2 2 3 0
    ##  [38] 2 0 0 1 0 1 1 0 0 1 1 0 2 0 0 1 1 1 0 1 0 1 1 2 3 0 1 2 0 1 2 1 1 4 1 0 1
    ##  [75] 1 3 0 0 2 1 2 3 2 2 2 0 0 1 0 0 0 0 0 0 2 0 1 3 1 0

# Print the sum of the minimum 4 values and print the sum of the maximum 4 values of an array

``` r
# function

miniMaxSum <- function(arr) {
  arr <- sort(arr)
  answer <- c(sum(arr[-5]),sum(arr[-1]))
  invisible(print(answer))
}

# test

arr <- c(63, 54, 17, 78, 43)

miniMaxSum(arr)
```

    ## [1] 177 238

# Print the proportion of values that are negative, positive, and zero

``` r
# function

plusMinus <- function(arr) {
  print(sprintf("%.6f", round(sum(arr < 1)/length(arr), 6)))
  print(sprintf("%.6f", round(sum(arr > 1)/length(arr), 6)))
  print(sprintf("%.6f", round(sum(arr == 0)/length(arr), 6)))
}

# test

arr <- c(0, 2, -5.6, -8, 0, 0, 0)

plusMinus(arr)
```

    ## [1] "0.857143"
    ## [1] "0.142857"
    ## [1] "0.571429"

# Tower breakers

``` r
towerBreakers <- function(n, m) {
  if (n%%2 == 0 | m == 1) {
    return(2)
  } else {
    return(1)
  }
}

n = 6
m = 4

towerBreakers(n,m)
```

    ## [1] 2

# Retain unique numbers

``` r
nums = c(0,0,1,1,1,2,2,3,3,4)

unique(nums)
```

    ## [1] 0 1 2 3 4

# Rotate array

Rotate/shift an array by a variable number, where x = array and n =
number to shift by.

``` r
# function

rotate <- function(x,n){
  return(c(tail(x,n),head(x,-n)))
}

# test

x = c(7,1,5,3,6,4,6,3,8,1)
n = 2

rotate(x,n)
```

    ##  [1] 8 1 7 1 5 3 6 4 6 3

# Check if a vector or array contains duplicates

``` r
# function

containsDuplicates <- function(x) {
  duplicates <- duplicated(nums)
  if (TRUE %in% duplicates) {
    return(TRUE)
  } else {
    return(FALSE)
  }
}

# test

nums = c(1,2,3,9,4,8)

containsDuplicates(nums)
```

    ## [1] FALSE

# Intersection of two arrays

``` r
nums1 = c(4,9,5)
nums2 = c(9,4,9,8,4)

intersect(nums1,nums2)
```

    ## [1] 4 9

# Add +1 to the last integer in a vector or array and ensure that only a single digit represents each element

``` r
# function

plusOne <- function(x) {
  n = length(x)
  new_value = x[n] + 1
  if (new_value >= 10) {
    new_value = as.numeric(unlist(strsplit(as.character(new_value), "")))
  }
  x = x[-n]
  x = append(x,new_value)
  return(x)
}

# test

x = c(4,3,2,9)

plusOne(x)
```

    ## [1] 4 3 2 1 0

# Move zeros

Move all zeros to the end of an array while maintaining the order of the
non-zero elements. You must do this without making a copy of the array.

``` r
# figuring out solution while creating copies of array
check_zeros <- nums == 0
retain <- nums[!check_zeros]
move <- nums[check_zeros]
append(retain,move)
```

    ## [1] 1 2 3 9 4 8

``` r
# function without creating a copy of the array
moveZeros <- function(x) {
  x = append(x[!x == 0],x[x == 0])
  return(x)
}

# test

nums = c(0,1,0,3,12)

moveZeros(nums)
```

    ## [1]  1  3 12  0  0
