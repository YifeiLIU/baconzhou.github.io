---
title: "Genetic Linkage Model in Rstan"
layout: post
comments: TRUE
---

# Genetic Linkage Model in Rstan
Peigen Zhou  

Stan is one of my faviorate programming language, there are tones of people using stan as daily research or analysis purpose, Facebook's new pacakge [PROPHET](https://facebookincubator.github.io/prophet/).



# Install R package _rstan_

Rstan is the R interface to stan, which using for statistical modeling, data analysis, and prediction in the social, biological, and physical sciences, engineering, and business.

This tutorial will introduce how to install R stan into our computer and how to use R stan in real problem.

```R
install.packages("rstan")
library(rstan)
```

# Genetic Linkage Model 

Suppose that 197 animals (Y) are distributed into four categories as follows:
$$ Y = (y_1, y_2, y_3, y_4) = (125, 18, 20, 34) $$
with cell probabilities
$$ (\frac{1}{2} + \frac{\theta}{4}, \frac{1}{4}(1-\theta), \frac{1}{4}(1-\theta), \frac{\theta}{4}) $$

The EM algorithm will use augment method to make the likelihood function easy to me maximized. So how to put the question into __stan__?



Model file or code:
Stan has 7 main blocks:   

+ functions **Define your own function, likelihood, sampling distribution**
+ data  **Using for input data**
+ transformed data 
+ parameters **Parameters need to estimate**
+ transformed parameters
+ model **As its name**
+ generated quantities **Some quantities you are interested**


```r
linkage_code <- '
data {
  int<lower=0> y[4];
}

parameters {
  real<lower=0, upper=1> theta;
}

transformed parameters {
  vector[4] ytheta;
  ytheta[1] = 0.5+theta/4;
  ytheta[2] = 0.25-theta/4;
  ytheta[3] = 0.25-theta/4;
  ytheta[4] = theta/4;
}

model {
  y ~ multinomial(ytheta);
}
'

y <- c(125, 18, 20, 34)
dat <- list()
dat$y <- y
```

First we compile our model to a S4 stanmodel object.


```r
model_obj <- stan_model(model_code = linkage_code, model_name = "GeneticLinkage")
```

## Sampling


```r
fit <- sampling(model_obj, data = dat, iter = 1000, chains = 4) 
```
you can also save your model_code into a file maybe call "genetic.stan", then use argument `file = "gentic.stan"`.

_iter_ means length of chains, _chains_ means how many chains you want to use.

You can use a list to store all the data, then put them into your stan model.

### Some inference

First you may need to check is whether the Markov chains are converged.


```r
print(fit)
```

```
## Inference for Stan model: GeneticLinkage.
## 4 chains, each with iter=1000; warmup=500; thin=1; 
## post-warmup draws per chain=500, total post-warmup draws=2000.
## 
##              mean se_mean   sd    2.5%     25%     50%     75%   97.5%
## theta        0.62    0.00 0.05    0.52    0.59    0.63    0.66    0.72
## ytheta[1]    0.66    0.00 0.01    0.63    0.65    0.66    0.67    0.68
## ytheta[2]    0.09    0.00 0.01    0.07    0.08    0.09    0.10    0.12
## ytheta[3]    0.09    0.00 0.01    0.07    0.08    0.09    0.10    0.12
## ytheta[4]    0.16    0.00 0.01    0.13    0.15    0.16    0.17    0.18
## lp__      -207.68    0.02 0.71 -209.53 -207.87 -207.43 -207.23 -207.17
##           n_eff Rhat
## theta       995    1
## ytheta[1]   995    1
## ytheta[2]   995    1
## ytheta[3]   995    1
## ytheta[4]   995    1
## lp__       1069    1
## 
## Samples were drawn using NUTS(diag_e) at Thu Mar 16 22:46:01 2017.
## For each parameter, n_eff is a crude measure of effective sample size,
## and Rhat is the potential scale reduction factor on split chains (at 
## convergence, Rhat=1).
```

you can check the Rhat.

Also stan use ggplot, so that you can trace plot the theta and see whether multiple chains merge together.


```r
traceplot(fit, "theta")
```

![]({{ site.baseurl}}/images/20170316rstan_files/figure-html/unnamed-chunk-6-1.png)

And if you want to get the sample from the model, you can use `extract` function.


```r
theta_p <- extract(fit, "theta")
mean(theta_p$theta)
```

```
## [1] 0.6232634
```

Extract function will drop 50% of data in the warming up process.

## Opimization

Stan also support optimization for research purpose, so that you can perform lasso or group lasso some penalized regression in stan. Just by adding `log_increment` to add in likelihood.


```r
opt_stan <- optimizing(model_obj, data = dat)
```

```
## STAN OPTIMIZATION COMMAND (LBFGS)
## init = random
## save_iterations = 1
## init_alpha = 0.001
## tol_obj = 1e-12
## tol_grad = 1e-08
## tol_param = 1e-08
## tol_rel_obj = 10000
## tol_rel_grad = 1e+07
## history_size = 5
## seed = 330616802
## initial log joint probability = -205.739
## Optimization terminated normally: 
##   Convergence detected: relative gradient magnitude is below tolerance
```


```r
opt_stan$par
```

```
##      theta  ytheta[1]  ytheta[2]  ytheta[3]  ytheta[4] 
## 0.62681804 0.65670451 0.09329549 0.09329549 0.15670451
```


# Further reading

[Stan](http://mc-stan.org/)  
[Rstan](http://mc-stan.org/interfaces/rstan.html)  
