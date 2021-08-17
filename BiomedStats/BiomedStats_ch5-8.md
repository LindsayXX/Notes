## *From Sample infer Population distribution (II)* 
## Chapter 5 假设检验
```
Z test
t test
```
### 5.1 Hypothesis (Significant) Test 
- The goal is to identify the reason that causes the difference of data: difference in the nature of data or due to sampling error
- *Parameter test* and *Non-parameter test*
- **Proof by contradiction** 反证法
	- Step 1: 
Null Hypothesis $H_0: \mu=\mu_0$;                  
Alternative Hypothesis $H_1:\mu \neq \mu_0 (\text{or } \mu >\mu_0, \mu<\mu_0)$
	- Step 2: pick test method, compute statstical value and it's p value
	- Step 3: make conclusion: Reject $H_0$ if $p\leq\alpha$;  Not reject $H_0$ if $p>\alpha$
- 根据小概率事件在一次随机试验中不大可能发生的推断原理
- Two-tailed test is more conservative, for inquiry based study, one-tailed test is more for confirmative study
- *Note:* 
-- Not reject $H_0$ does not mean accept $H_0$
-- When draw a conclusion, we can only say reject/not reject on $H_0$, accept/not accept on $H_1$
-- We are interested in $H_1$, and testing $H_0$
### 5.2 One sample hypothesis testing
#### 5.2.1 One sample test of Means
-- Z distribution: known population std $\sigma, Z=\frac{\overline{x}-\mu_0}{\sigma/\sqrt{n}}, v=n-1$
-- t distribution: unknown population std, replace with sample $S, t = \frac{\overline{x}-\mu_0}{S/\sqrt{n}}$ 
-- **t test** (student t test) usually used for small sample testing
-- **z test** when sample size is big (even if $\sigma$ is unknown or population is not a normal distribution)
#### 5.2.2 One sample test of Frequency ( $\chi^2$ Test)
- Direct compute of frequency $p$ : 
    $P(X\leq k)=\sum^k_{X=0}\frac{n!}{X!(n-X)!}\pi^x(1\pi)^{n-X}$
- **Z test** (when sample size is large and $n\pi n(1-\pi)>25$): 
$Z=\frac{p-\pi_0}{\sigma_p}=\frac{p-\pi_0}{\sqrt{\pi_0(1-\pi_0)/n}}$ 
- example

### 5.3 Two types of error
- **Type I Error** (rejection of a true null hypothesis, a.k.a. "false positive")弃真
-- P value $\leq\alpha$ 
-- Probablity of error $\leq \alpha$
-- (rejecting $H_0$ can only make type I error)
- **Type II Error** (non-rejection of a false null hypothesis, a.k.a. "false negative")纳伪 
-- P value $>\alpha$
-- Probablity of error $\leq \beta$
--  (not rejecting $H_0$ can only make type II error)
- $\alpha$ and $\beta$ are correlated, $\alpha\uparrow, \beta\downarrow$ 
- **Test effect** (Rejection of a false null hypothesis 
$H_0$, a.k.a. "true positive")
-- Probability $< 1 - \beta$, the bigger the better!
-- The bigger the difference of populations, the larger the test effect
-- The smaller the SE (std), the larger the test effect
-- The larger the sample size $n$, the larger the test effect
--The higher the significance level $\alpha$, the larger the test effect
- ***Note:***
-- If the point is to reduce type I error $\alpha$, let $\alpha=0.05 / 0.01$. E.g. normal hypothesis test
-- If the point is to reduce type II error $\beta$, let $\alpha = 0.10/0.20$ (or higher). E.g. Homogeneity of variance test, Normality test
-- "Hypothesis tests under significance level $\alpha$" = " Interval estimation under confidence interval $\alpha$"

