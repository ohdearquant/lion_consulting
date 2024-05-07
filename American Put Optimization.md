
<img width="700" alt="am_put_option_price" src="https://github.com/ohdearquant/lion_consulting/assets/122793010/81b6d406-95a7-497e-8861-4f90009db854">


<img width="700" alt="am_put_spot_path" src="https://github.com/ohdearquant/lion_consulting/assets/122793010/0476a985-0560-4a69-80bf-9cb646b00880">




## Introduction

Different methods are needed for pricing different derivatives depending on their structures. For European-style derivatives, the value of which only depends on the spot price at expiration, the Monet Carlo method is widely used. Pricing American-style derivatives require evaluation on each time step to check early exercise decisions. The difference in exercising timing can be understood in the following way. Denote

- $V_{t},~~ C_{t},~~ E_{t}~:$ as the option’s value, continuation value, and exercise value
- $S_{t},~~ K~:$ as the spot price, and strike
- $P_{i}^{E}~:$ as the probability of the option exercise decision being made on time step $i$
- $P_{i}~:$ as the probability that the option is exercised on time step $i$

An American option’s exercise value is the difference between strike and spot price or zero, the same as European option when $t=T$.  
$$E_t = \max\{0, K-S_{t} \}$$

The American-style [[Option Foundations|option’s]] value is the higher of its continuation value and exercise value 
$$V_{t}= \max \{E_{t,}~ C_{t}\}$$

An American put option can be thought of a series of events. Each event represents the option being exercised on a particular time step. The expected value of the set of these events, is the option’s value. By the law of total expectation, the option’s value is the sum of expected payoff for each time step. where, the expected exercise value of an option at time step $i$, is the option’s exercise value at $i$ multiplied by its probability of the option can be exercised, which is when the option’s exercise value exceeds its continuation value, or the spot price is below the exercise boundary. 

$$V(\cdot) = \sum\limits^{T}_{i=2} \mathbb E [max(K-S_i, 0) | P_i] P_i P_i^E$$



Therefore, if the continuation value of an American option is known, one can compare it with the option’s exercise value to get the option’s price. However, the continuation value is more challenging to get. If the price of the option depends on just one variable, the lattice models can be used. In his work on option valuation with swarm particle optimization, Dr. Chen introduced the general methods of derivative valuations. The lattice models, such as the binomial tree, or finite difference are efficient, in terms of the balance between speed and accuracy, in solving univariate continuation value problems. Also, since lattice models require discrete time, they can value options that don’t just depend on the final state. However, if the derivative’s value depend on more than one variable, the lattice models become infeasible. In order to solve multivariate continuation value problem, the Monte Carlo method can be modified using these two popular approaches. (Chen)

The first, proposed by Longstaff and Schwartz (2001), being approximating the continuation value of options via a regression function of an arbitrary form. There is one problem with this approach: the exact functional form of the regression needs to be provided as an input. Since the function itself is uncertain, the continuation value cannot be exact. The other approach is to treat derivative pricing as a free-boundary partial differential equation problem. Similarly, if the boundary can be estimated accurately, the price of an American-style derivative can be calculated, just as a barrier option. The second approach is more computationally efficient, but its accuracy depends on the estimation of boundary.


## Monte Carlo

### Longstaff and Schwartz Model - Least-Squares Quadratic Regression

The Least-Squares Monet Carlo method proposed by Longstaff and Schwartz (2001) emphasizes on conditional expectation function. At each time step, one compares the option’s exercise value with its expected continuation value; if the payoff for immediate exercise is higher than that of holding such options, early exercise should be made; otherwise, the options should remain open. The expected continuation value for each time step is conditioned on prior information. With the least-squares method, one can estimate this conditional expectation from the simulated cross-sectional information, allowing Monte Carlo method to be applied to path-dependent and multivariate options. Such conditional expectation function can be found via regressing the realized continuation payoffs on the basis function for each underlying asset, with the calculated fitted values as the expected continuation values. Then, the optimal strategy for each simulated path can be obtained; option’s value for each path, is then obtained by discounting the optimal payoff to time zero. The option’s value is, 

