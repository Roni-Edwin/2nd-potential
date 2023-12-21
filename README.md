# Interval Arithmetic Code + Simulations

A quick recap of what we're doing. We're explorign the ground state configurations at high densities under the potential $\phi:x \mapsto \frac1{x^4+1}$. For a configuration of points $X=\left(x_n\right)_{n=-\infty}^\infty$, with fixed density $\rho$, we defined its (lower) $\phi$-energy $E(X)$ as the following limit inferior:  
```math
E(X)=\liminf_{r \to \infty}\frac1{\left|X_r\right|}\sum_{\substack{i,j \in X_r \\ i\neq j}}\phi\left(\left|x_i-x_j\right|\right) \text{ where } X_r=\left\{i:\ \left|x_{i}\right|\le r\right\}
```
We extend this to measure the average potential energy of a borel measure $\mu$ on $\mathbb{R}$. For such a measure $\mu$ with $\frac{\mu\left([-r,r]\right)}{2r} \to 1$ as $r \to \infty$, we defined its average energy (under $\phi$) $\mathcal{E}(\mu)$ by 
```math
\mathcal{E}(\mu)=\liminf_{r \to \infty}\frac1{\mu\left([-r,r]\right)}\int_{[-r,r]}\int_{[-r,r]}\phi\left(|x-y|\right)d\mu(x)d\mu(y).
```
The goal then was to find such a borel measure $\mu$ that minimises $\mathcal{E}$, and experimentally, it seemed that the optimal measure $\mu^*$ is the counting measure on the lattice $\sqrt{2}\mathbb{Z}$, normalised by $\sqrt{2}$ to have average density $1$. Explicitly, for each set $A \subset \mathbb{R}$, we set 
```math
\mu^*\left(A\right)=\sqrt{2}\cdot \left|\left\{n:\sqrt{2}n \in A\right\}\right|
```
One way to test this, experimentally, is to consider the compact version of the minimisation problem. In this case the goal here is to find a configuration of $N$ points $x_1,x_2, . . . , x_N$ that minimise the average potential energy $E$ given by 
```math
E=\frac1{N}\sum_{\substack{i,j=1 \\ i\neq j}}^Np\left(\left|x_i-x_j\right|\right), \text{ with } \left|x_i\right|\le r
```
The idea here is that for large values of $r$, and $\frac{N}{r}$, the distribution of points should approximate the 'mass distribution' of the optimal measure $\mu^*$. I implemented this using a basic gradient descent algorithm, and the points tend to gather into equally spaced clusters, which would seem to support this. The way the code works is by running a basic gradient descent algorithm on the function $E$, and periodically plotting the current dfistribution of points. An interesting behavior observed is that for potentials of the form $p(x)=\frac1{x^{4n}+1}$, for $n \in \mathbb{N}$, the optimal configuration for large values of $r$ and $\frac{N}{r}$ is that the points tend to cluster into equally spaced positions. I've also included a simulation for $2$-dimensional euclidean space. The setup here is identical except now, the points $x_i$ are constrained to a disc of radius $r$ in $\mathbb{R}^2$.
The subsequents paragraphs deal with the interval arithmetic computations.




A recap and the code for Interval Arithmetic.
We define a function $\widetilde{R}$ as follows: Start by defining $\widehat{\phi}:\mathbb{R} \to \mathbb{R}$ by $\widehat{\phi}(x)=\pi e^{-\sqrt{2}\pi |x|}\cos\left(\sqrt{2}\pi|x|-\frac{\pi}{4}\right)$. We then define $T_n,\mathcal{T}_n:\mathbb{R} \to \mathbb{R}$ by
```math
T_n(x)=\frac{n^2+2x^2}{\left(n+\sqrt{2}x\right)^2}\cdot \frac{2x^2\left(\widehat{\phi}(x)-\widehat{\phi}\left(\frac{n}{\sqrt{2}}\right)\right)}{\left(x-\frac{n}{\sqrt{2}}\right)^2}, \ \mathcal{T}_n(x)=\frac{2x^2\left(n^2+2x^2\right)}{\left(n+\sqrt{2}x\right)^2 }\sum_{k=1}^{9}\frac{\widehat{\phi}^{(k)}\left(\frac{n}{\sqrt{2}}\right)}{k!}\left(x-\frac{n}{\sqrt{2}}\right)^{k-2}
```
Here $\widehat{\phi}^{(k)}$ is the $k$-th derivative of $\widehat{\phi}$. Explicitly,
```math
\widehat{\phi}^{(k)}\left(\frac{n}{\sqrt{2}}\right)=\pi\left(-2\pi\right)^{k}\cos\left(\frac{\pi\left(k+1\right)}{4}\right)\left(-e^{-\pi}\right)^{n}\ .
```
 You can think of $\mathcal{T_n}$ as giving a taylor expansion for $T_n$ when $x$ is close to $\frac{n}{\sqrt{2}}$ for some $n$. Then define $\mathcal{I}_n$ by, for some chosen $\epsilon<10^{-2}$, 