## Chapter 6 两样本定量资料的比较
```
Parametric tests:
	One sample hypothesis test:
		t test (small sample, Ch5)
		Z test (large sample, frequency, Ch5)
    Independent two samples/groups:
	    F test (variance equal)
	    t test (mean equal for equal variance)
	    t' test (mean equal for unequal variance)
		Normality tests
		Z test (Poisson distribution)
    Paired two groups:
		Paired t test (mean equal)
	Multi-group:
		Analysis of Variance/ANOVA (Ch7)
Non-parametric tests:
	Rank Sum test
	Wilcoxon signed-rank test (Paired)
	Kruskal-Wallis test (Ch7)
```
### 6.1 Two independent samples comparison (completely randomized design)
#### 6.1.1 Compare if their population mean are equal ---Parametric test
- population variance is the same ------ **t test**
	 1. Hypothesis: $H_0$: population mean $\mu_1=\mu_2$, $H_1: \mu_1\neq\mu_2, \alpha=0.05$
	2. $t = \frac{\overline{X_1} -\overline{X_2}}{\sqrt{S^2_c(\frac{1}{n_1}+\frac{1}{n_2})}}, v = n_1+n_2-2$ (refer to Chapter 4)
- population variance is not the same ----- **corrected t' test**
	1. Hypo:  $H_0$: population mean $\mu_1=\mu_2$, $H_1: \mu_1\neq\mu_2, \alpha=0.05$
	2. $t' = \frac{\overline{X_1} -\overline{X_2}}{\sqrt{\frac{S_1^2}{n_1}+\frac{S^2_2}{n_2}}}, v = \frac{(S_1^2/n_1+S_2^2/n_2)^2}{\frac{(S_1^2 / n_1)^2}{n_1-1} + \frac{(S_2^2 / n_2)^2}{n_2-1}}$
- *Requirements*:
-- choose different testing method according to if two population variance are equal
-- small sample require data(sample mean) following normal distribution 
-- lead to Homogeneity of variance test & Normality test
#### 6.1.2 Homogeneity of variances test / F-test of equality of variances
1. Hypo: $H_0: \sigma_1 =\sigma_2, \alpha=0.10$ (to reduce false negative/type II error)
2. $F = \frac{S_1^2/\sigma^2_1}{S_2^2/\sigma^2_2} \Rightarrow F =\frac{S_1^2}{S_2^2}$, $F\approx1$ when variance is equal
3. lookup P value/F value table: $P_{F, (v_1, v_2)}$ or $F_{\alpha, (v_1, v_2)}$
#### 6.1.3 Normality test
- *Method of moments*: 
Skewness $=\frac{m_3}{S^3} = 0$, Kurtosis $=\frac{m_4}{S^4} = 3/0$, where $m_n = \frac{\sum^N_{i=1}(x_i -\overline{x})^n}{(N-1)}$
	1. Hypo $H_0: \kappa=0$ and $s=3$, $H_0: \kappa\neq0$ or $s\neq3$
	2. **Jarque-Bera test**: $JB=\frac{n}{6}[s^2+\frac{(k-3)^2}{4}]$ (Matlab: [h, p] = jbtest(x, alpha))
	3. When h=1, reject $H_0$ under $\alpha$
	    $JB\sim 0$ when normal
	
- **Shapiro-Wilk test / W Test** (when $3\leq n\leq50$)
	1. Rank the sample from small to large $x_1\leq x_2\leq ...\leq x_n$
	2. Compute W, $W=\frac{\{\sum_{i=1}^{n/2}a_i[x_{(n+1-i)}-x_i]\}^2}{\sum_{i=1}^{n}(x_i - \overline{x})^2}$
	3. $w\sim 1$ when normal
	4. loop up $a_i$ and $W$ table

- **Kolmogorov-Smirnov test  / KS test**
	1. Hypo: $H_0$: the population sample comes from follows the certain distribution; $H_1$: the population sample comes from does NOT follow the certain distribution
	2. H = max$(\mid F_n(x)-G(x)\mid)$ where $F_n(x)$ is experience distribution function and $G(x)$ is theoritical distribution function (Matlab: [h, p]=kstest(X, cdf, alpha))

- **Q-Q plot**
-- X axis: sample ranked from small to large $x_1\leq x_2\leq ...\leq x_n$
-- y axis: (standard) normal distribution expectation
-- See if it is linear 
#### 6.1.4 Rank sum test --- non-parametric test
>- Not affected by the population parameter
>- testing the distribution instead of parameter
>- can be used on any distribution
>- low test effect, so choose parametric test when data (approx) fit the requirement of parameter test
1. Mix two groups of samples, rank from low to high.
2. Compute rank sum of two groups, $T_1, T_2$
3. small sample ($n_1\leq 10$ and $(n_1-n_2)\leq 10$)
4. large sample, apporximate normal, $Z = \frac{\mid T_1-n_1(n_1+n_2+1)/2\mid-0.5}{\sqrt{n_1n_2(n_1+n_2+1)/12}}$ (0.5 is correction for continuity)
5. Need correction when tie > 25%: $Z_c = Z / \sqrt{c}$,  where $c=1-\sum\frac{t_j^3-t_j}{N^3-N}$ ($t_j$ is the number of No.$j$ ties)
### 6.2 Paired design quantitive data Comparison (of mean)
- Paired Design
> -- smaller sampling error
> -- higher efficiency
> -- need smaller sample size

