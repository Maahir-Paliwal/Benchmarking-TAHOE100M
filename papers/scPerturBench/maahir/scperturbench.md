# Benchmarking Algorithms for Generalizable Single-Cell Perturbation Response Prediction

**Disclaimer**: This article benchmarks cellular context generalization as well as perturbation generalization. In the scope of this project, we will only be evaluating perturbation generalization. 

## Terminology
* Single-cell: The goal is to keep track of which RNA molecules come from which cell. This gives us a separate expression profile for each cell. 

* Foundation models: In this context, it is a large model that is trained on a large collection of single-cell gene-expression data that can be reused for downstream tasks. It encodes general patterns about cells and genes.

* cellular context generalization scenario: training a model on data measured in a set of cellular contexts (such as cell lines) for a specific perturbation. Then, using that model to predict the effects of the same perturbation in a different cell line. 

* perturbation generalization scenario: Train a model on a single cellular context, with a set of perturbations, then use the model to predict the effects of unseen perturbations.

* technical variation: scRNA-seq is usually noisy. To evaluate robustness to this noise, we can simulate varying gene expression values and introduce sparsity. 

* IID : Independent Identically Distributed. This means that training and test examples come from the same distribution. An example of this would be if cell line A saw drug A during training and cell line B saw drug B at training, but at inference cell line A and drug B were evaluated. Both have been seen during training.

* OOD : Out Of Distribution. The test data contains some kind of condition that the model did not see during training. An example of this would be the same cell line at inference, but a completely different drug.

## Abstract

Single-cell perturbation investigations enable thorough invesigation of gene functions and regulatory networks. However, combinatorically, it is near impossible to perform all of these experiments. Thus, computational methods, including foundational models have been proposed

The paper benchmarks 27 methods for single-cell perturbation response prediction, evaluated across 29 datasets using 6 complimentary performance metrics. 

The paper concludes that there is a need for cellular context embedding approaches to enhance generalizatin of perturbation effect prediction in single-cell research. 

## Main

Computational methods of perturbation effects modelling fall under two main categories:

1. Cellular context generalization scenario (leave cell line out)
2. Perturbation generalization scenario (leave drug out)

Foundation models have been on the rise in recent years. Models such as scGPT and scFoundation, have demonstrated the ability to predict generalizations across (1) and (2). 

However, recent concerns have voiced that simple linear models with straightforward assumptions may still outperform these complex methods. 

In the perturbation generalization scenario we compare **18 methods across 17 datasets** to investigate generalizability to unseen perturbations

**Evaluation metrics:**
* Mean Squared Error (MSE) (population or instance level)
* Pearson correlation coefficient delta (PCC-delta) (population or instance level)
* E-distance (always population level metric)
* Wasserstein Distance (always population level metric)
* KL-Divergence (always population level metric)
* Commonly Differentially Expressed Genes I (always population level metric)

Brief takeaway in the perturbation generalization scenario: 

Baseline models tend to perform better on smaller datasets. Meanwhile, foundation models tend to perform better on larger training sets. 


## Results 

### Methods 

In the perturbation generalization scenario 14 methods were examined:

AttentionPert, biolord, CPA, GEARS, GenePert, linearModel, scFoundation, scGPT, chemCPA, scouter, scELMo, GeneCompass, PRnet, cycleGDR. 

Additionally, 4 baseline models were tested:

baseReg, baseMLP, baseControl, trainMean

It should be noted that not all perturbations are chemical perturbations (drugs). Perturbations fall into 2 subclasses here: 
1. Genetic (like gene knockout)
2. Chemical (like adding a drug)

There were 4 datasets used on chemical perturbations: \
Sciplex3-A549 (single perturbation), Sciplex3-MCF7 (single perturbation), Sciplex3-K562 (single perturbation), Sciplex3-comb (combination of perturbations)

**Evaluation Metrics and Assessment of Methods**

They conducted an extensive literature survey covering 28 existing tools and benchmarking studies in the field. They landed on **three population-average metrics** and **three population-distribution metrics**

three population-average metrics:
1. MSE
2. E-distance: primarily collection difference in average effects between predicted and actual gene expression profiles
3. PCC-delta,\:  has better directional consistency than PCC

three population-distribution metrics:
1. Wasserstein distance: quantify the discrepancy between predicted and actual distributions of single-cell profiles, this is essential for capturing the heterogeneity and higher-order statistical properties.
2. KL-divergence: quantify the discrepancy between predicted and actual distributions of single-cell profiles, this is essential for capturing the heterogeneity and higher-order statistical properties.
3. Common-DEGs: the accuracy of the predicted most differentially expressed genes 

This paper primarily used MSE and PCC-delta for model performance. PCC-delta was emphasized due to its bounded range between -1 to 1, which is intuitive for assessment. 

metrics were calculated in two ways: 
1. for all genes
2. for 100 most differentially expressed genes ranked by absolute effect sizes 

The authors also evaluate robustness to technical variation. This is because scRNA-seq can be noisy. They simulated varying gene expression data and introducted some sparsity (zero'd out some gene expressions) to evaluate model robustness under challenging conditions

Additionally, the authors evaluated scalability for practical application. This was done in terms of runtime and CPU usage. 


**Benchmarking Analysis of Perturbation Generalization Scenario**

Evaluated the performance of 11 existing algo's for **Genetic Perturbation Effect Prediction**. For our study, the analysis of these is not necessary, we are more concerned with **Chemical Perturbation Effect Prediction**

In the **Chemical Perturbation Effect Prediction** case:

biolord, chemCPA, and CycleCDR are limited to single-perturbation datasets.

The deep learning models in the chemical perturbation generalization scenario can be found in **Fig. 5**: CPA, biolord, chemCPA, PRnet, CycleCDR, baseMLP. 

However, note that baseMLP is one of the "base models". 

chemCPA, performed the best on all metrics except for E-distance and Common DEGs. All models performed relatively poorly on Common DEGs. 

ChemCPA did not perform much robusness from the MSE metric in terms of sparsity and noise. However, it still placed top-3 in terms of CV PCC-delta. 

None of the algorithms exhibited OOM issues, all of their runtimes were within the acceptable limits. 

## Discussion

chemCPA is the preferred choice in the single-perturbation effect prediction scenario. 

baseReg baseline model achieves the highest accuracy in chemical combined-perturbation effect prediction. 


For designing future algorithms: 

models exhibited weaker performance on population-distribution metrics in comparison to population-average metrics. Typically, DL tools focus more on optimizing loss functions tat focus more on population-average metrics.

All current methods exhibited poor generalizability, especially when predicting perturbation effects in new cellular contexts. The authors recommend incorporating prior knowledge through cell-line embeddings, which can improve more generalizability. 

## Methods 

**Data Pre-Processing**: 

(1) Cell Filtering: Cells were excluded is they expressed fewer than 200 genes or exhibited a high mitochondrial gene fraction exceeding 10%. 

(2) For perturbations affecting over 2,000 cells. The authors used a random selection method to include only 2,000 cells for computational purposes. 

(3) Gene filtering: Genes that were expressed in fewer than 3 cells were removed. In the context of perturbation generalization, genes that did not have embedding from scGPT and scFoundation were excluded for each dataset. To balance the computational complexity, we constrained the dataset to the 5,000 genes exhibiting highest variability. HVGs are selected only using the training set, to avoid leakage. 

(4) Data Normalization. Used the global scaling normalization technique from scanpy, which standardizes gene expression levels in each by scaling them to a total count of 10,000. This is followed by a logarithmic transformation to stabilize variance and improve the variance and improve interpretability of the data. 


## Drug Embeddings Techniques

**TODO**