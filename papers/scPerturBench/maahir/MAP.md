# MAP: A Knowledge-driven Framework for Predicting Single-Cell Responses for Unprofiled Drugs

## Terminology

* Control-state cell embeddings: The cell before the drug is applied. 
* Gene Set Enrichment Analysis (GSEA): $\text{control cell state} + \text{held out drug} \rightarrow \text{predicted perturbation cell state}$. Then GSEA is run. it asks: Which biological pathways are predicted to be activated/suppressed by this drug?
  * For example, if MAP predicts that many genes involved in cell proliferation go down together, GSEA may identify a proliferation-related pathway as suppressed. 

## Abstract

Existing models struggle to generalize to unprofiled compounds. **MAP** is a proposed framewrk that integrates biological knowledge into cellular perturbation modelling and performs zero-shot classification for small molecules w/ scarce or absent perturbation profiles. 

They construct MAP-KG, a large knowledge graph tailored for cellular perturbation modelling. 

They propose a knowledge-driven pretraining strategy that aligns molecular structures, protein sequence features, and textual mechanistic descriptions into a unified embedding space via contrastive learning. 

This produces mechanism-aware and transferrable gene and compound embeddings. 

The resulting knowledge-informed gene and drug representations are then coupled with a pretrained single-cell foundation model to condition perturbation response prediction. 

MAP is eval'd under two zero-shot generalization regimes: 
1. unseen cell type-drug combinations 
2. unprofiled drugs 
   1. Here, it improves the top-50 DEG Pearson delta correlation by up to +13.3% and +12.2%, respectively, over the strongest baselines across three benchmarks. 

Then, they perform leave drug out experimentation, where MAP predicts coherent, mechanism-consistent programs on unprofiled candidate drugs, and prioritizes 4 of 5 approved anti-cancer drugs in A-549 (non-small cell lung cancer). 

## Introduction

Computational methods for leave-drug-out are important for accelerating early-stage drug discovery. 

Robust generalization to unprofiled compoints remains a central challenge. In many settings, representations place drugs in a latent space where proximity is learned only from co-occurence in the training atlas. They do not encode shared biological mechanisms such as shared binding modes or similar pathway modulation. 

A fix is to condition embeddings on molecular structures or targets, which partially supports OOD prediction. However, these are often incomplete. And, they do not provide an interface for integrating diverse biological evidence (e.g., SMILES strings, protein sequences, pathway membership, and free-text mechanism descriptions) into a single mechanims-aware representation. 

**Knowledge Injection**: We start by constructing MAP-KG, a perturbation oriented biomedicial knowledge graph that consolidates evidence from 14 public resources. MAP-KG links 187,089 drugs (nodes) and 22,924 genes (nodes) through 694,246 mechanistic relations (edges). 

Here are a few examples of subgraphs $G' \subseteq G$:

$\text{Drug X} \xrightarrow{\text{inhibits}} \text{Gene Y}$

$\text{Drug A} \xrightarrow{\text{activates}} \text{Gene B}$


**Model Development**: They propose a pretraining strategy that learns transferable drug and gene representations from MAP-KG bby jointly aligning multi-modal attributes and enforcing relation-level consistency. 

Then, they use the resulting knowlege encoders into a perturbation predictor built on single-cell foundation model, combining control-state cell embeddings (an embedding of the cell before the drug is applied) with knowledge-informed drug and gene embeddings to forcast perturbed transcriptomes. 


**Experiment Evaluation**
1. Zero-shot compositional generalization to unseen cell type-drug combos, MAP improves top-50 DE Pearson delta corr. by 13.3 on Tahoe-100M, 8.7% on SciPlex3, improves direction accuracy by 6.7% on the OP3 benchmark. 
2. for zero-shot prediction of unprofiled drugs, MAP improved top-50 DE Pearson delta by 12.2% (Tahoe-100M), 8.2% (OP3), and 10.4% (SciPlex3)
3. In silico screening for 1549, MAP prioritizes 4 of 5 approved anti-cancer drugs within the top 15 among 58 held out compounds. 
4. Gains in zero-shot embedding, with lower resource costs. 


## Results

### Evaluation Protocols

1. **Zero-shot compositional generalization (unseen cell line-drug combos)**:
- This is IID on combos (Drug A and Cell line B have been seen separately before)

2. **Zero-shot prediction for unprofiled drugs**: leave drug-out. To avoid leakage, drugs are excluded from MapKG

### Metrics 

1. **Pearson-Delta Correlation**: defined as the Pearson Correlation between predicted and observed perturbation-induced expression changes (log-fold changes relative to control)
2. **Direction Accuracy**: the fraction of genes whose predicted sign of regulation (up/down) relative to control matches the observed sign
3. **Perturbation discrimination Score**: quantifies whether the predicted responses remain separable across different perturbation

To mitigate single-cell measurement noise, all evaluations are performed at pseudobulk resolution by aggregating a fixed number of cells within each experimental context. 


### Baseline models

CRISP, ChemCPA, PRnet, linear baseline, trainMean (predicts perturbation effects using mean responses form the training set)

### Zero-shot Generalization Across Unseen Cell Line–Drug Combinations

Keep in mind, one model is trained on 6 cell lines. 

Q: How do they discern between cell lines in the input?
A: The difference in gene expressions of cells in different cell lines suffices as difference enough. 

- MAP significantly outperforms all baselines across metrics, achieving relative improvements over the best performing baseline. 

#### Cell-line specific performance on TAHOE

MAP experiences larger gains than CRISP and ChemCPA. CRISP and ChemCPA also improve substantially over the linear and trainMean baseline. 

#### Generalization across datasets

MAP consistently improves top-50 DEG Person delta Corr, top-50 DEG direction accuracy, and HVG perturbation on both datasets. 

but note that **HVG perturbation discrimination scores are relatively low for all methods on these two datasets**


### Zero-shot Generalization to Unprofiled Drugs

more challenging regime: prediction for *unprofiled drugs*. 

Map improves over baseline methods with +21% top-50 DEG direction accuracy, +12.2% top-50 DEG delta Pearson correlation, and +16.9% HVG perturbation discrimination score. 


### Gene-level agreement for representative drugs

To examine gene-level behaviour, they visualize the actual gene change $\delta_{real}$ vs $\delta_{predicted}$. In the ideal scenario, for each gene, we would have $\delta_{real} = \delta_{predicted}$. That would create a linear plot $y = x$. 

Consistently, Pearson Delta Corr. is higher for MAP

### Simulated in-silico drug screening

GSEA tested by curating a set of disease-relevant pathways whose activity is desirable to suppress. For each drug, they compute pathway enrichment scores from the predicted transcriptional response and aggregate them across the curated pathways to obtain a drug-level downregulation score. 

## Methods

- We may extract drug embeddings alone from this model as well as run the whole pipeline. 
- Specifics on graph construction, multimodal pretraining, knowledge guided perturbation response prediction, and dataset descriptions are available here. 