---
title: "Pseudo Poisson Maximum Likelihood Estimation for Regression Data Containing Zeroes"
date: "2021-01-13"
output:
  html_document:
    df_print: paged
    keep_md: yes
  pdf_document: default
---





In many modelling problems the relationship between the dependent and independent variables is a log-linear relationship, however if the dependent variable contains zero values then it is not possible to apply the log function. There are many datasets where this problem may occur in practice, for example a wind turbine may produce zero energy in certain conditions or a seasonal product may have zero sales in a period of time. 

There are a number of commonly suggested solutions to the log function being undefined at zero. One solution is to remove the zero entries, this may be reasonable if there are few zeroes and they are zero at random, however if this is not the case then we may introduce bias into the estimates of the model coefficients. Another solution is to add a small number to each value of the dependent variable, and then apply the log function to a now strictly positive set of numbers. This latter approach may also bias the coefficient estimates because the log function is not linear and therefore the relationship with the independent variables may be impacted differently for small and large dependent values. 

In this note we examine the bias introduced by applying a log transformation to the dependent variable plus a small constant, and an alternative solution using a Pseudo Poisson Maximum Likelihood model. 

More precisely, suppose that we have observations $Y = \{y_1, \ldots, y_n \}$ that we believe have a log-linear relationship with indepedent variables $X = \{ x_1, \ldots, x_n \}$, and that some of *Y* are zero. By adding a small constant, we are able to take a log transformation and then our model becomes
$$  \log \left( y_i + \delta \right) = \beta_0 + \beta_1 x_i + \epsilon_i \quad i = 1, \ldots, n  $$
where $\epsilon_i \sim N(0, 1)$.

The alternative method is by using an exponential transformation that does not require adding a constant to each observation:
$$ y_i = \exp \left( \beta_0 + \beta_1 x_i + \epsilon_i \right) \quad i = 1, \ldots, n . $$
This "distribution" is known as a Quasi Poisson distribution; it is not a real distribution but we can still calculate estimates of the model coefficients. 

There exist a number of statistical packages that allow us to fit a regression model using the Pseudo Poisson distribution. Here, we use the *glm* package in R by setting the family to Pseduo Poisson with a log link function. 

The Pseduo Poisson distribution is primarily used to model over-dispersion in count data, when the assumption of equality in the mean and variance is not reasonable and the Poisson distribution is no longer appropriate. The Quasi Poisson distribution estimates the variance as a linear function of the mean, and is considered an alternative to the negative Binomial distribution for over-dispersed count data. In the regression framework assumed in this note, the Pseudo Poisson maximum likelihood method can be applied to any non-negative data, not just count data.

## Simulated Data Example

We simulate data from a log-linear model containing zeroes and show there is bias in the linear regression fitted to a log transformation of the observations plus a small constant and that the Pseudo Poisson Maximum Likelihood model provides more accurate coefficient estimates. 

The data-generating model is as follows:
$$ y_i = 
\begin{cases} \exp \left( -1 + 2 \cdot x_i + \epsilon_i \right)  & \quad \text{if } \epsilon_i > -0.7 \\
            0 & \quad \text{otherwise,} \end{cases}  \quad \quad \text{where } i = 1, \ldots, n. $$

In the first stage, we see how varying $\delta$ impacts the bias in the coefficient estimates. In this case, we fix the sample-size to be $n = 50$ and use a Monte Carlo simulation with simulation-size $M = 250$. 

<div class="figure" style="text-align: center">
<img src="D:/Phillip/GitHub/Datasets/Pseudo Poisson Maximum Likelihood/CoefficientBias_withDelta.png" alt="Figure 1: Plot Showing the Impact of Adding a Small Constant to the Observations to Apply a Log Transformation for OLS Regression" width="50%" />
<p class="caption">Figure 1: Plot Showing the Impact of Adding a Small Constant to the Observations to Apply a Log Transformation for OLS Regression</p>
</div>
The bias in estimating the coefficient of the relationship with $x$ is shown in Figure 1.

In the second stage, we compare the Pseudo Poisson Maximum Likelihood method to the OLS from a log transform (adding a small constant to the observations). In this case we fix $\delta = 0.1$ and vary the sample-size $n$, the Monte Carlo iteration size is still $250$. 

<div class="figure" style="text-align: center">
<img src="D:/Phillip/GitHub/Datasets/Pseudo Poisson Maximum Likelihood/CoefficientBias_samplesize.png" alt="Figure 2: Plot Comparing OLS and Pseudo Poisson Maximum Likelihood for Regression in a Log-Linear Relationship with Zeroes in the Observations" width="50%" />
<p class="caption">Figure 2: Plot Comparing OLS and Pseudo Poisson Maximum Likelihood for Regression in a Log-Linear Relationship with Zeroes in the Observations</p>
</div>

In Figure 2 we see that for the choice of $\delta$ the Pseudo Poisson Maximum Likelihood method returns a much closer estimate of the regression coefficient than the OLS approach. 




