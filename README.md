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

