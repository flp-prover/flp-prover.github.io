---
layout: default
---

# Introduction

FLP is an inductive theorem prover for separation logic entailments. It
utilizes a structural induction proof system to reason about the
recursive structures of induction heap predicats. Moroever, it also has
an ability to learn from the failures of the unsuccessful proof attempts
and repair them by generalizing, weakening or performing case analysis
on corresponding entailments

# Why needs to learn from failure?

When proving separation logic entailments by induction, an induction
hypothesis may contain specific constants, varibles, or pure constraints
which prevent it from being applied to prove other goal entailments.
This induction hypothesis is said to be unproductive.

Standard induction-based provers often set an unproductive induction
hypothesis aside and try out other induction hypotheses. However, these
provers may fail to prove a goal entailment if they cannot apply any
induction hypotheses. Here, a possible solution is to anlayze and learn
from the failure of the induction hypothesis application to repair the
unsuccessful proof.

# How to learn from failure?

FLP first identifies the main reason why an induction proof fails. It
identifies constants or varibles in the heap part of an induction
hypothesis's antecedent, which cannot be matched with the corresponding
expressions in the goal entailment, or determines pure formulas in the
pure part of an induction hypothesis, which cannot be proved by the goal
entailment's antecedent.

These constants, varibles, pure formulas are said to be unmatchable.
Afterward, FLP can perform one of the three steps:

- Generalize the unmatchable constants or variables in the unproductive
  induction hypotheses to obtain more potential induction hypotheses.

- Weaken the unmatchable pure formulas in the unproductive induction
  hypotheses to obtain more potential induction hypotheses.

- Perform case analysis on a pure formula of the current entailment,
  which corresponding to the matchable pure formula of the unproductive
  induction hypothesis to derivew new entailments which can be proved by
  the current induction hypothesis.

# How good FLP is?

We first experimented FLP on proving valid entailments and unsatisfiable
formulas of the separation logic competition SL-COMP 2019. **FLP can prove
97.5% of the entailment benchmark and 91.2% of the formula benchmark**.
These results are slightly behind the prover S2S, which can prove 100%
these two benchmarks. The better performance of S2S is due to its
ability to compute decidable first-order formulas to under-approximate
and over-approximate heap predicates in these entailments/formulas.

In contrast, our prover FLP was not equipped with the approximation
technique, hence, it is less efficient than S2S in this particular
benchmark. Instead, we developed FLP using induction proof, and our
prover **FLP outperforms other induction based provers Songbird and
FLP**. These two provers can respectively prove only 91.7% and 30.0% of
the entailment benchmark and 75.8% and 78.0% of the formula benchmark.
The detailed results is presented in the below table.

|--------------|-----|------|--------|-------|---------|-----|----------|-----|
| Entailment   |   # | Spen | Harrsh | Slide | Cyclist | S2S | Songbird | FLP |
|--------------|-----|------|--------|-------|---------|-----|----------|-----|
| SLL          | 392 |  175 |    251 |    27 |     205 | 392 |      392 | 392 |
| NLL          |  22 |   22 |     11 |     0 |      14 |  22 |       22 |  22 |
| SKL          |  26 |   26 |      4 |     0 |      10 |  26 |       20 |  26 |
| DLL          |  77 |   10 |     54 |    39 |      46 |  77 |       73 |  75 |
| TREE         |  43 |    0 |     24 |    15 |      24 |  43 |       36 |  41 |
|--------------|-----|------|--------|-------|---------|-----|----------|-----|
| SLL (arith)  |  78 |    x |      x |     x |       x |  78 |       65 |  74 |
| SLS (arith)  |  25 |    x |      x |     x |       x |  25 |       15 |  22 |
| DLL (arith)  | 116 |    x |      x |     x |       x | 116 |      100 | 112 |
| TREE (arith) |  12 |    x |      x |     x |       x |  12 |        2 |   7 |
|--------------|-----|------|--------|-------|---------|-----|----------|-----|
| Total        | 791 |  233 |    344 |    81 |     299 | 791 |      725 | 771 |
|--------------|-----|------|--------|-------|---------|-----|----------|-----|


|--------------|----|------|--------|---------|------------|-----|----------|-----|
| Formula      |  # | Spen | Harrsh | Cyclist | CyclistSat | S2S | Songbird | FLP |
|--------------|----|------|--------|---------|------------|-----|----------|-----|
| SLL          | 63 |   55 |     63 |      43 |         61 |  63 |       58 |  63 |
| DLL          |  4 |    0 |      4 |       0 |          4 |   4 |        0 |   2 |
| TREE         |  6 |    0 |      6 |       4 |          6 |   6 |        4 |   5 |
|--------------|----|------|--------|---------|------------|-----|----------|-----|
| SLS (arith)  | 11 |    x |      x |       x |          x |  11 |        6 |  10 |
| DLL (arith)  |  6 |    x |      x |       x |          x |   6 |        0 |   2 |
| TREE (arith) |  1 |    x |      x |       x |          x |   1 |        1 |   1 |
|--------------|----|------|--------|---------|------------|-----|----------|-----|
| Total        | 91 |   55 |     73 |      47 |         71 |  91 |       69 |  83 |
|--------------|----|------|--------|---------|------------|-----|----------|-----|


We also experimented FLP with valid entailments collected from the
verification of programs. In particular, we collect the entailments
generated by an existing verifier Hip/Sleek (it might return the answer
*unknown*). Then, we manually inspect to select only valid entailments.
These entailments contain not only complicated data structures but also
additional pure constraints over temporary variables. These temporary
variables are introduced by the verification system Hip/Sleek when it
transform input programs into the static single assignment form (SSA).
Hence, most of them cannot be prove by existing provers like S2S and
Songbird.

In contrast, our prover **FLP can easily apply the weakening rule** to
remove all these additional pure constraint. Hence, **it can efficiently
prove 100% of these entailments** in averagely 0.47 seconds per
entailment. The details of this experiment is shown in the below table.

|--------------|-----|-----|----------|-----|
| Entailment   |   # | S2S | Songbird | FLP |
|--------------|-----|-----|----------|-----|
| SLL (size)   |  35 |  25 |        4 |  35 |
| DLL (size)   |  48 |  34 |       28 |  48 |
| TREE (size)  |  24 |   x |       18 |  24 |
|--------------|-----|-----|----------|-----|
| SLL (arith)  |  31 |  18 |        8 |  31 |
| DLL (arith)  |  28 |  18 |       18 |  28 |
| TREE (arith) |  26 |   x |       18 |  26 |
|--------------|-----|-----|----------|-----|
| Total        | 192 |  95 |       94 | 192 |
|--------------|-----|-----|----------|-----|

# Download

- Our prover FLP and the experimented entaiment benchmarks can be
  downloaded here: [FLP and its
  benchmarks](https://www.dropbox.com/prover-benchmarks.zip)

- The details of our experiment are reported in this PDF file:
  [experiment details](https://www.dropbox.com/SAS19-experiment.pdf)
