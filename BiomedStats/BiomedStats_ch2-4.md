
## Chapter 2 统计描述

 ### 2.1 Quantity Analysis
 - **Frequency Table & Histogram** 
	 -- 描述样本分布 (v.s. 总体分布 -- distribution)
	-- 3 types of y axis: frequency, relative frequency(~probability?), density.
	-- ***Use:***
	 1. find distributions: normal, bimodal, right-skewed, left-skewed, random distribution
	 2. Attribute of frequency distribution
	 3. Find extreme value
	 4. For furture statistics

- **Descriptive Indicators**
	- **Central tendency**: Mean, Geometric Mean, Median, Percentile.
	-- Mean only suitable for symmetric distribution, especially normal distributions. 
	-- Geometric Mean for data becomes symmetric after logrithm transformation.
	-- Median for various distribution, especially for skewed and open-ended distribution.
	-- Median and Percentile shows ranking position info

	- **Divergence tendency**: Range, Inter-quartile range, Variance/Mean square deviation, Coefficient of variation.
	-- *Coefficient of variation = std / mean*, use for different scales, distributions with different means. 

	- **Distribution tendency**: Skewness (=$\frac{m_3}{S^3},$, third standardized central moment $\tilde{\mu}_3$), Kurtosis (=$\frac{m_4}{S^4}$, fourth standardized central moment) 

### 2.2 Quality Analysis
- Variable types
	- 常量
	- 变量：
	-- 定量变量：连续 or 离散
	-- 定性变量 (二项or多项分类)：有序变量 or 名义变量
- Relative indicator相对数指标
	- Frequency (numerator is part of the denominator)
	- Intensity (related to the time)
	- Relative Ratio (ratio of any two related values, should be mutually inclusive)

	- ***Note:***
	-- Denominator shouldn't be too small (e.g. <20), usually 100 as base
	-- 观察单位数不等的相对数需要将分子分母合并之后再相除求平均值
	-- 求合计率的时候必须采用"标准化率"的方法
		>甲乙医院病人数不同 治愈率求法的例子 标准病人数=两医院各型人数之和
		
		-- Not all % are quality 
		-- 注意可比性、均衡性，差别必须经过假设检验才可以下结论

### 2.3 Making statistical table and chart

## Chapter 3 概率分布 
### 3.1 Normal Distribution
- $f(X) = \frac{1}{\sigma \sqrt{2\pi}}^{\frac{-(X-\mu)^2}{2\sigma^2}}$
	-- $\mu$ - position parameter, $\sigma$ - shape parameter
	-- $\mu \pm 1.96\sigma$ - 95%
	-- $\mu \pm 2.58\sigma$ - 99%
	-- $X_1 \pm X_2  \sim (\mu_1\pm\mu_2, \sigma_1^2 \pm \sigma_2^2)$
- PDF: $f(z) = \frac{1}{\sqrt{2\pi}}^{-\frac{z^2}{2}}$
- CDF: $\Phi(z) = \frac{1}{\sqrt{2\pi}}\int^z_{-\infty}e^{-\frac{t^2}{2}}dt$
- ***Use:***
	- **Making reference value in medical - 95%**: 
	-- 百分位数法 (any distribution): 双侧(P2.5, P97.5) 如白细胞数，血红蛋白数；单侧(>P5 or <P95) 如肺活量，血铅，血清转氨酶
	-- 正态分布法: $\overline{X} \pm 1.96S$
	- 质量控制：随机误差（正态）v.s. 系统误差
 
### 3.2 Binomial Distribution
- $X \sim B(n, \pi)$
	-- $\mu = n\pi$
	-- $\sigma^2 = n\pi(1-\pi)$
	-- Independent, 2 possible outcomes, fixed probability
- PDF: $P_{(X=k)} = C^k_n \pi^k (1-\pi)^{n-k}$
- Binomial is Bernolli distribution when$$n=1, X = \begin{cases}
      1 & \text{with probability } \pi\\
      0 & \text{with probability } 1-\pi \end{cases} $$
- Central Theorm: when $n$ is large, $n\pi$ and $n(1-\pi) > 5$, binomial distribution is close to normal distribution. It becomes normal distribution when $n \rightarrow\infty$ 
- ***Use:***
e.g. 细胞膜上单个离子通道电流的检测——噪声分析法

### 3.3 Poisson Distribution
- $X \sim \Pi (\mu)$
	-- $\mu=\sigma^2$ 
	-- $X_1 + X_2 \sim \Pi(\mu_1+\mu_2)$
