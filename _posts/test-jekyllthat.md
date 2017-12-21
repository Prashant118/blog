---
title: "R package primefactr"
author: "Florian Privé"
post_date: "August 11, 2016"
layout: single
permalink: /testttt/
categories : R
output: jekyllthat::jekylldown
excerpt_separator: <!--more-->
---

In this post, I will present my first R package, [available on CRAN](https://cran.r-project.org/web/packages/primefactr/index.html). It makes use of [Prime Factorization](https://en.wikipedia.org/wiki/Prime_factor) for computations.

This small R package was initially developed to compute [hypergeometric probabilities](https://en.wikipedia.org/wiki/Hypergeometric_distribution) which are used in Fisher's exact test, for instance. It was also a way to get introduced with CRAN submission :').

Installation and Attachment
---------------------------

``` r
## Installation
install.packages("primefactr")
```

``` r
## Attachment
library("primefactr")
```

Features
--------

### Main feature

For instance, to compute
$$P(X = k) = \\dfrac{\\binom{K}{k}~\\binom{N-K}{n-k}}{\\binom{N}{n}} = \\dfrac{K!~(N-K)!~n!~(N-n)!}{k!~(K-k)!~(n-k)!~(N-K-n+k)!~N!},$$
 you can use

``` r
f <- function(k, N, K, n) {
  ComputeDivFact(c(K, (N-K), n, (N-n)),
                 c(k, (K-k), (n-k), (N-K-n+k), N))
}
f(4, 50, 5, 10)
```

    ## [1] 0.003964583

``` r
f(5, 50, 5, 10)
```

    ## [1] 0.0001189375

You can check the results [here](https://en.wikipedia.org/wiki/Hypergeometric_distribution#Application_and_example).

Let us now check large numbers:

``` r
f(k = 1000, N = 15100, K = 5000, n = 3100)
```

    ## [1] 0.009003809

A direct approach would require computing `factorial(15100)`, while `factorial(100) = 9.332622e+157`.

### Implementation

This uses a Prime Factorization to simplify computations.

I code a number as follows,
*n**u**m**b**e**r* = ∏*i*<sup>*c**o**d**e*\[*i*\]</sup>,
 or, which is equivalent,
log(*n**u**m**b**e**r*)=∑*c**o**d**e*\[*i*\]×log(*i*).
 For example,

-   5 is coded as (0, 0, 0, 0, 1),
-   5! is coded as (1, 1, 1, 1, 1),
-   8! is coded as (1, 1, 1, 1, 1, 1, 1, 1).

So, to compute 8!/5!, you just have to substract the code of 5! from the code of 8! which gives you (0, 0, 0, 0, 0, 1, 1, 1).

Then there is the step of Prime Factorization:

Factorization by 2:

-   it becomes (0, 2, 1, 1, 0, 0, 1, 0) because 8 = 4 × 2 and 6 = 3 × 2,
-   then it becomes (0, 4, 1, 0, 0, 0, 1, 0) because 4 = 2<sup>2</sup>.

This is already finished (this is a small example). You get that 8!/5!=2<sup>4</sup> × 3<sup>1</sup> × 7<sup>1</sup>. Let us verify:

``` r
cat(sprintf("%s == %s", factorial(8) / factorial(5), 2^4 * 3 * 7))
```

    ## 336 == 336

### Play with primes

You can also test if a number is a prime and get all prime numbers up to a certain number.

Submission to CRAN
------------------

It was easier than I thought. I've just followed the instructions of the book [R packages](http://r-pkgs.had.co.nz/) by Hadley Wickham. I had two notes:

1.  It is my first submission.
2.  File README.md cannot be checked without ‘pandoc’ being installed. For this note, I used the same comment as [here](https://github.com/klarsen1/Information/blob/b3a826a6f8a38aa8c664156cef4f16edae196ec3/cran-comments.md#r-cmd-check-results) and CRAN didn't complain.

