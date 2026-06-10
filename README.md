# 🧬 SARS-CoV-2 Spike Protein Inhibitor Screening

*Dimple Srivastava | MSc Biotechnology*
*Jamia Hamdard, Department of Virology, New Delhi | June 2026*

---

## 📌 Project Overview

This project presents a complete *virtual screening and molecular docking pipeline* for identifying potential SARS-CoV-2 spike protein inhibitors. 85 bioactive and drug compounds were screened against three SARS-CoV-2 spike protein targets using AutoDock Vina, followed by RDKit-based post-docking analysis, Lipinski drug-likeness filtering, and data visualization.

---

## 🎯 Objectives

- Prepare SARS-CoV-2 protein structures for docking analysis
- Prepare and optimize 85 ligand structures for virtual screening
- Perform molecular docking simulations using AutoDock Vina
- Analyze binding affinities and identify promising drug-like compounds
- Visualize protein–ligand complexes and binding interactions
- Post-docking analysis using RDKit and Python

---

## 🧪 Target Proteins

| PDB ID | Description |
|--------|-------------|
| *6M0J* | Spike RBD — Receptor Binding Domain |
| *6VXX* | Whole Spike Glycoprotein |
| *7DWZ* | S2 Fusion Domain |

---

## 🛠️ Tools and Software

| Tool | Purpose |
|------|---------|
| AutoDock Vina | Molecular docking simulations |
| PyMOL | Protein–ligand visualization |
| Open Babel | File format conversion and energy minimization |
| Ubuntu Linux | Docking environment |
| Bash Scripting | Automated batch docking workflows |
| Python (RDKit) | Post-docking analysis and visualization |
| Pandas / Matplotlib | Data analysis and charting |

---

## 📁 Repository Structure


sars-cov2-spike-inhibitor-analysis/
│
├── sars_cov2_inhibitor_analysis.ipynb   ← RDKit analysis notebook
├── results.csv                           ← Docking scores (all 85 compounds)
├── full_analysis.csv                     ← Compounds + molecular properties
├── top_drug_like_hits.csv                ← Top Lipinski-filtered candidates
├── sars_cov2_analysis.png                ← Analysis charts
├── top_inhibitors.png                    ← 2D structures of top compounds
├── workflow.md                           ← Docking commands and methodology
└── README.md


---

## ⚙️ Methodology

### Part 1 — Molecular Docking (Ubuntu + AutoDock Vina)

#### 1. Protein Preparation
- Retrieved protein structures from RCSB Protein Data Bank (PDB)
- Removed water molecules and unwanted ligands
- Added polar hydrogens and Gasteiger charges
- Converted to PDBQT format for AutoDock Vina

#### 2. Ligand Preparation
- Collected 85 compound SDF files from PubChem
- Format conversion: SDF → PDB → PDBQT using Open Babel
- Energy minimization using MMFF94 force field

#### 3. Molecular Docking
- Grid box defined around active site for each protein
- Exhaustiveness set to 8 for reliable results
- Batch docking of all 85 compounds per protein target
- Results extracted as binding affinity (kcal/mol)

#### 4. Visualization
- Docked complexes visualized in PyMOL
- Hydrogen bond interactions identified
- Binding pose analysis for top candidates

### Part 2 — Post-Docking Analysis (Python + RDKit)

- Loaded docking results from results.csv
- Matched compounds to SDF structure files
- Calculated molecular properties: MW, LogP, HBD, HBA, TPSA
- Applied Lipinski Rule of 5 drug-likeness filter
- Identified top hits per protein target
- Generated visualizations and saved results

---

## 💻 Key Commands Used

### Ligand Preparation (Open Babel)

bash
# Convert SDF to PDB with 3D coordinates
for f in *.sdf; do
    obabel "$f" -O "${f%.sdf}.pdb" --gen3d --addh
done

# Energy minimization
for f in *.pdb; do
    obabel "$f" -O "${f%.pdb}_min.pdb" --minimize --ff MMFF94
done

# Convert PDB to PDBQT
for f in *_min.pdb; do
    obabel "$f" -O "${f%.pdb}.pdbqt"
done


### Molecular Docking (AutoDock Vina)

bash
# Target 1: 6M0J — Spike RBD
for f in *.pdbqt; do
  vina --receptor ../6moj.pdbqt \
       --ligand "$f" \
       --center_x -47.011 --center_y -28.575 --center_z 2.208 \
       --size_x 25 --size_y 25 --size_z 25 \
       --exhaustiveness 8 \
       --out "${f%.pdbqt}_out.pdbqt"
done

# Target 2: 6VXX — Whole Spike
for f in *.pdbqt; do
  if [[ "$f" != *_out.pdbqt ]]; then
    vina --receptor ../6vxx.pdbqt \
         --ligand "$f" \
         --center_x 210 --center_y 210 --center_z 189 \
         --size_x 30 --size_y 30 --size_z 30 \
         --exhaustiveness 8 \
         --out "${f%.pdbqt}_out.pdbqt"
  fi
done

# Target 3: 7DWZ — S2 Fusion Domain
for f in *.pdbqt; do
  if [[ "$f" != *_out.pdbqt ]]; then
    vina --receptor ../7dwz.pdbqt \
         --ligand "$f" \
         --center_x 151.746 --center_y 153.256 --center_z 164.642 \
         --size_x 30 --size_y 30 --size_z 30 \
         --exhaustiveness 8 \
         --out "${f%.pdbqt}_out.pdbqt"
  fi
done


### Extract Binding Energies

bash
for f in *_out.pdbqt; do
    energy=$(grep "REMARK VINA RESULT" "$f" | head -n 1 | awk '(print $4)')
    echo "$(basename "$f" _out.pdbqt),$energy"
done > results.csv


### PyMOL Visualization

python
# Load and display docked complex
hide everything
show cartoon
show sticks, emodin_out
color red, emodin_out
zoom emodin_out

# Export image
bg_color white
ray 3000,3000
png docking_complex.png


---

## 📊 Results

![Analysis Chart](sars_cov2_analysis.png)

### Key Findings
- Screened *85 compounds* across *3 SARS-CoV-2 spike protein targets*
- Performed *automated batch docking* using Bash scripting in Ubuntu
- Identified top drug-like inhibitors per target using Lipinski Rule of 5
- Generated comparative binding affinity datasets for all targets
- Full results available in full_analysis.csv
- Top drug-like candidates in top_drug_like_hits.csv

---

## 🔑 Key Achievements

- Automated docking of 85 ligands using AutoDock Vina batch processing
- Screened against three SARS-CoV-2 spike protein targets (6M0J, 6VXX, 7DWZ)
- Built complete end-to-end virtual screening pipeline
- Post-docking RDKit analysis with Lipinski filtering
- Identified compounds with favorable binding interactions with viral targets

---

## 💡 Skills Demonstrated

Molecular Docking Structure-Based Drug Design Virtual Screening
Bioinformatics Linux Command Line Bash Scripting
Python RDKit Scientific Data Analysis
Protein–Ligand Interaction Analysis Computational Drug Discovery
AutoDock Vina PyMOL Open Babel

---

## 👩‍🔬 Author

*Dimple Srivastava*
MSc Biotechnology | J.C. Bose University (YMCA), Faridabad
Dissertation Research | Jamia Hamdard, Department of Virology, New Delhi

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/dimple-srivastava-1a15142b7)
[![GitHub](https://img.shields.io/badge/GitHub-Profile-black)](https://github.com/dimple-srivastava-bio-24)