- comparing two mean == comparing the diff of means with 0
	- If the difference is normal --- use **paired t test**
	-- $H_0: \mu_d=0, H_1: \mu_d \neq 0, \alpha=0.05$
	-- $t = \frac{\overline{d}}{S_d /\sqrt{n}}, v=n-1$
	-- CI of mean diff: $\overline{d} \pm t_{\alpha/2, v}S_{\overline{d}}$
	- Else, use **Wilcoxon signed-rank test**
	-- $H_0:$ *medium* of diff is 0, $H_1:$ medium of diff is not 0
	-- Rank the ***absolute*** value from low to high (***exclude zeros***), then add their original sign. Compute rank sum of positive and negative groups T+, T-, let T = min(T+, T-).
	-- When sample size is small, check T table according to n.
	-- When sample size is large, $T\sim N(\mu_T, \sigma_T^2)$. Use *Z test* --- $Z = \frac{T-\mu_T}{\sigma_T}$, where $\mu_T = n(n+1)/4, \sigma^2_T = n(n+1)(2n+1)/24$

### 6.3 Comparison of two groups of Poisson distribution data (mean/sigma^2, when approximate to normal distribution)
>- When sum of observation of each group $X_1, X_2$ is greater than 20, it approximate to Normal distribution
>- Use Z test
>--
>
- $H_0:$ two Poisson distributions have same mean,  $H_1:$  two Poisson distributions have different mean.
- When unit is the same: use ***sum*** of observation for **Z test**
	-- For two normal distribution $X_1\sim N(\mu_1, \mu_1), X_2\sim N(\mu_2, \mu_2)$
	-- $X_1-X_2\sim N(0, \mu_1+\mu_2)$, use Z test
	-- $\mu_1, \mu_2$ is unknown, use $X_1, X_2$ for estimation: $Z = \frac{X_1-X_2}{\sqrt{X_1+X_2}}$
- When unit is not the same: use ***average*** of observation for **Z test**
-- $\overline{X}_1\sim N(\frac{\mu_1}{n_1}, \frac{\mu_1}{n_1}), \overline{X}_2\sim N(\frac{\mu_2}{n_2}, \frac{\mu_2}{n_2})$ 
-- $\overline{X}_1 - \overline{X}_2 \sim N(0, \frac{\mu_1}{n_1}+\frac{\mu_2}{n_2})$
-- $\mu_1, \mu_2$ is unknown, use $\overline{X}_1, \overline{X}_2$ for estimation: $Z = \frac{\overline{X}_1-\overline{X}_2}{\sqrt{\frac{\overline{X}_1}{n_1} + \frac{\overline{X}_2}{n_2}}}$

## Chapter 7 多组定量资料的比较
```
When comparing two and more independent samples:
1) first consider using ANOVA 
   -- normality and variance equality check
2) If variance is equal and all samples follow normal distribution 
   -- use ANOVA
3) If variance is not equal or some samples do not follow normal distribution 
   -- try data transformation to let it meet the requirement of ANOVA
4) If still cannot meet the requirement of ANOVA, 
   -- use Kruskal-Wallis Rank-Sum test
5) If the result of ANOVA or Rank-Sum is statstically significant, then 
   -- choose a proper method for pair-wise comparison (Bonferonni, LSD, Normal Distribution Method)
```
### 7.1 Use ANOVA(F-test) instead of t-test for analysis differences among mean of multi-group
**Why t-test does not fit comparison of multi-group mean?**
- multiple comparison: k group need k(k-1)/2 tests, too tedious
- without a unified sample error estimation ($t =\frac{\overline{X_i}-\overline{X_j}}{S_{\overline{X_i}-\overline{X_j}}}$), the accuracy of error estimation and sensitivity of testing will be affected
- low reliability, chance of making type I error increase: 4 groups need 6 tests, probablity of having type I error becomes $1 - 0.95^6= 0.265 >> 0.05$

