# Benchmarking Pretrained Molecular Embedding Models for Molecular Representation Learning

## Terminology

* Modality: More specifically the input modality. This is representative of the way the input is represented. AKA SMILES string (complimentary to NLP style embedding models) or 2D molecular graph (enables the usage of graph based deep learning models like GNNs)

* Isomorphic graph: Two graphs are isomorphic if they contain the exact same structural layout. 

* Constrastive Learning: teach the model which representations should be close together and which should be far apart. 

* Conformation: One particular 3D arrangement of the atoms in a molecule. The same molecule can often adopt multiple 3D shapes without changing which atoms are bonded to which. 

* Masked Language Modelling (MLM): Training regime where token(s) are masked and the model's goal is to predict the masked token. In the drug setup, the SMILES representation may mask an atom. 

* Multi Regression Task (MTR): Name is self-explanatory but the acronym is important. 

* ADMET: predicting how a drug-like molecule is likely to behave in the body. It stands for: 
  * A - Absorption: how well the drug gets into the bloodstream
  * D - Distribution: where it goes in the body
  * M - Metabolism: how the body chemically modifies it or breaks it down
  * E - Excretion: how it is eliminated
  * T - Toxicity: whether it may cause harmful effects

* Area Under the Receiver Operating Characteristic Curve (AUROC): How well does the model rank positive examples above negative examples across all possible classification thresholds? For binary classification, suppose we have 100 toxis molecules and 100 non-toxic molecules at inference. Pick one toxic molecule and one non-toxic at random. AUROC asks: Did the model give the toxic molecule a higher score? If you repeat this over all possible positive-negative pairs, AUROC is the fraction of pairs where the positive is ranked higher. 

* Region of Practical Equivalenc (ROPE): a range of effect sizes so small that we consider the difference practically unimportant. 

## Abstract

**Motivation:** Neural Nets have attracted significant interest in chemistry and molecule drug design. There is no unified, large-scale comparison of embeddings from those models in literature, making their advatanges over classical molecular fingerprints unclear. 

**Results:** Comparison of 25 models across 25 datasets. They assess models spanning various modalities, architectures, and pretraining strategies. Using a dedicated hierarchical Bayesian statistical testing model, the result: nearly all neural models show negligible or no improvement over baseline ECFP molecular fingerprint. Only the CLAMP model, also based on molecular fingerprints performs statistically significantly better than the alternatives. 

## Introduction

In different fields, notable successes include models based on Self-Supervised Learning (SSL), such as DINO and DINOv2, Sentence Transformers, and TSDAE for dense text representations in MLP. 

This study focuses on evaluating static embeddings rather the alternative approach of task-specific finetuning. This means that we compute $z_drug = f_{model}(x)$, there is no task-specific finetuning. The reason for this, is that task-specific finetuning can rescue a poor encoding model. The later architecture may overcompensate for the drug encoder network. The rationale is three-fold: 

(1) to test the knowledge encoded during pretraining, assess generalization to O.O.D. 

(2) to evaluate their use in unsupervised applications such as molecular similarity searching and clustering

(3) to address the challenge of low-data learning, common in chemistry where fine-tuning complex models would lead to overfitting 

They found that embeddings derived from GNNs generally exhibit poor performance across the tested benchmarks. 

Pretrained transformers that incorporate a strong chemical bias perform acceptably, but they do not demonstrate a definitive advantage. 

**Note:** Think of self-attention, but the network is aware of which molecules are actually bonded together, so during self-attention, if token a is chemically bonded with token b, then we inject the bias that a's representation should be somewhat reliant on b. 

### Molecular Embedding Approaches

Classical methods rely on deterministic feature extraction techniques, exemplified by molecular fingerprints. 

These embedding models can be categorized according to their input modality, architecture, and pretraining objectives. 

**Molecular Graphs**, based on atom and bond structure, provide a natural representation of chemical compounds. In most cases, models use the topological 2D graph, ignoring the spatial 3D conformation. The 2D graph retains most of the information. These models include GNNs and Graph Transformers. 

Compounds can also be serialized as **SMILES or SELFIES strings**, which are the standard formats for molecular datasets. Many NLP-inspired models use these inputs, primarily using transformer-based architectures. 

Finally, **Hybrid models** that that utilize multimodal representations or pretraining objectives. For example, incorporating inductive biases of graph-based representations in more scalable and easily trainable text-based models. 

#### Molecular Fingerprints

These are feature extraction methods based on identifying small subgraphs within a molecule and detecting their presence or counting their occurences, yielding binary and count variants. They can be broadly classified into substructural and hashed types. Substructural detect predefined patterns, such as functional groups or ring systems, typically identified by expert chemists. 

Hashed fingerprints, on the contrary, define general shapes of extracted subgraphs, convert them into numerical identifiers, and hash them using a modulo function into a fized-length output vector. 

