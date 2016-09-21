JP LECTURE NOTES:

### <p style="text-align: right;"> Week 1 </p>
***
#### Week 1, Day 1 -- Aug. 29
(Fill in first week of notes)


Homebrew
`brew list` shows all command-line apps installed via homebrew
`brew cask list` shows all GUI apps installed via homebrew.



Plotting in MPL:
plt.style.available #lists all available styles
plt.style.use('style_chosen')





### <p style="text-align: right;"> Week 2 </p>
***

#### Week 2, Day 3 -- Sep. 8
Understanding *why* we use certain distributions to model our data is the important key.  It isn't important to memorize every aspect about every dist.  We can look that up.  We need to know have a *feel* for the dists.

If we don't know the parameters of an obersved dist, we can estimate them using various techniques.  The two we are covering are:
+ Model of Moments (MOM)
  + Older, but still effective in general.

+ Maximum Likelihood Estimation (MLE)
  + Ubiquitous method in general data science and statistics for estimating parameters.  See slides from Week 2 for more detail.  In short, it is a cost function and attempts to maximize it for your data points and given parameter.

**Sampling Distribution**  
The distribution of a repeatedly sampled parameter from a parent population (or parent sample).
So, given a parent population N, take a sample size A from the pop and compute the mean of that sample.  Repeat this X number of times, and then plot the distribution of these means.  This is now the *sampling distribution* of the mean for that pop.  Aggregate functions, like the mean, will obey the Central Limit Theorem, more clearly so as X number of repeat samplings increases.  Other summary statistics, like the sample max(), do not obey the CLT -- their sampling distribution is strictly dependent upon the underlying parent population distribution.  This can help identify some aspects of the parent distribution, but makes other inferences about the parent population impossible since those inferences, such as the true mean of the population, depend on the CLT to be correct.


**Hypothesis Testing**  
Basically, "prove it." The null hypo is always the 'standard' or the 'normal' mode, and the alternative hypo is the 'claim' you are making.  Think your coffee makes workers more productive than the 'normal' coffee they're already using? Prove it.  Think your medicine works better than the 'normal' one? Prove it.  Using confidence levels allows us to say how certain we are something is 'proved' if the hypo test says it is.

A quick rule of thumb for determining if the variances of the two samples used in the hypo test is to take the ratio of the larger var to the smaller.  If it is less than 2, probably okay to go ahead and test without variance modification (Welsh t-test assumes diff variances, and can also pool variances).

Note that by the design of hypothesis testing, we are more likely to fail to reject the null than is correct, much like "innocent until proven guilty" in our judicial system -- the onus is upon the alt. hypo to confirm validity ('guilty').


**Five Steps to a Hypothesis Test**
1. State Null and Alt. hypotheses
2. Calculate Test Statistic based on distribution(s) of stat(s).
3. Obtain p-value.
    + Probability of getting your data (or something more extreme) given that the null hypo is true.  Smaller = less likely to get the observed results by mere chance.  
4. Compare p-value to α threshold.
    + If p-val is less than a pre-set threshold, called the α level, then we can say that the observed result is real/true/valid at an α confidence level, such as 95%.  A good way to think of α is as the Type I Error Rate, which is the amount of error (False Positives) you are willing to tolerate.  So, an α = .05 means we would be willing to accept a failure rate of 5%, assuming the hypothesis test passed.
5. State conclusion in terms of the problem.
    + Properly stated as either we "reject" or "fail to reject" the null hypo.  Assuming p-val < α, mathematically speaking, we haven't absolutely 'confirmed' the alt. hypo.  Other explanations for our observed data may still exist, but we can say with α confidence that, whatever the source, the data we observed is not due to random chance.  When using multiple hypo tests, say 20, we should divide the α value by the number of tests performed, and use that as our new α threshold.  So, say α = .05 and we did 20 tests, we would use .05/20 = .01 as our new α value.

α: Type I Error (False Positive)
β: Type II Error (False Negative)
1 - α: Confidence Interval
1 - β: Power  


+ Example: I have three versions of a website. I want to know which version is the best to use in practice.

