---
author-meta:
- Michael N. Zietz
date-meta: '2017-08-24'
keywords:
- work-in-progress
- markdown
- manuscript
- publishing
title: Vagelos Report Summer 2017
...

<small><em>
This manuscript was automatically generated
from [zietzm/Vagelos2017@3f3da33](https://github.com/zietzm/Vagelos2017/tree/3f3da3385c57006048de569bfd61f4a5175e7014)
on August 24, 2017.
</em></small>

## Authors


+ **Michael N. Zietz**<br>
    ![ORCID icon](images/orcid.svg){height="13px"}
    [0000-0003-0539-630X](https://orcid.org/0000-0003-0539-630X)
    · ![GitHub icon](images/github.svg){height="13px"}
    [zietzm](https://github.com/zietzm)<br>
  <small>
     Greene Lab, Department of Systems Pharmacology and Translational Therapeutics, University of Pennsylvania
  </small>



## Scientific background

### Drug development
Drug development times and R&D expenditures have risen considerably in the last decades.
In fact, to bring a new compound to market can take 14 years on average [@LlRXV6lE].
This development process, according to a 2016 estimate by DiMasi et al. [@13c9OPizf], costs an average of $2.87 billion dollars, including post-approval R&D.
This amount is up from two previous studies by the same authors which found the average captialized R&D costs (in inflation-adjusted 2016 dollars) to be $1.06 billion in 2003 [@nn9rnuHx] and $568 million in 1991 [@269zMv7S].
Despite increases in drug development time and expenditures, the rate of R&D failure has increased since the 1990s [@z38InjRh].
Only about one in 5,000 compounds which begin preclinical testing are eventually approved [@g5JcTYOY].
Even among those which enter phase I of clinical development, only an estimated one in ten drugs will receive FDA approval [@o8yROPbx].

### Drug repurposing
Drug repurposing refers to the application of an existing therapeutic to a different disease than the one for which it was originally intended.
Because candidates for drug repurposing have already been approved for other diseases, the time and cost associated with repurposing a drug are very small compared to the development of a new drug [@c7z5BwrM].
In fact, even compounds which have been deemed safe but have failed in clinical development for other reasons can be candidates for drug repurposing.
Several examples exist of drugs which have been repurposed, for example aspirin to treat coronary artery disease, sildenafil to treat erectile dysfunction, and gabapentin to treat postherpetic neuralgia.
Most successfully repurposed drugs were discovered serendipitously and not through any systematic discovery mechanism.
Our goal is to make accurate predictions of drugs which are candidates for drug repurposing.
We hope to do this using heterogeneous networks of biomedical information to learn the patterns of connections between compounds which treat diseases.


## Summer aims

This summer I worked within the Greene Lab project called 'Hetmech' [@X1hJHTyw].
Hetmech aims to improve upon and expand our existing method for predicting drug repurposing targets.

I had three primary aims for my work this summer.
The details of each are covered in the Methods section.

First, I wanted to add a capability within Hetmech to compute precisely the degree-weighted path count (DWPC).
Previously, the two options had been an inaccurate DWPC method and a degree-weighted walk count (DWWC), whose limitations will be covered in the Methods section.

Second, I hoped to decrease the time required to make calculations of DWPC.
Towards this aim, I hoped to speed up computation by at least an order of magnitude.
Sparse matrices and parallel computation were planned solutions to this problem.

Finally, it was my goal to add a multiple search capability.
This involves querying a set of nodes to return a set of predictions for nodes which connect the queried nodes.
For example, one should be able to search a set of symptoms and return a list of potential diseases that connect the symptoms.
As with the previous problems, moving to strictly matrix computation makes this much more simple.

My secondary aim for the summer was to become familiar with and contribute to open-source, reproducible, computational science in biology.
I hoped to contribute to a collaboratively written review of deep learning methods in the fields of biology and medicine [@yLr4pV4G].


## Methods

### Heterogeneous networks

Heterogeneous networks ('hetnets') are networks with multiple node types and edge types.
In the network used this summer, titled Hetionet v1.0 (Figure {@fig:metagraph}B), nodes represent instances of 11 biomedical entity types, and edges correspond to one of 24 edge types, or relationships between entities.
'Graph' in this context refers to the entire network of nodes and edges.
We define 'metagraph' to mean a graph of the types of nodes and edges in Hetionet (Figure {@fig:metagraph}A).


![A. Metagraph. The graph of metandoes (node types) and metaedges (edge) type. B. Graph (Hetionet v1.0) The circles and lines
represent nodes within the labeled types. For example, within the metanode 'Anatomy' we could have the node 'Leukocyte'.](https://raw.githubusercontent.com/zietzm/Vagelos2017/master/content/images/graph_metagraph.png){#fig:metagraph}


Hetionet v1.0 incorporated 47,031 nodes and 2,250,197 edges [@11rVTcUCK]. A further breakdown of the nodes and edges can be found below in Tables @tbl:nodes-by-type and @tbl:nodes-by-source, respectively.

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

![An example graph. Blue nodes are genes, while green nodes are diseases. The edge type between all the nodes in this graph is 'DaG' or 'Disease-associates-Gene'. This is also the complete graph for the relationship 'DaGaD' starting with 'spinal cancer'](images/graph.svg){#fig:eg_graph width="5in"}

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


An adjacency matrix is identical to the information about connections between nodes along a given metaedge.
Another way to consider an adjacency matrix is as a list of the nodes at which one can arrive in one step from a given start node.
In this sense, performing a matrix multiplication with two adjacency matrices gives the nodes at which one can arrive in exactly *two* steps.
Further, an arbitrary number of multiplications can be performed between adjacency matrices corresponding to various metaedges, so long as the dimensionality is appropriate to the matrix multiplication in question.
Using this method, we can extract what is known as a walk count, or the number of ways to traverse the graph between two nodes.
In this way of thinking, an adjacency matrix corresponds to the path count for paths of length one.
Using the graph in Figure @fig:eg_graph, we could perform a traversal along the meta*path* 'GaDaG', and would obtain the following matrix:


\begin{bmatrix}
6 & 1 & 2\\
1 & 3 & 1\\
2 & 1 & 3
\end{bmatrix}


Notice that the elements along the main diagonal of the above matrix are not zero.
This indiciates that we are accounting for walks in which we traverse from nodes as follows: a ⟶ b ⟶ a.
Useful information cannot be gained from looping walks such as these, and they introduce considerable noise in measures of connection between nodes.
We therefore wanted to eliminate any usage of walk count, and replace it with path count, where a path is a type of walk which *cannot* loop backwards on itself.
In this example and for paths of length two, this is trivial; we simply subtract the main diagonal, and we have converted from a walk count to a path count.
However, this conversion becomes non-trivial when dealing with longer paths and overlapping metanode repeats.
Using the graph in Figure @fig:eg_graph, _NF2—associates—spinal cancer—resembles—peripheral nervous system neoplasm—associates—NF2_ is considered a walk, but it is not a path because its start and end nodes are the same.
Note that it is perfectly acceptable to repeat *metanodes*, meaning that we can have metapaths of the form `CrCrCrC`.
Paths simply exclude the repeat of *specific nodes* within an ordering of nodes.

Path counts provide useful information for predicting potential new relationships within a graph.
Higher path-counts between two nodes shows good performance as a feature for predicting novel connections [@WkPlH1ds].
However, as high-degree nodes make many connections, superior performance was acheived by downweighting nodes according to their degree.
To do this, row sums and column sums are taken for a matrix at each step.
These one-dimensional arrays are exponentiated and the matrix is multiplied by each.
This represents the 'degree-weight' portion of the 'degree-weighted path count'.
My work toward this will be further discussed in the Results section.


### Computational tools

All computational work was done in Python version 3.6.
We used an open-source Conda environment to manage packages and dependencies for Python.


#### Matrices

Python's mathematical and array library, NumPy [@eye5ay6P], has an n-dimensional array class called an `ndarray`.
`numpy.ndarray`s are very useful for representing matrix information, and have superior functionality for our purposes than the native `numpy.matrix` class.
However, as can be seen in Table @tbl:nodes-by-type, the number of nodes in a given adjacency matrix can be upwards of 20,000.
In performing matrix multiplications, this can become a computation-intensive process that requires both significant CPU power and memory.
Additionally, since the adjacency matrices are full of more zeros than ones, a majority of the multiplications performed are trivial zero multiplications.


One of my early goals for the summer was the conversion of all walk-count (and subsequently, path count) functions to sparse matrices.
Sparse matrices, as employed by the Python library SciPy [@LsT2mKA3], represent data in matrices which are primarily composed of zeros.
The selection of sparse representation and threshold are discussed in the Results section, but sparse matrices warrant a brief description.

In the sparse representation we used, Compressed-sparse-column format (CSC), a matrix is stored as three one-dimensional arrays.

Consider the following matrix:

\begin{bmatrix}
4 & 5 & 0 & 0\\
0 & 0 & 1 & 0\\
3 & 0 & 0 & 9\\
0 & 6 & 0 & 5
\end{bmatrix}

We represent the nonzero elements with one array, with the elements being taken from top to bottom, from left to right.

\begin{bmatrix}
4 & 3 & 5 & 6 & 1 & 9 & 5
\end{bmatrix}

Next, we give the row indices of these elements.

\begin{bmatrix}
0 & 2 & 0 & 3 & 1 & 2 & 3
\end{bmatrix}

The final array represents what is called a column pointer.
This array gives the indices where each column starts.

\begin{bmatrix}
0 & 2 & 4 & 5 & 7
\end{bmatrix}

These three arrays represent the entirety of a matrix.

#### Other tools

A significant amount of work done later in the summer involved attempting to compare the new functions to the quite slow old functions.
As has become relatively standard for much of computational work, we made use of Jupyter notebooks [@klbqKLT4] to display the code used to run analysis and its output in a re-usable way.
Within this, we were able to effectively incorporate the data manipulation and analysis library pandas [@iE9hnE2z] and the multiprocessing library concurrent.futures [@AwSIzjc6].
For visualization, we utilized the online service Neo4j to represent Hetionet v1.0 [@cagYjYkt], and the Python graphing library Matplotlib [@118qTQ4yr].

The most important tool we used this summer to track progress was the version control software, Git.
Our repositories were hosted on the online git hosting service, GitHub [@tgH1jNoV].
Specifically, every contribution I made this summer can be viewed in detail at my GitHub profile (https://git.io/v5qC9).
This will be discussed further in the Results section below.


## Results

The main problem towards which I worked this summer was an implementation of the degree-weighted path count.
While the degree-weighted walk count is relatively trivial to implement with matrix multiplication, the path count is non-trivial.
If computational efficiency is to be considered, each category of metapath will require a different path-count method.
As of mid-August 2017, I have merged 7 pull requests into the main Hetmech repository on GitHub, and I have one open development branch.
These contributions amounted to 1043 lines added and 264 lines deleted in the repository.

### DWPC

I have completed an implementation of the degree-weighted path count (DWPC) in the form of several independent functions.
What follows are the specifics of this new method.

When a user calls the `dwpc` function over a metapath, a series of steps occur before any actual path-counting occurs.
First, the metapath is categorized according to its repeated metanodes.
For example, the metapath `GaDrDaG`, ('Gene-associates-Disease-resembles-Disease-associates-Gene') would be classified `BAAB`.
Further examples of this classification method are in Table @tbl:classification below.

Next, the metapath is split into segments according to its classification.
This step allowed for the abstraction of metapath patterns to metapaths which followed the pattern of a classification but included randomly inserted, non-repeating metanodes anywhere in the bath.
Splitting the metapath essentially allowed us to work with paths like `A-B-C-D-B-A` in the same way that we work with `A-B-B-A`, by abstracting any non-repeating metanodes to within segments.

| Metapath | Classification | Segments |
| -------- | -------------- | -------- |
| CbGiGbC | BAAB | CbG; GiG; GbC |
| DaGaDaG | BABA | DaG; GaD; DaG |
| DlAeGaDaG | BABA | DlAeG; GaD; DaG |
| CrCrC | short_repeat | CrCrC |
| DlAeG | no_repeats | DlAeG |
| CrCrCrC | long_repeat | CrCrCrC |
| CbGiGiGaDrDpCpD | interior_complete_group | CbG; GiGiG; GaD; DrD; DpC; CpD |

Table: Example metapaths with classifications and segments {#tbl:classification}

See Figure @fig:metapath_breakdown for a breakdown of all the metapaths according to their categorization.

![1170 metapaths by categorization](images/metanodes_by_category.svg){#fig:metapath_breakdown width="5in"}

Once the metapath is split into segments, the original metapath classification is used to select the appropriate DWPC function.
For example, if the metapath classification is `BAAB`, then the segmented metapath will be run in the function `dwpc_baab`.

Each DWPC function has a unique method for ensuring that it outputs a path-count rather than a walk count.
For many metapaths this was a non-trivial method to unravel, and often involved several steps of additions, subtractions, multiplications, and normalizations.
Our work was greatly aided by the help of the mathematician Dr. Kyle Kloster with whom we collaborated on some of the more challenging linear algebra algorithms.

In addition to the specific DWPC functions, I created a general method which works over all metapaths, no matter the length.
While slower than the other methods, this function allows us to ensure that every path is covered by the DWPC.
The method uses a dictionary of history vectors for every index in the matrix and splits computations whenever a path has the opportunity to diverge into multiple potential paths.

### Calculation time

Our new matrix method for calculating DWPC decreased the computation time by 98.5 percent.
We reduced the time to compute DWPC over nearly 1200 metepaths from roughly four-and-a-half days to roughly one hour and thirty-seven minutes [@1BU7iA21F].
One of the major advances we made in our method this summer was the realization that we can calculate the DWPC more efficiently by categorizing each metapath and feeding it to a DWPC function corresponding to a particular metapath category.
These specialized functions allow us to increase significantly the speed with which we can make calculations.
However, while we were able to reduce almost all computation times, there are a number of metapaths which we cannot calculate significantly more quickly than we could with the old method.
36 of 1206 metapaths had to use the generalized function I wrote which enumerates each path independently using a dictionary of history vectors in order to ensure no nodes are repeated.

For the remaining 1170 metapaths, the corresponding DWPC calculation times are summarized in Figure @fig:all_metapath_times.

![Matrix method DWPC calculation times for each of 1170 metapaths](images/all_path_time.svg){#fig:all_metapath_times width="5in"}

As mentioned, we created a new system whereby metapaths are categorized according to the method we use for computing the DWPC.
For example, the metapath `CbGbCbGaD` is classified as `BABA`, due to the overlapping repeat nature of the metanodes for 'Compound' and 'Gene'.
See Figure @fig:metapath_breakdown for a breakdown of all the metapaths according to their categorization.

In trying to reduce runtime, we were constantly trying to optimize every step in our computation.
We were able to reduce runtimes significantly using sparse matrices as previously mentioned.
The key, however, to a faster computational pipeline was categorizing metapaths and using specialized functions for each metapath.
A breakdown of these functions' performance is in Figure @fig:category_runtimes.

![DWPC calculation runtimes by metapath category](images/category_runtimes.svg){#fig:category_runtimes width="5in"}

Finally, in tracing back the slowest calculations, we found that a few metapaths took significantly longer than all others.
We discovered that these involved the metapaths with the segment `[...]GeAeG[...]` (see Figure  @fig:gene_times).

![Breakdown of the slowest metapath computation time by repeated Gene segment. 'G_X_G' is all metapaths which include a segment of three metanodes, whose innermost metanode is not 'Anatomy'. 'G_A_G' is all metapaths with a segment 'Gene'-'Anatomy'-'Gene' except those of the form 'GeAeG'. 'GeAeG' is strictly metapaths with the segment corresponding to 'Gene-expressed-Anatomy-expressed-Gene', meaning that there are two genes expressed in a certain anatomical region. Please note that there is no overlap between any of the three groups. 'GeAeG' is the most specific, and the other two groups do not include it. 'G_X_G' does includes neither any of the 'G_A_G' nor the 'GeAeG' metapaths.](images/gene_time_breakdown.svg){#fig:gene_times width="5in"}


### Multiple search capability

As with many graph traversal tasks, using adjacency matrices to calculated DWPC meant that querying a set of nodes has become simpler.
Our initial work has been on metapaths starting with compounds and ending with diseases.
Having cached our DWPC matrices along every C-[...]-D metapath with five or fewer edges, we can extract path counts for queried nodes by performing matrix-vector multiplication.

### Other summer results

I also gained a deeper appreciation of and respect for open-access and collaborative science.
In working towards this, I reported a relevant bug in the open source Python repository SciPy which dealt with issues involving matrix multiplication [@R0MGiivw].

In the deep learning review paper, I contributed a section on deep learning applications in the field of protein-protein interaction predictions, with a subsection on deep learning methods for the prediction of MHC-peptide binding [@otFK6ZiU].


## Next steps

We plan to use the functionality created this summer to predict potential targets for drug repurposing.
By doing this, we can provide further verification for our path-count method in the same way we did for the original study.
Additionally, we can verify the accuracy of our new matrix-formulation by comparing it to the far slower but precise graph method.
Yet another method for verifying the predictions of our method involves comparing our predictions to new drugs which are entering the later phases of clinical trial.
As these compounds are not in our Hetnet, we would hope to see that later-phase clinical trial drugs score highly in our treatment predictions for their target diseases.
Luckily, a description of all such clinical trials-- both active and complete in 195 countries-- is available on a government website [@vbD9t39A] run by the National Institutes of Health and the National Library of Medicine.

Now that our methods for traversing the graph are much faster and more expanded, we can add significantly more data into our heterogeneous network.
A project is already underway in our lab which hopes to incorporate data mined from biomedical literature through natural language processing into our network.
This project is a natural complement to the work I did this summer, because while a bigger graph requires more computation time, the matrix-based DWPC method will scale far better to increased data size than the conventional graph method.

Another use-case for our faster computation method is an increase in considered metapaths.
In previous work, we have considered only a subset of the potential metapaths.
For one, we only investigated metapaths starting with a compound and ending with a disease.
Further, even within compound-disease metapaths, we did not consider all possible paths.
Path lengths were constrained to be of length four or fewer edges in the previous work.
My work this summer will allow us to expand the reach of potential metapaths we consider without sacrificing hugely on performance.

As with many of the open-source projects in the Greene Lab, we hope to eventually create an open-access webserver which can access the results of our method.
It should allow a user to search for as many nodes as desired and be returned a set of possible connections between them with corresponding metapaths.
If this could be done, the functionality could be expanded to automatically generate a Neo4j query which could show the graphs being described by our predictions.

A final potential step forward involves extracting different features from the graph in order to make predictions about potential connections.
Our method utilizes path count which corrects for degree-weight, and it has shown good performance toward predicting targets for drug repurposing.
Other measures of connectedness within a graph, or other methods for degree correction could show equal or better performance, though.
For example, graph centrality is a measure of the importance of certain vertices in a graph.
There are feasible methods which could, for example, correct for graph centrality instead of degree for every node in a path.
Even more distant from our current method is one which could incorporate some of the most exciting advances in computer science and statistics like deep learning.
In such a method, we would feed a model a large amount of information about each node, and then computationally extract features without human selection.
For example, in predicting protein-protein interactions, Du et al. [@rVgq22nD] used a two-stage deep neural network first to learn the features of individual proteins which are important for predicting their interactions and second to learn the higher-level features of two proteins' pre-selected features which predict their binding.
The best way to test other methods is to split our data into training, validation, and test sets and compare the performance of different methods.
In short, there are many additional methods we could employ to make predictions of future graph connections.


## Conclusion

This summer I worked in the Greene Lab on implementing a new method for predicting drugs which could be repurposed.
I contributed to this goal through several means, including the implementing a new matrix formulation of a graph node connected-ness measure called the degree-weighted path count (DWPC).
This measure and its new calculation method allows us to make significantly faster (66.8-fold decrease in time) calculations, whose outputs can be used to accurately predict future connections in a graph.
In trying to predict compound-disease connections, our work represents a methodological advance from the previously serendipitous nature of discovering drug repurposing targets.
I found this summer extremely valuable for this reason, as well as for my personal development.
My computational skills have improved considerably, and my depth of understanding in the field of computational biomedical science has grown.
We hope that our new method will prove useful to future researchers hoping to quickly and cheaply bring effective drugs to market, and I hope that the skills I gained this summer will be beneficial in a future biomedical research capacity.

## Acknowledgements

Greene Lab
Daniel Himmelstein
Casey Greene
Kyle Kloster
Michael Mayers


## Citations
