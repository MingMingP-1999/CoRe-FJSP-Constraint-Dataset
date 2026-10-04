# CoRe FJSP Constraint Dataset

A benchmark of **140 flexible job-shop scheduling (FJSP) scenarios** with single or combined extension constraints, accompanying the paper:

**CoRe: A Collaborative and Reflective Language Model Framework for Flexible Job Shop Scheduling**

Mingming Peng, Jie Yang, Jin Huang, Qihao Liu, Zhendong Chen, Hao Zhang, Chunjiang Zhang, Liang Gao, and Xinyu Li.

*IEEE Transactions on Automation Science and Engineering*, vol. 23, pp. 15357-15370, 2026.

[Paper on IEEE Xplore](https://ieeexplore.ieee.org/document/11664462) | [DOI](https://doi.org/10.1109/TASE.2026.3727075) | [License: CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

## Overview

The dataset extends standard FJSP with different operational constraints, individually and in combination, to simulate diverse workshop scenarios. It supports evaluating whether large language models (LLMs) can understand different scheduling requirements and translate them into correct mathematical formulations and executable optimization models.

All scenarios use the same underlying scheduling instance. The variation is in the additional constraints, their parameters, their target jobs or machines, and their combinations. This makes it possible to study modeling capability across workshop requirements while keeping the basic processing environment fixed.

The benchmark contains:

- **60 single-category scenarios:** 10 scenarios for each of six extension-constraint categories.
- **80 combined-category scenarios:** 10 scenarios for each of eight category combinations.
- Chinese natural-language descriptions, reference mathematical formulations, reference code snippets, and complete reference models.
- The underlying processing data and reference optimal makespan values for all 140 scenarios.

The original Chinese descriptions are preserved as benchmark inputs.

## Repository Structure

```text
CoRe-FJSP-Constraint-Dataset/
├── README.md
├── LICENSE
├── constraints/
│   ├── 1.json
│   ├── 2.json
│   ├── 3.json
│   ├── 4.json
│   ├── 5.json
│   ├── 6.json
│   ├── 1+2.json
│   ├── 1+2+3.json
│   ├── 1+4+5.json
│   ├── 1+5+6.json
│   ├── 4+6.json
│   ├── 5+6.json
│   ├── 1+4+5+6.json
│   └── 1+2+3+4+5+6.json
└── scheduling_data/
    ├── FJSP_data.txt
    └── reference_solutions.xlsx
```

`constraints/` contains the scenario descriptions and their reference annotations. `scheduling_data/` contains the common physical processing instance and the reference objective values.

## Constraint Categories

Every scenario includes the standard FJSP requirements: compatible-machine assignment, within-job operation precedence, machine capacity without overlapping operations, makespan bounds, and nonnegative operation start times. Additional requirements are organized into six categories:

| ID | Category | Additional requirements | Coupling tier |
| --- | --- | --- | --- |
| 1 | Job start-time constraints | Lower bounds, upper bounds, or windows on job start times | Tier I |
| 2 | Job completion-time constraints | Deadlines, lower bounds, or windows on job completion times | Tier I |
| 3 | Inter-operation waiting time | Minimum, maximum, or bounded waiting intervals between consecutive operations | Tier II |
| 4 | Inter-job temporal dependencies | Relations between job start or completion times, including processing order and synchronized completion | Tier II |
| 5 | Machine setup / switching time | Required separation when a machine processes operations belonging to different jobs | Tier III |
| 6 | Transportation time | Required delay when consecutive operations of a job use different machines | Tier III |

A filename identifies the included categories. For example, `1.json` contains category 1 scenarios, while `1+2+3.json` combines categories 1, 2, and 3. Each file contains 10 records.

The four difficulty levels used in the paper can be recovered from these categories:

| Level | Definition | Scenarios |
| --- | --- | ---: |
| 1 | Only Tier I categories | 30 |
| 2 | Includes Tier II categories, with no Tier III category | 30 |
| 3 | Includes exactly one Tier III category | 40 |
| 4 | Includes both Tier III categories | 40 |

## JSON Format

Each JSON file is a UTF-8 array of 10 records. For the `N`-th record, the fields are:

| Field | Type | Meaning |
| --- | --- | --- |
| `constraintN` | String | Full natural-language description, including the standard and extension constraints |
| `formulaN` | List of strings | Reference mathematical formulations for the extension constraints, expressed in LaTeX |
| `codeN` | List of strings | Reference Gurobi Python code snippets for the extension constraints |
| `codeN_processed` | String | Complete reference model, defining `FJSPModel(...)` |

The items in `formulaN` and `codeN` correspond to the extension categories in filename order. A single-category file has one item per list; a combined-category file has one item for each included category. One item may contain multiple equations or constraints.

An instance ID is `<filename_without_extension>_<record_number>`, with record numbers starting at 1. For example, `1+2+3_10` identifies the tenth record in `constraints/1+2+3.json`. The suffix in a field name identifies the record, not the extension category. Historical equation and comment labels are annotations and should not be used as instance IDs.

Natural-language job and machine labels use 1-based numbering, while the reference Python models use 0-based array indices. Some mathematical annotations retain legacy index labels; interpret them together with their paired descriptions and reference code.

The reference models minimize the **makespan**, denoted by `C_max`. They receive processing parameters as inputs rather than embedding the processing instance in the JSON. Some transportation reference formulations use products of binary assignment variables, handled by Gurobi's quadratic-constraint interface or an equivalent linearization.

## Scheduling Data and Reference Results

### Processing instance

`scheduling_data/FJSP_data.txt` contains **10 jobs and 5 machines**, with **5 operations per job**. The FJS-style format is:

- The first line gives the number of jobs, the number of machines, and an additional header value retained from the source file. The supplied reference models use the first two values as the dimensions.
- Each subsequent line describes one job. It starts with the number of operations.
- For each operation, the line gives the number of eligible machines, followed by `(machine_id, processing_time)` pairs.
- Machine IDs in this file start at 1. Processing and waiting times use the same abstract time units.

### Reference objectives

`scheduling_data/reference_solutions.xlsx` contains 140 rows in the `optimization_results` worksheet:

| Column | Meaning |
| --- | --- |
| `ID` | Instance ID, such as `3_10` |
| `Objective_Value` | Reference optimal makespan |
| `Status` | Reference solver status |
| `JSON_File` | Source JSON filename, located under `constraints/` |
| `Code_Key` | Complete reference model field, such as `code10_processed` |

The workbook provides objective values rather than complete operation-by-operation schedules.


## License

The dataset and included reference annotations are released under the **Creative Commons Attribution 4.0 International license (CC BY 4.0)**. You may use, share, and adapt them, including for commercial purposes, provided that you give appropriate credit, link to the license, and indicate any changes.

Attribute the dataset to the CoRe authors, link to this repository, and cite the accompanying paper when using it in research or publications. See [LICENSE](LICENSE) and the [CC BY 4.0 legal code](https://creativecommons.org/licenses/by/4.0/legalcode).

The license covers the material distributed here, not external solver software or the IEEE publication itself.

## Citation

If you use this dataset, please cite:

```bibtex
@article{peng2026core,
  author  = {Peng, Mingming and Yang, Jie and Huang, Jin and Liu, Qihao and
             Chen, Zhendong and Zhang, Hao and Zhang, Chunjiang and
             Gao, Liang and Li, Xinyu},
  title   = {{CoRe}: A Collaborative and Reflective Language Model Framework
             for Flexible Job Shop Scheduling},
  journal = {IEEE Transactions on Automation Science and Engineering},
  year    = {2026},
  volume  = {23},
  pages   = {15357--15370},
  doi     = {10.1109/TASE.2026.3727075},
  url     = {https://ieeexplore.ieee.org/document/11664462}
}
```

Dataset URL: https://github.com/MingMingP-1999/CoRe-FJSP-Constraint-Dataset