****Analysis of variance (**ANOVA**), i.e. **F-test**:****
- Based on law of total variance, the observed variance in a paritcular variable is partitioned components attributable to different sources of variation. i.e., Total variation = Between group variation + Within Group variation
- Commonly used normal linear models for a completely randomized experiment: 
-- $y_ {ij} = \mu_j + \epsilon_{ij} = \mu + \tau_j+\epsilon_{ij}$ 
-- where $\tau_j$ is the jth treatment effect, a deviation from the grand mean
- Partitioning of sum of squares **SS** (of deviations from mean): $SS_{Total} = SS_{Treatments} + SS_{Error}$
- Partitioning of degree of freedom **DF**: $DF_{Total} = DF_{Treatments} + DF_{Error}$, i.e. $(N-1) = (g-1) + (N-g)$
- Use **mean square (MS)** for comparison of variations:
	  - $MS_{Error} = \frac{SS_{Error}}{v_{Error}}$: random error 
	  -  $MS_{Treatments} = \frac{SS_{Treatments}}{v_{Treatments}}$: random error + treatment factor
	  - where $S^2 = \frac{\sum (x-\overline{x})^2}{n-1}$
- 3 types:
	-  One-way ANOVA
	- Two-way ANOVA
	- N-way ANOVA
- 3 types of variations (for one-way ANOVA): 
	- $X_{ij} - \overline{X}$
	-  $\overline{X_i} - \overline{X}$
	- $X_{ij} - \overline{X_i}$
	- $(X_{ij} - \overline{X}) = (X_{ij} - \overline{X_i}) + (\overline{X_i} - \overline{X})$
	- $\sum^g_{i}\sum^{n_i}_{j}(X_{ij} - \overline{X})^2  = \sum^g_{i}\sum^{n_i}_{j} (X_{ij} - \overline{X_i})^2 +\sum^g_{i} n_i (\overline{X_i} - \overline{X})^2$	
- **Requirements/Assumptions:**
	- Independence: all samples are mutally independent random variable
	- Normality: all samples comes from normal distribution 
	- Equality/Homogeneity: *Variances of populations are equal* -  prerequisition of within-group analysis of variance
	  -- two sample: F-test (Ch6.1.2)
	  -- multi-sample: Bartlett and Levene test (Ch 7.5)
	-  If data are ordinal, a non-parametric alternative Kruskal–Wallis one-way analysis of variance should be used (Ch7.6). If the variances are not known to be equal, a generalization of 2-sample Welch's t-test can be used.
### 7.2 Experiment Design
Three key elements:
- Object
- Treatment/Factor
	- Single-factor single-level
	- Single-factor multi-level
	- Multi-factor single-level
	- Multi-factor multi-level
- Experimental Effect
	- fixed-effect models
	- random-effects models
	- mixed-effects models
	
3 types of experiment design for one-way ANOVA
-  Completely randomized experiments with a single factor
-- when g=2, ANOVA = t-test and $t = \sqrt{F}$ 
- Completely randomized blocks: extension of paried design 
- Latin Square

### 7.3 One-way ANOVA
 Completely randomized experiment: one-factor g-level (the simplest)
- H0: $\mu_1=\mu_2=\mu_3$
   H1: are not all equal/at lease one pair of population mean are not equal (which means the treatment has impact)
- Testing $F=\frac{MS_{\text{Treatments}}}{MS_{\text{Error}}}$, $v_{\text{Treatments}} = g-1, v_{\text{Error}} = N-g$
   where $F = \frac{SS_{\text{Treatments}}v_{\text{Error}}}{SS_{\text{Error}}v_{\text{Treatments}}} = \frac{(N-g) \sum^g_in_i(\overline{X}_i - \overline{X})^2}{(g-1) \sum^g_i\sum^{n_i}_j(X_{ij} - \overline{X}_i)^2}$
- The expected value of F is  $1+\frac{n\sigma^2_{\text{Treatments}}}{\sigma^2_{\text{Error}}}$ which is 1 for no treatment effect.
-- If $F\approx 1$. not reject H0. 
-- Else if $F$ is large, use F-table to get P-value (right-tail test) to further reject H0 (or not)

