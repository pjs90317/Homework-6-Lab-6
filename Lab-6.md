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

# Logit: Fam Size Only

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

![](Lab-6_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

    ## Warning in predict.lm(object, newdata, se.fit, scale = 1, type = if (type == :
    ## prediction from a rank-deficient fit may be misleading

    ## 
    ## 0.782049822949747 0.784094351232601 0.786124945491883 0.788141602115143 
    ##              2950              2999              2944              2907 
    ## 0.790144319544462 0.792133098248625 0.794107940694993 0.796068851321099 
    ##              2606              2748              2414              2532 
    ## 0.798015836505979 0.799948904541277 0.801868065602124 0.803773331717816 
    ##              2548              2505              2466              2151 
    ##  0.80566471674232 0.807542236324607 0.809405907878852 0.811255750554504 
    ##              2067              2289              2229              2510 
    ## 0.813091785206246 0.814914034363867 0.816722522202064  0.81851727451018 
    ##              2321              2271              2358              2326 
    ## 0.820298318661906 0.822065683584958 0.823819399730745  0.82555949904404 
    ##              2552              2372              2315              2449 
    ## 0.827286014932679 0.828998982237288 0.830698437201063 0.832384417439615 
    ##              2455              2623              2494              2504 
    ## 0.834056961910891 0.835716110885181 
    ##              2560              2470

    ##        true
    ## pred    Not in LF in LF
    ##   FALSE      5406 21747
    ##   TRUE       8335 39447

    ##        true
    ## pred     Not in LF      in LF
    ##   FALSE 0.07214252 0.29021152
    ##   TRUE  0.11122973 0.52641623

    ## [1] 0.5985588

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

![](Lab-6_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

    ## 
    ## 0.860779455857241 0.862260546681488 0.863728375437842 0.865183007862792 
    ##              2950              2999              2944              2907 
    ## 0.866624510531686 0.868052950828758 0.869468396917486 0.870870917711314 
    ##              2606              2748              2414              2532 
    ## 0.872260582844713 0.873637462644614 0.875001628102202 0.876353150845074 
    ##              2548              2505              2466              2151 
    ## 0.877692103109782 0.879018557714737 0.880332588033512 0.881634267968511 
    ##              2067              2289              2229              2510 
    ## 0.882923671925037 0.884200874785744 0.885465951885476 0.886718978986508 
    ##              2321              2271              2358              2326 
    ## 0.887960032254175 0.889189188232895 0.890406523822593 0.891612116255522 
    ##              2552              2372              2315              2449 
    ##  0.89280604307348 0.893988382105423 0.895159211445481 0.896318609431367 
    ##              2455              2623              2494              2504 
    ## 0.897466654623187 0.898603425782644 
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

# Probit: All Variables

Result: The Probit estimation also shows small familes with the highest
probabilities to being in the labor force, which is similar to the logit
models, albeit with a smaller probability than logit. The medium-size
and large-size families follow just like in the logit models.

    ## 
    ## Call:
    ## glm(formula = LABFORCE ~ AGE + female + SmallFamily + MediumFamily + 
    ##     LargeFamily + Individual, family = binomial(link = "probit"), 
    ##     data = dat_use1)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -2.2155   0.4513   0.5633   0.6693   0.9944  
    ## 
    ## Coefficients: (1 not defined because of singularities)
    ##               Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)   0.974338   0.027452  35.493  < 2e-16 ***
    ## AGE          -0.006627   0.000611 -10.847  < 2e-16 ***
    ## female       -0.330755   0.010996 -30.080  < 2e-16 ***
    ## SmallFamily   0.564221   0.014526  38.843  < 2e-16 ***
    ## MediumFamily  0.427560   0.014697  29.092  < 2e-16 ***
    ## LargeFamily   0.177056   0.029283   6.046 1.48e-09 ***
    ## Individual          NA         NA      NA       NA    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 71408  on 74934  degrees of freedom
    ## Residual deviance: 69086  on 74929  degrees of freedom
    ## AIC: 69098
    ## 
    ## Number of Fisher Scoring iterations: 4

![](Lab-6_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

    ## Warning in predict.lm(object, newdata, se.fit, scale = 1, type = if (type == :
    ## prediction from a rank-deficient fit may be misleading

    ## 
    ## 0.784326659497045   0.7862615593576 0.788186310494023  0.79010088204436 
    ##              2950              2999              2944              2907 
    ##   0.7920052442017 0.793899368212494 0.795783226374691 0.797656792035702 
    ##              2606              2748              2414              2532 
    ## 0.799520039590191 0.801372944477698 0.803215483180086 0.805047633218824 
    ##              2548              2505              2466              2151 
    ## 0.806869373152101 0.808680682571776 0.810481542100163 0.812271933386657 
    ##              2067              2289              2229              2510 
    ## 0.814051839104191 0.815821242945547 0.817580129619502 0.819328484846814 
    ##              2321              2271              2358              2326 
    ##  0.82106629535607 0.822793548879367 0.824510234147849   0.8262163408871 
    ##              2552              2372              2315              2449 
    ## 0.827911859812385  0.82959678262375 0.831271102000981 0.832934811598417 
    ##              2455              2623              2494              2504 
    ## 0.834587906039634 0.836230380911986 
    ##              2560              2470

    ##        true
    ## pred    Not in LF in LF
    ##   FALSE      4930 19718
    ##   TRUE       8811 41476

    ##        true
    ## pred     Not in LF      in LF
    ##   FALSE 0.06579035 0.26313472
    ##   TRUE  0.11758190 0.55349303

    ## [1] 0.6192834

# Probit: All Variables (Interactions)

Result: The resulting probit regression on the same X and Y variables
(along with the same interactions) are very similar to logit model where
we see all the interactions between female and all three sizes of family
have a negative probability but the probability is not as pronounced in
the profit model as in the logit model.

    ## 
    ## Call:
    ## glm(formula = LABFORCE ~ AGE + female + MediumFamily + LargeFamily + 
    ##     SmallFamily + I(MediumFamily * female) + I(LargeFamily * 
    ##     female) + I(SmallFamily * female) + I(female * AGE), family = binomial(link = "probit"), 
    ##     data = dat_use1)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -2.3103   0.4133   0.5083   0.7099   1.0097  
    ## 
    ## Coefficients:
    ##                           Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)               0.711687   0.040126  17.736  < 2e-16 ***
    ## AGE                      -0.006285   0.000937  -6.708 1.98e-11 ***
    ## female                    0.335983   0.055020   6.107 1.02e-09 ***
    ## MediumFamily              0.932423   0.021819  42.734  < 2e-16 ***
    ## LargeFamily               0.719815   0.048538  14.830  < 2e-16 ***
    ## SmallFamily               0.790468   0.020154  39.220  < 2e-16 ***
    ## I(MediumFamily * female) -1.004892   0.030645 -32.792  < 2e-16 ***
    ## I(LargeFamily * female)  -1.080143   0.062281 -17.343  < 2e-16 ***
    ## I(SmallFamily * female)  -0.563447   0.029481 -19.112  < 2e-16 ***
    ## I(female * AGE)          -0.001576   0.001244  -1.267    0.205    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 71408  on 74934  degrees of freedom
    ## Residual deviance: 67899  on 74925  degrees of freedom
    ## AIC: 67919
    ## 
    ## Number of Fisher Scoring iterations: 4

![](Lab-6_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

    ## 
    ## 0.861221375490006 0.862607226750129 0.863983598172992 0.865350500608589 
    ##              2950              2999              2944              2907 
    ## 0.866707945577768 0.868055945266239 0.869394512518527 0.870723660831854 
    ##              2606              2748              2414              2532 
    ## 0.872043404349967 0.873353757856907 0.874654736770717 0.875946357137101 
    ##              2548              2505              2466              2151 
    ## 0.877228635623025 0.878501589510268 0.879765236688925 0.881019595650855 
    ##              2067              2289              2229              2510 
    ## 0.882264685483091 0.883500525861197 0.884727137042585 0.885944539859791 
    ##              2321              2271              2358              2326 
    ## 0.887152755713708 0.888351806566782 0.889541714936171 0.890722503886867 
    ##              2552              2372              2315              2449 
    ## 0.891894197024787  0.89305681848983 0.894210392948902 0.895354945588914 
    ##              2455              2623              2494              2504 
    ## 0.896490502109756 0.897617088717235 
    ##              2560              2470

    ##        true
    ## pred    Not in LF in LF
    ##   FALSE      7392 30963
    ##   TRUE       6349 30231

    ##        true
    ## pred     Not in LF      in LF
    ##   FALSE 0.09864549 0.41319811
    ##   TRUE  0.08472676 0.40342964

    ## [1] 0.5020751

# Comparing all the predictions

Result: Comparing the logit and the probit where we used *All Variables*
we can see that both the logit and probit models predicted accurately
\~60% of the time. And for the *All Variables (Interaction)* we can see
that both models predicted \~51%. In the end, we are able to see what we
learned from the lecture notes: that probit and logit are approximately
the same; the differences are rather small.

    ## [1] 0.5985588

    ## [1] 0.6192834

    ## [1] 0.5215186

    ## [1] 0.5020751

\#OLS: All Variables

### Bibliography

Accessed: 11/09/2020
<https://www.census.gov/programs-surveys/cps/technical-documentation/subject-definitions.html#householder>

<https://www.bls.gov/cps/definitions.htm#laborforce>

<https://usa.ipums.org/usa-action/variables/FAMSIZE#description_section>