$$V_{t}=\max (E_{t}, C_{t})$$

$$E_{t}=\max(0,K-S_{t})$$

and the continuation value is understood as the risk-neutral expected value $\mathbb E^{Q}_{t}[\cdot]$, also a function of the current spot price, with $f(S_t)$ being an arbitrary function. 

$$C_t= \mathbb E ^Q_t[E_{t+1}]=f(S_t)$$

Using a simple quadratic regression, 

$$f(S_{t})=a_{0}+a_1 S_t +a_2 S^2_t$$

$$V_{t+1}=a_{0}+a_{1}S_{t}+a_{2}S^2_t +  e_{t+1}$$

with a boundary condition of option price at expiration is its exercise value, 

$$V_{T}=E_{T}=\max(K-S_{T},0) $$

### Explicit Boundary method

Pricing American style option is a type of free boundary problem, which, according to Wikipedia, is a [[Black-Scholes Partial Differential Equation|partial differential equation]], involved with an unknown function $u$, and an unknown domain $\Omega$. Here, we are interested in the unknown exercise boundary function, where the option’s exercise value is equal to its continuation value for a given time step; if the exercise boundary can be accurately estimated, the price of option is just an integration over the boundary. However, the unknown exercise boundary function is difficult to approximate because it requires a backward recursive dependent structure, meaning that the boundary value of current time step is dependent on that of the immediate next time step. Intuitively, the option’s price at time $t$ is the expected discounted maximum exercise value any time before expiration, the stopping time $\tau$ is when the option gets exercised,

$$V_t(\cdot) = \mathbb E ^Q_t[e^{-r\tau}\max(0, K-S_t)]$$

Since exercise should happen if the exercise value exceeds continuation value, meaning that at the boundary, the spot price and boundary price should be the same, the exercise value is strike over spot price at time of exercise $\tau$, 

$$E_{t}=K - S(\tau)=K-B(\tau)$$

Expand on the expectation on the option’s value formula, we get   

$$V (t)= \frac{1}{N}\sum\limits_{j=1}^{N}e^{-r\tau_{j}}\max\{K-B(\tau_{j}), 0\}$$

The common types of function that the boundary function can take include, constant, linear, exponential, exponential constant, polynomial and carr et al. 


## Exercise Boundary Optimization
The problem was formulated as a parabolic partial differential inequality problem, which can be reformulated as a linear complementarity problem (LCP), i.e., Given a matrix. $(A)$ and vector $(q)$ find a pair of vectors ($w$ and $b$) such that;  
1) the vectors are non-negative ($w,z \geq 0$)  
2) satisfy the complementary conditions ($z^Tw = 0$)  
3) $b = Aw+q$  
  
For the American Option pricing problem, there is a matrix $V$, the result of discretizing both the $S$ (stock price) and $\tau$ (time), i.e. $V_{i,j}$ is the option price $S(i)$ with time value $t(j)$. The second derivative w.r.t stock price is then represented using a finite-difference method, typically Crank-Nicholson (for stability). This derivative is $Aw$ in the LCP where $A$ comes from the finite-difference stencil and $w$ and $b$ from the discretization of the differential equation, i.e;  
$$\large w_{i,v+1} - \frac{\lambda}{2}\left(w_{i+1,v+1} - 2w_{i,v} + w_{i-1,v+1}\right) - w_{i,v} - \frac{\lambda}{2}\left(w_{i+1,v} - 2w_{i,v} + w_{i-1,w}\right)  b_i$$

$$\large  = w_i + \lambda \left(w_{i+1,v} - 2*w_{i,v} + w_{i-1,v}\right)$$

where $\large \lambda=\frac{\delta_t}{2\delta_x^2}$, 

An iterative method to solve this linear system of equations is successive over-relaxation (SOR). $b = Aw$, described (4.6.5; Seydel and Seydel 2006) First the problem is discretized as above. There is a loop where the values of $b$ are updated over the discretized time $v$ -- the $\tau$ loop. Then the LCP solution is found for $w$ via SOR. Where $A$ is decomposed into the $D,L$ and $U$, the Diagonal, Upper and Lower triagnular matrices. The system can then be rewritten as, 