Completely randomized blocks / Paired: one-factor g-level + 1 non-treatment factor
- blocks
- $SS_{Total} = SS_{Treatments} + SS_{Blocks} + SS_{Error}$
- $v_{Total} = v_{Treatments} + v_{Blocks} + vF_{Error}$
- 
Latin Square experiments: one-factor g-level + 2 non-treatment factors
- $SS_{Blocks} = SS_{Row-wise} + SS_{Colume-wise}$ 
- 
### 7.4 Pair-wise comparison of multi-sample
- Least significant difference t-test (**LSD-t test**): essentially t-test with unified error
	- $|\overline{X}_A - \overline{X}_B| \geq  t_{\alpha/2, v} \sqrt{MS(\frac{1}{n_A} + \frac{1}{n_B})}$
	- 
		|       |  LSD-t test | t-test |
		|-----|----------------------|---------------|
		|std  | $\text{SE}_{LSD-t} = \frac{\mid\overline{X}_A - \overline{X}_B\mid}{S_{\overline{X}_A-\overline{X}_B}} = \frac{\mid\overline{X}_A-\overline{X}_B\mid}{\sqrt{MS_{\text{error}}(1/n_A + 1/n_B)}}$  | $\text{SE}_{t} =  \frac{\overline{X}_1 - \overline{X}_2}{\sqrt{S_c^2(1/n_1 + 1/n_2)}}$ | 
		|DF| $v = v_{\text{error}}=N-g$ | $v=n_1+n_2-2$ | 
		|MS| $MS_{\text{error}} = \frac{\sum^g_i\sum^{n_i}_j (X_{ij} - \overline{X_i})^2}{N-g}$ | $S_c^2 = \frac{(n_1-1)S_1^2 + (n_2-1)S_2^2}{n_1+n_2-2} = \frac{\sum(X_1 - \overline{X}_1)^2 +\sum(X_2 -\overline{X}_2)^2}{n_1+n_2-2}$ |
	- Simple and high sesentivity
	-- More times of comparisons lead to higher type I error probability
	-- Suitable for comparison between treatment groups and control group
	
- **Bonferroni test**: change testing level $\alpha\prime=\alpha / m$ when comparing $m$  times
	- $t = \frac{|\overline{X}_A - \overline{X}_B|}{MS(1/n_A + 1/n_B)}$
	- $v = v_{\text{error}} = N-g$
	- type I error probability $=1-(1-\alpha)^m \approx m\alpha$

- *Summary*:
	- both LSD and Bonferroni are **post-hoc** test, using $MS_{\text{error}}$ after ANOVA
	- LSD is more sensitive
	- sometimes when ANOVA test have significant difference but pair-wise does not

### 7.5 Equality of variances for a variable calculated for two or more groups
- Bartlett test

- **Levene's test**
	- Has no requirement on population distribution
	- transfer observation $X_{ij}$ to $Z_{ij} = |X_{ij} - \overline{X}_i |$, then do single-factor ANOVA
	- Test $F = \frac{(N-g)\sum^g_{i=1}n_i(\overline{Z}_i - \overline{Z})^2}{(g-1) \sum^g_i\sum^{n_i}_j(Z_{ij} - \overline{Z}_i)^2} = \frac{MS_{\text{treatment}}}{MS_{\text{error}}}$
	- DF: $v_1 = g-1, v_2 = N-g$
	- H0: all groups has equal variance; 
	  H1: all groups are not all equal.
	  $\alpha=0.10$
- Residual Plot -- for normality and variance equality
	- $e_{ij} = X_{ij} - \overline{X}_i$ 
	
### 7.6 Kruskal-Wallis test
When the sample data doesn't fit the prerequisit of ANOVA:
- ***Data transformation:***
	- Used for better normality or variance equality, so that the data can fit the prerequisit of ANOVA
	- Log-transformation ($X^\prime = ln(X + a)$): Logarithmic normal distribution (std $\propto \mu$)
	- square root transformation ($X^\prime = \sqrt{X}$): Poisson distribution (var $\propto \mu$)
	- arcsine transformation ($X^\prime = arcsin\sqrt{X}$): Binomial distribution （%） 
