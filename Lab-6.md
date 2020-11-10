Lab 6
================
Patrick Sinclair, Kieran Yuen

### Data Check

As we are using the ACS Data to examine labor force participation, we
double check we have the correct interpretation of “NA” as a label
within the variable. Per the [Bureau of Labor
Statistics](https://www.bls.gov/cps/definitions.htm#laborforce) website,
“NA” should indicate that that particular respondent is too young and
*is not* eligible to be counted in the labor force.

    ##           
    ##               NA Not in LF in LF
    ##   (0,15]   31680         0     0
    ##   (15,25]      0     11717 13256
    ##   (25,35]      0      4271 20523
    ##   (35,45]      0      4064 18924
    ##   (45,55]      0      5406 21747
    ##   (55,65]      0     10563 18106
    ##   (65,100]     0     28701  5880

    ## 
    ##        NA Not in LF     in LF 
    ##     31680     64722     98436

    ##           
    ##            Not in LF     in LF
    ##   (0,15]                      
    ##   (15,25]  0.4691867 0.5308133
    ##   (25,35]  0.1722594 0.8277406
    ##   (35,45]  0.1767879 0.8232121
    ##   (45,55]  0.1990940 0.8009060
    ##   (55,65]  0.3684468 0.6315532
    ##   (65,100] 0.8299644 0.1700356

Here, we’ve broken the ages into groups, including an extra group to
check whether our assumption about the “NA” label was correct. Only the
age 0-15 returns any “NA” observations.

“Not in the Labor Force” are those respondents who are not participating
in the labor force during the survey week. These people may be retirees
or students.

In the second table, we can see the proportion of respondents within the
various age brackets who are and who are not participating in the labor
force. Other predictors that we think may give an indication of whether
a person is participating in the labor force are their [family
size](https://usa.ipums.org/usa-action/variables/FAMSIZE#description_section).
This may have some confounding issues - heads of larger households
([householders](https://www.census.gov/programs-surveys/cps/technical-documentation/subject-definitions.html#householder)
maybe be more likely to work while those who are not leading the family
unit may not be as likely to participate in the workforce.

We also think rent may be a good indicator of whether a person is
participation in the workforce. Lower rents may still be affordable to
those who are retired, students or unable to actively participate in the
labor force while higher rents would require someone to be working.

Finally, we thought examining whether someone is living below the
poverty line would be a good indicator. The initial assumption is that
those below the poverty line are less likely to be participating in the
labor force, *however*, given the NYC minimum wage in 2017 was $11.00,
it is conceivable that even those working may still be living below the
[poverty
line](https://www2.census.gov/programs-surveys/cps/tables/time-series/historical-poverty-thresholds/thresh17.xls).
This is even more likely if the [NYC thresholds,
p. 8](https://www1.nyc.gov/assets/opportunity/pdf/NYCgovPoverty2019_Appendix_B.pdf)
are used instead of the US National guidelines.

    ## poorworkers
    ##     0     1 
    ## 92244  6192

    ## poorworkers
    ##          0          1 
    ## 0.93709618 0.06290382

When we compare the groups, those in the workforce who live below the
poverty line comprise only 6.29% of the workforce.

However, of those who live below the poverty line, 25.8% are active in
the workforce. A step further would be to assess how many of those not
in the labor force are discouraged workers.

    ## poverty
    ##        NA Not in LF     in LF 
    ##      5833     11960      6192

    ## poverty
    ##        NA Not in LF     in LF 
    ## 0.2431937 0.4986450 0.2581614

### Creating our first subset that we will be performing regressions on

We would like to use FAMSIZE to see how family size affects a person’s
probabilities of being in the labor force. We decided to break up the
family size column into three groups (\# of family members): small
(0-3), medium (4-6), large (7-29).

Let’s first take a bird’s eye view of the acs2017 data to see how many
of our three Family size groups are in the Labor Force.

Results: Looking at the overview table of our data we can see that a
majority of people of all ages and family sizes participate in the labor
force. This is to be expected as most people do need to make a living
and bring in income to survive. But we see there is also a good
proportion of those who are not in the labor force, which allows us to
be able to do a regression analysis on the difference.

    ## , ,  = (0,3]
    ## 
    ##            
    ##             (0,15] (15,25] (25,35] (35,45] (45,55] (55,65] (65,100]
    ##   NA          7079       0       0       0       0       0        0
    ##   Not in LF      0    6316    2555    2107    3781    8992    26085
    ##   in LF          0    7851   13867    9575   13754   14889     5346
    ## 
    ## , ,  = (3,6]
    ## 
    ##            
    ##             (0,15] (15,25] (25,35] (35,45] (45,55] (55,65] (65,100]
    ##   NA         21297       0       0       0       0       0        0
    ##   Not in LF      0    4653    1465    1720    1475    1390     2301
    ##   in LF          0    4823    6010    8559    7437    2938      485
    ## 
    ## , ,  = (6,29]
    ## 
    ##            
    ##             (0,15] (15,25] (25,35] (35,45] (45,55] (55,65] (65,100]
    ##   NA          3304       0       0       0       0       0        0
    ##   Not in LF      0     748     251     237     150     181      315
    ##   in LF          0     582     646     790     556     279       49

Next, let’s make separate columns in the acs2017 data for each of the
three (3) family sizes similar to the (5) columns for education
(educ\_nohs, educ\_hs, etc….).

(Note: we have created an extra “Individual” column so that that can
serve as the “dropped variable” when we are running our logit
regressions)

# Logit: Fam Size ONLY

Next, let’s run the logit regression to see how the family sizes affect
whether someone is in the labor force or not.

Expectation: We are expecting that one of the three (3) variables will
drop off as that is necessary so that the variables can be compared to
something. We were thinking that the larger the size of the family the
less likely the labor participation rate would be because the more
people in a household the less likely everyone in the household would
need to work. The counter argument to that is that those with larger
families have more mouths to feed and thus would increase their
likelihood to be in the labor force. So it is hard to estimate which of
the three (3) family sizes will show to have the largest effect on labor
participation rates.

Result: it appears that *small* families have the highest probability of
being in the labor force. Those in *medium* families have the second
highest probability of being in the labor force and those with *large*
families have the lowest probabilities of being in the labor force. All
of these coefficients used an Individual as the basis for comparison.

    ## 
    ## Call:
    ## glm(formula = LABFORCE ~ SmallFamily + MediumFamily + LargeFamily + 
    ##     Individual, family = binomial, data = dat_use1)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.9753   0.5538   0.5538   0.6198   0.8184  
    ## 
    ## Coefficients: (1 not defined because of singularities)
    ##              Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)   0.92196    0.01891  48.744  < 2e-16 ***
    ## SmallFamily   0.87555    0.02479  35.313  < 2e-16 ***
    ## MediumFamily  0.63034    0.02486  25.360  < 2e-16 ***
    ## LargeFamily   0.21660    0.04927   4.397  1.1e-05 ***
    ## Individual         NA         NA      NA       NA    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 71408  on 74934  degrees of freedom
    ## Residual deviance: 70117  on 74931  degrees of freedom
    ## AIC: 70125
    ## 
    ## Number of Fisher Scoring iterations: 4

# Logit: All Variables

Next, let’s add in all the other variables to see how that affects our
probabilities.

Result: Small families continue to show the highest probability of labor
force participation. Followed by medium families. Last is large
families. All three family sizes are statistically significant at the
0.05 level. We note that adding the variables of AGE and gender increase
the probabilities of labor force participation of the three family
variables.

    ## 
    ## Call:
    ## glm(formula = LABFORCE ~ AGE + female + SmallFamily + MediumFamily + 
    ##     LargeFamily + Individual, family = binomial, data = dat_use1)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -2.1975   0.4583   0.5657   0.6684   1.0018  
    ## 
    ## Coefficients: (1 not defined because of singularities)
    ##               Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)   1.659558   0.048556  34.178  < 2e-16 ***
    ## AGE          -0.012036   0.001084 -11.108  < 2e-16 ***
    ## female       -0.569325   0.019672 -28.941  < 2e-16 ***
    ## SmallFamily   0.974176   0.025270  38.551  < 2e-16 ***
    ## MediumFamily  0.716009   0.025245  28.362  < 2e-16 ***
    ## LargeFamily   0.280068   0.049725   5.632 1.78e-08 ***
    ## Individual          NA         NA      NA       NA    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 71408  on 74934  degrees of freedom
    ## Residual deviance: 69135  on 74929  degrees of freedom
    ## AIC: 69147
    ## 
    ## Number of Fisher Scoring iterations: 4

# Logit: All Variables (Interactions)

Result: The interactions of family size and being female appear to be
negative. This is telling us that being female negative impacts the
probabilities of being in the labor force whatever the person’s family
size. Similarly, women’s probability of participation in the labor force
only changes 0.09% with every year increase in age.

    ## 
    ## Call:
    ## glm(formula = LABFORCE ~ AGE + female + MediumFamily + LargeFamily + 
    ##     SmallFamily + I(MediumFamily * female) + I(LargeFamily * 
    ##     female) + I(SmallFamily * female) + I(female * AGE), family = binomial, 
    ##     data = dat_use1)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -2.3092   0.4127   0.5094   0.7096   1.0132  
    ## 
    ## Coefficients:
    ##                            Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)               1.2379564  0.0726666  17.036  < 2e-16 ***
    ## AGE                      -0.0124146  0.0017113  -7.254 4.04e-13 ***
    ## female                    0.4936177  0.0978378   5.045 4.53e-07 ***
    ## MediumFamily              1.6790857  0.0404093  41.552  < 2e-16 ***
    ## LargeFamily               1.2666258  0.0911349  13.898  < 2e-16 ***
    ## SmallFamily               1.3966952  0.0359585  38.842  < 2e-16 ***
    ## I(MediumFamily * female) -1.7981617  0.0545846 -32.943  < 2e-16 ***
    ## I(LargeFamily * female)  -1.8633964  0.1115125 -16.710  < 2e-16 ***
    ## I(SmallFamily * female)  -0.9986488  0.0518064 -19.277  < 2e-16 ***
    ## I(female * AGE)          -0.0009579  0.0022252  -0.430    0.667    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 71408  on 74934  degrees of freedom
    ## Residual deviance: 67895  on 74925  degrees of freedom
    ## AIC: 67915
    ## 
    ## Number of Fisher Scoring iterations: 5

![](Lab-6_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

    ## 
    ## 0.860779455857246 0.862260546681493 0.863728375437847 0.865183007862796 
    ##              2950              2999              2944              2907 
    ## 0.866624510531691 0.868052950828762  0.86946839691749 0.870870917711318 
    ##              2606              2748              2414              2532 
    ## 0.872260582844717 0.873637462644618 0.875001628102205 0.876353150845078 
    ##              2548              2505              2466              2151 
    ## 0.877692103109785  0.87901855771474 0.880332588033515 0.881634267968514 
    ##              2067              2289              2229              2510 
    ##  0.88292367192504 0.884200874785746 0.885465951885478  0.88671897898651 
    ##              2321              2271              2358              2326 
    ## 0.887960032254177 0.889189188232896 0.890406523822594 0.891612116255524 
    ##              2552              2372              2315              2449 
    ## 0.892806043073481 0.893988382105424 0.895159211445482 0.896318609431368 
    ##              2455              2623              2494              2504 
    ## 0.897466654623188 0.898603425782644 
    ##              2560              2470

    ##        true
    ## pred    Not in LF in LF
    ##   FALSE      7006 29120
    ##   TRUE       6735 32074

    ##        true
    ## pred     Not in LF      in LF
    ##   FALSE 0.09349436 0.38860346
    ##   TRUE  0.08987789 0.42802429

    ## [1] 0.5215186

How do we do the zero-to-one plots?

# For homework, I will ask for predicted values so you can start to figure out how to get those.

# Do the X variables have the expected signs and patterns of significance? Explain if there is a plausible causal link from X variables to Y and not the reverse. Explain your results, giving details about the estimation, some predicted values, and providing any relevant graphics. Impress.

# Start of Probit Models

Also estimate a probit model (details in Lecture Notes) and OLS, with
the same X and Y variables. Compare the results, such as coefficients
and predicted values. If you’re eager, try to split the sample into
training and test data, then compare which model predicts better in the
sample that it hasn’t seen yet.

Logit models are very very commonly used in many models. But on the
other hand there are people that say it says too much. Sometimes bland
“OLS” might be better

# Notes

## Appendix

To clear up some of the definitions,

### Bibliography

Accessed: 11/09/2020
<https://www.census.gov/programs-surveys/cps/technical-documentation/subject-definitions.html#householder>

<https://www.bls.gov/cps/definitions.htm#laborforce>

<https://usa.ipums.org/usa-action/variables/FAMSIZE#description_section>