- $P_{(X=k)} = \frac{\mu^k}{k!}e^{-\mu}$
- Poisson is a special case of bionomial distribution, with large $n$, $\pi$ close to 0 or 1 (e.g. <0.001 or >0.999)
	 -- e.g. $X\sim B(100, 0.01) => X\sim\Pi(1)$ 
- When $\mu$ is small, poisson is a skewed distribution, and it become more symmetric when $\mu$ increase. When $\mu\geq20$ it can be seen as a normal distribution.
- ***Use:*** 
	- 描述单位时间、平面或单位空间中的罕见“质点”总数的随机分布规律。"Discrete probability distribution that expresses the probability of a given number of events occurring in a *fixed interval of time or space (or distance, area of volume)* if these events occur with a known constant mean rate and independently of the time since the last event. 
	- 如血细胞或微生物在显微镜下的计数，人群中患病率很低的非传染性疾病的患病数。
	- "Law of rare events"

## *From Sample infer Population distribution (I)* 
## Chapter 4 参数估计
### 4.1 Sampling Distribution & Standard Error
- **Sample Mean** $\overline{X}$ V.S. mean of poplulation $X$
-- If $X \sim N(\mu, \sigma^2)$ —— $\overline{X} \sim N(\mu, \sigma^2/n)$
-- If $X \nsim N$ —— $\overline{X}$ approx *normal* when sample size $n$ is large (central limit theorem), else $\overline{X}$ is not normal

- **Standard Error (SE)** is the standard deviation of sampling distribution/sample statistics, $\sigma_{\overline{X}} = \frac{\sigma}{\sqrt{n}}$
-- Left skewed distribution, Skewness is large when $n$ is small 
-- Become more symmetric as the sample size $n$ increase
-- If $X$ is normal, its sample variance $S^2$ follows a $\chi^2$ distribution with $n-1$ degrees of freedom
-- Approx: $\sigma_{S^2} = \frac{S}{\sqrt{2n}}$
-- ways to decrease SE: increase $n$, reduce $\sigma$ by design


- **Difference of two sample means** $\overline{X}_1 - \overline{X}_2$ from $X_1,  X_2$
-- $\mu_{\overline{X}_1 - \overline{X}_2} = \mu_1 - \mu_2$
-- If population variance is known ($X_1\sim N(\mu_1, \sigma_1), X_2\sim N(\mu_2, \sigma_2)$) :  $\sigma_{\overline{X}_1 - \overline{X}_2} = \sqrt{\frac{\sigma_1^2}{n_1} + \frac{\sigma_2^2}{n_2}}$
-- If population variance is unknown and $\sigma^2_1 \neq \sigma^2_2$ : $S_{\overline{X}_1 - \overline{X}_2} = \sqrt{\frac{S_1^2}{n_1} + \frac{S_2^2}{n_2}}$ 
-- If population variance is unknown and $\sigma^2_1 = \sigma^2_2 = \sigma^2_c$ : $S_{\overline{X}_1 - \overline{X}_2} = \sqrt{\frac{S_c^2}{n_1} + \frac{S_c^2}{n_2}} = S_c\sqrt{\frac{1}{n_1} + \frac{1}{n_2}}$

- **Sample Frequency** $p = \frac{X}{n}$ (Binomial distribution) 
-- sample mean ( $\mu_p = \pi$ ) V.S. population mean ( $\mu=n\pi$ )
-- sample SE ( $\sigma_p = \sqrt{\frac{\pi(1-\pi)}{n}}$ ) V.S. population STD ( $\sigma = \sqrt{n\pi(1-\pi)}$ )
-- sample SE when $\pi$ is unknow: $S_p = \sqrt{\frac{p(1-p)}{n}}$
-- when $p$ is not close to 0 and $n$ is big enough, sample distribution of $p$ is close to *normal distribution*

- **Difference of two sample freqencies** $p_1 - p_2$
-- $\mu_{p_1-p_2} = \pi_1 - \pi_2$
-- When $\pi_1 \neq \pi_2$ : $\sigma^2_{p_1-p_2} = \frac{\pi_1(1-\pi_1)}{n_1} +  \frac{\pi_2(1-\pi_2)}{n_2}$
-- When $\pi_1 \neq \pi_2$ and $\pi$ is unknown: $S_{p_1-p_2} = \sqrt{\frac{\pi_1(1-\pi_1)}{n_1} +  \frac{\pi_2(1-\pi_2)}{n_2}}$
-- When $\pi_1 = \pi_2 = \pi_c$ : $\sigma^2_{p_1-p_2} = \pi_c(1-\pi_c)(\frac{1}{n_1} + \frac{1}{n_2}), p_c = \frac{X_1+X_2}{n_1+n_2}$
-- When  $\pi_1 = \pi_2 = \pi_c$ and $\pi$ is unknown, $S_{p_1-p_2} = \sqrt{\pi_c(1-\pi_c)(\frac{1}{n_1} + \frac{1}{n_2})}$