Version | Users | Purchases | Clicks
--------|-------|-----------|-------
1 | 300 | 20 | 100
2 | 400 | 15 | 100
3 | 350 | 30 | 150

  + null: V1 = V2 = V3
  + alt: at least 1 site is diff
  + 3 tests = 3-choose-2 = 3
  + Modified α = .05 / 3 =
  + We will take the
  + There is a raw approach to this since we do have actual real data:
    1. Take a sample from each of the three  versions
    2. Bootstrap that sample for each version
    3. Subtract the statistic from Version A from Version B (i.e. mean A from bootstrap A from mean B from bootstrap B)
    4. Repeat steps 2 & 3 many times
    5. Plot the resulting sampling distribution of differences
    6. Throw away the top 2.5% and bottom 2.5%
    7. We now have the inner 95% range, and can say we expect the true mean for the given version to lie within this range at a 95% confidence level
    8. IF this range contains zero (i.e. if the number zero is in our 95% plot range) then we say that there is no observed difference between these two versions.
    9. The advantage of this is that it is real data and is not based upon any assumption about the underlying distribution, so no asymptotic assumptions (or equations) need be used.



#### Week 2, Day 4 -- Sep. 9: Statistical Power and Bayes
**Two-sided test**: when the hypothesis is *not equal* to something  
**One-sided test**: when the hypo is *greater than* or *less than* something  
**Sensitivity**: true positive rate  
**Specificity**: α rate.  Amount of allowed Type I error we are willing to accept.  
**Power**: the probability that we **correctly reject null** when the null hypo *is* false. (1 - β).  

Symbolically:  
**α**: Type I Error - Reject null when null is True (false positive)  
**β**: Type II Error - failt to reject null when null is false (false negative)  
**(1 - β)**: Power - reject null when null is false  


The calculatin of *Power* is not trivial because it is different for every test.
See *power-calculation.ipynb* in `DSI_Camp/Week2/power-bayesian/` for interactive calcs on Power and how diff variables affect the result. (neat: bigger changes are easier to detect, so smaller num of samples are required to observe.)
A common level of Power used is 80%.

For Power in Python:
```python
import scipy.stats as st
st.norm.ppf(alpha)
st.norm.ppf(1 - beta)
```

More:
```python
import scipy.stats as stats
## inverse of the cdf (percent point function)
alpha = 0.05
z = stats.norm.ppf(1-(alpha/2.0))
print("z=%s"%(round(z,2)))

## find a critical value z
phi_z = stats.norm.cdf(z)
print("phi_z=%s"%(phi_z))
```


In general, R handles Power calculations more readily than does Python at present.  As such, the power-calculation.ipynb named above has some examples of how to use R for Power calcs, including the package (pwr) to install in R as well as the Python package (rpy2) needed to access R via Python.  Do not install via pip, install via `[unknown atm]`

1. `brew install r`
  + can do `brew install r --reinstall` if needed to redo.  Installing R can take quite a while.

2. `brew install Caskroom/cask/rstudio`
  + install the GUI component of R (not required but recommended)

3. `pip install rpy2`
  + install the python module to interface with R

4. `sudo R` then `install.packages('pwr')`
  + access R and install the 'pwr' package for Power calcs.




**Bayes**
Priors often are distributions and not merely a single value.
Commonly we evaluate Bayes theorem by P(H|E) proportional to P(E|H) * P(H) after considering the denominator of P(E) as a normalizing constant.

### <p style="text-align: right;"> <span style="color:purple"> Week 3 </span> </p>
***
##### Week 3, Day 1 -- Sep. 12: Linear Algebra

`np.reshape` - returns a copy of the vector  
`np.resize` - changes shape in place


##### Week 3, Day 2 -- Sep. 13: Linear Regression

In most of our Machine Learning we will focus on Supervised learning.  

+ Supervised
  + Target you want to predict (predict a *y* for a given *x* (usually a large dataframe))
+ Unsupervised
  + No target
+ Semi-Supervised
  + Some targets are labeled, some are unknown.  Not as common.


