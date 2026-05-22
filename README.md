
# Multi-Omics Constraint-Based Modeling of Environmental Stress Responses in *Synechocystis* sp. PCC 6803

---

## 1. Project Title
**Multi-Omics Constraint-Based Modeling of Environmental Stress Responses in *Synechocystis* sp. PCC 6803**  
*An Integrated Computational Framework for Transcriptomic Analysis and Transcript-Constrained Flux Balance Analysis (FBA) of Photosynthetic Adaptation*

---

## 2. Project Overview
This repository contains a complete computational systems biology workflow designed to investigate the metabolic plasticity of the model cyanobacterium ***Synechocystis* sp. PCC 6803** under environmental perturbations. By integrating transcriptomic differential gene expression (DGE) datasets with a custom stoichiometric core metabolic model (`iSynCore`), this project implements an expression-based flux constraint pipeline (inspired by the **E-Flux** algorithm) to predict physiological flux distributions under four distinct stress conditions: **nitrogen limitation, light stress, carbon limitation, and oxidative stress**. 

The workflow is implemented in Python and optimized for execution in Google Colab, leveraging industry-standard libraries such as `COBRApy` for constraint-based modeling, `scikit-learn` for dimensionality reduction, `NetworkX` for graph theory, and `SciPy`/`Statsmodels` for statistical analysis.

---

## 3. Biological Background
Cyanobacteria are the only prokaryotes capable of performing oxygenic photosynthesis. They are central to global biogeochemical cycles and serve as promising cell factories for sustainable chemical production (e.g., biofuels, bioplastics).

*   ***Synechocystis* sp. PCC 6803** is the premier model organism for cyanobacterial research. It features a compact genome, robust genetics, and a highly versatile metabolic network capable of autotrophic, mixotrophic, and heterotrophic growth.
*   **Environmental stress responses** dictate the survival and metabolic yield of cyanobacteria in both natural habitats and industrial photobioreactors. Fluctuations in nitrogen, light, carbon dioxide, and exposure to reactive oxygen species (ROS) trigger immediate transcriptional remodeling.
*   **Photosynthetic organisms** face a unique challenge: they must coordinate light harvesting (energy capture) with carbon and nitrogen assimilation (energy consumption). Imbalances lead to photoinhibition, cellular damage, or metabolic arrest. Understanding this coordination requires systems-level analysis, as individual components (e.g., a single enzyme or transcript) are highly interconnected.

---

## 4. Core Research Question
The project is built around answering a central biological question:

> **"How do environmental stress conditions reshape transcriptional regulation and metabolic flux distributions in cyanobacteria?"**

Specifically, we investigate the regulatory and metabolic mechanisms that coordinate light reactions, carbon fixation (Calvin-Benson-Bassham cycle), nitrogen assimilation (GS-GOGAT pathway), and maintenance energy demands under nutrient and physical stress.

---

## 5. Objectives of the Study
1.  **Quantify Transcriptional Signatures:** Process raw sequencing counts across stress conditions to identify differentially expressed genes (DEGs).
2.  **Evaluate GPR Associations:** Map statistical expression changes to metabolic reactions using boolean Gene-Protein-Reaction (GPR) logic.
3.  **Implement E-Flux Integration:** Scale metabolic reaction capacities ($V_{max}$) proportionally to normalized transcript levels.
4.  **Simulate Stress Phenotypes:** Execute Parsimonious Flux Balance Analysis (pFBA) and Flux Variability Analysis (FVA) to predict metabolic flux redistribution.
5.  **Examine Network Topology:** Build a reaction-adjacency graph to determine how topological network properties (e.g., centrality hubs) align with physiological stress responses.

---

## 6. Why This Project Matters
### The Limits of Single-Layer Analysis
*   **Transcriptomics alone** cannot predict metabolic flux. Changes in mRNA abundance do not translate linearly to changes in reaction rates due to post-transcriptional modifications, translational regulation, enzyme kinetics ($K_m, k_{cat}$), substrate availability, and thermodynamic constraints.
*   **Metabolic modeling alone (standard FBA)** lack regulatory constraints. Standard FBA assumes the cell operates under optimal, unconstrained enzyme capacities, which often leads to unrealistic predictions (such as active pathways that are transcriptionally silenced by the cell).

