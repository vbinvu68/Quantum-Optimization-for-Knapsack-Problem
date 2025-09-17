# Quantum-Optimization-for-Knapsack-Problem

Optimize nonprofit funding with the **Quantum Approximate Optimization Algorithm (QAOA)**.  
We model grant allocation as a **Knapsack problem**: maximize social impact while staying under budget.  

## Example
A $100k budget must be distributed across 20 nonprofit projects, each with a **cost** and an **impact score**.  
Goal: **maximize impact without exceeding the budget**.  

## Approach
- Start with a small 5-project case using classical brute-force.  
- Scale up using **QAOA** with **QUBO encoding**.  
- Compare two methods for inequality constraints:  
  - **Slack variables** (common, but qubit-heavy)  
  - **Unbalanced penalization** (efficient, hardware-friendly)  

Includes classical benchmarks, quantum simulations, and post-processing tools.  
Inspired by [4].

## Installation
This notebook requires installing **`openqaoa`**, which includes a pre-implemented Knapsack model and supports unbalanced penalization.  

```python
conda create -n openqaoa-env python=3.10
conda activate openqaoa-env
conda install numpy sympy
pip install qiskit qiskit-aer openqaoa
pip install pylatexenc
```

⚠️ Note: As of September 2025, `openqaoa` is **not compatible** with Python versions above 3.10.

## Objective function
### The main objective function without inequality constraint
The initial objective function is:

$$\min_{x,s}f(x)+p(\lambda,x,s)=\min_{x,s}\left(-\sum_i v_i x_i\right)$$

### Encoding inequality constraint with slack variables
Using slack variables to map inequality constraint, the objective function becomes [2-4]:

$$\min_{x,s}f(x)+p(\lambda,x,s)=\min_{x,s}\left[-\sum_i v_i x_i +\lambda(\sum_iw_ix_i-W)^2\right]$$

where $\lambda$ is set to 2 which is enough large for the constraint to always be fulfilled [4]

### Encoding inequality constraint with unbalanced penalization
Using unbalanced penalization to map inequality constraint, the objective function now is [2-4]:

$$\min_{x,s}f(x)+p(\lambda,x,s)=\min_{x,s}\left[-\sum_i v_i x_i -\lambda_1(\sum_iw_ix_i-W)^2+\lambda_2(\sum_iw_ix_i-W)^2\right]$$

where $\lambda_{i}$ ($i=1$ or $2$) are again penalty coefficients, which are proved to have good results for slack problem for Knack sack problem with $\lambda_1=0.9603$ and $\lambda_1=0.0371$ in [2]. 

 
## Solving with Quantum Approximate Optimization Algorithm (QAOA)
1. **Init:** Apply Hadamards → equal superposition.  
2. **Problem unitary:** $U_C(\gamma) = e^{-i\gamma H_C}$ encodes cost (QUBO).  
3. **Mixer unitary:** $U_B(\beta) = e^{-i\beta H_B}; H_B=\sum X_j$.  
4. **p layers:** Alternate $U_C, U_B$ → final state $|\psi_p(\gamma,\beta)\rangle$.  
5. **Measure & optimize:** Estimate $\langle \psi_p | H_C | \psi_p \rangle$, tune $\gamma,\beta$.  
6. **Result:** Approximate solution to the optimization problem.

## Results
### 5-project set
<img width="570" height="432" alt="5_project" src="https://github.com/user-attachments/assets/569dcf3c-9862-40e9-8786-3da98401a737" />

- **Slack Variables**
  - Standard approach  
  - Requires extra qubits (costly)  
  - Less efficient sampling  

- **Unbalanced Penalization**
  - Fewer qubits needed  
  - Stronger penalty for violations  
  - Better sampling efficiency  
  - Scales more easily to larger problems  

➡️ Result: QAOA with unbalanced penalization gave ~464 optimal solutions out of 5000 shots.

## Full 20-project set
<img width="570" height="432" alt="20_project" src="https://github.com/user-attachments/assets/eb62de59-8533-4512-a395-c0db8d0b9076" />

- Search space: $2^{20} = 1,048,576$ configs  
- Need ~1M shots for coverage  
- Random guess → ~0.95 optimal solutions  
- Actual result: **21 optimal solutions / 1M shots**  
- More shots → better results

## References
[1] Farhi, E., Goldstone, J., & Gutmann, S. (2014). A Quantum Approximate Optimization Algorithm. http://arxiv.org/abs/1411.4028

[2] Montanez-Barrera, A., Willsch, D., A., Maldonado-Romo, & Michielsen, K. (2022). Unbalanced penalization: A new approach to encode inequality constraints of combinatorial problems for quantum optimization algorithms. http://arxiv.org/abs/2211.13914

[3] Montanez-Barrera, J. A., Heuvel, P. van den, Willsch, D., & Michielsen, K. (2023). Improving Performance in Combinatorial Optimization Problems with Inequality Constraints: An Evaluation of the Unbalanced Penalization Method on D-Wave Advantage. https://doi.org/10.1109/QCE57702.2023.00067

[4] A. Montanez, "Quadratic Unconstrained Binary Optimization tutorial," Pennylane.ai, Feb. 28, 2024. [Online]. Available: https://pennylane.ai/qml/demos/tutorial_QUBO. [Accessed: Sep. 14, 2025].