#### Graph Neural Networks

GNNs follow a message passing framework. The initial embedding of each atom constist of elementary chemical descriptors such as element type or charge. In subsequent layers, an atom receives messages from it's neighbours and updates it's own embedding accordingly. Most molexular GNNs also include bond features, and embeddings. To obtain a whole molecule embedding, atom embeddings are aggregated using a readout function, such as channel wise average or sum. 

**Graph Isomorphism Network (GIN)** is a widely used GNN architecture. A GNN should ideally produce different embeddings for molecules whose graphs are structurally different. All GNN pretraining methods described brlow rely on a GIN backbone. 

**Context Prediction (ContextPred)** SSL pretraining for message passing GNNS. These perform an operation such as: $molecular \; graph \rightarrow GNN \; embeddings \rightarrow predict \; structural \; relationship$

**GraphMVP** conbines contrastive and generative SSL, aligning molecular 2D and 3D representations. 2D graph is encoded by GIN, 3D conformer is encoded by SchNet. For the same molecule, those two vectors form a positive pair. The model is trained to make them similar. For different models, the vectors form negative pairs, and the model is encouraged to keep them less similar. I.e, $z_{2D}^A \approx z_{3D}^A$ but $z_{2D}^A \not\approx z_{3D}^B$. 

**GraphFP** introduces graph fragmentation for pretraining, employing contrastive and predictive SSL. In contrastive learning, fragments and their constituent atoms form positive pairs, while atoms from unlreated fragments serve as negative pairs. The atom-level GNN is pretrained to generate atom-level embeddings (which can later be pooled), while a separate GNN encodes entire fragments. 

**MolR** uses contrastive learning, but incorporates chemical reaction information. Positive samples are constructed from known reactant-product pairs in reaction databases. 

**GEM** relies on predictive SSL, primarily using 3D molecular conformations. 


#### Graph Transformers

These architectures extend self-attention mechanisms to molecular graphs, replacing or augmenting the message-passing of GNNs, with global attention layers. Atoms are treated as tokens, and edges are encoded as pairwise biases injected into the attention scores, allowing information to propagate between ant two atoms within a single layer. Compared to GNNs, the design more efficiently captures long-range dependencies. 

**GROVER** is a hybrid transformer-GNN model in which the attention heads are biased by chemoinformatiocs-derived edge features. It is pretrained using two self-supervised tasks: multioutput regression (MTR), predicting eight physiochemical descriptors based on masked subgraphs, and multioutput classification (MTC), predicting the presence of functional groups. 

**MAT** introduces distance aware attention by incorporating adjacency and shortest-path kernels into the query-key dot product. Mathematically, something like: $score_{ij} = Q_i K_j^T + b(d_{ij})$ where $d_{ij}$ is the shortest path between two atoms and $d(.)$ is referred to as the kernel function. The kernel function assigns less mass to longer paths and more mass to shorter paths. 

**R-MAT** builds on MAT by introducing relative positional encodings derived from graph distances and ring memberships. This improves robustness to molecule size and graph sparsity. 

**Uni-Mol** apply graph transformers to 3D conformers using SE(3)-equivariant attention mechanisms. 


#### Text Transformers

Molecules serialized as SMILES or SELFIES can be processed directly using NLP inspired methods alongside chemistry specific biases. In encoder-decoder architectures, only the encoder is used after the pretraining to compute molecular embeddings. 

**Continuous Data-Driven Descriptors (CDDD)** is an RNN-based arch. It was trained using purely text-based translation, from random SMILES to canonical SMILES, calculated with RDKit. 

**Chemformer** is based on BART encoder-decoder transformer, pretrained using denoising SSL objective. Given a SMILES string, it is first randomized (e.g., by starting at a different atom) and some tokens are randonly masked; the model is then trained to reconstruct the original SMILES.

**MolBERT** is an encoder-only transformer based on the OG BERT architecture. It is pretrained using two objectives: 1. masked language modelling (MLM), and 2. multitask regression (MTR), in which the model predicts 200 physiochemical molecular properties computed with RDKit. This dual objective is intented to combine contextual leaning from SMILES with molecular-level inductive biases. 

**ChemBERTa** uses a RoBERTa-style encoder-only architecture. Two pretraining variants were explored solely MLM and solely MTR. MTR-only is reported to be simpler to train than MolBERT, and achieves better performance than MLM variant and MolBERT combined objective. 

**MoLFormer** another encoder-only SMILES transformer. The model is based on BERT, with modifications including linear attention and rotary position embeddings, and is pretrained using MLM. 

**SimSon** uses an encoder only backbone and is pretrained by contrastive SSL. Randomized SMILES strings of the same molecule for positive pairs, while strings from diff. molecules form negative pairs. Despite being simple, it is shown to be competitive. 

