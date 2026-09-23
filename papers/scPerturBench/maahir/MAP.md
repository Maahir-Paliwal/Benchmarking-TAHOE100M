# MAP: A Knowledge-driven Framework for Predicting Single-Cell Responses for Unprofiled Drugs

## Terminology



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

## Results

## Discussion

## Methods