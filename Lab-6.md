Lab 6
================
Patrick Sinclair, Kieran Yuen

### Factoring LABFORCE and MARST

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

\#Creating an age break band

\#Creating our first subset that we will be performing regressions on

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

Next, let’s make separate columns in the acs2017 data for each of the
three (3) family sizes similar to how there are five (5) columns for
education (educ\_nohs, educ\_hs, etc….).

(Note: we have created an extra “Individual” column so that that can
serve as the “dropped variable” when we are running our logit
regressions)

# Logit: Fam Size ONLY

Next, let’s run the logit regression to see how the family sizes affect
whether someone is in the labor force or not.

Expectation: We are expecting that one of the three (3) variables will
drop off as that is necessary so that the variables can be compared to
something. Also, we were thinking that the larger the size of the family
the less likely the labor participation rate would be because the more
people in a household the less likely everyone in the household would
need to work, but the counter argument to that is that those with larger
families have more mouths to feed and thus would increase their
likelihood to be in the labor force. So it is hard to estimate which of
the three (3) family sizes will show to have the largest effect on labor
participation rates.

Result: So it appears that *small* families have the highest probability
of being in the labor force. Next would be *medium* families and last is
*large* families which have the lowest probabilities of being in the
labor force.

# Logit: All Variables

Next, let’s add in all the other variables to see how that affects our
probabilities.

Result: Small families continue to show the highest probability of labor
force participation. Followed by medium families. Last is large
families. All three family sizes are statistically significant at the
0.05 level.

# Logit: All Variables (Interactions)

Result: The interactions of family size and being female appear to be
negative. This is telling us that being female negative impacts the
probabilities of being in the labor force whatever the person’s family
size maybe.

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