**ChemFM** built on an encoder only LLaMA backbone and is trained on GPT-style causal language modeling (CLM). The molecule embedding is derived from the output vector and EOS token. 

**ChemGPT** adopts a decoder only architecture, and is trained on SELFIES strings using CLM. The model is trained to predict the next token, but we extract hidden vectors as embeddings after training. 

**SELFormer** is based on an encoder-only RoBERTa architecture but uses SELFIES, rather than SMILES

#### Hybrid Models

**Mol2Vec** combines graph-based ECFP4 descriptors with the text-based Word2Vec approach,to learn context-aware token representations. ***Look deeper into this if you have time!!!***

**COATI** employs a multimodal encoder-decoder architecture, pretrained using CLIP-style multimodal contrastive learning. 3D molecule conformer is encoded using an E(3)-equivariant GNN, and its SMILES string with a RoFormer. The model is trained with two objectives: 1. Contrastive learning between those representations and 2. and autoregressive SMILES reconstruction using a decoder. At inference time, molecular embeddings are obtained from the SMILES encoder. 

**CLAMP** is a multimodal encoder model trained with supervised contrastive learning based on ChEMBL bioactivity assay data. Each molecule is encoded via the concat. of three molecular fingerprints (ECFP, RDKit, and MACCS) processed through a two-layer MLP, which serves at the final embedding. Assay descriptions are encoded using Latent Dirichlet Allocation (LDA). Positive pairs consist of a bioactive molecule and the description of the assay in which it is active, which negative pairs involve non-bioactive compounds. This design incorporates a bioactivity-specific inductive bias into the fingerprint-based representation. 

## Methods

Evaluated Molecular embeddings using 25 datasets, 7 from MoleculeNet and 18 from TDC. 

Focus is on classification tasks (like binary bioactivity classification) and ADMET prediction. 

Q: How does it perform this classification task? \
A: Supervised Learning with embeddings and input paired with toxicity labels for example. We specifically train a classifier for this approach. 

Specifically, for classification, they used Random Forest (RF), Logistic Regression (LR), and KNN. 

AUROC was used as an evaluation metric. It is well-suited for imbalanced datasets, common in molecular ML. 

**ECFP** count fingerprint was used as the main baseline due to its popularity and strong theoretical foundations. 


#### Bayesian Bradley-Terry Model 

Since this benchmarking problem concerns multiple models on multiple datasets, then aggregated metrics alone like AUROC and mean-rank are insufficient. Instead, Bayesian testing is recommended for this settings. Bayesian procedures return the posterior probability that one model is better another and all explicit probability statements about practical equivalence. Bayesian tests model $P(i \succ j)$, the posterior predictive probability: "the probability that model i beats model j". Further, the notion of ROPE allows returning a tie. 

Specifically, this paper uses the hierarchical Bayesian Bradley-Terry (BBT) model:

#### Bradley-Terry (BBT) model

**Pairwise Win Counts**: Let $W_{ij}$ be the number of datasets on which model i outperforms model j in a given metric, e.g., AUROC. If this difference is too small, e.g., 1% these models are deemed practically equivalent. We denote the total number of comparisons $N_{ij} = W_{ij} + W_{ji}$

**Likelihood**: The win counts are modeled with a binomial Bradley-Terry Likelihood

**Continue looking into this for further understanding of Bayesian Testing!!! This information is however unnecessary in the comprehension of the results.**


## Results 

**Only four models outperformed the ECFP fingerprint**. CLAMP performed the best, R-MAT, MolBERT, and ChemBERTa use distinct architectures and training strategies. Noted that MTR variant vastly outperformed the MLM variant in all cases. 

Among the worst performing models, most are message-passing GNNs. SELFIES-based text transformers also ranked among the weakest performers

CLAMP
| Model | Mean rank $\downarrow$ | Mean AUROC $\uparrow$ |
| :--- | :--- | :--- |
| **CLAMP** | 5.40 | 82.55% |
| **R-MAT** | 6.08 | 80.83% |
| **MolBERT** | 6.92 | 80.51% |
| **ChemBERTa** | 7.32 | 79.99% |
| **ECFP** | 7.52 | 79.89% |

Under BBT, R-MAT, MolBERT, ChemBERTa, CDDD, Atom Pair, MAT are equivalent. 

These results show that only the CLAMP model is statistically significantly better than ECFP. 

**Inter-model win rates** Figure 2 shows win rates between models. Models are ranked by their number of wins against the ECFP baseline. To exclude signficant differences, they treated AUROC differences below 0.01% as ties

On 5 datasets, no models outperformed ECFP. 

## Discussion

1. Always use ECFP count fingerprint as a baseline, paired with a tree-based classifier. Ideally, test other fingerprints like Atom Pair. 
2. Consider CLAMP model
3. Other notable models are R-MAT, MolBERT, ChemBERTa, and CDDD