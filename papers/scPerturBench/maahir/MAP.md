# MAP: A Knowledge-driven Framework for Predicting Single-Cell Responses for Unprofiled Drugs

## Terminology

* Control-state cell embeddings: The cell before the drug is applied. 

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



## Discussion



## Methods