### The Power of Integration
By combining the two layers:
$$\text{Transcriptomics (Regulation)} \quad \xrightarrow{\text{GPR Logic}} \quad \text{Constraint Scaling} \quad \xrightarrow{\text{Stoichiometric FBA}} \quad \text{Phenotypic Fluxes}$$

We restrict the mathematical solution space of the metabolic network using real biological constraints. This integrated approach yields predictions that are both stoichiometrically feasible and transcriptionally consistent.

---

## 7. Systems Biology Strategy Used
This study applies a multi-step integration strategy:
1.  **Bioinformatics Layer:** We process raw RNA-seq counts, perform normalization, stabilize variance, and run differential expression testing using Welch's t-test with a Benjamini-Hochberg FDR correction.
2.  ** stoichiometric modeling Layer:** We construct a stoichiometrically balanced metabolic network representing photosystem activity, carbon fixation, glycolysis, the TCA cycle branch, nitrogen assimilation, and maintenance.
3.  **Integration Layer:** We evaluate GPR expressions using boolean logic ($\min$ for complexes, $\max$ for isozymes) to derive reaction expression scores, scaling reaction bounds based on their expression ratio relative to the control.
4.  **Optimization Layer:** We resolve alternative optimal solutions by minimizing total enzyme usage (pFBA) and calculate pathway flexibility ranges (FVA).
5.  **Network Theory Layer:** We analyze the topological features of the carbon-nitrogen network using graph theory metrics.

---

## 8. Computational Workflow

```
┌────────────────────────────────────────────────────────┐
│               1. Raw Count Data Generation              │
│       - Simulated biological replicates (15 samples)   │
│       - Controls vs. N-lim, Light, C-lim, Oxidative     │
└───────────────────────────┬────────────────────────────┘
                            ▼
┌────────────────────────────────────────────────────────┐
│               2. Preprocessing & Normalization          │
│       - Filter low-expression genes                    │
│       - Counts Per Million (CPM) normalization         │
│       - Log2(CPM + 1) variance stabilization           │
└───────────────────────────┬────────────────────────────┘
                            ▼
┌────────────────────────────────────────────────────────┐
│            3. Differential Expression Analysis          │
│       - Welch's t-test (Control vs. Stress)            │
│       - Benjamini-Hochberg FDR p-value correction     │
│       - Volcano & MA Plot QC visualizations            │
└───────────────────────────┬────────────────────────────┘
                            ▼
┌────────────────────────────────────────────────────────┐
│             4. Stoichiometric Model Building           │
│       - Build iSynCore model in COBRApy                │
│       - Set GPR rules for core metabolic pathways     │
└───────────────────────────┬────────────────────────────┘
                            ▼
┌────────────────────────────────────────────────────────┐
│              5. E-Flux Multi-Omics Integration         │
│       - Evaluate GPR rules using mean CPM values      │
│       - Scale reaction upper/lower bounds by ratio     │
│       - Apply environmental limits (boundary flux)     │
└───────────────────────────┬────────────────────────────┘
                            ▼
┌────────────────────────────────────────────────────────┐
│                6. FBA / pFBA / FVA Runs                 │
│       - Optimize biomass growth rate                   │
│       - Minimize overall enzyme flux sum (pFBA)        │
│       - Perform Flux Variability Analysis (95% growth) │
└───────────────────────────┬────────────────────────────┘
                            ▼
┌────────────────────────────────────────────────────────┐
│           7. Network Topology & Graph Analysis         │
│       - Construct reaction-adjacency graph             │
│       - Calculate Degree Centrality for key hubs       │
└────────────────────────────────────────────────────────┘
```

---

## 9. Transcriptomics Analysis Pipeline
*   **Low-Expression Filtering:** Excludes unexpressed genes to reduce statistical noise.
*   **CPM Normalization:** Normalizes for varying sequencing depth across samples:
    $$\text{CPM}_i = \frac{\text{Reads}_i}{\sum \text{Reads}} \times 10^6$$