- **Kruskal-Wallis test by ranks (Kruskal-Wallis H test):** 
    - Non-parametric method for testing whether samples originate from the same distribution, for comparing two or more independent samples of equal or different sample sizes.
	- Hypothesis:
	  -- H0: all populations from all samples have same distribution / the medians of all groups are equal
	  -- H1: all populations from all samples do not have same distribution / at least one population median of one group is different from the population median of at least one other group
	- Rank, sum
	   Mix all data from all groups together and rank then from low to high. Assign any tied values the avergae of the ranks. (no need if they are from same sample group?)
	- Computing H
	   $H = \frac{12}{N(N+1)}\sum^g_{i=1} n_i(\overline{R}_i - \overline{R})^2 = \frac{12}{N(N+1)}\sum^g_{i=1}\frac{R_i^2}{n_i}-3(N+1)$, where $R_i$ is the rank sum of sample $i$, $N= \sum n_i$. 
	- Inferring
	  -- When sample size is small, compare with $H_c$ from exact probablity H table (reject H0 if $H > H_c$);
	  -- When sample sizeis big, H approximate $\chi^2_{\alpha, (g-1)}$
	  -- When there are a large number of ties, use corrected H $H_c = \frac{H}{[1-\frac{\sum^G(t_j^3-t_j)}{n^3-n}]}$, where $G$ is the number of groupings of different tied ranks and $t_j$ is the number of tied values within group $j$ that are tied at a particular value.

- Post-hoc: Normal approximation -- Z test
	- $Z_{ij} = \frac{\mid \overline{R}_i - \overline{R}_j \mid}{\sigma_{\overline{R}_i - \overline{R}_j}}$, $\sigma_{\overline{R}_i - \overline{R}_j} = \sqrt{\frac{N(N+1)}{12}(1/n_i+1/n_j)}$
	- $\alpha^\prime = \alpha /m$

## Chapter 8 定性资料的比较
```
Recap:
- 3 relative indicators for quality data: Frequency, Intensity, Relative ratio
- Z test for one sample frequency test (Binomial or Normal) (Ch4)
- Z test for comparing difference of two samples' frequency (Ch5)
Chi-Square test: 名义资料
Rank-sum test: 有序资料
```
### 8.1 Chi-Square test (名义资料）
- Null hypo H0: sample data follow certain known distribution
  Alternative hypo H1: sample data does not follow the known distribution
- Basic form: $\chi^2 = \sum^k_i\frac{(A_i -T_i)^2}{T_i}$ -- compute the error between **Actual Frequency** and **Theoritical Frequency**.
- Essentially a ***goodness of fit test***
### 8.2 Two groups of binary data (2x2 table)
#### 8.2.1 Independent data (completely random)
2x2 / 4-grid table:
|Group| happen | unhappen | sum
|--|--|---|--|
| A | a | b | a+b |
| B | c | d | c+d |
|sum|a+c|b+d| n |
- **H0: $\pi_1 = \pi_2 = \pi$, H1: $\pi_1 \neq \pi_2$, $\alpha=0.05$**
- Computing $\chi^2$:
	- Theoritical frequency: happening - $p= \frac{a+c}{n}$, not happening - $1-p= 1- \frac{a+c}{n} = \frac{b+d}{n}$,
	- **Theoritical frequency: $T_{RC} = \frac{n_R n_C}{n}$** 
e.g. $T_a = \frac{(a+b)(a+c)}{n}$, $T_b = \frac{(a+b)(b+d)}{n}$, $T_c = \frac{(c+d)(a+c)}{n}$, $T_d = \frac{(c+d)(b+d)}{n}$  
	- $\chi^2=\sum^k_{i=1}\frac{(A_i -T_i)^2}{T_i} = \frac{(ad-bc)^2n}{(a+b)(a+c)(b+d)(c+d)}$, $v=(2-1)(2-1)=1$
- Compare with the P value in exact probablity $\chi^2$ table, and draw conclusion
- ***Note*:** 
	- DF of $\chi^2$  != sample size 
	- $n\geq 40 & T \geq 5$ or $v\geq 2$, no need for correction
	- $n\geq 40 & 1< T < 5$ and $v=1$ use **Correction for continuity**: $\chi^2_c=\sum\frac{(\mid A-T\mid|-0.5)^2}{T} = \frac{(\mid ad-bc\mid - n/2)^2n}{(a+b)(a+c)(b+d)(c+d)}$
	- $n< 40$ & $T < 1$ or $P\approx\alpha$, compute frequency directly (**Exact frequency method**) instead of using $\chi^2$ test:
		- H0:  $\pi_1=\pi_2$, H1:  $\pi_1 \neq \pi_2$, $\alpha=0.05$
		- compute probability of all the combinations: $p_i = \frac{(a+b)!(c+d)!(a+c)!(b+d)!}{a!b!c!d!n!}$
		- Compute $P$: sum of all possible $p_i$ that is smaller or equal to sample observation probability $p_{\text{obs}}$
		- Reject H0 and accept H1 under $\alpha$ if $P\leq\alpha$ 
