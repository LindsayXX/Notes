## Chapter 9 关联性分析
```
Linear relatioship between two RANDOM variables:
- First Scatter plot: linear trend & abnormal points
- 关联性(Ch9):
  - Continuous variable -- linear correlation (simple correlation)
	- Normally distribution: Pearson correlation (coefficient)
	- Non-normality: Spearman Rank correlation (coefficient)
	* Require hypothesis test or compute confidence interval*
  - Categorical variable -- asscociation -- chi-square test
	* 2x2 cross classification
	* 2x2 paried data
	* multi-class data
- 依存性-Ch10
```
### 9.1 Correlation analysis between two continuous random variable
#### 9.1.1 Intro
- Two variables are all normalily distributed ?
- Types:
	- positive correlation
	- negative correlation
	- zero correlation
	- non-linear correlation
- Scatter plot -- the most direct way
- Correlation coefficient: populstion use $\rou\in[-1,1]$, sample use $r\in[-1,1]$ 
	- correlation coefficient
	- coefficient of product-moment correlation
	- Pearson correlation coefficient
- $r = \frac{\sum(X-\overline{X})(Y-\overline{Y})}{\sqrt{\sum(X-\overline{X})^2 \sum(Y-\overline{Y})^2}} = \frac{Cov}{s_xs_y}$
	- Numerator - covariance
	- Denominator - standard deviation
#### 9.1.2 Hypothesis Testing
- H0: $\rho=0$,  H1: $\rho\neq 0$
- Case 1: when two variables are all Normalily distributed
	- **t-test**: $t=\frac{r-0}{s_r}=\frac{r}{\sqrt{\frac{1-r^2}{n-2}}}$, $v=n-2$.
	- **Direct exact probablity $r$ value table** with DF $v= n-2$
	- *the linear relation not only depends on $r$ but also the sample size*
- Case 2: two variables are not normalily distributed / population distribution is unknown (open-ended) / raw data is represented by level 
	- **Spearman Rank Correlation**
		- Rank $X_i \rightarrow p_i, Y_i \rightarrow q_i$
		- $r_s = \frac{\sum(p_i - \overline{p})(q_i - \overline{q})}{\sqrt{\sum(p_i - \overline{p})^2\sum(q_i - \overline{q})^2}}$
		- $\begin{cases}
 \text{exact probablity } r_s \text{ table},  & n\leq 50  \\t=\frac{r_s-0}{s_r} = \frac{r_s}{\sqrt{(1-r_s^2)/(n-2)}}     & n > 50\end{cases}$
- Cofidence Interval
	- Sampling from the population of $\rho\neq0$, the correlation coefficient of sample is skewed distributed. 
	- Using log-transformation to make it normal distribution: $z=\frac{1}{2} ln\frac{1+r}{1-r} \Longleftrightarrow r=\frac{e^{2z}-1}{e^{2z}+1}$
	- $z \sim \mathcal{N}(0, 1)$, $S_z = \frac{1}{\sqrt{n-3}}$(SE)
	- CI of $z$: $z\pm Z_{\alpha/2}S_z = z\pm Z_{\alpha/2}\frac{1}{\sqrt{n-3}}$
- ***Note:***
	- The "correlation" inferred by scatter plot and hypothesis test is not necessarily real !
	- $r \rightarrow 0$ does not necessarily mean zero correlation -- not enough sample size or non-linearity?
	- Careful to use correlation when thre is abnormal values
	- Do not do correlation test if one of the variable is chosen manually
### 9.2 Correlation analysis between two categorical variables
#### 9.2.1 Association analysis of 2x2 cross-category (2 binary attributes)
>- Independency test  V.S. Frequency comparison in Ch8.2.1 
>- One sample 2 attributes V.S. Two (independent) samples same attribute
>- H0: two attributes are independent V.S. H0: $\pi_1=\pi_2$
- H0: two attributes are **independent**; H1: two attributes are **associated**
- Joint probablity if independent: $\pi_{ij} = \pi_{ri}\pi_{cj}$
- Frequency from probablity: $T_{ij} = n\pi_{ij} \approx n\frac{n_{ri} n_{cj}}{n\dot n} = \frac{n_{ri} n_{cj}}{n} \in [0, \sqrt{0.5}]$
- $\chi^2$ test: $\chi^2 =\sum\frac{(A-T)^2}{T}$
- If the association exists, compute correlation coefficient: $r = \sqrt{\frac{\chi^2}{\chi^2 + n}}$
- $\rho=0 \Leftrightarrow$ independency $\chi^2$ test of two attributes
- e.g. 随机调查居民，分析冠心病患者与否 与其行为类型(A or B)的关联性

#### 9.2.2 Association analysis of 2x2 paired data
>- (Association test) V.S. Frequency Comparison (McNemar test) in Ch8.2.2
>- Association of the results of two methods V.S. If the frequency(probablity) of two methods are the same
>- $\chi^2 =\sum\frac{(A-T)^2}{T}$ V.S. $\chi^2 =\frac{(b-c)^2}{b+c}$
- $r = \sqrt{\frac{\chi^2}{\chi^2+n}}$
- e.g. 两种影像学检查A和B的结果（阳性/阴性）的关联性

