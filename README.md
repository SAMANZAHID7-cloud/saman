# HOGVAX for Personalized Cancer Vaccines

An optimization-based approach to neoantigen vaccine design utilizing **Integer Linear Programming (ILP)** and **Greedy Overlap Compression**. This project adapts the original HOGVAX framework (initially designed for global viral pathogens) into a dynamic, personalized tumor-vs-germline pipeline featuring cleavage-aware junction controls.

---

## 📖 Project Overview

Cancer immunotherapy relies heavily on personalized peptide vaccines targeting neoantigens—short, mutated proteins arising exclusively from tumor somatic mutations. Designing these vaccines presents a critical trade-off:

* 
**Breadth:** Covering a diverse patient population with varied HLA alleles requires many distinct epitopes.


* 
**Compactness:** Physical manufacturing limits and safety constraints require the shortest possible polypeptide sequence.



This repository implements a three-stage pipeline that exploits amino acid sequence overlaps to compress multiple epitopes safely into a highly compact vaccine construct, preventing redundant overlapping regions typical of traditional top-N concatenation methods.

---

## 🏗️ Architecture

The cancer-adapted HOGVAX engine operates across three major modules:

```
[Tumor vs. Germline DNA] 
          │
          ▼
┌────────────────────────────────────────┐
│ Module A: Neoantigen Selection         │ 
│ - SNV calling via WES                  │
│ - MHCflurry binding filtering (<500nM) │
└────────────────────────┬───────────────┘
                         │
                         ▼
┌────────────────────────────────────────┐
│ Module B: Cleavage-Aware ILP           │
│ - Mathematical epitope optimization    │
│ - NetChop biological junction control  │
└────────────────────────┬───────────────┘
                         │
                         ▼
┌────────────────────────────────────────┐
│ Module C: Greedy Overlap Compression   │
│ - Prefix-suffix matching & absorption  │
│ - Final compact therapeutic fragments  │
└────────────────────────────────────────┘

```

1. 
**Module A: Neoantigen Selection:** Compares tumor DNA against healthy germline sequences to identify Single Nucleotide Variants (SNVs). Sliding windows extract candidate peptides, which are filtered using `MHCflurry` to retain only strong binders ($A_i < 500\text{ nM}$).


2. 
**Module B: Cleavage-Aware ILP:** Solves a constrained optimization problem to maximize total vaccine immunogenicity. It introduces NetChop-predicted junction constraints to prevent the creation of un-cleavable neo-junctions that could trigger off-target autoimmune issues.


3. 
**Module C: Adjuvant Spacing & Assembly / Greedy Overlap Compression:** Evaluates selected peptides using a prefix-suffix matching algorithm ($O(n^2 \cdot L)$ complexity) to merge overlapping candidates. Where zero overlap occurs, it inserts structural AAY/KK spacers.



---

## 📊 Performance & Experimental Results
<img width="1099" height="594" alt="immunogenicity_plot" src="https://github.com/user-attachments/assets/5157ef6a-c13e-4195-bb27-ed30f36bb0a9" />
<img width="1156" height="608" alt="dashboard" src="https://github.com/user-attachments/assets/95c60fcc-7c37-454a-baf2-40f60247f5b8" />
<img width="1100" height="622" alt="results_output" src="https://github.com/user-attachments/assets/be0e5edd-709b-4c30-bd49-fa7946306c12" />

The framework was evaluated across two distinct cancer cohorts from The Cancer Genome Atlas (TCGA):

### Key Results Breakdown

* 
**TCGA-SKCM (Skin Cutaneous Melanoma):** Achieved **88.1%** simulated population coverage via the ILP reproduction baseline.


* 
**TCGA-LUAD (Lung Adenocarcinoma):** Achieved **90.5%** population coverage.


* 
**Vaccine Length Efficiency:** Successfully compressed **53 ILP-selected peptides** (originally 644 amino acids long) down into just **8 compact segments (244 amino acids)**—yielding a **62.1% total length reduction** while retaining 100% of all target epitopes.



### Approach Comparison

| Method | Key Idea | Limitation |
| --- | --- | --- |
| <br>**Epitope Cocktail** 

 | Separate peptides | No overlap savings |
| <br>**String-of-Beads** 

 | Concatenate + spacers | Still NP-hard |
| <br>**Mosaic** 

 | Cover viral diversity | Mimics natural sequence |
| <br>**OptiVax** 

 | Heuristic coverage | No overlap model |
| <br>**HOGVAX (Ours)** 

 | HOG + ILP + Greedy Merge | Post-hoc compression is local, not global |

---

## 🛠️ Tech Stack & Requirements

* **Language:** Python 3.x
* 
**Mathematical Optimization:** PuLP / CBC Solver 


* 
**Numerical Utilities:** NumPy 


* 
**Immunological Predictors (External dependencies or simulated equivalents):** MHCflurry, NetChop 



---

## 🔮 Limitations & Future Work

While the project demonstrates significant sequence savings, future versions will focus on addressing current constraints:

* 
**Pre-ILP Graph Integration:** Building the Hierarchical Overlap Graph *before* running the ILP solver so the optimization model picks global overlap-aware paths natively.


* 
**Real Genomic Pipelines:** Moving away from simulated data to integrate real `NetMHCpan` and `NetChop` predictions directly on raw TCGA patient cohort files.


* 
**Validation:** Broadening testing to BRCA/COAD cohorts and introducing wet-lab validation for predicted cleavability patterns.



---

## 👥 Authorship & Citation

* 
**Author:** Saman Zahid 


* 
**Contact:** 1257637@isb.nu.edu.pk 


* 
**Course Reference:** Deep Learning Research Project (251-7637) — March 2025