*   **Variance Stabilization:** Converts counts to a logarithmic scale ($\log_2(\text{CPM} + 1)$) to make biological variance independent of average transcript abundance.
*   **Quality Control PCA:** Projects high-dimensional gene expression profiles onto Principal Component space to confirm replicate clustering and treatments separation.
*   **Differential Testing:** Performs a Welch's t-test for each gene between control and stress replicates.
*   **FDR Correction:** Applies the Benjamini-Hochberg procedure to correct for false positives in multiple testing:
    $$\text{FDR}_i = p_i \cdot \frac{N}{\text{rank}(p_i)}$$

---

## 10. Genome-Scale Metabolic Modeling Workflow
Metabolism is modeled as a set of mass-balanced chemical reactions under steady-state assumptions:
$$S \cdot v = 0$$

Where $S$ is the stoichiometric matrix ($m$ metabolites $\times n$ reactions), and $v$ is the vector of flux rates (mmol/gDW/h). 

The custom model `iSynCore` represents the core metabolic engine of *Synechocystis*:
1.  **Light Reactions:** Photosystems split water, absorb photons, and generate ATP and NADPH.
2.  **Cyclic Electron Flow:** Dissipates energy and generates ATP independently of NADPH production.
3.  **RuBisCO & Calvin Cycle:** Converts CO₂ into G3P using ATP and NADPH.
4.  **Glycolysis:** Catabolizes G3P to Pyruvate.
5.  **TCA Branch (Isocitrate Dehydrogenase):** Converts Pyruvate and G3P to alpha-ketoglutarate (AKG).
6.  **GS-GOGAT Pathway:** Incorporates ammonium into glutamate using ATP and NADPH.
7.  **Biomass Reaction:** Represents growth by consuming G3P, Glutamate, and ATP.

---

## 11. Multi-Omics Integration Strategy
Integrating gene expression levels into the metabolic network requires evaluating **Gene-Protein-Reaction (GPR)** rules:
*   **AND Rules (Protein Complexes):** For reactions catalyzed by multi-subunit enzyme complexes (such as RuBisCO *rbcL-rbcS*), the expression score is set to the minimum value of its components:
    $$E_{\text{reaction}} = \min(e_1, e_2, \dots, e_k)$$
*   **OR Rules (Isozymes):** For reactions catalyzed by alternative, redundant enzymes, the expression score is set to the maximum value:
    $$E_{\text{reaction}} = \max(e_1, e_2, \dots, e_k)$$
*   **Boundary Scaling:** Using the control condition as a reference, the upper bound ($v_{ub}$) of each enzymatic reaction is scaled:
    $$v_{ub, \text{stress}} = v_{ub, \text{default}} \times \max\left(0.01, \min\left(3.0, \frac{E_{\text{stress}}}{E_{\text{control}}}\right)\right)$$

---

## 12. Environmental Stress Conditions Modeled

| Experimental State | Photon Uptake (`EX_photon`) | Nitrogen Uptake (`EX_nh4`) | Carbon Uptake (`EX_co2`) | Cellular Maintenance |
| :--- | :--- | :--- | :--- | :--- |
| **Control** | $100.0$ (Saturating) | $[-100.0, 100.0]$ (Excess) | $[-100.0, 100.0]$ (Excess) | Baseline (`ATPM` $\ge 1.0$) |
| **Nitrogen Limitation** | $100.0$ (Saturating) | $[-0.1, 100.0]$ (Restricted) | $[-100.0, 100.0]$ (Excess) | Baseline (`ATPM` $\ge 1.0$) |
| **Light Stress** | $5.0$ (Low Light) | $[-100.0, 100.0]$ (Excess) | $[-100.0, 100.0]$ (Excess) | Baseline (`ATPM` $\ge 1.0$) |
| **Carbon Limitation** | $100.0$ (Saturating) | $[-100.0, 100.0]$ (Excess) | $[-0.15, 100.0]$ (Restricted) | Baseline (`ATPM` $\ge 1.0$) |
| **Oxidative Stress** | $100.0$ (Saturating) | $[-100.0, 100.0]$ (Excess) | $[-100.0, 100.0]$ (Excess) | High ROS removal (`ROS_DETOX` $\ge 15.0$) |