$$\large (D+\omega L)\mathbf {w} =\omega \mathbf {b} -[\omega U+(\omega -1)D]\mathbf {w}$$  
For a relaxation factor $\omega$ and solved iteratively, by updating $w$ until a convergence criteria is met;  
$$\large \mathbf {w} ^{(k+1)}=(D+\omega L)^{-1}{\big (}\omega \mathbf {b} -[\omega U+(\omega -1)D]\mathbf {w} ^{(k)}{\big )}=L_{w}\mathbf {w} ^{(k)}+\mathbf {c}$$

### Algorithm 1
Replacing PDE with a system of linear equations. American Put (Brennan and Schwartz)
1. Calculate the RL-decomposition of A
2. Set $\tilde A = L$, and calculate $\tilde{b}$ from $R\tilde{b}=b$, via a backward loop
3. forward loop for growing i:
	- Start with $i=1$. Calculate the next component of $\tilde{A}w=\tilde{b}$ 
	- denote it $\tilde{w_{i}}$. Set $\tilde W_{i} := \{\tilde{w_{i}}, g_{i}\}$

### Algorithm 2
Solving the system by SOR, and get option prices, 
1. Verify call or put, by comparing r, the interest rate, and delta, the dividend rate (used in this package). The interest rate must be higher than dividend for a call option. For a put there is no such requirement. 
2. define parameters, such as spot space, time space, q, degree of accuracy, theta, lambda…
3. verify that the algorithm is stable by checking is lambda > 0.5
4. initialize variables, such as time/spot space, boundary conditions and empty matrix for storing the result. 
5. Core algorithm 
	1. fill matrix b with values. Case 1 and 2 are boundary condition, and case 3 is for calculating values between each spot price step.  
	2. initialize vector V for storing option prices at each time step, to be the max of exercise value and zero. 
	3. Use SOR iteration to get $w$ of the system of linear equations, then transform the results into original dimensions and re-arrange t and V in an increasing time order. 

Output: 
V : Matrix with corresponding stock values:
- The value $V(i,j)$ is the option price corresponding to the stock price $S(i)$ and the time value $t(j)$, for $i = 1,...,m+1$ and $j = 1,...,n+1$.

### Algorithm 3
Get the exercise boundary
1. Define and initialize variables. 
2. for American Put option, the exercise boundary price is the last $n$ indices corresponding to nonzero elements in abs(V(:,j)-K+S)< eps_star, which is the indice of last option at boundary condition. For its index correlated to Spot prices matrix, we can find the boundary spot prices. 

Output: 
$Sf$: Row array of length $n+1$. 
- The values $(t(j),Sf(j))$ form the optimal exercise boundary.


## Tests and Output

We chose the following methods to compare the performances in pricing the put options with the above algorithms, using MATLAB:

This package was found on MATLAB forum, which is described in part 3
- FreeBoundary

These following methods are from standard MATLAB packages (financial toolbox and financial instrument toolbox)
- Simple Monte Carlo Simulation with Longstaff-Schwartz least squares method. 
- With the Cox-Ross-Rubinstein method 
- with the Barone-Adesi and Whaley (BAW)

We also found another package from MATLAB forum
- BAW Quadradtic Approximation
- Early Exercise Boundary Approach (EEB)
- EEB Antithetic Variates (AV)
- EEB Control Variates (CV)
- EEB AV CV

We consider the following cases, for each IV = 0.05, 0.1, 0.15, compare the option prices calculated using above methods with r = 0.03, 0.06, 0.09. And, $S_{0} = 100$, $K = 110$, $T=1$