- e.g. 两种药物治疗老年期抑郁症的疗效， 比较随机分为2组的病情相近的患者的有效率是否有统计学意义

#### 8.2.2 Paired data -- **McNemar test:**
- Compare position $b$ and $c$ in the (4-grid) table: when H0 is true, the theoritical frequency is $\frac{b+c}{2}$
- $\Rightarrow \chi^2 =\frac{(b-\frac{b+c}{2})^2}{\frac{b+c}{2}} + \frac{(c-\frac{b+c}{2})^2}{\frac{b+c}{2}} = \frac{(b-c)^2}{b+c}$
- When $b+c <40$: correction for continuity $\chi^2 = \frac{(|b-c| -1)^2}{b+c}$
- e.g. 比较两种采血方法检查乙型肝炎抗原的检查结果总体阳性率有无差异

### 8.3 Multiple independent groups of binary data (R*2 table)
- $\chi^2 =\sum^k_{i=1}\frac{(A_i - T_i)^2}{T_i} = n(\sum^R_i\sum^{C=2}_j\frac{A_{ij}^2}{n_i m_j} - 1)$
- $v = (R-1)(C-1) = R-1$
- First: Hypothesis H0, H1 -> compute $\chi^2$ -> exact probablity $\chi^2$ table -> conclusion
- If reject H0: need further pair-wise comparison -- **Bonferrroni test** with  corrected $\alpha^\prime$
	- Pair-wise comparison between all groups: $\alpha^\prime = \alpha / k$ or $\alpha^\prime = \alpha / (k+1)$ , where $k=R(R-1) / 2$
	- Pair-wise comparison between experiement(treatment) groups with control group: $\alpha^\prime = \frac{\alpha}{(R-1)}$ or $\alpha^\prime = \frac{\alpha}{2(R-1)}$ (single-side, more conservative)
- e.g. 比较3种磷霉素制剂治疗皮肤软组织感染所对应的总体痊愈率是否相同

### 8.4 Multiple independent groups of multi-class data 
#### 8.4.1 Unordered data:  R*C table
- $\chi^2 =\sum^k_{i=1}\frac{(A_i - T_i)^2}{T_i} = n(\sum^R_i\sum^C_j\frac{A_{ij}^2}{n_i m_j} - 1)$
- $v = (R-1)(C-1)$
- Notice that $T$ cannot be too small in any case of $\chi^2$ test (T>1), and the number of grids where T<5 shouldn't be greater than 1/5 of the total number of grids (RxC). Otherwise, there will be errors.
- How to deal with small $T$:	
	- Increase sample size
	- Delete rows/colums where T is too small according to domain knowledge, or merge neighbouring rows/columns. However, this will hurt the randomness of sample.
	- Use *Fisher Exact probability method* for two-side RxC table 
- e.g. 三个民族职业分布

#### 8.4.2 Ordered data: Rank-sum test
- using $\chi^2$ test on ordered data will weaken the effect of statistic test
- H0, H1, $\alpha$
- Rank all data
- Compute H: $H = \frac{12}{N(N+1)}\sum\frac{R^2_i}{n_i} - 3(N+1)$
   - where $n_i$ is the number of observed values of each group, $N=\sum n_i$, $R_i$ is the rank-sum of each group
   - When there are a large number of ties, use corrected H: $H_c = \frac{H}{c}$ where $c = 1-\sum(t_j^3 - t_j)(N^3 - N)$
 - Conclusion:
	 - R = 3 and $n_i \leq 5$: check exact probability H table
	 - R$\geq 3$ and $n_i > 5$: check exact probability $\chi^2$ table using $v=R-1$
- **Further pair-wise comparison when rejecting H0**
- e.g. 某疾病3个治疗组疗效比较