---

## 13. Flux Balance Analysis (FBA) Explanation
FBA optimizes a cellular objective function (in our case, maximizing the growth rate `BIOMASS_SYN`) subject to stoichiometric mass balance and flux constraints:

$$\max \quad v_{\text{BIOMASS\_SYN}}$$
$$\text{subject to} \quad \sum_{j} S_{ij} v_j = 0 \quad \forall i$$
$$v_{lb, j} \le v_j \le v_{ub, j} \quad \forall j$$

FBA assumes the cell operates under steady-state conditions, meaning intracellular metabolite concentrations remain constant over the simulation timeframe.

---

## 14. Transcriptome-Constrained Metabolic Modeling Explanation
### Parsimonious FBA (pFBA)
FBA can yield multiple alternative optimal flux distributions that produce the same maximum growth rate. To resolve this, **Parsimonious FBA** adds a secondary objective: it minimizes the sum of all absolute fluxes:

$$\min \quad \sum_{j} |v_j|$$
$$\text{subject to} \quad v_{\text{BIOMASS\_SYN}} = v_{\text{growth, max}}$$

This represents the biological assumption that cells minimize overall protein synthesis and enzymatic overhead, helping resolve alternative optima.

### Flux Variability Analysis (FVA)
FVA identifies the flexibility of the network by calculating the minimum and maximum allowable flux for each reaction while maintaining a user-defined fraction of optimal growth (e.g., 95% of maximum growth):

$$\min / \max \quad v_k$$
$$\text{subject to} \quad S \cdot v = 0$$
$$v_{\text{BIOMASS\_SYN}} \ge 0.95 \cdot v_{\text{growth, max}}$$
$$v_{lb} \le v \le v_{ub}$$

If a reaction's range is narrow, the pathway is considered rigid; a wide range indicates pathway flexibility.

---

## 15. Key Computational Methods Used

| Method | Tool / Library | Biological Problem Solved | Computational Complementarity |
| :--- | :--- | :--- | :--- |
| **Normalization & Transformation** | `Pandas` / `NumPy` | Corrects for sequencing depth and stabilizes variance. | Prepares raw count matrices for statistical analysis and modeling. |
| **Dimensionality Reduction (PCA)** | `Scikit-Learn` | Detects batch effects and groups biological replicates. | Verifies the consistency of experimental replicates before differential testing. |
| **Statistical Differential Expression** | `SciPy` / `Statsmodels` | Identifies significant fold changes in transcripts. | Identifies stress-responsive genes and provides scaling factors for modeling. |
| **Constraint-Based Optimization** | `COBRApy` | Solves stoichiometric equations to predict metabolic flux. | Simulates metabolic states using stoichiometric constraints. |
| **Network Centrality Analysis** | `NetworkX` | Identifies central reaction hubs in metabolic pathways. | Connects topological properties of the metabolic network with simulated fluxes. |

---

## 16. Data Sources
The dataset format is based on typical RNA-Seq count profiles for *Synechocystis* sp. PCC 6803:
*   **Locus Tags:** Real *Synechocystis* genomic identifiers (e.g., `sll1502` for *psbA1*, `slr0288` for *glnA*, `slr0009` for *rbcL*).
*   **Sample Structure:** 15 samples consisting of 3 biological replicates across 5 environmental conditions.
*   **Noise Modeling:** Normal distribution noise applied in log-space ($\sigma = 0.12$) to replicate standard experimental variation.

---

