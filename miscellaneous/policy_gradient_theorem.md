# Policy Gradient Theorem

In order to run gradient ascent on a policy, we must find a way to compute the gradients of the reward with respect to the parameters of the policy. We can derive a usable formula through standard manipulations.

The following equation defines $J$ as the expected reward that a policy $\pi$, parameterized by $\theta$, will attain in a trajectory sampled from it.

$$
J(\theta)=\mathbb{E}[R(\tau) | \pi(\theta)] 
$$

We want to find the gradient of the expected reward function with respect to the parameters of our policy:
$$
\begin{equation}
\nabla_\theta J=\nabla_\theta \mathbb{E}[R(\tau) | \pi(\theta)]
\end{equation}
$$

### Probability of a trajectory

Expanding the expectation in equation 1, we get $\mathbb{E}[R(\tau) | \pi(\theta)]=\sum_\tau \mathbb{P}(\tau|\pi(\theta)) R(\tau)$. From this we can see that we need a formula for the probability of a trajectory under $\pi$.

The probability that a given trajectory—$\tau$, with $T$ actions—consisting of a set of states, $(s_0, s_1, ... ,s_T)$, and actions, $(a_0, a_1, ...,a_{T-1})$, occurs under a policy $\pi$ can be calculated as follows:

$$
\begin{equation}
\mathbb{P}(\tau|\pi(\theta)) = \mathbb{P}(s_0)\prod_{t=0}^{T-1} \pi_\theta(a_t|s_t) \mathbb{P}(s_{t + 1} | a_t, s_t)
\end{equation}
$$

Noticing the product, we can derive a similar expression with a sum (which simplifies gradient algebra) by taking the natural log of both sides of the equation, yielding:

$$
\log \mathbb{P}(\tau|\pi(\theta)) = \log \mathbb{P}(s_0) + \sum_{t=0}^{T-1} \log \pi_\theta(a_t|s_t) + \log \mathbb{P}(s_{t + 1} | a_t, s_t)
$$

Making the observation that the terms $\log \mathbb{P}(s_0)$ and $\log \mathbb{P}(s_{t + 1} | a_t, s_t)$ do not depend on theta, if we take the gradient of both sides with respect to $\theta$, we can get:

$$
\begin{equation}
\nabla_\theta \log \mathbb{P}(\tau|\pi(\theta)) = \sum_{t=0}^{T-1} \nabla_\theta \log \pi_\theta(a_t|s_t)
\end{equation}
$$


### Log Derivative Trick

Using the chain rule, we can derive the following identity for an arbitrary differentiable multivariate function, $P$, where $\log$ denotes the natural logarithm:

$$
\nabla_\theta P(\theta) = P(\theta) \nabla_\theta \log {P(\theta)}
$$

This is true because:

$$
P(\theta) \nabla_\theta \log {P(\theta)} \\
= P(\theta) \ (\nabla_\theta {P(\theta)}) \ \cdot \ \frac{1}{P(\theta)} \\
= \nabla_\theta {P(\theta)}
$$


### Putting it all together

Revisiting equation 1, we can use the facts derived above to find a usable formula for $\nabla_\theta J(\theta)$:

$$

\begin{align}
\nabla_\theta J &= \nabla_\theta \mathbb{E}[R(\tau) | \pi(\theta)] \\
                &= \nabla_\theta \sum_\tau \mathbb{P}(\tau|\pi(\theta)) R(\tau) \\
                &= \sum_\tau \nabla_\theta \mathbb{P}(\tau|\pi(\theta)) R(\tau) \\
                &= \sum_\tau \mathbb{P}(\tau|\pi(\theta)) \ \nabla_\theta [\log \mathbb{P}(\tau|\pi(\theta))] \ R(\tau) \\
                &= \sum_\tau \mathbb{P}(\tau|\pi(\theta)) \ \sum_{t=0}^{T-1} \nabla_\theta \log \pi_\theta(a_t|s_t) \ R(\tau) \\
                &= \mathbb{E}[ \ \sum_{t=0}^{T-1} \nabla_\theta \log \pi_\theta(a_t|s_t) \ R(\tau) \ ]
\end{align}
\\
\therefore \nabla_\theta J = \mathbb{E}[ \ \sum_{t=0}^{T-1} \nabla_\theta \log \pi_\theta(a_t|s_t) \ R(\tau) \ ]
$$
