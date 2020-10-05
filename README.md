Lab 4
================

<p style="color:rgb(182,18,27);font-family:corbel">

Econ B2000, MA Econometrics

</p>

<p style="color:rgb(182,18,27);font-family:corbel">

Kevin R Foster, the Colin Powell School at the City College of New York,
CUNY

</p>

<p style="color:rgb(182,18,27);font-family:corbel">

Fall 2020

</p>

For this lab, we finally do some regression models to explain wages. We
want to understand how important is a college degree (and what major).

Form a group. Groups should prepare a 4-min presentation by one of the
group members about their experiment process and results. You get 45 min
to prepare. Next homework assignment will ask you to go deeper.

Make sure you’re stepping up your coding – at minimum, by now you should
have your code as R-Script (in top left panel of R Studio) so that you
can easily see what you run (CTRL-Enter is an easy shortcut). If you get
signficicant errors then stop and re-run your code from a fresh start
(clear memory, re-load data) so that you don’t pile one mistake on top
of another. When you submit homework, I’ll want to see a file with your
code and another with the output from that code. It would be even better
to submit an R-Markdown file with code and text, where text tells the
story clearly, along with that output – but I understand that not
everybody is that far along yet.

As you begin the analysis, you should first consider what subgroup to
use – for example, you could run code like this,

``` r
attach(acs2017_ny)
use_varb <- (AGE >= 25) & (AGE <= 55) & (LABFORCE == 2) & (WKSWORK2 > 4) & (UHRSWORK >= 35)
dat_use <- subset(acs2017_ny,use_varb) # 
detach()
attach(dat_use)
```

This selects people 25-55 (often called prime age), in labor force,
working year round, fulltime. Otherwise the regression would be trying
to explain why so many people make zero wages despite so many
qualifications. (Think through: if you include retirees in the data and
estimate a linear model of age affecting wage, what would you expect for
the sign of the coefficient?) Of course when you look at college major,
you’re looking at only people with at least a college degree but you can
code that for yourself.

Make sure you run *summary(dat\_use)* to check for obvious mistakes.
Maybe some crosstabs to also look.

First, *before running a regression,* consider what variables should be
in your model. What are some of the important factors that influence a
person’s wage? Is there a plausible causal link from X variables to Y
and not the reverse?

Now try a linear regression, with wage as the dependent variable, a
dummy for major, and other variables of your choice.

Here is some sample code,

``` r
model_temp1 <- lm(INCWAGE ~ AGE + female + AfAm + Asian + Amindian + race_oth + Hispanic + educ_hs + educ_somecoll + educ_college + educ_advdeg)
summary(model_temp1)
plot(model_temp1)
# maybe get fancy
require(stargazer)
stargazer(model_temp1, type = "text")
# play with stargazer instead of summary, it can look nicer!
```

For many of the hypothesis tests, you’ll want the AER package

``` r
require(AER)
```

Show a plot of regression line with data points, something like this (as
suggested in the AER text),

``` r
# subset in order to plot...
NNobs <- length(INCWAGE)
set.seed(12345) # just so you can replicate and get same "random" choices
graph_obs <- (runif(NNobs) < 0.1) # so something like just 1/10 as many obs
dat_graph <-subset(dat_use,graph_obs)  

plot(INCWAGE ~ jitter(AGE, factor = 2), pch = 16, col = rgb(0.5, 0.5, 0.5, alpha = 0.2), data = dat_graph)
# ^^ that looks like crap since Wages are soooooooo skew!  So try to find some sensible ylim = c(0, ??)
plot(INCWAGE ~ jitter(AGE, factor = 2), pch = 16, col = rgb(0.5, 0.5, 0.5, alpha = 0.2), ylim = c(0,150000), data = dat_graph)
# discus what you see in this plot

# change this line to fit your regression
to_be_predicted2 <- data.frame(AGE = 25:55, female = 1, AfAm = 0, Asian = 0, Amindian = 1, race_oth = 1, Hispanic = 1, educ_hs = 0, educ_somecoll = 0, educ_college = 1, educ_advdeg = 0)
to_be_predicted2$yhat <- predict(model_temp1, newdata = to_be_predicted2)

lines(yhat ~ AGE, data = to_be_predicted2)
```

Create some more, for different X-values.

Carefully explain the hypothesis tests of each coefficient and all of
the coefficients jointly. For each coefficient, explain the t-stat,
p-value, and confidence interval.

You might show how heteroskedasticity-consistent standard errors, as
with *coeftest(model1,vcovHC)*, would affect each of these.

Try both *lm(INCWAGE \~ …* and *lm(log(INCWAGE) \~ …* Compare mean of
predicted values of first version with exp(x) mean of predicted values
of second version. Discuss.

Finally, always, always, ALWAYS **\!\!** remember this part,

``` r
detach()
```

You might do that multiple times, if you were careless previously…

Try some more linear regressions. Explain if there is a plausible causal
link from X variables to Y and not the reverse. Explain what additional
restrictions to put on the dataset (eg just prime age, just females,
just college degree, whatever).

Explain your results, giving details about the estimation and providing
any relevant graphics. What are the changes from previous regression on
state-level averages and why might this be so? How do changes in
specification (e.g. logs) change the estimated coefficients? What are
some relevant predicted values? Do those seem sensible? What additional
information would be useful? Impress me.
