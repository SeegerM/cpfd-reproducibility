# cpfd — Automatic Consistency Assessment through Partial Functional Dependency Mining

Reproducibility artifacts for the paper:

> **Automatic Consistency Assessment through Partial Functional Dependency Mining.**
> Marcian Seeger, Thorsten Papenbrock, Felix Naumann, Lisa Ehrlinger.
> *VLDB 2026 Workshop: 15th International Workshop on Quality in Databases (QDB'26).*

This repository contains the source code, datasets, and experiment notebooks needed to reproduce all results reported in the paper.

## What is `cpfd`?

`cpfd` is a fully automated, deterministic technique to assess the **consistency** dimension of data quality for relational datasets. Given a (potentially dirty) input table, it

1. **discovers** all partial functional dependencies (pFDs) with their scope of validity using a threshold-and genuineness-based extension of [HyFD](https://github.com/SeegerM/HyFD/tree/gpdep),
2. **weights** every discovered pFD by a statistical genuineness score (`gpdep`) that quantifies how much the dependency deviates from random chance, and
3. **aggregates** the weighted pFDs into a single consistency score

$$
\mathrm{cpfd}(\mathcal{F}) \;=\; \frac{\sum_{f \in \mathcal{F}} \omega(f)\cdot r(f)}{\sum_{f \in \mathcal{F}} \omega(f)}
$$

where $r(f) \in \{0,1\}$ indicates whether the pFD is exact and $\omega(f) \in [0,1]$ is the normalized `gpdep` weight.

Unlike prior consistency metrics, `cpfd` requires no manually specified rules, is robust against minor data errors, and satisfies all five requirements for data quality metrics defined by Heinrich et al. (2018).

## Reference implementation in Metis

The `cpfd` metric has also been integrated into **Metis**, the data quality assessment framework developed at HPI: <https://github.com/HPI-Information-Systems/Metis>. This repository provides the standalone pFD discovery + weighting code used to produce the experimental results in the paper; Metis offers a production-oriented implementation embedded in a multi-dimensional DQ assessment tool.

## Repository layout

```
.
├── HyFD/                        # Submodule: HyFD fork with pFD + gpdep extension (branch: gpdep)
├── data/
│   ├── allergen/clean/          # clean.csv + <dataset>.txt (discovered pFDs with gpdep)
│   ├── beers/clean/
│   ├── eudract/clean/
│   ├── flights/clean/
│   ├── hospital/
│   │   ├── clean/
│   │   └── polluted/            # AggregationOfPollution.csv (Sec. 4.2 input)
│   ├── rayyan/clean/
│   └── tax/clean/
├── experiments/
│   ├── Coverage.ipynb                  # Figure 2 — attribute coverage (Sec. 4.1)
│   ├── Runtime Comparison.ipynb        # Table 2 — discovery runtimes (Sec. 4.1)
│   ├── Metric Polution.ipynb           # Figure 3 — metric behavior across pollution stages (Sec. 4.2)
│   ├── Correlation.ipynb               # Table 3 — Pearson correlations between metrics (Sec. 4.2)
│   ├── Dataset-Pollution-Generator/    # Submodule: tab_err-based pollution pipeline (Sec. 4.2)
│   └── results/                        # Generated figures and CSVs (corr_matrix_*, variant_level_metrics)
├── README.md
└── REPRODUCE.md                 # Step-by-step reproduction guide
```

## Datasets

All seven datasets are taken from prior data-cleaning work:

| Dataset   | Source                                                               | Notes                  |
| --------- |----------------------------------------------------------------------| ---------------------- |
| Allergen  | [Boeckling & Bronselaer (2025)](https://doi.org/10.1145/3712205)     | Cleaning experts' FDs  |
| Beers     | [Mahdavi & Abedjan (2020)](https://doi.org/10.14778/3407790.3407801) | Dataset owners' FDs    |
| Eudract   | [Boeckling & Bronselaer (2025)](https://doi.org/10.1145/3712205)     | Cleaning experts' FDs  |
| Flights   | [Mahdavi & Abedjan (2020)](https://doi.org/10.14778/3407790.3407801)                                            | Dataset owners' FDs    |
| Hospital  | [Mahdavi & Abedjan (2020)](https://doi.org/10.14778/3407790.3407801)                                   | Dataset owners' FDs    |
| Rayyan    | [Mahdavi & Abedjan (2020)](https://doi.org/10.14778/3407790.3407801)                                           | Dataset owners' FDs    |
| Tax       | [Mahdavi & Abedjan (2020)](https://doi.org/10.14778/3407790.3407801)                                          | Dataset owners' FDs    |

Synthetic pollution variants used in Section 4.2 are generated with the [`tab_err`](https://pypi.org/project/tab-err/) library (Jung et al., 2025) via the [`Dataset-Pollution-Generator`](experiments/Dataset-Pollution-Generator/) submodule, which bundles the per-dataset error profiles, seed, and CLI used in the paper. See its README for usage.

## Reproduction

See [REPRODUCE.md](REPRODUCE.md) for hardware/software requirements, build instructions, and per-experiment reproduction steps.

## Citation

The paper is currently under submission; a citable reference will be added once the proceedings are published.

```bibtex
@inproceedings{seeger2026cpfd,
  title     = {Automatic Consistency Assessment through Partial Functional Dependency Mining},
  author    = {Seeger, Marcian and Papenbrock, Thorsten and Naumann, Felix and Ehrlinger, Lisa},
  booktitle = {VLDB 2026 Workshop: 15th International Workshop on Quality in Databases (QDB'26)},
  year      = {2026},
  note      = {To appear}
}
```

## Acknowledgments

This work was supported by the Deutsche Forschungsgemeinschaft (DFG) under project numbers 560957958 and 560743026.
