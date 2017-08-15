---
author-meta:
- Michael N. Zietz
date-meta: '2017-08-15'
keywords:
- work-in-progress
- markdown
- manuscript
- publishing
title: Vagelos Report Summer 2017
...

<small><em>
This manuscript was automatically generated
from [zietzm/Vagelos2017@0dec375](https://github.com/zietzm/Vagelos2017/tree/0dec375441ff18eab1a81eb5014ba3a9566c4d7f)
on August 15, 2017.
</em></small>

## Authors


+ **Michael N. Zietz**<br>
    · ![GitHub icon](images/github.svg){height="13px"}
    [zietzm](https://github.com/zietzm)<br>
  <small>
     Greene Lab, Department of Systems Pharmacology and Translational Therapeutics, University of Pennsylvania
  </small>



## Scientific background

### Drug development
Drug development times and R&D expenditures have risen considerably in the last decades.
In fact, to bring a new compound to market can take a 14 years on average [@LlRXV6lE].
This development process, according to a 2016 estimate by DiMasi et al. [@13c9OPizf], costs an average of $2.87 billion dollars, including post-approval R&D.
This amount is up from two previous studies by the same authors which found the average captialized R&D costs to be $802 million in 2003 [@XJuQlZl1] and $318 million in 1991 [@269zMv7S].
Despite increases in drug development time and expenditures, the rate of R&D failure has increased since the 1990s [@z38InjRh].
Only about one in 5,000 compounds which begin preclinical testing are eventually approved [@g5JcTYOY].
Even among those which enter phase I of clinical development, only an estimated one in ten drugs will receive FDA approval [@o8yROPbx].

### Drug repurposing
Drug repurposing refers to the application of an existing therapeutic to a different disease than the one for which it was originally intended.
Because candidates for drug repurposing have already been approved for other diseases, the time and cost associated with repurposing a drug are very small compared to the development of a new drug [@c7z5BwrM].
In fact, even compounds which have been deemed safe but failed in clinical development for other reasons can be candidates for drug repurposing.
Several examples exist of drugs which have been repurposed, for example aspirin to treat coronary artery disease, sildenafil to treat erectile dysfunction, and gabapentin to treat postherpetic neuralgia.
Most successfully repurposed drugs were discovered serendipitously and not through any systematic discovery mechanism.
Our goal is to make accurate predictions of drugs which are candidates for drug repurposing.
We hope to do this using heterogeneous networks of biomedical information to learn the patterns of connections between compounds and diseases.

### Graphs
Write some intro graph theory stuff here, ie adjacency matrix, path counts, etc.


## Summer aims
thing ---->> methods
speed up computation -- sparse and multiprocessing
multiple search nodes -- dwpc with adjacency matrices
path count over walk count


## Methods

### Heterogeneous networks

Heterogeneous networks ('hetnets') are networks with multiple node types and edge types.
In the network used this summer, titled Hetionet v1.0 (Figure @fig:metagraph B), nodes represent instances of 11 biomedical entity types, and edges correspond to one of 24 edge types, or relationships between entities.
'Graph' in this context refers to the entire network of nodes and edges.
We define 'metagraph' to mean a graph of the types of nodes and edges in Hetionet (Figure @fig:metagraph A).


![A. Metagraph. The graph of metandoes (node types) and metaedges (edge) type. B. Graph (Hetionet v1.0) The circles and lines
represent nodes within the labeled types. For example, within the metanode 'Anatomy' we could have the node 'Leukocyte'.](https://raw.githubusercontent.com/zietzm/Vagelos2017/master/content/images/graph_metagraph.png){#fig:metagraph}


Hetionet v1.0 incorporated 47,031 nodes and 2,250,197 edges [@XJuQlZl1]. A further breakdown of the nodes and edges can be found below in Tables @tbl:nodes-by-type and @tbl:nodes-by-source, respectively.

| Metanode | Nodes |
|----------|-------|
|Anatomy             | 402 |           
|Biological Process  | 11,381 |       
|Cellular Component  | 1,391 |         
|Compound            | 1,552 |        
|Disease             | 137 |          
|Gene                | 20,945 |       
|Molecular Function  | 2,884 |       
|Pathway             | 1,822 |        
|Pharmacologic Class | 345 |          
|Side Effect         | 5,734 |        
|Symptom             | 438 |           

Table: Breakdown of nodes by type {#tbl:nodes-by-type}


| Metaedge                               | Edges          | Source                                           |
|----------------------------------------|----------------|--------------------------------------------------|
| Anatomy-downregulates-Gene             | 102,240        | Bgee                                             |
| Anatomy-expresses-Gene                 | 526,407        | Bgee and TISSUES                                 |
| Anatomy-upregulates-Gene               | 97,848         | Bgee                                             |
| Compound-binds-Gene                    | 11,571         | BindingDB, DrugBank, DrugCentral                 |
| Compound-causes-Side Effect            | 138,944        | SIDER                                            |
| Compound-downregulates-Gene            | 21,102         | LINCS L1000                                      |
| Compound-palliates-Disease             | 390            | PharmacotherapyDB                                |
| Compound-resembles-Compound            | 6,486          | Dice coefficient \textgreater= 0.5               |
| Compound-treats-Disease                | 755            | PharmacotherapyDB                                |
| Compound-upregulates-Gene              | 18,756         | LINCS L1000                                      |
| Disease-associates-Gene                | 12,623         | GWAS Catalog, DISEASES, DisGeNET, DOAF           |
| Disease-downregulates-Gene             | 7,623          | STARGEO                                          |
| Disease-localizes-Anatomy              | 3,602          | MEDLINE                                          |
| Disease-presents-Symptom               | 3,357          | MEDLINE                                          |
| Disease-resembles-Disease              | 543            | MEDLINE                                          |
| Disease-upregulates-Gene               | 7,731          | STARGEO                                          |
| Gene-covaries-Gene                     | 61,690         | Evolutionary rate covariation \textgreater= 0.75 |
| Gene-interacts-Gene                    | 147,164        | Evolutionary rate covariation \textgreater= 0.75 |
| Gene-participates-Biological Process   | 559,504        | Gene Ontology                                    |
| Gene-participates-Cellular Component   | 73,566         | Gene Ontology                                    |
| Gene-participates-Molecular Function   | 97,222         | Gene Ontology                                    |
| Gene-participates-Pathway              | 84,372         | Gene Ontology                                    |
| Gene-regulates-Gene                    | 265,672        | Gene Ontology                                    |
| Pharmacologic Class-includes-Compound  | 1,029          | DrugCentral                                      |

Table: Breakdown of edges by type and data source {#tbl:nodes-by-source}


### Graph analysis

An adjacency matrix refers to a labeled matrix with 1 or 0 at every position, corresponding to the presence or absence of a connection between two nodes [@w4Mi034s].
The matrix is labeled, meaning that each row and column correspond to a source and target node, respectively.
A function was created, titled `metaedge_to_adjacency_matrix` which performed the conversion from a string metaedge, such as 'DaG', to an adjacency matrix.

![An example graph. Blue nodes are genes, while green nodes are diseases. The edge type between all the nodes in this graph is 'DaG' or 'Disease-associates-Gene'.](images/graph.svg){#fig:eg_graph width="5in"}

For example, the graph in Figure @fig:eg_graph has the following adjacency matrix corresponding to 'GaD':

$$
\begin{equation}
\begin{bmatrix}
1 & 1 & 0 & 1 & 1 & 1 & 0 & 1 & 0\\
0 & 0 & 1 & 1 & 0 & 0 & 1 & 0 & 0\\
0 & 0 & 0 & 1 & 0 & 0 & 0 & 1 & 1
\end{bmatrix}
\end{equation}
$$

with labels for rows and columns, respectively:

\begin{bmatrix}
\textrm{NF2}\\
\textrm{NRXN2}\\
\textrm{SMARCE1}
\end{bmatrix}

\begin{bmatrix}
\textrm{epilepsy syndrome}\\
\textrm{kidney cancer}\\
\textrm{pancreatic cancer}\\
\textrm{spinal cancer}\\
\textrm{malignant mesothelioma}\\
\textrm{peripheral nervous system neoplasm}\\
\textrm{autistic disorder}\\
\textrm{meningioma}\\
\textrm{salivary gland cancer}
\end{bmatrix}


An adjacency matrix is identical to the information about connections between nodes along a given metanode.
Another way to consider an adjacency matrix is that it lists the nodes at which one can arrive in one step from a given start node.
In this sense, performing a matrix multiplication with two adjacency matrices gives the nodes at which one can arrive in exactly *two* steps.
Further, an arbitrary number of multiplications can be performed between adjacency matrices corresponding to various metaedges, so long as the dimensionality is appropriate to the matrix multiplication in question.
Using this method, we can extract what is known as a path count, or the number of ways to traverse the graph between two nodes.
In this way of thinking, an adjacency matrix corresponds to the path count for paths of length one.
Using the graph in Figure @fig:eg_graph, we could perform a traversal along the meta-*path* 'GaDaG', and would obtain the following matrix:


\begin{bmatrix}
6 & 1 & 2\\
1 & 3 & 1\\
2 & 1 & 3
\end{bmatrix}


Notice that the elements along the main diagonal of the above matrix are not zero.
This indiciates that we are accounting for paths in which we traverse from nodes as follows: A -> B -> A.
Useful information cannot be gained from looping paths such as these, and they introduce considerable noise in measures of connection between nodes.
We therefore wanted to eliminate any usage of path count, and replace it with walk count, where a walk is a type of path which *cannot* loop backwards on itself.
In this example and for paths of length two, this is trivial; we simply subtract the main diagonal, and we have converted from a walk count to a path count.
However, this conversion becomes non-trivial when dealing with longer paths and overlapping metanode repeats.
My work toward this will be further discussed in the Results section.

**Discuss what an adjacency matrix is, that this corresponds to a metaedge, path counts, walk counts, why path is better**




### Computational tools

All computational work was done in Python version 3.6.
Specifically we used an open-source scientific distribution of Python called Anaconda.
Python's notable library, NumPy has an n-dimensional array class called an `ndarray`.
`ndarray`s are very useful for representing matrix information, and have superior functionality for our purposes than the native `numpy.matrix` class.
However, as can be seen in Table @tbl:nodes-by-type, the number of nodes in a given adjacency matrix can be upwards of 20,000.
In performing matrix multiplications, this can become a computation-intensive process that requires both significant CPU power and memory.
Additionally, since the adjacency matrices are full of more zeros than ones, a majority of the multiplications performed are trivial zero multiplications.


One of my early goals for the summer was the conversion of all walk-count (and subsequently, path count) functions to sparse matrices.
Sparse matrices, as employed by the Python library SciPy, represent data in matrices which are primarily composed of zeros.
The selection of sparse representation and threshold are discussed in the results section, but sparse matrices warrant a brief description.

In the sparse representation we used, Compressed-sparse-column format (CSC), a matrix is stored as three one-dimensional arrays.

Consider the following matrix:

\begin{bmatrix}
4 & 5 & 0 & 0\\ 
0 & 0 & 1 & 0\\ 
3 & 0 & 0 & 9\\ 
0 & 6 & 0 & 5
\end{bmatrix}

We represent the nonzero elements with one array, with the elements being taken from left to right, from top to bottom.

\begin{bmatrix}
4 & 5 & 1 & 3 & 9 & 6 & 5
\end{bmatrix}

Next, we give the row indices of these elements.

\begin{bmatrix}
0 & 1 & 2 & 1 & 3 & 1 & 3
\end{bmatrix}

The final array represents what is called a column pointer. 
This array gives the indices where each column starts.

\begin{bmatrix}
0 & 2 & 3 & 5 & 7
\end{bmatrix}

These three arrays represent the entirety of a matrix.


**Discuss python arrays, multiplication, PEP465, sparse matrices, jupyter notebooks, visualization, include several of my github issue graphs as data within the results section, etc.**


## Results


## Next steps


## Citations
