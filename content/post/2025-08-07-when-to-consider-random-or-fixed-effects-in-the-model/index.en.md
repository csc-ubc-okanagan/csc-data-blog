---
title: "When to Consider Random or Fixed Effects in the Model?"
author: ["Amir Golzan"]
date: 2025-08-11 09:00:00 -0800
categories: ["Modelling", "R"]
tags: ["modelling", "r"] # tags always lowercase
---

One common question in statistical modeling is whether to consider a particular factor as a **fixed effect** or a **random effect**. In R, this often translates to deciding between a standard generalized linear model (`glm`) and a generalized linear mixed-effects model (`glmer`). 

For example, if we have data on patients from multiple hospitals, should we include hospital as a fixed effect (using dummy variables for each hospital) or as a random effect (considering hospital as a grouping factor)? This decision can significantly impact your model’s interpretability and inference. Traditionally, the distinction is explained as follows: are you interested in the *specific levels* themselves or are they considered a *random sample* from a broader population of levels? If the former, treat it as fixed; if the latter, treat it as random! *(Note that this is a heuristic, not a strict rule. In practice, the choice also depends on data structure and goals, as we later discuss.)*

In simpler terms, **fixed effects** apply to measured levels that exhaust our interest (e.g. specific treatments or specific known categories), whereas **random effects** assume the levels are drawn from a **wider population** and you mainly care about the **variation** among them rather than each **specific** level. 

Let's elaborate more on this:

## Fixed Effects vs Random Effects: what’s the difference?

In a regression context, a **fixed effect** is a non-random parameter that may vary across specific levels of a factor but is assumed constant in the population (e.g., the same effect for all males). If you include a categorical variable as a fixed effect, you are estimating a coefficient for each level (category) of that variable (minus one reference level). By contrast, a **random effect** assumes the effect of that categorical factor varies *randomly* across levels, which we model by specifying a distribution (usually normal) for those effects. Instead of estimating an *independent coefficient* for each level, a random-effects model estimates variance components that describe the distribution of the level effects.

