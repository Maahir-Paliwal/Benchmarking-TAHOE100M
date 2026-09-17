# Timeline for Project

Semester 1: Benchmarking previous paper
Semester 2: Proposing a new architecture

## Semester 1 Timeline:

Existing leave-drug-out architectures based on ScPerturBench: 
1. CPA
2. ChemCPA
3. Biolord
4. PRnet
5. CycleCDR
6. BaseMLP

These are from the MAP paper:
7. Crisp
8. State
9. MAP 

Drop two of the oldest ones
Existing 13 drug embedding models we want to ablate:
1. CLAMP 
2. ??? 
3. ??? 

Select 3 meaningful drug embeddings; the best, different architectures, or what Benchmarking papers are using, or drug signature based on different cell line:
1. The best 
2. ChemBERTa
3. MolBert

Our informal idealized pipeline: \
$drug\_representation \rightarrow f(.) + untreated\_cell = proposed\_generated\_cell$


| Date | Event | Description |
| :--- | :--- | :--- |
| **Sep 16th - Sep 22nd** | Background Research | Read ScPerturBench + Background Knowledge |
| **Sep 23rd - Oct 6th** | Discover + Research Metrics | Search for other similar benchmarking papers + research / understand the evaluation metrics we are going for|
| **Oct 7th - Oct 20th** | Pre-process TAHOE100M dataset | Filtering for HVGs, running one model on the data we have, with one embedding, get some metrics **This should be october 6th** |
| **Oct 21 - Dec 2nd** | Implement models | Implement the proposed existing architectures alongside CLAMP + One other drug embedding |
| **FINAL WEEK** | Report | Compile a final report for the benchmarking study |

4-5 multipanel figures

So far, this is two sections: leave drug out and drug embedding ablation

### Meeting Minutes

Do drug embeddings come with a set dimension size? If not, do we add an MLP for this?
It depends?
It pretty much depends whether the model takes in a fixed size embedding, if so, see if the drug embedding method can output a different size. 

Worst case scenario: We add an MLP


## Semester 2 Timeline:

