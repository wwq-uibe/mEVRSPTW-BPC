# Instances and Results: Enhanced Exact Solution of the Electric Vehicle Routing and Overnight Charging Scheduling Problem

This repository contains the instances and the computational results of the paper

> W. Wang. *Enhanced Exact Solution of the Electric Vehicle Routing and Overnight Charging Scheduling Problem.* To be submitted.

The source code of the branch-price-and-cut algorithm will be added to this repository upon publication of the paper.

## Contents

```
data/
  benchmark.zip         112 benchmark instances with 25 and 50 customers
  new-<n>.zip           56 new instances with n = 60, 70, 80, 90, 100 customers each
  sha256.txt            SHA-256 of every instance file
docs/
  detailed_results.pdf  results of our algorithm on each new instance (one table per number of customers)
  detailed_results.tex  LaTeX source of detailed_results.pdf
results/
  benchmark.zip         logs on the benchmark instances: benchmark/ours/ (our algorithm), benchmark/yamin/ (code of Yamín et al.)
  new.zip               logs on the new instances: new/ours/, new/yamin/
  ablation.zip          logs of the six variants of the ablation study: ablation/<variant>/
  summary/              one CSV file per run, one row per instance
  tables/               Tables 3-5 of the paper, data of the figures, and counts of Section 6
```

The logs (one text file per instance and run) are packed in three archives; unpack them with

```bash
cd results
unzip benchmark.zip && unzip new.zip && unzip ablation.zip
```

## Instances

The instances use the XML format of Yamín et al. (2025). Unpack them with

```bash
unzip data/benchmark.zip -d instances                     # instances/benchmark/*.xml
for f in data/new-*.zip; do unzip "$f" -d instances; done  # instances/new/*.xml
```

- **Benchmark instances** (112): the instances with 25 and 50 customers of Yamín et al. (2024, 2025), available at https://github.com/INFORMSJoC/2023.0404.
- **New instances** (280): 60, 70, 80, 90, and 100 customers, generated from the 56 Solomon instances with the procedure of Yamín et al. (2025), with a battery capacity of 20 kWh. The number of chargers `num_chargers` of each class and size is given in Table 2 of the paper.

`data/sha256.txt` lists the SHA-256 of every instance file. Every log records the SHA-256 of the instance file used in the run (`input_sha256`), and these values coincide with the files of this repository.

## Results

Each file `<set>/<run>/<instance>.txt` in these archives is the log of one run on the Fir cluster of the Digital Research Alliance of Canada. All runs used one core of an AMD EPYC 9655 processor, a single thread, 32 GB of memory with a Java heap of 24 GB, and a time limit of 7,200 seconds. Our algorithm is implemented in Java 17 and uses Gurobi 12.0.0. A log contains the console output and the files written by the code, in particular `summary.json` (status, bounds, computing times, and statistics), `incumbents.json`, and `routes.json`. The run labels in the logs are the job names used on the cluster.

The ablation variants switch off, respectively, the bidirectional labeling algorithm (`no_bidirectional`), the charger–customer window inequalities (`no_window_inequalities`), the pricing strategy (`no_pricing_strategy`), reduced-cost arc fixing (`no_arc_fixing`), route enumeration with the whole gap (`no_route_enumeration`), and the primal framework (`no_primal_framework`); see Section 6.5 and Table EC.3 of the paper.

The files in `results/summary/` contain one row per instance with the columns

| Column | Content |
|---|---|
| `instance`, `class`, `customers`, `B` | instance name, Solomon class, number of customers, number of chargers |
| `status` | `OPTIMAL` or the status at the time limit |
| `UB`, `LB` | best solution value and global lower bound (empty if none) |
| `time_s` | computing time in seconds, as reported in the paper (`search_seconds` for our code, `bpc_seconds` for the code of Yamín et al.) |
| `nodes` | number of processed branch-and-bound nodes |
| `ails_seed` | seed of the initial heuristic (our code only) |
| `ladder_certificate` | 1 if optimality was proved by the certificate of the enumeration ladder (Proposition 7), 0 otherwise (our code only) |
| `root_bound` | value of the linear relaxation at the end of the root cut loop, empty if no round was completed (our code only) |
| `input_sha256` | SHA-256 of the instance file used in the run |
| `node` | cluster node on which the run was performed |

The files in `results/tables/` contain Tables 3 (benchmark instances), 4 (new instances), and 5 (ablation study) of the paper, the data of Figures 1, 2, and EC.1 (`solve_times.csv`, `figure_ec1_by_class.txt`), and the counts quoted in Sections 6.3 to 6.5 (`text_counts.txt`), all computed from the files in `results/summary/`. `docs/detailed_results.pdf` lists the results of our algorithm on every new instance.

### Code of Yamín et al. (2025)

The runs in `benchmark/yamin/` and `new/yamin/` use the public code of Yamín et al. (2024), https://github.com/INFORMSJoC/2023.0404. Since this code relies on CPLEX, which is not available on the cluster, we replaced the calls to CPLEX with the equivalent calls to Gurobi 12.0.0 and left the algorithm unchanged. The only other changes are a retry when the license server is temporarily unavailable and the output of the run statistics in `summary.json`.

For C101-60 and C101-70, the code reports an optimal solution, but the statistics output writes the status INFEASIBLE because the variables of this solution equal 0.99999 instead of 1. The selected routes cover every customer exactly once and their cost equals the lower bound, so these two runs are counted as optimal in `results/summary/new_yamin.csv` and in the paper.

## References

- Yamín D, Desaulniers G, Mendoza JE (2024) The electric vehicle routing and overnight charging scheduling problem on a multigraph. https://doi.org/10.1287/ijoc.2023.0404.cd. Available at https://github.com/INFORMSJoC/2023.0404.
- Yamín D, Desaulniers G, Mendoza JE (2025) The electric vehicle routing and overnight charging scheduling problem on a multigraph. *INFORMS Journal on Computing* 37(4):808–830.

## Contact

Weiquan Wang, Université Laval (weiquan.wang.1@ulaval.ca).
