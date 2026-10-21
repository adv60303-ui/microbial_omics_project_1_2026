Evolutionary analysis of ColAP across Colwelliaceae.

Reference protein: UniProt [Q7WVY1](https://www.uniprot.org/uniprotkb/Q7WVY1), RefSeq WP_011044230.1, PDB [3CIA](https://www.rcsb.org/structure/3CIA). Residue numbers follow UniProt. The signal peptide is 1–24. The mature protein is 25–629. Catalytic and zinc-binding residues: E300, H324, E325, H328, E347, Y410.

Run the notebooks from this directory, in order. Paths are relative to this folder. Figures are PNG files in `figures/`.

```bash
conda env create -f environment.yml
conda activate colap
```

`requirements.txt` lists the Python packages. MAFFT, ClipKIT, IQ-TREE 2, and HyPhy are external programs.

| Tool | Version |
Python 3.13.1
NumPy 2.1.3 
pandas 3.0.2
SciPy 1.17.1
Biopython 1.88 
matplotlib 3.10.8 
MAFFT 7.526
ClipKIT 2.14.0
IQ-TREE 2 2.3.6 
HyPhy 2.5.103

## Notebooks

### 01_data_collection.ipynb
Identifies putative ColAP orthologs from the DIAMOND screening results in `data/screening/`.

Filters sequences by similarity to Q7WVY1, coverage, length, conserved functional residues, and completeness. Removes duplicates and additional copies.

**Output:** 202 ColAP sequences in `results/sequences/`.

The original DIAMOND search is not repeated.

### 02_alignment.ipynb
Aligns sequences using MAFFT 7.526 and trims the mature-protein alignment using ClipKIT 2.14.0 (`gappy`, gap fraction 0.9).

Output: 610 aligned positions, including 364 parsimony-informative sites.

Existing alignments are reused when available.

### 03_phylogeny.ipynb
Reconstructs a maximum-likelihood tree using **IQ-TREE 2.3.6** with ModelFinder, 1,000 UFBoot replicates, and 1,000 SH-aLRT replicates.

Selected model: WAG+F+R6 (BIC).

Output: 202-tip phylogeny; 73/199 internal branches strongly supported (UFBoot ≥ 95, SH-aLRT ≥ 80).

Existing tree files are reused when available.

### 04_tree_comparison.ipynb
Compares the ColAP gene tree with the published Colwelliaceae species tree from Gentilhomme et al. (2024), using shared strains and unrooted splits.

Output: 91 shared strains; Robinson–Foulds distance 67/176; 30/32 well-supported ColAP splits concordant with the species tree.

Also generates a compact phylogenetic tree.

### 05_temperature_analysis.ipynb
Tests associations between optimal growth temperature (OGT) and three ColAP sequence features: proline fraction, GRAVY, and Arg/(Arg+Lys).

Uses **PGLS** to account for phylogenetic relationships and Holm correction for three comparisons.

Output: 38 strains; no statistically significant associations after correction.

### 06_structural_analysis.ipynb
Maps ColAP residues onto the experimental structure PDB 3CIA (chain A).

Calculates relative solvent accessibility (Shrake–Rupley), residue contacts (Cβ distance ≤ 8 Å, sequence separation ≥ 6), and distance to the functional center.

Output: 587 structurally mapped amino acid positions.

### 07_evolutionary_rates.ipynb
Estimates site-specific evolutionary rates using HyPhy LEISR 2.5.103, with the WAG substitution model and the ColAP phylogeny.

Combines evolutionary rates with structural features.

Output: `results/rates/sites_with_structure.csv`.

Existing LEISR estimates are reused when available.

### 08_hypothesis_testing.ipynb
Tests whether ColAP sites farther from the functional center evolve faster.

Uses multiple regression adjusted for solvent accessibility and contacts, a circular-shift permutation test, and block bootstrap (2,000 replicates, block size 10).

Results:
- Distance coefficient: β = +0.221
- Partial R² = 0.193
- Permutation p = 0.0017
- Holm-adjusted p = 0.0068 (original four-test analysis)

### 09_results_visualization.ipynb
Generates publication-ready figures in `figures/`:

- ColAP phylogenetic tree
- Gene-tree vs. species-tree comparison
- H1 temperature analysis
- H2 evolutionary rate vs. distance
- H2 robustness analysis

The robustness figure uses stored results from LEISR WAG/LG, an alternative tree, and Rate4Site. These analyses are not rerun.

## External Data

Growth-temperature data and the published Colwelliaceae species tree were obtained from:

Gentilhomme A. et al. (2024). *Genomic signatures of cold adaptation in the family Colwelliaceae*.

[Publication](https://doi.org/10.1007/s00792-024-01356-0) · [GitHub repository](https://github.com/agentilhomme/Colwelliaceae-OGT-Genomic-Signatures)

