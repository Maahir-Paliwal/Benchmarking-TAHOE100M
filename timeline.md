# Timeline for Project

Semester 1: Benchmarking previous paper
Semester 2: Proposing a new architecture

## Semester 1 Timeline:

**For models:**
From ScPerturBench:
* CPA 
* ChemCPA
* Biolord 
* PRnet
* CycleCDR
* BaseMLP

From MAP paper:
* Crisp 
* State 
* CycleCDR 

Drop two of the oldest ones

**For Drug Embeddings, Here are some suggested:**
1. CLAMP
2. ChemBERTA
3. MolBert
4. The best as reported in embeddings paper

For drug embeddings, we should select based on the following criteria: \
The best, different architectures, what Benchmarking papers are using, or drug signature based on different cell line.

That leaves us with 7 x 4 = 28 pipelines to implement.

Our informal idealized pipeline: \
$drug\_representation \rightarrow f(.) + untreated\_cell = proposed\_generated\_cell$


| Date | Event | Description |
| :--- | :--- | :--- |
| **Sep 16th - Sep 22nd** | Background Research | Read ScPerturBench + Background Knowledge |
| **Sep 23rd - Sep 29th** | Discover + Research Metrics | Search for other similar benchmarking papers + research / understand the evaluation metrics we are going for|
| **Sep 30th - Oct 6th** | Pre-process TAHOE100M dataset | Filtering for HVGs, running one model on the data we have, with one embedding, get some metrics |
| **Oct 7 - Dec 2nd** | Implement models | Implement the proposed existing architectures alongside CLAMP + One other drug embedding |
| **Dec 3rd - Dec 9th** | Start Report | Compile a first pass of the final report for the benchmarking study |
| **Dec 10th - Jan 6th** | Finalize Report | Revise Report, make a finalized submission to the ISMB/ECCB conference |

4-5 multipanel figures


## Semester 2 Timeline:

