# OptimizationFinalProject
This project develops an optimization model to allocate user queries along different AI language models, minimizing total cost while meeting minimum quality requirements and capacity limits. Using public benchmark data, Python and Gurobi, it explores how to balance cost and response quality under different deman scenarios.


**Course:** Optimization and Simulation · 3rd year iMAT, ICAI  
**Status:** Project proposal; implementation and results are pending.

## Motivation

Language models differ in cost and accuracy across task categories. Using the same model for every query may therefore be inefficient. This project explores whether allocating queries across several models can reduce costs while maintaining a minimum expected accuracy in each category.

## Objective and scope

The model will determine how many queries of each category should be assigned to each language model during a planning period. The initial scope includes three models and three task categories.

The objective is to minimize the total expected cost of serving all queries, subject to:

- All demand in each category must be served, with each query assigned to one model.
- Each model has a maximum capacity during the planning period.
- Each category must meet its own minimum expected accuracy.
- Assigned query counts must be nonnegative integers.

The project optimizes the use of existing models using published benchmark results. It does not train new language models.

## Mathematical formulation

Let \(i\) index query categories and \(j\) index language models.

| Symbol | Meaning |
| --- | --- |
| `d_i` | Number of queries demanded in category `i` |
| `c_ij` | Estimated average cost per query of category `i` assigned to model `j` |
| `a_ij` | Estimated accuracy of model `j` on category `i` |
| `q_i` | Minimum required expected accuracy for category `i` |
| `K_j` | Query capacity of model `j` during the planning period |
| `x_ij` | Decision variable: number of queries of category `i` assigned to model `j` |

Minimize total expected cost:

```text
minimize  Σ_i Σ_j c_ij x_ij
```

Subject to:

```text
Σ_j x_ij = d_i                  for every category i
Σ_i x_ij ≤ K_j                  for every model j
Σ_j a_ij x_ij ≥ q_i d_i         for every category i
x_ij ∈ {0, 1, 2, ...}          for every category i and model j
```

This is an integer linear optimization model. The quality constraint requires sufficient expected accuracy within each category, so strong performance in one category cannot compensate for poor performance in another.

## Data sources

- [RouterBench dataset](https://huggingface.co/datasets/withmartian/routerbench): planned source of model responses, performance scores, and estimated costs.
- [RouteLLM research project, UC Berkeley](https://sky.cs.berkeley.edu/project/routellm/): reference for the broader topic of LLM routing.

We will select comparable models and tasks and prepare a CSV containing average costs and accuracy estimates for each category–model combination. Benchmark costs will be treated as benchmark values, rather than current provider prices.

Demand and capacity limits will be explicitly defined synthetic scenarios. The case study will therefore not claim to represent the actual operations of a specific company.

## Planned implementation

The optimization model will be implemented in **Python** and solved using **Gurobi**.

1. Select models and compatible task categories from the benchmark.
2. Separate data used to estimate model performance from data used to evaluate the allocation policy.
3. Estimate average costs and accuracies and define demand, quality, and capacity scenarios.
4. Formulate and solve the integer optimization model.
5. Compare the resulting allocation with simple baseline policies, including using a single model when feasible.
6. Analyze how the recommendations change across scenarios.

Execution instructions will be added when the implementation and data preparation workflow are available.

## Planned analysis

The analysis will examine total expected cost, expected accuracy by category, allocation across models, and capacity utilization. Sensitivity analysis will vary:

- Demand volume and category mix.
- Minimum accuracy requirements.
- Available model capacities.
- Average costs per query.

We will also identify infeasible scenarios, such as cases where quality requirements or capacity constraints prevent all demand from being served.

## Assumptions and limitations

- Accuracy estimates describe expected performance; they do not guarantee that an individual answer will be correct.
- Average costs simplify differences in query and response lengths.
- Synthetic demand and capacities are scenario assumptions, not observed company data.
- Evaluation answers will not be used in advance to decide which model receives a query.
- The model produces aggregate allocations by category, rather than a learned routing decision for each individual prompt.

## Expected outcome

A recommended distribution of queries across models, supported by a comparison of costs and quality, sensitivity analysis, and a discussion of the model's limitations.