> **Note**: In essence, “a fixed factor assumes that the levels are ***separate***, ***independent***, and ***not similar***. A random effect assumes the levels come from a ***distribution*** and are ***related*** and ***exchangeable***” ([Data Analysis in R by Steve Midway](https://bookdown.org/steve_midway/DAR/random-effects.html)).

What are the implications of these approaches? One big difference is **degrees of freedom** and generality. A fixed-effect model with many levels uses up **a lot of degrees of freedom** (each level is an extra parameter) and treats those particular levels as the only ones of interest. A random-effect model is more **parsimonious** when levels are numerous: it “eats up” only one or a few parameters no matter how many groups you have (one parameter for the variance, and possibly one for each random slope if you have random slopes)

### Why (and when) should we consider random effects?

- ***Hierarchical Data (Nested Data):*** 
If your data have a clear **hierarchical** or **clustered structure** (patients within hospitals, students within schools, repeated measurements on the same individuals, etc.), then random effects are usually appropriate. They explicitly model the **multiple sources** of random variability. For example, patients in the same hospital may tend to have more similar outcomes compared to patients in different hospitals, because of unobserved hospital-level characteristics. A random-effects model accounts for this by adding a **random intercept** for hospital, which captures between-hospital variability. In a fixed-effects only model, this hospital-to-hospital variability would either be **ignored** (if hospital not included) or **soaked up** by dummy variables (if included as fixed), but either way you wouldn’t get an estimate of how much variation is attributable to hospitals. By using a random effect, you are no longer assuming all observations are **independent**; you’re allowing that observations **within** a group are correlated. Technically, including a random intercept permits the model’s error term to have an intraclass correlation within groups (errors within a hospital are positively correlated, and potentially have their own variance different from others). Ignoring such structure when it exists can lead to **pseudoreplication** (treating non-independent observations as independent), which in turn can make your p-values too **optimistic** (since effective sample size is smaller than it appears). Random effects provide a principled way to account for this correlation.


- ***Unobserved Group-Level Heterogeneity:*** 
Random effects act as a parsimonious way to model the impact of unobserved factors at the **group level**. In many cases we suspect that there are group-specific influences (e.g. each hospital has slightly different practices affecting patient outcomes, each doctor has their own technique, each cluster has different baseline risk, etc.) even after controlling for measured covariates. By adding a **random intercept** (and/or slope), we let the model learn how much variability is present across groups that is not explained by the measured predictors. As a result, our fixed-effect estimates (like treatment effects) are estimated after accounting for this extra variability. This often leads to **more reliable** inference. 


- ***Generalizing to a Larger Population of Groups:*** 
As mentioned, if your groups (e.g. hospitals) can be seen as a random sample from a wider population (all possible hospitals, or all hospitals in the region), using a random effect lets you make inferences about that **wider** population. You get an estimate of the **between-group variance**, which is often of scientific interest. For example, you might report that “the standard deviation of the hospital intercepts is 0.5 on the log-odds scale,” indicating how much outcomes vary by hospital after accounting for patient-level factors. 


- ***Degrees of Freedom:*** 
A practical reason to consider random effects is when the number of levels is large. Including a categorical predictor with many levels as fixed effects can chew up **degrees of freedom** and potentially overfit. With limited data, your model might not even be able to estimate a large number of fixed effects reliably (e.g. if some hospitals have no events, the fixed effect coefficient might blow up or be unidentifiable in a logistic model). Random effects **“borrow strength”** from the whole dataset and use far fewer parameters. For example, instead of estimating 50 school effects, you estimate one variance and perhaps 50 random offsets which are not free parameters but shrinkage estimates. This can make models converge where a fixed-effects approach might fail or produce huge standard errors.


> **Note**: To summarize, you should consider a random effects model (`GLMM`) when your data have inherent groups or repeated measures that you suspect have different intercepts (or slopes), and especially when those groups are numerous and not the **primary focus individually**. 


### Why (and when) should we consider fixed effects?

- ***When the group levels are of direct interest and are not viewed as random samples:***
If you have a few specific categories that you **explicitly** want to compare (and you have enough data per category), **fixed effects** may be more straightforward. For example, suppose you’re analyzing data from *exactly* two hospitals and you want to compare them; those hospitals are your *entire focus*. Treating “hospital” as a random effect in a sample of size 2 isn’t very useful. You’re better off just comparing those two with a fixed effect (a two-sample comparison). Similarly, binary indicators like gender (male vs female) or other factors with only 2–3 levels that are **inherently limited** and of interest are usually treated as **fixed effects**. We typically don’t consider “male and female” to be a **random sample** of a larger set of genders. They are just the two categories that exist, so a random-effect assumption doesn’t make sense there.


> **Note**: There is no strict rule for the **minimum number** of factor levels needed for a random effect, and you can **technically** use any factor with two or more levels. However, it is often suggested, **as a rule of thumb**, that you should have **at least five factor levels** for a random effect to gain the full benefit of using it. You might also avoid using a random effect if you do not want the factor levels to influence each other or if you do not assume they come from the same underlying distribution. As mentioned above, male and female is a factor with only two levels, and we often prefer to estimate their information **separately** ([Data Analysis in R by Steve Midway](https://bookdown.org/steve_midway/DAR/random-effects.html)).


- ***When you need to estimate each level’s effect explicitly:*** As noted, random effects will not give you **named coefficients** for each level. If your analysis goal is to say “Hospital X has significantly higher outcomes than hospital Y,” a fixed-effect model allows that kind of specific contrast *(though you could also achieve this via post-hoc comparisons in a mixed model using BLUPs and their standard errors, but it’s more complicated)*. In an exploratory analysis where you only care about the **measured units** and **not generalization**, fixed effects might be simpler. In a clinical context, if you had a fixed set of hospitals and you want ***each*** hospital’s performance estimate, you might use fixed effects *(though an alternative is to use random effects to get shrinkage estimates and then report the BLUPs. This can actually be more accurate for ranking, but it’s an advanced consideration)*.

- ***When the assumption of a common distribution for effects is questionable:*** Random effects impose the assumption that the group effects are **random** draws from a **normal (usually) distribution**. In some cases, you might **not** be willing to assume that. For example, imagine an educational study across 3 very distinct school types (perhaps public, private, charter); treating “school type” as random would assume those three are like a **random sample** of some **wider** variety of school types, which might not be sensible if those are essentially fixed categories of interest. In such cases, a fixed effect approach (even if levels are few) might be more appropriate to avoid **potentially mis-specifying** the model. Essentially, if the levels are fixed by the **design** or by **nature**, and you do not intend to generalize beyond them, use fixed effects. If the levels can be thought of as exchangeable draws from a population, and you do care about that population, random effects make sense.


- ***Small number of levels and risk of over-parametrization:*** Although random effects are often a cure for having many levels, if you have a very small number of groups, the model might have trouble estimating the variance component accurately. As mentioned, you technically can fit a random effect with 2 levels, but the variance estimate will be based on essentially *one degree of freedom* (the difference between the two) *(It should be noted that modern methods (e.g., Bayesian GLMMs) handle this better than frequentist approaches)*. Some analysts prefer to avoid random effects in such extreme cases and would use *fixed effects* or even a *simpler paired analysis* (depending on context). With exactly 2 levels, random and fixed effects models often produce similar results (e.g., both allow different intercepts), but random effects add a variance estimate that's poorly informed and may introduce unnecessary assumptions. So in practice, **fixed effects** are simpler and preferred here. With 3–4 levels, you can use random effects, but be **cautious** in interpreting the **variance** component. If inference on the variance itself is crucial, it might be unreliable with so few groups. In contrast, if you have lots of groups but each with tiny sample size, random effects are usually the only viable approach (fixed effects would be unestimable or lead to huge uncertainty).


> **Note:** In practice, there are cases that fall in a **grey area**. For example, what if you have 5 hospitals in your study, not an extremely large sample of hospitals, but you might view them as representative of other hospitals? Here one could go either way: a fixed-effect model will let you compare those 5 hospitals specifically; a random-effect model will assume they’re just a sample and focus on variance among hospitals. A useful strategy is often to try **both** (if feasible) or consider a mixed modeling philosophy: you could include hospital as random to get overall treatment effects accounting for clustering, and if you really need hospital-specific estimates you can extract them (BLUPs) or do a fixed-effect model separately for that purpose. Always consider the **scientific question** and the **data structure**: Are the “levels” a means to an end (blocking factors, nuisance, or random sample)? Then random effects are great. Are the levels themselves the object of interest (specific treatments, named locations, categories that exhaust all possibilities)? Then you lean towards fixed effects.


## GLM vs GLMM (R Demonstration)

Let’s walk through a simple example in R to illustrate the differences between using a fixed effect and a random effect. Suppose we are analyzing data from a hypothetical clinical trial conducted at 10 different hospitals. We want to model a binary outcome (e.g., patient improved = 1 or not = 0) based on whether the patient received a new treatment or a standard treatment. We suspect that outcomes might differ by hospital (perhaps because of differing quality of care, patient populations, etc.), but we’re not primarily interested in the specific effect of each hospital; we just don’t want those differences to **confound** our treatment effect or **violate** model assumptions.


**Simulating the data:** For reproducibility, we will simulate a dataset where hospital differences are present. In the simulation below, we create 10 hospitals, each with 30 patients, and give each hospital a random “intercept” effect (some hospitals have overall higher success rates, some lower):


``` r
library(lme4)

set.seed(5) # For Reproducibility
n_hosp <- 10 # Number of Hospitals
patients_per_hosp <- 30 # Patients per Hospital
hospital <- factor(rep(1:n_hosp, each = patients_per_hosp))
N <- length(hospital)

# Random hospital effects
sigma <- 1.5   # True SD of Hospital Effects
hospital_effect <- rnorm(n_hosp, mean = 0, sd = sigma)

# Treatment assignment (random 0/1)
treatment <- rbinom(N, size = 1, prob = 0.5)

# Linear predictor for log-odds
beta0 <- -1    # Intercept
beta_trt <- 1   # Treatment Effect (log-odds)
eta <- beta0 + beta_trt * treatment + hospital_effect[hospital]

# Generate binary outcome
prob <- plogis(eta)      # Inverse-logit to Get Probability
outcome <- rbinom(N, size = 1, prob = prob)

# Combine into data frame
clinical_data <- data.frame(hospital, treatment = factor(treatment, labels=c("Standard","NewTreatment")), 
outcome = factor(outcome, labels=c("No Improvement","Improved"))
)

head(clinical_data)
```

```
##   hospital    treatment        outcome
## 1        1 NewTreatment No Improvement
## 2        1 NewTreatment No Improvement
## 3        1     Standard       Improved
## 4        1     Standard No Improvement
## 5        1     Standard No Improvement
## 6        1     Standard No Improvement
```

In the above, `clinical_data` contains a factor `hospital` (with levels "1"..."10"), a `treatment` group factor, and the binary `outcome`. Now, let’s fit two models:

**GLM (fixed effects only):** We **ignore** hospital clustering (or treat it as nonexistent). This model will just have an intercept and a treatment effect.

**GLMM (mixed model):** We include hospital as a **random intercept** effect to account for clustering.


``` r
# Model 1: Logistic Regression without Hospital (fixed effects only)
glm_fit <- glm(outcome ~ treatment, data = clinical_data, family = binomial)

# Model 2: Logistic Regression with Hospital (random intercept)
glmer_fit <- glmer(outcome ~ treatment + (1 | hospital), data = clinical_data, family = binomial)

summary(glm_fit)
```

```
## 
## Call:
## glm(formula = outcome ~ treatment, family = binomial, data = clinical_data)
## 
## Coefficients:
##                       Estimate Std. Error z value Pr(>|z|)    
## (Intercept)            -0.8473     0.1725  -4.911 9.04e-07 ***
## treatmentNewTreatment   0.5010     0.2433   2.059   0.0395 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 389.69  on 299  degrees of freedom
## Residual deviance: 385.42  on 298  degrees of freedom
## AIC: 389.42
## 
## Number of Fisher Scoring iterations: 4
```

``` r
summary(glmer_fit)
```

```
## Generalized linear mixed model fit by maximum likelihood (Laplace
##   Approximation) [glmerMod]
##  Family: binomial  ( logit )
## Formula: outcome ~ treatment + (1 | hospital)
##    Data: clinical_data
## 
##      AIC      BIC   logLik deviance df.resid 
##    343.8    354.9   -168.9    337.8      297 
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -2.0481 -0.6501 -0.4343  0.5934  3.2227 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev.
##  hospital (Intercept) 1.285    1.133   
## Number of obs: 300, groups:  hospital, 10
## 
## Fixed effects:
##                       Estimate Std. Error z value Pr(>|z|)  
## (Intercept)            -0.9121     0.4089  -2.231   0.0257 *
## treatmentNewTreatment   0.3901     0.2754   1.416   0.1567  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##             (Intr)
## trtmntNwTrt -0.331
```

Let’s examine the outputs. For the **GLM (no random effects)**, this suggests an intercept of about -0.84 (baseline odds ~ e^-0.84 ≈ 0.43, so probability p = odds / (1 + odds) ≈ 0.432 / 1.432 ≈ 0.301, or 30.1% probability of improvement for standard treatment) and a treatment effect log-odds of +0.50. The treatment effect is reported as statistically significant (*p* ≈ 0.03). **However, this model’s inference is suspect** because it assumes each of the 300 patients is an independent observation. In reality, they are clustered by hospital. If hospital has a strong influence, our effective sample size is less than 300.

For the GLMM, we see a random effect section indicating the estimated variance of hospital intercepts is ~1.28 (Std. Dev. ≈ 1.13). This is close to the true *σ^2* = 2.25 (since we set *σ=1.5*). [Note that with only 10 hospitals, the estimated variance (1.28) is lower than the true value (2.25) used in simulation, which is common due to small-sample bias in REML estimation. Larger n_groups (e.g., >20) yield more accurate variances.] This tells us there is **substantial** variability in baseline outcome probabilities across hospitals. The fixed effects table now shows the treatment effect estimate ~0.39 but importantly the standard error is larger (0.27 vs 0.24 before). Consequently, the *z* value is ~1.41 and the p-value is about 0.15, which is not conventionally significant. In other words, after accounting for the **variability** between hospitals, the evidence for the treatment’s effectiveness is **weaker**. The GLM was **overconfident**. It **underestimated** the standard error by ignoring the **clustering**. The GLMM’s **wider** interval is more appropriate given that patients within each hospital weren’t offering as much independent information as the GLM assumed.

-*Fixed vs Random Hospital as a deliberate modeling choice:* If we had treated `hospital` as a fixed effect in a GLM (e.g., `glm(outcome ~ treatment + hospital, ...)`), we would get an **estimate** for each `hospital` (with one as baseline). That model would have many **more** parameters (making it harder to fit if data is **sparse** in some hospitals) and we could **directly** see which hospitals have higher or lower outcomes. But we wouldn’t get a clean summary of “`hospital variability`” as a single number. If the number of hospitals is large or some have few patients, the fixed-effect estimates may be **noisy**. The random effect model “shares” information between hospitals to yield more stable estimates. Often in clinical studies with many centers, a **random-intercepts model** is preferred to account for center differences without expending too **many degrees of freedom**.


---

This brings us to the end of this post. Hopefully, you now have a better understanding of fixed and random effects and when to choose each. Below are the references used for this post, which are highly recommended for further details and explanations, especially for uncommon scenarios and data.

**Stay tuned for future posts!**

---
**Reference:**

[Data Analysis in R by Steve Midway](https://bookdown.org/steve_midway/DAR/random-effects.html)

[Is it a fixed or random effect? by Brian McGill](https://dynamicecology.wordpress.com/2015/11/04/is-it-a-fixed-or-random-effect/#:~:text=%28y~x%2Crandom%3D~1,that%20is%20what%20is%20happening)

[Introduction to Linear Mixed Models by Gabriela K Hajduk](https://ourcodingclub.github.io/tutorials/mixed-models/)