```math
\mathcal{I}_n(x)=\begin{cases}
			\mathcal{T}_n(x) & \text{if $\left|x-\frac{n}{\sqrt{2}}\right|<\epsilon$}\\
            T_n(x) & \text{otherwise}
		 \end{cases}
```
We then set 
```math
\widetilde{R}(x)=\widehat{\phi}\left(x\right)-\widehat{\phi}(0)+\sum_{n=1}^{5\cdot 10^4} \mathcal{I}_n(x)
```
Witht his definition, we aim to show $\widetilde{R}\ge  -\frac{\pi}{\sqrt{2}}\tanh\left(\frac{\pi}{2}\right)+\frac{681}{2^{20}}+10^{-10}$ on $[0,14]$. To do this, we introduce an auxialiary function $\widetilde{R_2}$ given by
```math
\widetilde{R}_2(x):=\widehat{\phi}(x)-\widehat{\phi}(0)+T_1\left(\frac1{\sqrt{2}}\right)+\sum_{n=2}^{5\cdot 10^4}\mathcal{I}_n(x)=\widehat{\phi}(x)-\widehat{\phi}(0)+\frac{\pi^3 e^{-\pi}}{\sqrt{2}}+\sum_{n=2}^{5\cdot 10^4}\mathcal{I}_n(x)\ .
```
We've seen(inlcude link) that for $\left|x-\frac1{\sqrt{2}}\right|\le 0.015$, $\left|\mathcal{R}(x)-\widetilde{R}_2(x)\right|\le \frac{681}{2^{20}}+0.2+10^{-10}$. So we the way we show the desired inequality is to first check that 
```math
\widetilde{R}_2(x)\ge -\frac{\pi}{\sqrt{2}}\tanh\left(\frac{\pi}{2}\right)+\frac{681}{2^{20}}+0.2+10^{-10} \text{ for } x \in \left[\frac1{\sqrt{2}}-0.015,\frac1{\sqrt{2}}+0.015\right]
```
then show 
```math
\widetilde{R}(x)\ge  -\frac{\pi}{\sqrt{2}}\tanh\left(\frac{\pi}{2}\right)+\frac{681}{2^{20}}+10^{-10} \text{ on } \left[0,14\right]\setminus \left[\frac1{\sqrt{2}}-0.015,\frac1{\sqrt{2}}+0.015\right]\ .
```
To do this we use the IntervalArithemtic package. The way it works basically, is that once you've defined your function $f$, you pass in you interval $I$ and returns an interval $J$ such that $f\left(I\right)\subset J$. Here attention to detail is important, especially when working with numbers that aren't representable exactly with floating point arithmetic, such as $\pi$, $e$, and $\frac{4}{3}$. So when implenting the function $f$, it's important to make sure you represent these constants as degenrate intervals. So for example, you would use $[\pi,\pi]$ (instead of just $\pi$), which Julia would represent as a small interval containing the true value of $\pi$, rather than its floating point representation. With this implementation, the general outline of the code is as follows. We have our function $f$. Let $f^*(I)$ denote the interval returned by Julia, so we wish to show  and wish to show $f\left(I\right)> [l,l]$. For two intervals $[a,b]$, $[c,d]$, $[a,b]>[c,d]$ means $a>d$. The way the code works is like so. Suppose $I=[a,b]$. Intialize $d$ to $b-a$, and repeatedly check if $f\left([a,a+d]\right)>[l,l]$, and if not, reassign $l \to \frac{l}{2}$. Once we find some $l$ for which this works, we reassing $a \to l$, and repeat until we've covered the whole interval $[a,b]$. This is why I defined $\mathcal{R}_2$, because I was running into an infinite loop.


I've also included code simualting the behavior of $N$ particles interacting under a potential function $p$ constrained to move in a disk of radius $r$. here the set-up is we have a potential $p:[0,\infty) \to [0,\infty)$, so the goal here is to fund a configuration of $N$ points $x_1,x_2, . . . , x_N$ that minimise the average potential energy $E$ given by 
```math
E=\frac1{N}\sum_{\substack{i,j=1 \\ i\neq j}}^Np\left(\left|x_i-x_j\right|\right).
```
The way the code works is by running a basic gradient descent algorithm on the function $E$, and periodically plotting the current dfistribution of points. An interesting behavior observed is that for potentials of the form $p(x)=\frac1{x^{4n}+1}$, for $n \in \mathbb{N}$, the optimal configuration for large values of $r$ and $\frac{N}{r}$ is that the points tend to cluster into equally spaced positions. 

