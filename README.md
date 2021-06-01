# Udacity A/B Testing by Google

The final project of Udacity A/B Testing by Google.

[This project](https://classroom.udacity.com/courses/ud257/lessons/4126079196/concepts/42072285530923) is from Udacity free course [A/B Testing by Google](https://www.udacity.com/course/ab-testing--ud257).

---

## Project Instructions

### Project Resources

> [Project Instructions](https://classroom.udacity.com/courses/ud257/lessons/4126079196/concepts/42072285530923)
> [Project Instructions - Full Version](https://docs.google.com/document/u/1/d/1aCquhIqsUApgsxQ8-SQBAigFDcfWVVohLEXcV6jWbdI/pub?embedded=True)
> [The template](https://docs.google.com/document/d/16OX2KDSHI9mSCriyGIATpRGscIW2JmByMd0ITqKYvNg/edit)
> [The rubric](https://docs.google.com/document/u/1/d/1Hga00A4258wSJ9dir0Td_ZPLeAtJ079UGbM3C0ykUtE/pub?embedded=true)

---

### Datasets

> [Final Project Baseline Values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0)
> [Final Project Results: Control and Experiment](https://docs.google.com/spreadsheets/d/1Mu5u9GrybDdska-ljPXyBjTpdZIUev_6i7t4LRDfXM8/edit#gid=0)

---

### Project overview

In this project, you will consider an actual experiment that was run by Udacity. The specific numbers have been changed, but the patterns have not. You will flesh the experiment idea out into a fully defined design, analyze the results, and propose a high-level follow-on experiment. The [project instructions](https://docs.google.com/document/u/1/d/1aCquhIqsUApgsxQ8-SQBAigFDcfWVVohLEXcV6jWbdI/pub?embedded=True) contain more details.

---

- **Summary**: Udacity currently have two options on the course overview page: **"start free trial"**, and **"access course materials"**.
  - If the student clicks **"start free trial"**, they will be asked to enter their credit card information, and then they will be enrolled in a free trial for the paid version of the course. After 14 days, they will automatically be charged unless they cancel first. 
  - If the student clicks **"access course materials"**, they will be able to view the videos and take the quizzes for free, but they will not receive coaching support or a verified certificate, and they will not submit their final project for feedback.

- **Experiment**: Free Trail Screener
- **Goal**: Maximize the course completion rate of **"Free Trail"** users through guiding the students who do not have enough time to **"access course materials"**.
- **Experiment Hypothesis**: The hypothesis was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.
- **Experiment Change**: For the users who click on **"start free trail"**, Udacity will ask the users how much time they are available to devote to the course.
  - For users who will devote 5 or more hours per week. It's the same as usual.
  - For users who will devote fewer than 5 hours per week, Udacity will suggest the users choose "access course materials"
    ![](https://raw.githubusercontent.com/ZacksAmber/PicGo/master/img/20210525024923.png)

- **Unit of diversion**: cookies
  - If the student enrolls in the free trial, they are tracked by user-id from that point forward. **The same user-id cannot enroll in the free trial twice. For users that do not enroll, their user-id is not tracked in the experiment, even if they were signed in when they visited the course overview page.**

- **The Process of Users' Behaviors**:
  1. Visit Udacity website
  2. Open course page (`cookies` or called `pageviews`)
  3. Choose "start free trail" or "access course materials".
     1. "access course materials"
        1. Free users -> no charging
     2. "start free trail" (`clicks`)
        1. Fewer than 5 hours per week for learning -> suggested for switching to "access course materials"
        2. 5 hours per week for learning -> stay here (`user-id`)
           1. Course completion & Subscription (`enrollment` & `payment`)
           2. Course completion & Cancel Subscription (`enrollment`)
           3. Course incomplete & Subscription (`enrollment` & `payment`)
           4. Course incomplete & Cancel Subscription (`enrollment`)

---

## Experimental Design

---

### Hypothesis Testing

- control group, also called group`A`.
- experiment group, also called group `B`.

$\displaystyle H_0: P_A - P_B = 0$
$\displaystyle H_1: P_A - P_B = d$

$\alpha = P(\mbox{reject null | null true})$
$\beta = P(\mbox{failed to reject | null false})$

- **Null Hypothesis**: There is no difference in group `A` and `B`.
- **Alternative Hypothesis**: There is a difference in group `A` and `B`.

---

### Metric Choice

A/B Testing requires two types of metrics: Invariant Metrics and Evaluation Metrics.

The following are possible metrics:

Any place "unique cookies" are mentioned, the uniqueness is determined by day. (That is, the same cookie visiting on different days would be counted twice.) User-ids are automatically unique since the site does not allow the same user-id to enroll twice.

- **Number of cookies**: That is, number of unique cookies to view the course overview page. ($d_{min}=3000$)
- **Number of user-ids**: That is, number of users who enroll in the free trial. ($d_{min}=50$)
- **Number of clicks**: That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger). ($d_{min}=240$)
- **Click-through-probability**: That is, number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page. ($d_{min}=0.01$)
- **Gross conversion**: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. ($d_min=0.01$)
- **Retention**: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of user-ids to complete checkout. ($d_{min}=0.01$)
- **Net conversion**: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. ($d_{min}=0.0075$)

---

#### Invariant Metrics

**Invariant metrics:**
- Population sizing metrics, based on your unit of diversion. Your population of control and experiment should be comparable.
- Actual invariants, those metrics that shouldn’t change when you run your experiment.

The good invariant metrics are the metrics before the **Experiment Change** happens in **The Process of Users' Behaviors**, and the invariant metrics should not changed by any reason including your **Experiment Change**. In this experiment, the **Experiment Change** locates in the step while the users are clicking on "start free trail".

|Metric Name|Metric Formula|$d_{min}$|Notation|Python Notation|Reason|
|:-:|:-:|:-:|:-:|:-:|:-:|
|Number of cookies|#number of unique cookies to view the course overview page|3000 cookies|$C_{cookies}$|`cookies`|Population sizing metric|
|Number of clicks|#number of unique cookies to click the "Start free trial" button|240 clicks|$C_{clicks}$|`clicks`|Actual invariant|
|Click-through-probability|$\displaystyle \frac{C_{clicks}}{C_{cookies}}$|0.01|CTP|`CTP`|Actual invariant|

---

#### Evaluation Metrics

> [Evaluation Metrics](https://deepai.org/machine-learning-glossary-and-terms/evaluation-metrics)

Evaluation metrics are used to measure the quality of the statistical or machine learning model. It is very important to use multiple evaluation metrics to evaluate your model. This is because a model may perform well using one measurement from one evaluation metric, but may perform poorly using another measurement from another evaluation metric. Using evaluation metrics are critical in ensuring that your model is operating correctly and optimally. 

|Metric Name|Metric Formula|$d_{min}$|Notation|Python Notation|Reason|
|:-:|:-:|:-:|:-:|:-:|:-:|
|Gross conversion|$\frac{C_{enrolled}}{C_{clicks}}$|0.01|Gross conversion|`gross_conversion`|The performance of a model|
|Retention|$\frac{C_{paid}}{C_{user-id}}$|0.01|Retention|`retention`|The performance of a model|
|Net conversion|$\frac{C_{paid}}{C_{clicks}}$|0.0075|Net conversion|`net_conversion`|The performance of a model|

---

### Calculation

#### Baseline Values

> [Baseline Values](https://docs.google.com/spreadsheets/d/1MYNUtC47Pg8hdoCjOXaHqF-thheGpUshrFA21BAJnNc/edit#gid=0)

For each metric you selected as an evaluation metric, make an analytic estimate of its standard deviation, given sample size of 5000 cookies visiting the course overview page. Enter each estimate in the appropriate box to 4 decimal places.

|Metric Notation|Python Notation|Description|Estimator|$d_{min}$|
|:-:|:-:|:-:|:-:|:-:|
|$C_{cookies}$|`cookies`|Unique cookies to view course overview page per day|40000|3000|
|$C_{clicks}$|`clicks`|Unique cookies to click "Start free trial" per day|3200|240|
|$C_{enrolled}$|`enrolled`|Enrollments per day|660|NA|
|CTP|`CTP`|Click-through-probability on "Start free trial"|0.08|0.01|
|Gross conversion|`gross_conversion`|Probability of enrolling, given click|0.20625|0.01|
|Retention|`retention`|Probability of payment, given enroll|0.53|0.01|
|Net conversion|`net_conversion`|Probability of payment, given click|0.1093125|0.0075|

```py Python
import pandas as pd
import numpy as np
import math
import scipy.stats as stats
import plotly.express as px
import plotly.graph_objects as go
from IPython.display import display, Latex
```

```py Python
# Define baseline metrics
cookies = 40000
clicks = 3200
enrolled = 660
CTP = clicks / cookies
gross_conversion = enrolled / clicks
retention = 0.53
net_conversion = gross_conversion * retention

metrics_key = ['cookies', 'clicks', 'enrolled', 'CTP', 'gross_conversion', 'retention', 'net_conversion']
metrics_value = [cookies, clicks, enrolled, CTP, gross_conversion, retention, net_conversion]

# Define DataFrame baseline
baseline = pd.DataFrame(columns=['Metric', 'Estimator'])

for key, value in zip(metrics_key, metrics_value):
    baseline.loc[len(baseline)] = [key, value]

baseline.set_index('Metric', inplace=True)
baseline
```

```py Python
# Replace the sample metrics
baseline.loc['cookies'] = 5000
baseline.loc['clicks'] = baseline.loc['clicks'] * (5000/40000)
baseline.loc['enrolled'] = baseline.loc['enrolled'] * (5000/40000)

baseline
```

```py Python
display(Latex(r"$\sigma^2 = p(1 - p) \Rightarrow \sigma = \sqrt{p(1 - p)}$"))
def get_se(p, n):
    SE = np.sqrt(p * (1-p) / n)
    return round(SE, 4)

print(f"Gross conversion: {get_se(baseline.loc['gross_conversion', 'Estimator'], baseline.loc['clicks', 'Estimator'])}")
print(f"Retention: {get_se(baseline.loc['retention', 'Estimator'], baseline.loc['enrolled', 'Estimator'])}")
print(f"Net conversion: {get_se(baseline.loc['net_conversion', 'Estimator'], baseline.loc['clicks', 'Estimator'])}")
```

```py Python
# Define function sanity_check to calculate margin of error, confidence interval
def sanity_check(N, X, alpha=0.05, p=0, alternative="two-sided", round=4, formula=False):
    """
    Return
        - Margin of Error
        - Left edge of Confidence Interval
        - Right edge of Confidence Interval
        - Observed probability
        - If Observed probability inside Confidence Interval

    N: int
        Control data and Experiment data
    X: int
        Control data
    alpha: float
        Default: 0.05
        Significance Level: between 0 to 1
    p: float
        Population probability. Leave it blank if it is unknown.
    alternative: str
        One of "two-sided" or "one-sided"
    round: int
        Default: 4
        Round a number to a given precision in decimal digits.

    Reference:
        [Binomial proportion confidence interval](https://en.wikipedia.org/wiki/Binomial_proportion_confidence_interval)

    >>> binomial_CI(N=1000, X=450, p=0.5, alternative="two-sided", round=4)
    """

    # Validate significance level - alpha
    if (0 <= alpha <= 1) is False:
        raise ValueError(f'significance level (alpha) must be in the range of [0, 1]')

    if alternative == "two-sided":
        z1 = get_z_score(1 - alpha/2)
        if formula is True:
            display(Latex(r"$\displaystyle H_0: P_A - P_B = 0$"))
            display(Latex(r"$\displaystyle H_1: P_A - P_B = d$"))
            display(Latex(r"$\displaystyle \sigma^2 = \hat p (1 - \hat p)$"))
            display(Latex(r"$\displaystyle \displaystyle SE = \sqrt{\frac{\sigma^2}{n}}$"))
            display(Latex(r"$\displaystyle Z_{\gamma} = Z_{1 - \frac{\alpha}{2}}$"))
            display(Latex(r"$\displaystyle MOE_{\gamma} = Z_{\gamma} \cdot SE$"))
            display(Latex(r"$\displaystyle CI = \hat p \pm MOE_{\gamma}$"))
    elif alternative == "one-sided":
        z1 = get_z_score(1 - alpha)
        if formula is True:
            display(Latex(r"$\displaystyle H_0: P_A - P_B = 0$"))
            display(Latex(r"$\displaystyle H_1: P_A - P_B = d$"))
            display(Latex(r"$\displaystyle \sigma^2 = \hat p (1 - \hat p)$"))
            display(Latex(r"$\displaystyle \displaystyle SE = \sqrt{\frac{\sigma^2}{n}}$"))
            display(Latex(r"$\displaystyle Z_{\gamma} = Z_{1 - \alpha}$"))
            display(Latex(r"$\displaystyle MOE_{\gamma} = Z_{\gamma} \cdot SE$"))
            display(Latex(r"$\displaystyle CI = \hat p \pm MOE_{\gamma}$"))
    else:
        raise ValueError("alternative must be one of ['two-sided', 'one-sided']")
    
    # Estimated probability of success, which is the sample statistic, the midpoint of Confidence Interval
    p_hat = X / N

    # If population probability is unknown, the program will use estimated probability for calculation.
    if p == 0:
        p = p_hat

    # When not proper for normal distribution assumption
    if N * p <= 5 or N * (1 - p) <= 5:
        return False

    # Standard Error of Two-tailed test
    SE = math.sqrt(p * (1 - p) / N)

    # Margin of Error
    m = z * SE

    # Left & Right boundary of Confidence Interval
    CI_left, CI_right = p - m, p + m

    return np.round(m, round), np.round(CI_left, round), np.round(CI_right, round), np.round(p_hat, round), CI_left <= p_hat <= CI_right
```