## 17. Folder Structure
```
├── tables/
│   ├── raw_counts.csv                  # Raw transcript count matrix (13 genes x 15 samples)
│   ├── metadata.csv                    # Sample mapping table (SampleID -> Condition, Replicate)
│   ├── normalized_log_expression.csv   # Log2(CPM + 1) transformed values
│   └── nitrogen_degs.csv               # Welch's t-test and FDR results
├── results/
│   ├── fba_flux_distributions.csv      # Standard FBA outputs for all conditions
│   ├── pfba_flux_distributions.csv     # Parsimonious FBA outputs for all conditions
│   ├── simulated_stress_phenotypes.csv # Comparative rates for key metabolic hubs
│   └── network_topology_properties.csv # Degree centrality and pathway metrics
├── plots/
│   ├── expression_distributions.png    # Density QC before/after normalization
│   ├── pca_qc.png                      # Principal Component analysis sample clustering
│   ├── sample_correlation.png          # Replicate Pearson correlation heatmap
│   ├── volcano_nitrogen.png            # DEG volcano plot (LFC vs. -log10 q-value)
│   ├── ma_plot_nitrogen.png            # DEG MA plot (Mean vs. LFC)
│   ├── metabolic_flux_heatmap_pfba.png # Comparative pathway flux heatmap (pFBA)
│   └── metabolic_network_topology.png  # Graph visualization of network hubs
└── main_workflow.ipynb                 # Executable Google Colab Notebook
```

---

## 18. Installation Instructions
To run this pipeline locally, install the required packages:

```bash
# Clone the repository
git clone https://github.com/your-username/synechocystis-multiomics.git
cd synechocystis-multiomics

# Install dependencies using pip
pip install -r requirements.txt
```

### `requirements.txt`
```text
cobra>=0.31.0
pandas>=2.0.0
numpy>=1.24.0
scipy>=1.10.0
statsmodels>=0.14.0
scikit-learn>=1.2.0
networkx>=3.0
matplotlib>=3.7.0
seaborn>=0.12.0
```

---