|   |   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|---|
|σ = 0.05|Option Price|   |   ||Running Time|   |   |
|r|0.03%|0.06%|0.09%|||||
|Sim|9.9910|9.9818|9.9729|||||
|CRR|10.0000|10.0000|10.0000|||||
|BAW|10.0000|10.0000|10.0000|||||
|BAW Quad|10.0000|10.0000|10.0000|||||
|EEB|9.9913|9.9843|9.9763||0.0050|0.0050|0.0050|
|EEB AV|9.9918|9.9835|9.9752||0.0050|0.0050|0.0050|
|EEB CV|9.9922|9.9843|9.9752||0.0050|0.0050|0.0050|
|EEB AV CV|9.9917|9.9835|9.9751||0.0050|0.0050|0.0050|
|Boundary|10.0000|10.0000|10.0000||0.0700|0.0280|0.4480|
|||||||||
|σ = 0.10|Option Price|   |   ||Running Time|   |   |
|r|0.03%|0.06%|0.09%|||||
|Sim|10.0060|9.9828|9.9729|||||
|CRR|10.0000|10.0000|10.0000|||||
|BAW|10.0000|10.0000|10.0000|||||
|BAW Quad|10.0000|10.0000|10.0000|||||
|EEB|9.3373|8.2473|7.2542||0.4072|0.2822|0.1981|
|EEB AV|9.9935|9.9835|9.9752||0.0058|0.0050|0.0050|
|EEB CV|9.9963|9.9770|9.9694||0.0059|0.0050|0.0050|
|EEB AV CV|9.9889|9.9835|9.9753||0.0061|0.0050|0.0050|
|Boundary|10.0130|10.0000|10.0000||0.0000|0.0080|0.4930|
|||||||||
|σ = 0.15|Option Price|   |   ||Running Time|   |   |
|r|0.03%|0.06%|0.09%|||||
|Sim|11.1480|10.3190|10.0020|||||
|CRR|11.1800|10.0130|10.0000|||||
|BAW|10.9810|10.2350|10.0000|||||
|BAW Quad|10.9810|10.2350|10.0000|||||
|EEB|10.9040|9.5698|8.5868||0.4183|0.3226|0.2561|
|EEB AV|11.0470|10.2730|9.9865||0.2519|0.0855|0.0066|
|EEB CV|11.0340|10.2570|9.9933||0.2450|0.0849|0.0068|
|EEB AV CV|10.9650|10.2380|9.9827||0.2514|0.0879|0.0065|
|Boundary|11.0500|10.2950|10.0180||0.0000|0.0000|0.0000|

For IV = 0.05, we find that the free-boundary method is very similar to the results of the built-in methods from standard MATLAB toolboxes, and the prices as well as standard deviation are within expectation. With the interest rate going up, the American put option price is lower, and the standard deviation among different methods gets bigger. For the methods from the other package we found, the results are more interesting. The Early Exercise Boundary Approach produces output that are less consistent. 

<img width="972" alt="am_put_bounds1" src="https://github.com/ohdearquant/lion_consulting/assets/122793010/766e7d93-196c-45ae-943e-536b89eb7c64">

<img width="972" alt="am_put_bounds2" src="https://github.com/ohdearquant/lion_consulting/assets/122793010/9d55692f-7e5b-4e32-a0f4-0585cb9fc949">

<img width="972" alt="am_put_bounds_3" src="https://github.com/ohdearquant/lion_consulting/assets/122793010/b8f95b15-6f62-4c3d-a13a-141eb59071ea">





As expected, as IV goes up, the option’s price also goes up, which is the case for all methods we are testing. However, the increase of option prices w.r.t increasing IV, is more obvious for interest rate that’s lower. As interest rate goes up, the price goes down, and the standard deviation goes up. 


### References 


Seydel, Rüdiger, and Rudiger Seydel. Tools for computational finance. Vol. 3. Berlin: Springer, 2006.  
  
[https://en.wikipedia.org/wiki/Successive_over-relaxation](https://en.wikipedia.org/wiki/Successive_over-relaxation)





#ToDo

- [ ] cite Chen
- [ ] cite Longstaff and Schwartz (2001)
- [ ] add matlab code snippets 
- [ ] add more plots
- [ ] find better ways to embed plots
