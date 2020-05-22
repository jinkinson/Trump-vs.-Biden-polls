# Trump-vs.-Biden-polls
This repository does the following things: 
1. It compiles state-level 2020 general election polls between Trump and Biden. Only polls in which these were the only two candidates (so no third-party candidates) are included in this dataset. All polls were obtained from FiveThirtyEight's poll tracker: https://projects.fivethirtyeight.com/polls/
2. It presents both raw and weighted estimates of the margins between Trump and Biden based on state-level general-election polls in every state in which at least 1 such poll is available.

The weighting procedure takes both age of poll (today-average(start date, end date)) and pollster grade (based on FiveThirtyEight's pollster grades: https://projects.fivethirtyeight.com/pollster-ratings/) into account. This is done as follows:

1. Weighting procedure for poll age
a) The average of the start and end dates is calculated for each poll (e.g. a poll starting on 5/17/20 and ending on 5/19/20 would have an average of 5/18/20)
b) This average is then subtracted from today, again for each poll, to give the poll's "true age", denoted x
c) The average "true age" of all polls in the dataset is calculated, denoted y.
d) The "age weight", denoted z, is calculated for each poll as follows: z = (y-x)/(100y)
e) The overall weight for that poll is calculated as follows, where n = poll's sample size, m = margin in that poll (m = Biden % - Trump %, so m > 0 if Biden is ahead of Trump in that poll and vice versa), and g = grade weight (explained below): nm(1+z)g
In this step, the use of the sample size is intended to weight polls higher if their sample sizes are larger.
f) The sum of the overall weights described in step e) for a given state is calculated and then divided by the sum of the sample sizes (n) for each poll conducted in that state to give the weighted average margin for that state: sum(nm(1+z)g)/sum(n)

2. Weighting procedure for poll grade
First I should note: If, in the FiveThirtyEight pollster ratings, the letter grade is surrounded by a solid circle, this indicates a more definitive grade than if the grade is surrounded by a dashed circle. Also, the latter type of grades are always in the form of two letter grades in one (e.g. A/B or B/C). 
a) The solid-circle letter grade given by FiveThirtyEight for a pollster (if the pollster had one at all) is converted into a number as follows: A+ = 1, so 1 is the best possible grade, A = 2, A- = 3, etc. For dashed-circle grades, this is done in a similar way, so that A/B = 1, B/C = 2, etc.
b) The grade log (denoted L) is calculated as, if the pollster has a solid-circle grade, the base 10 log of the numerical grade described in the above step. If the pollster has only a dashed-circle grade, then the grade log is one half of the base 10 log of the numerical grade. In other words: where pollster has solid-letter grade denoted g, L = log(g); where pollster has dashed-letter grade denoted d, L = (log(d))/2 The division by 2 is intended to reduce the weight caused by a given grade for a pollster if that grade is denoted with a dashed circle and thus is considered less definitive than a solid-circle grade.
If the pollster has no grade, this value is shown as blank and all subsequent grade-weighting steps are skipped for that poll. In such cases, the term g in the formula in step 1e is omitted.
c) The grade weight is calculated as w = (L-average(L))/100, where average(L) is the average grade log across all included polls.
d) The value of g, described in step 1e above, is then calculated as (1+w) for solid-circle polls and as (1+0.5w) for dashed-circle polls.