+ Five Assumptions of Linear Regression:
  + X and Y are linearly related (Note: this does not mean X var has to be linear -- can be log or exponential, just the relationship has to be linear)
  + Homoscedasticity of residuals (variance is equal across the range of values)
  + Normally distributed residuals
  + No multicolinearity among features (Variance Inflation Factors - **VIF**)
    + VIF > 10: suggests pulling features out of regression
    + VIF > 5: review this term to check for colinearity with another term of similar VIF.
    + Regularization methods are the most common for choosing which terms to keep.
  + Independence of the observations (For example, independence assumption violated if data is a time series)
    + Dependent variable (y) is continuous, Independent variable (x) can be continuous or discrete
    + Categorical values (names, field, etc.) will be converted to *dummy variables*

Understanding what's going on under the hood of regression is a major factor in standing apart from other candidates as it is a frequent interview topic.  One thing to know: we use the Least Sum of Squares -- i.e. we square our errors and take the root -- because it **has a clean, computable derivative which allows us to use the gradient descent method of finding the smallest error value.**  This means that squaring our residuals in the OLS (LSS) method is computationally viable and has an easy segue to to further methods of error optimization. Also, OLS is built in to just about every piece of statistical software, meaning we don't have to write our own method.  Knowing why we are doing what we are doing is as important as being able to implement the methods themselves in some ways.

Use a canonical formula (such as a log() for Poisson) to link the function of the parameters for a distribution to a linear combination of inputs.

**Dummy Variables**
+ Set first to 0
+ Set last to 0
+ 1, 0, -1