#### 9.2.3 Association test of multi-category data 
>$\chi^2$ test same as *goodness of fit test*(Ch8)
- $\chi^2 = n(\sum^R_i\sum^C_j\frac{A_{ij}^2}{n_i m_j} - 1)$, $v=(R-1)(C-1)$
- $r = \sqrt{\frac{\chi^2}{\chi^2+n}} \in [0, \sqrt{1 - \frac{1}{min(R, C)}}]$
- e.g. 3 个民族4种血型的分布情况：民族与血型有无关联



## Chapter 10 简单线性回归分析
```
Regression: require only Y is normaly distributed
vs. 
Asccocasion: require both X and Y normaly distributed
- Steps:
Scatter plot -> Estimate regression coefficient(b) and y-intercep(a) -> Regression function and draw the line -> Hypothesis test, coefficient of determination -> Application
- Simple linear regression only has one independent variable
- Usually use Least sum of squares to compute a and b
- The decomposition of total variance of Y (ANOVA) helps understanding the basic philosophy of simple LR
- Usual application: prediction and control
- If the dependency is non-linear, consider non-linear regression
```
### 10.1 Regression
- Classification:
	- #of Independent variable: simple regression and multiple linear regression(Ch11)
	- regression model: linear and other
	- attribute of X: type I (X is controled values) and type II (X is normally distributed random variable)
- Established by F. Galton
- Goal and attributes
### 10.2 Simple linear regression 
- Define
	- Population mean: $\mu_{Y|X} = \alpha + \beta X$
	- Sample individual observation: $Y_i = \alpha + \beta X_i +\epsilon$
	- $\Rightarrow$ Sample **point estimation** for population: $\hat{Y} = a + bX$
	   $a$ (y-intercept) and $b$ (slope/gradient) are estimations of $\alpha$ and $\beta$, respectively.
	- $\epsilon \sim N(0, \sigma^2)$   
- Parameter estimation -- **Least sum of squares** 
	- $\hat{Y} = a + bX$,  Residual (error): $Y -\hat{Y}$
	- Get $a$ and $b$ to minimize the residual square sum $SS_{RE}$, i.e. the square sum of the distance between observations $Y_i$ and regression line is minimum. 
	  $\min_{a, b}Q = \sum_i(Y_i - \hat{Y})^2 = \sum_i (Y_i - a - bX_i)^2 \\ \Rightarrow b = \frac{\sum(X - \overline{X})(Y-\overline{Y})}{\sum(X-\overline{X})^2}, a=\overline{Y} - b\overline{X}$
	 - the regression line must past through $(\overline{X}, \overline{Y})$
- Prerequisite of linear regression model: **LINE**
	- **L**inear
	- **I**ndependent
	- **N**ormal
	- **E**qual variance
		 
### 10.3 Hypothesis test of regression function
- **Hypo test 1:** Does regression model stand (is X contributing to Y)? => **ANOVA**
	- $Y - \overline{Y} = (\hat{Y} - \overline{Y}) + (Y - \hat{Y})$
	- $\sum(Y - \overline{Y})^2 = \sum(\hat{Y} - \overline{Y})^2 + \sum(Y - \hat{Y})^2$ 
	-- i.e. $SS_{\text{population}} = SS_{\text{regression}} + SS_{\text{residual}}$
	-- $v_{\text{population}} = v_{\text{regression}} + v_{\text{residual}} = 1 + (n-2) = n-1$
	- $\sum(\hat{Y} - \overline{Y})(Y - \hat{Y}) = 0$
	- If there is no regression relation, the total variance of Y is random error. Otherwise, the contribution of regression should be larger than random error. How to determine:
		- $F = \frac{SS_{\text{regression}} / v_{\text{regression}}}{SS_{\text{residual} }/ v_{\text{residual}}} = \frac{MS_{\text{regression}}}{MS_{\text{residual}}}$
		- $v_{\text{regression}}=1, v_{\text{residual}} = n-2$
		- $F \sim F(v_{\text{regression}}, v_{\text{residual}})$ 
- **Hypo test 2**: Is population regression coefficient $\beta=0$ ? => **t-test**
	- H0: $\beta=0$; H1: $\beta\neq0$; $\alpha = 0.05$
	- $t = \frac{b-0}{S_b}, v=n-2$
	 $S_b = \frac{S_{Y, X}}{\sqrt{\sum(X-\overline{X})^2}}$ -- SE of b, 
	$S_{Y, X} = \sqrt{\frac{SS_{\text{error}}}{n-2}} = \sqrt{\frac{\sum(Y_i - \hat{Y}_i)^2}{n-2}}$ -- STD of regression residual
	- CI: $b\pm t_{\alpha/2, v} S_b$
	- $b$ is affected by the unit of variables, so it cannot completely represent the relationship between Y and X => **Coefficient of determination** $R^2$
		 - $R^2 = \frac{SS_{\text{regression}}}{SS_{\text{total}}} \in (0,1)$
		 - $R^2$ is related to $r$: when X and Y are both random variables, $R^2 = r^2$ 
		 - The higher the more meaningful

### 10.4 Application
- Compute: tolerance interval of  individual Y , confidence interval of population mean $\mu_{Y|X}$
- For: Prediction, Quality control
- e.g. 车流量$X_i$时对空气中NO气体的含量估计：空气中NO的95%容许区间，空气中NO总体均数的95%置信区间

## Chapter 11 多重线性回归分析
```
```
### 11.1 Multiple linear regression

### 11.2

