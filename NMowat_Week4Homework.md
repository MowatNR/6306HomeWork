# Bootstrapping and Central Limit Theorem
Nathan Mowat  
June 1, 2016  
# Our goal is use the Bootstrap method to show the central limit theorem
## Create four samples: 
#### Two from normal distribution that are different sizes with equal mean and sd.
#### Two from exponential distribution that are different sizes with equal rates.

```r
set.seed(44);NormalSmall <-  rnorm(10, mean = 5, sd = 3 )
set.seed(44);NormalLarge <- rnorm(80, mean = 5, sd = 3 )
set.seed(44);ExponentialSmall <- rexp(10, rate = 5 )
set.seed(44);ExponentialLarge <- rexp(80, rate = 5 )
```
# Visualize the raw data

```r
hist(NormalSmall)
```

![](NMowat_Week4Homework_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

##### For this NormalSmall sample, we can see a bi-modal structure in the histogram. This is most likely due to the small sample size and randomization. This sample is scattered and not very normally distributed.


```r
hist(NormalLarge)
```

![](NMowat_Week4Homework_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

##### For this NormalLarge sample, we can see a slightly more normal distribution. The right side of the histogram is lighter and more empty than the left side.


```r
hist(ExponentialSmall)
```

![](NMowat_Week4Homework_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

##### For this ExponentialSmall sample, the missing data that forms the gaps is noticeable. This is likely due to the small sample size and randomization.The data is skewed to the right.


```r
hist(ExponentialLarge)
```

![](NMowat_Week4Homework_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

##### With a larger sample, this ExponentialLarge sample follows a rough exponential distribution and is highly skewed to the right.

# Create Bootstrap Code For Each Sample


### Bootstrap loop for NormalSmall:
###### Where BootMeanNS = Vector of 5000 Means of a Bootstrap sample for NormalSmall


```r
R <- 5000
BootMeanNS <- numeric(R)
x <- NormalSmall
for(i in 1:R){
  bootsample <- sample(x, size = length(x), replace = TRUE)
  BootMeanNS[i] <- mean(bootsample)
}
```

#### Mean for Bootstrap for NormalSmall: 

```r
mean(BootMeanNS)
```

```
## [1] 3.339773
```

#### Original mean for NormalSmall: 

```r
mean(NormalSmall)
```

```
## [1] 3.343506
```


### Bootstrap loop for NormalLarge:
###### Where BootMeanNL = Vector of 5000 Means of a Bootstrap sample for NormalLarge


```r
G <- 5000
BootMeanNL <- numeric(G)
x <- NormalLarge
for(i in 1:R){
  bootsample <- sample(x, size = length(x), replace = TRUE)
  BootMeanNL[i] <- mean(bootsample)
}
```
#### Mean for Bootstrap for NormalLarge

```r
mean(BootMeanNL)
```

```
## [1] 4.629029
```

#### Original mean for NormalLarge: 

```r
mean(NormalLarge)
```

```
## [1] 4.630449
```

### Bootstrap loop for ExponentialSmall:
###### Where BootMeanES = Vector of 5000 Means of a Bootstrap sample for ExponentialSmall


```r
S <- 5000
BootMeanES <- numeric(S)
x <- ExponentialSmall
for(i in 1:R){
  bootsample <- sample(x, size = length(x), replace = TRUE)
  BootMeanES[i] <- mean(bootsample)
}
```
#### Mean for Bootstrap for ExponentialSmall

```r
mean(BootMeanES)
```

```
## [1] 0.1897568
```

#### Original mean for ExponentialSmall: 

```r
mean(ExponentialSmall)
```

```
## [1] 0.1890586
```

### Bootstrap loop for ExponentialLarge:
###### Where BootMeanEL = Vector of 5000 Means of a Bootstrap sample for ExponentialLarge


```r
G <- 5000
BootMeanEL <- numeric(G)
x <- ExponentialLarge
for(i in 1:R){
  bootsample <- sample(x, size = length(x), replace = TRUE)
  BootMeanEL[i] <- mean(bootsample)
}
```
#### Mean for Bootstrap for ExponentialLarge

```r
mean(BootMeanEL)
```

```
## [1] 0.1907148
```

#### Original mean for ExponentialLarge: 

```r
mean(ExponentialLarge)
```

```
## [1] 0.1904196
```
# Histograms of the Bootstrap Distribution of Sample Means


#### The bootstrap method shows how the central limit theorem works. As the sample size get sufficiently large (in this case 5000), the distribution of the sample means approximates the normal distribution. It is easy to compare these distributions to the orginals and see how the bootstrap method helps with non-parametric samples.

```r
hist(BootMeanNS)
```

![](NMowat_Week4Homework_files/figure-html/unnamed-chunk-18-1.png)<!-- -->



```r
hist(BootMeanNL)
```

![](NMowat_Week4Homework_files/figure-html/unnamed-chunk-19-1.png)<!-- -->



```r
hist(BootMeanES)
```

![](NMowat_Week4Homework_files/figure-html/unnamed-chunk-20-1.png)<!-- -->



```r
hist(BootMeanEL)
```

![](NMowat_Week4Homework_files/figure-html/unnamed-chunk-21-1.png)<!-- -->