+ Our matrix X must be full rank to be invertible.
  + B = (X' X)^- X'y

Use `pd.get_dummies( , drop_first=True)`   to get dummies and drop the old cols.
If the categories for dummy vars don't necessarily contain all data points (such as not being High / Med / Low) and a data point is NaN, then treat NaN as an additional category.

Use AIC, BIC, Cp, R^2, and p-vals to determine if you should drop data or not.  Of these, R^2 and p-vals are the most spurious and potentially misleading, making relying upon them alone a bad idea.  Cp might have the most predictive power at times, but BIC tends to be the most preferred.


##### Week 3, Day 3 -- Sep. 14: Regularization and Bias v. Variance
**Bias v. Variance**
Bias v. Variance can be thought of much like Accuracy v. Reliability in science.

<div align="center">
    <img height="400" src="/Users/jpw/Dropbox/Work/Galvanize/DSI_Camp/Week3/regularized-regression/images/bias_variance.png">
</div>

+ Bias = Accuracy
  + Bias can be thought of as how accurate we predict/model the true signal, the true data points.  With increased accuracy we have less bias.  
+ Variance = Reliability
  + Variance is how much our model's results will change with different input data.  If we change our input data/signal and our output is consistent, even if it is wrong, it has low variance -- it is reliable.  

The big conundrum is balancing Bias v. Variance; As we try to increase bias by adding model complexity we can *overfit* the model to *that particular data*, then our model will be unreliable with *different data* (from the same population), meaning it has a high variance.  We ideally would love very low bias and variance, of course, but outside of instructional simple systems, this is not likely achievable.  As with any system of tradeoffs, there is a sweet spot (i.e. a local minimum, see below) where we have as low of a bias and variance as is reasonable.

<div align="center">
    <img height="400" src="/Users/jpw/Dropbox/Work/Galvanize/DSI_Camp/Week3/regularized-regression/images/bias_variance_graph.png">
</div>


**Cross-Validation**
Must be done *completely randomly*.  When choosing the training set and the test set, we cannot filter these sets by using any knowledge, such as which features carry the most impact.  It must be done randomly, before the data is examined.  

Four Fundamental Steps:
1. Split your data into training/validation sets.  
    + 70/30 or 90/10 splits are commonly used
2. Use the training set to train several models of varying complexity.
    + e.g. linear regression (w/ and w/out interaction features), neural nets, decision trees, etc. (we’ll talk about hyperparameter tuning, grid search, and feature engineering later)
3. Evaluate each model using the validation set.
    + calculate R2, MSE, accuracy, or whatever you think is best
4. Keep the model that performs best over the validation set.


The K-folds approach is common and results in data and error to graph.

Using the "Leave-One-Out" approach is computationally expensive and by design gives a low-variance model, both of which we want to avoid (low variance is good, but not when it is systemically forced).

Always remember that as dimensionality increases (more features), overfitting a model becomes naturally easier.  Have to work against overfitting by trying to select subsets of the features, usually starting with one and gradually adding in other important ones.  The best model is a *lean* model -- as few features as possible that give the desired accuracy and reliability.  Don't add ingredients to the recipe if the recipe already does what you want.

**Regularization**
The fundamental approach of Regularization is to limit the magnitude of the coefficients to limit overfitting.  As we increase the number of features/terms in a regression, the magnitude of each tends to increase (sometimes greatly).  As a result, limiting the magnitude of these terms' coefficients will help to reduce overfitting.

In Regularization models like Ridge (L2) or Lasso (L1), the λ term is the 'Regularization' parameter, and acts as a tuning knob for the coefficients -- how loose will we let them be?
<div align="center">
    <img  src="/Users/jpw/Dropbox/Work/Galvanize/DSI_Camp/Week3/regularized-regression/images/lasso.png">
</div>
<div align="center">
    <img  src="/Users/jpw/Dropbox/Work/Galvanize/DSI_Camp/Week3/regularized-regression/images/ridge.png">
</div>

Smaller values (all negative) of λ mean we have a more complex model and let the coefficients have a broader range, larger values (up to zero) damped coefficients more strenuously.





##### Week 3, Day 4 -- Sep. 15: Logistic Regression

odds = p^k * (1-p)^n-k

so p = 1/100 and q = 99/100: 1/100 / 99/100 = 1/99
<div align="center">
    <img  src="/Users/jpw/Dropbox/Work/Galvanize/DSI_Camp/Week3/logistic-regression/images/logistic.png">
</div>

Classification Metrics II:  
+ Recall / True Positive Rate / Sensitivity
  + Of those observations that are actually positives, which ones did I label as positive?
  + True Positives / (True Positives + False Negatives)

 + Specificity / True Negative Rate
   + Of those observations that are actually negative, which ones did I label as negative?
   + True Negatives / (True Negatives + False Positives)

+ Precision / Positive Predictive Value
  + Of those observations that I labeled as positive, which ones are actually positive?
  + True Positives / (True Positives + False Positives)

+ Accuracy
  + How many observations did I label correctly?
  + (True Positives + True Negatives) /  Total number of obs.
+ False Positive Rate
  + Of those observations that are actually negatives, which ones did I label as positive?
  + False Positives / (False Positives + True Negatives)




##### Week 3, Day 5 -- Sep. 16: Gradient Descent
All day pair work.


##### Week 4, Day 1 -- Sep. 19: K-Nearest Neighbor (KNN)
Non-parameterized algorithm.  Unlike parameterized regression models, KNN does not assume anything about the data (e.g. no underlying distribution, etc.)
Curse of dimensionality is rife with Non-parameterized models.

There is no actual 'training' for this model.  You simply plot your data and measure the distances bww the points, sort by distance, and classify.

Rule of thumb is sqrt(N) for sample size, but use Power calcs from last week for more precision on power of sample size.

When using categorical features, such as genres of music, must be aware of *exchangeability*, which is the quandary of ordering/assigning genres in a manner that suggests one is more closely related to another (or more preferable).  Ex: order of R&B, Country, Rock, EDM, Folk, Classical would get values of R&B = 1, Country = 2, ..., Classical = 6.  But Why?  And what effect does that have on the resulting clustering?  So, this is a real problem.


##### Week 4, Day 2 -- Sep. 20: Random Forest
Random Forest is simply Bagging (Bootstrap Aggregating) with one tweak:
the number of predictors chosen to split on at each node of the tree is the square root of the total number of predictors in the dataset.  So, if we have 9 predictors to choose from, each node will randomly choose 3 of them to split on.  This ensures no one predictor dominates the splits.  So to be clear, we Bag the trees then do a Random Forest (n predictors per split = sqrt(p total predictors))

Note if we did not Bag and RF, but still made 100 trees, we would simply make 100 identical trees because they'd all have the same dataset and same predictors, and as such would choose to split on the same predictors each time.  So, Bootstrapping and RF give us a much better statistical look at our data given the same initial dataset.

When testing make sure to specify random_state = <constant> so that repeated trials give the same results of splits.

1. From training set, bootstrap -- gives one tree from Bagged data
2. Do first split, use subset of sqrt(predictors) on every split, giving a Random Forest model

For regression we are trying to minimize RSS via RF
Can use sklearn.utils.resample to help with bootstrap.