## 19. Google Colab Usage Instructions
The workflow is designed to run in a single Google Colab session:
1.  Open [Google Colab](https://colab.research.google.com).
2.  Upload `main_workflow.ipynb`.
3.  Run the first cell to install `COBRApy` inside the temporary environment:
    ```python
    !pip install -q cobra
    ```
4.  Execute cells sequentially. The GLPK (GNU Linear Programming Kit) solver is precompiled within the COBRApy installation, meaning no external compilers are required.
5.  All plots and data tables will be generated and saved in the Colab virtual filesystem under `/content/results/`, `/content/plots/`, and `/content/tables/`.

---

## 20. Results and Biological Interpretation

### Summary of Metabolic Flux Redistribution (pFBA Simulation)

| Reaction | Control | Nitrogen Lim. | Light Stress | Carbon Lim. | Oxidative Stress |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **`BIOMASS_SYN`** (Growth) | $3.6636$ | $0.4000$ | $0.1121$ | $0.1200$ | $2.5421$ |
| **`L_RXN`** (Photosynthesis) | $5.0374$ | $0.5500$ | $0.1542$ | $0.1650$ | $3.4953$ |
| **`CYCLIC_LIGHT`** (Cyclic Flow) | $39.9252$ | $5.2500$ | $2.1916$ | $2.2725$ | $43.0093$ |
| **`RuBisCO`** (Carbon Fixation) | $5.4953$ | $0.6000$ | $0.1682$ | $0.1800$ | $3.8131$ |
| **`GS`** (Nitrogen Assimilation) | $0.9159$ | $0.1000$ | $0.0280$ | $0.0300$ | $0.6355$ |
| **`ROS_DETOX`** (Detoxification) | $0.0000$ | $0.0000$ | $0.0000$ | $0.0000$ | $15.0000$ |

### Biological Interpretation
*   **The Nitrogen Starvation Chlorosis Phenotype:** 
    Under nitrogen limitation, biomass synthesis drops by **90%** ($0.40$ vs. $3.66$ h⁻¹). The cell downregulates photosynthesis (`L_RXN` drops from $5.04$ to $0.55$) and carbon fixation (`RuBisCO` drops to $0.60$) to protect itself from light damage while nitrogen is unavailable.
*   **Cyclic Light Adaptation:** 
    Under nitrogen limitation, the required `CYCLIC_LIGHT` flux is **$5.25$ mmol/gDW/h**. Since linear photophosphorylation (`L_RXN`) decreases to avoid excess NADPH production, the cell relies on cyclic electron flow to meet its maintenance ATP requirements.
*   **Oxidative Stress Trade-off:** 
    To survive oxidative stress, the cell must run `ROS_DETOX` at a high rate ($15.0$). This drains ATP, reducing the growth rate to $2.54$ h⁻¹ (a **30% reduction** compared to control), representing the metabolic cost of cell repair.

---

## 21. Systems-Level Insights Obtained
1.  **Resolving Alternative Optima:** Standard FBA yields a wide range of possible cyclic light fluxes ($5.25$ to $48.90$ mmol/gDW/h under N_lim) due to excess photons. pFBA resolves this redundancy to identify the parsimonious state ($5.25$).
2.  **C-N Metabolic Coupling:** The topology map highlights the role of Isocitrate Dehydrogenase (`AKG_GEN`). It acts as a topological hub connecting carbon and nitrogen pathways, showing how the cell balances these processes under nutrient stress.

---

## 22. Visualizations Generated
1.  **Expression Density Plot:** Shows the normalization of read counts from skewed distributions to log-normal curves.
2.  **PCA Projection Plot:** Confirms experimental consistency by showing replicate samples clustering by treatment group.
3.  **Correlation Heatmap:** Shows high Pearson correlation ($r > 0.98$) within replicate groups and distinct expression profiles between treatments.
4.  **Volcano Plot:** Highlights statistically significant up- and down-regulated genes.
5.  **MA Plot:** Checks for systematic intensity-dependent biases in gene expression.
6.  **Parsimonious Flux Heatmap:** Compares predicted flux distributions across treatments.
7.  **Metabolic Network Graph:** Displays reaction connectivity, showing how pathways are topologically integrated.

---

## 23. Reproducibility and Workflow Design
The reproducibility of this pipeline is supported by:
*   **Fixed Random Seed:** Using a fixed seed (`np.random.seed(42)`) ensures identical data generation and statistical testing across runs.
*   **Software Version Audit:** The workflow logs the exact package versions used (e.g., `COBRApy`, `SciPy`, `Scikit-Learn`), helping prevent package conflicts in future environments.

---

## 24. Limitations of the Study
*   **Static stoichiometric modeling:** FBA assumes steady-state conditions, meaning it does not capture short-term dynamic changes in transcript or metabolite levels.
*   **Linear Transcript-to-Flux Scaling:** The model assumes that enzyme capacity scales linearly with transcript levels. This assumption does not account for post-translational regulations or enzyme kinetics.

---

## 25. Future Improvements
*   **Enzyme-Constrained FBA (ecFBA):** Using proteomics data to scale bounds based on actual enzyme concentration ($V_{max} = k_{cat} \cdot [E]$).
*   **Dynamic FBA (dFBA):** Simulating how metabolic fluxes change over time in a batch culture as nutrients are depleted.
*   **Kinetic Integration:** Adding Michaelis-Menten kinetic equations to capture enzyme-substrate interactions.

---

## 26. Synthetic Biology and Metabolic Engineering Relevance
*   **Pathway Engineering:** Identifying metabolic bottlenecks under different conditions helps select targets for genetic engineering.
*   **Maximizing Biosynthetic Yields:** The model can be used to predict which gene knockouts or overexpressions would optimize production yields (e.g., of biofuels or bioplastics) under specific cultivation conditions.

---

## 27. Skills Demonstrated Through This Project
*   **Constraint-Based Modeling:** Stoichiometric matrix representation, FBA, pFBA, and FVA using COBRApy.
*   **Bioinformatics & Stats:** Normalization (CPM), variance stabilization, PCA, Welch's t-test, and Benjamini-Hochberg FDR correction.
*   **Network Science:** Graph construction, topological analysis, and degree centrality calculation using NetworkX.
*   **Scientific Python:** Data handling with Pandas/NumPy and data visualization with Matplotlib/Seaborn.

---

## 28. References / Scientific Inspiration
*   **E-Flux Algorithm:** Colijn et al., *PLoS Computational Biology*, 2009.
*   ***Synechocystis* GEM iJN678:** Nogales et al., *BMC Systems Biology*, 2012.
*   **Parsimonious FBA:** Lewis et al., *Molecular Systems Biology*, 2010.

---

## 29. Author
**Shagun Srivastava**  
*Early-Stage Computational Systems Biology Researcher | Bioinformatics Engineer*  
For inquiries or collaborations, contact me via shagun.srivastava.work@gmail.com.