### 4.2 $Z$ distribution & $t$ distribution
- Apply $Z$ transform on a random variable $X\sim N(\mu, \sigma^2)$:
-- $Z \sim N(0, 1)$ where $Z=\frac{X-\mu}{\sigma}$
-- When $\overline{X}$ is sample mean, $\frac{\overline{X}-\mu}{\sigma/\sqrt{n}} \sim Z$ distribution
-- $z$ score
-- When population std is unknown (use sample std $S$ instead), the following becomes **$\bm{t}$ distribution**: 
- $\frac{\overline{X}-\mu}{S/\sqrt{n}} \sim t(v)$
-- $v=n-1, S=\sqrt{\frac{\sum(X-\overline{X})^2}{n-1}}$
-- unimodal distribution, zero centered, symmetric
-- the smaller the $v$, the more sparse the $t$ (the lower the peak)
-- when degree of freedom $v \rightarrow\infty$, $t$ is close to nromal distribution $S_{\overline{X}}\rightarrow\sigma_{\overline{X}}$, so normal distribution is a special case of $t$ distribution 
-- $f(t)=\frac{\Gamma[(v+1)/2]}{\sqrt{\pi v}\Gamma(v/2)}(1+\frac{t^2}{v})^{\frac{-(v+1)}{2}}$

### 4.3 Parameter Estimation
- **Point Estimation** is a single value estimate of a parameter. 
-- a sample mean is a point estimate of a population mean



- **Interval Estimation** gives you a range of values where the parameter is expected to lie. **Confidence interval (CI)** is the most common type of interval estimation.
	- CI of population mean
-- **Z (Normal) distribution** : $\overline{X} \pm Z_{\alpha/2}\sigma_{\overline{X}}$
-- **t distribution** (when $\sigma$ is unknown and $n$ is small) : $\overline{X} \pm t_{\alpha/2, v}S_{\overline{X}}, v=n-1$ 
	- CI of population mean difference 
	--$\mu_1-\mu_2$: $(\overline{X_1} - \overline{X_2}) \pm t_{\alpha/2, v}S_{\overline{X}_1 - \overline{X}_2}$
	-- $S_{\overline{X}_1 - \overline{X}_2} = \sqrt{\frac{S_1^2}{n_1} + \frac{S_2^2}{n_2}}$ 
	-- $v = \frac{(S_1^2/n_1 + S_2^2/n_2)^2}{\frac{(S_1^2/n_1)^2}{n_1-1} + \frac{(S_2^2/n_2)^2}{n_2-1}}$, $v = n_1+n_2-2$ when $\sigma_1^2=\sigma_2^2$
	- CI of population probability
	-- **normal distribution approximation** (when $n>50, np>5, n(1-p)>5$) : $p \pm Z_{\alpha/2}S_p$, where $S_p = \sqrt{\frac{p(1-p)}{n}}$ 
	- CI of population probabiliity difference
	-- normal distribution approximation $(p_1 - p_2) \pm Z_{\alpha/2}S_{p_1-p_2}$
	--  $S_{p_1-p_2} = \sqrt{\frac{p_1(1-p_1)}{n_1} + \frac{p_2(1-p_2)}{n_2}}$
	- CI of population variance
	-- **$\bm{\chi^2}$ distribution**: suppose $X$ follow normal distribuion, then its sample variance $S^2$ follow $\chi^2_{(n-1)}$ distribution: $\frac{n-1}{\sigma^2}S^2 \sim \chi^2_{(n-1)}$
	-- For a given Confidence level ($1-\alpha$): $P\{\frac{(n-1)S^2}{\chi^2_{\alpha/2, (n-1)}} < \sigma^2 <\frac{(n-1)S^2}{\chi^2_{1-\alpha/2, (n-1)}}\}$
- The Accuracy and Precision of CI（准确度与精确度）
-- The bigger the confidence level ($1-\alpha$), the higher the accuracy, but not necessarily useful/good
-- Precision is half width of CI, the shorter the better
-- when SE is the same, accuracy and precision are contradictory
-- Increase sample size $n$ can reduce SE, therefore increase the precision of parameter estimation
