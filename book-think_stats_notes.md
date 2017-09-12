# Think Stats by Allen Downey
Think Stats is an 'interactive' book, if you will, by virtue of using Jupyter Notebooks.  It also includes a lot of code modules that are used throughout the book which Downey wrote just for this purpose.  So, using them is advisable when going through the book.  In my opinion, they are written at a high enough level to actually use anytime in dealing with distributions and stats -- just `import thinkstats2 as ts2` in the correct directory (or simply put the module somewhere and add to PYTHONPATH).

Many passages below are directly from the book and were simply things I enjoyed or wanted to save.


## Distributions

### 2.1 Histograms
Counts up items in a list / series / sample and tells you how many times they appear, or their _frequency_.  Helps reveal the distribution of the data.  


### 2.9 Effect size
An effect size is a summary statistic intended to describe (wait for it) the size of an effect. For example, to describe the difference between two groups, one obvious choice is the difference in the means.  

Mean pregnancy length for first babies is 38.601; for other babies it is 38.523. The difference is 0.078 weeks, which works out to 13 hours. As a fraction of the typical pregnancy length, this difference is about 0.2%.  

If we assume this estimate is accurate, such a difference would have no practical consequences. In fact, without observing a large number of pregnancies, it is unlikely that anyone would notice this difference at all.

Another way to convey the size of the effect is to compare the difference between groups to the variability within groups. Cohen’s _d_ is a statistic intended to do that; it is defined


**_d_ = (x<sub>1</sub> − x<sub>2</sub>) / s**  

where _x<sub>1</sub>_ and _x<sub>2</sub>_ are the means of the groups and _s_ is the “pooled standard deviation.”

Here’s the Python code that computes Cohen’s _d_:
```python
def CohenEffectSize(group1, group2):
    diff = group1.mean() - group2.mean()
    var1 = group1.var()
    var2 = group2.var()
    n1, n2 = len(group1), len(group2)
    pooled_var = (n1 * var1 + n2 * var2) / (n1 + n2)
    d = diff / math.sqrt(pooled_var)
    return d
```

In this example, the difference in means is 0.029 standard deviations, which is small. To put that in perspective, the difference in height between men and women is about 1.7 standard deviations (see https://en.wikipedia.org/wiki/Effect_size).






## Probability Mass Function

### 3.1 PMFs
Another way to represent a distribution is a probability mass function (PMF), which maps from each value to its _probability_. A probability is a frequency expressed as a fraction of the sample size, _n_. To get from frequencies to probabilities, we divide through by _n_, which is called _normalization_.

Given a Hist, we can make a dictionary that maps from each value to its probability:
```python
n = hist.Total()
d = {}
for x, freq in hist.Items():
d[x] = freq / n
```

Or we can use the Pmf class provided by thinkstats2. Like Hist, the Pmf constructor can take a list, pandas Series, dictionary, Hist, or another Pmf object. Here’s an example with a simple list:

```bash
>>> import thinkstats2
>>> pmf = thinkstats2.Pmf([1, 2, 2, 3, 5])
>>> pmf

Pmf({1: 0.2, 2: 0.4, 3: 0.2, 5: 0.2})
```
The Pmf is normalized so total probability is 1.
Pmf and Hist objects are similar in many ways; in fact, they inherit many of their methods from a common parent class. For example, the methods Values and Items work the same way for both. The biggest difference is that _a Hist maps from values to integer counters; a Pmf maps from values to floating-point probabilities._

Note that modifying a probability within a PMF will result in a cumulative probability of the PMF not adding up to 1 (which makes the conclusions drawn from a PMF useless).  In order to fix this we must re-normalize (divide through by the total probability to make it 1 again) the PMF before we can use it again.  Intuitively, if we drop the probability for one item, the probabilities for the remaining items in the PMF will increase and vice versa.

The reason a PMF is valuable is that it allows to use cut through differing sample sizes and compare simple probabilities.  They also allow the flexibility to bias or unbias a distribution.  Below is an example of taking in a PMF of marathon runners' speeds and choosing a given speed that someone might run at and seeing how the other runners appear to someone running that speed (e.g. the distribution will shift based on how fast you run since you will pass/be passed at different rates).

```python
def ObservedPmf(pmf, speed, label=None):
    """Returns a new Pmf representing speeds observed at a given speed.

    The chance of observing a runner is proportional to the difference
    in speed.

    Args:
        pmf: distribution of actual speeds
        speed: speed of the observing runner
        label: string label for the new dist

    Returns:
        Pmf object
    """
    new = pmf.Copy(label=label)
    for val in new.Values():
        diff = abs(val - speed)
        new[val] *= diff
    new.Normalize()
    return new
```


### Chapter 3 Glossary
+ Probability mass function (PMF): a representation of a distribution as a function that maps from values to probabilities.
+ Probability: A frequency expressed as a fraction of the sample size.
+ Normalization: The process of dividing a frequency by a sample size to
get a probability.
