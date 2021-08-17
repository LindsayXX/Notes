
### 1. Before a test
- Not every idea is worth testing - qualitative analysis
- Some are hard to test, e.g. new experiencee, long-term effect
	- Analyze the user activity logs
	- Conduct retrospective analysis
	- Conduct user experience research
	- Focus groups and surveys
	- Human evaluation
### 2. Designing A/B tests
- Sample size of a test
	- Type II error rate $\beta$ or Statistical Power ($1-\beta$)
	- significance level $\alpha$
	- using Minumum detectable effect to estimate $\delta$ (difference between treatment and control)
	- sample variance $\sigma$
	- rule of thumb: $\frac{\beta/\alpha * \sigma^2}{\delta^2}=\frac{16 * \sigma^2}{\delta^2}$
	- e.g. we need more samples if the sample variance is larger, and fewer samples if the delta is larger
- How long to run a test 
	- 2 weeks and more
- Interference between control and treatment groups
	- Sometimes the independence assumption does not hold
	- example: the increase in the number of posts caused by new feature will be more than 1% because of the **network effect**
	- in **social networks** the treatment effect *underestimates* the real benefi, in **two-sided markets** the treatment effect *overestimates* the actual effect.
- How to deal with interference
	- Main goal is to isolate users in the control and treatment groups
	- Social Networks: [network clusters](https://engineering.linkedin.com/blog/2019/06/detecting-interference--an-a-b-test-of-a-b-tests),  [ego-cluster randomization](https://arxiv.org/abs/1903.08755)
	- Two-sided markets: Geo-based randomization, Time-based randomization

### 3. Analyzing Results
- The Initial Primacy and Novelty Effects 
	- Primacy Effects / change aversion: some users are relectant to change 
	- **Novelty Effects**: some users welcome changes
	- How to deal: 
		- completely rule out the possibility of those effects, e.g. run only on first-time users
		- compare new users' results in the control group with those in the treatment group to evaluate novelty effect
		- compare new users' results with existing users' results in the treatment group to get an actual estimation of the impact of the novelty / primacy effect
		
- Multiple testing problem
	- testing multiple features/variants
	- significance level will change accordingly: P(FP >=1) = 1-P(FP = 0) = 1 - 0.95^3 = 0.143
	- Solutions: 
		- Bonferroni correction (too conservative)
		- control the **False discovery rate**: FDR = E[#false positive / #rejections] (when having a huge number of metrics)
		
### 4. Making Decisions
- Win-lost trade-off
	- focusing on the **current objective** of the experiment. Is it to maximize engagement, retention, revenue, or something else? 
	- Also, we want to quantify the negative impact, i.e. the negative shift in a non-goal metric. 
	- For instance, if revenue is the goal, we could choose it over maximizing engagement assuming the negative impact is acceptable.
	
### 5. Resources
- [# A Summary of Udacity A/B Testing Course](https://towardsdatascience.com/a-summary-of-udacity-a-b-testing-course-9ecc32dedbb1)
