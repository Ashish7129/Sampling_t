# Graph Sampling Package

[Social Network Analysis](https://en.wikipedia.org/wiki/Social_network_analysis) (SNA) has recently been gaining more and more popularity in various domains. Unfortunately, performing SNA is not always an easy task. Not because it is difficult to construct corresponding network (although, this might, as well, happen in some cases), but since, due to the volume of data which translates to huge network/graph, it is very time consuming and [computationally expensive](https://en.wikipedia.org/wiki/Computational_complexity) to perform analysis on these graphs. Depending on the type of task, handling graphs with even just dozens of thousands of nodes can be unfeasible, as some studies show. An intuitive solution to deal with this situation, just as in any scenario where we have a massive amount of data, is to sample the graph and then perform relevant simulation/analysis on obtained [sub-graph](https://en.wikipedia.org/wiki/Subgraph).
[Graph sampling](https://en.wikipedia.org/wiki/Sampling_(statistics)) is a technique to pick a subset of vertices or edges from original graph. The biggest advantage of sampling methods are their execution efficiency so that the graph transformation procedure won’t take longer time than straightforward computation on original graph. This is a simple sampling repo that helps you find a representative sample of the original graph via different [Sampling Techniques](https://cs.stanford.edu/~jure/pubs/sampling-kdd06.pdf). 

### Graph Sampling by Exploration
Exploration or traversal (also called topology-based) approaches are based on the idea of randomly selecting one node and then exploring its neighborhood. Sampling algorithms based on this techniques are :

- **Simple Random Walk Sampling (SRW) :** Uniformly at random pick a starting node and then simulate a [random walk](https://people.math.osu.edu/husen.1/teaching/571/random_walks.pdf)(select neighboring node uniformly and randomly) on the graph. Random walk is continued until we reach the required sample size *n<sub>s</sub>*. In the unconnected graph, it is possible that there is no node in the component that could be added to the sample. To handle this we defined a period *T* and an expected growth size *M* in that period and after *T* iterations check whether the sample growth is large enough and if not, select again the node randomly to continue random walk. This way we ensure that the sample will reach the required size *n<sub>s</sub>* . 
```sh 
  sampled_subgraph = random_walk_sampling_simple(complete_graph, nodes_to_sample)
```
- **Random Walk Sampling with Fly Back Probability (RWF) :** In *SRW* at any stage, we choose only one of the neighboring node to continue random walk. Choosing only one neighboring node affects graph properties like average degree which in turn affect many properties related to it. *RWF* is a variation of random walk to improve the performance. The Fly-back probability *(p)* is used to sample more than one neighboring node at any stage of already sampled node. *RWF* picks a node uniformly at random as start point and begins a sequence. At each step, with *1-p* probability it selects one node among neighbors of the current node with equal probability and moves to that node. If the neighboring node or the corresponding edge does not exist in the sample graph, they will be added to the graph; with *p* probability, we will fly back to the starting point. This ensures that the neighborhood of a selected node could be sufficiently explored. The higher the fly back probability, the more similar random walk is to [Breadth First Search](https://en.wikipedia.org/wiki/Breadth-first_search).
     To avoid being stuck we defined a period *T* and an expected growth size *M* in that period and after *T* iterations check whether the sample growth is large enough and if not, select again the node randomly to continue random walk. This way we ensure that the sample will reach the required size *n<sub>s</sub>* .  
```sh 
  sampled_subgraph = random_walk_sampling_with_fly_back(complete_graph, nodes_to_sample, fly_back_prob)
```
- **Induced Subgraph Random Walk Sampling (ISRW) :**  We observed that *SRW* and *RWF* fundamentally biases the structure of the sampled subgraph, as at every step we choose only one neighbor uniformly and randomly of the node we sampled at the previous iteration. When a node is selected for inclusion in the sample, it is unlikely that all of its neighbors will be included in the sampled subgraph, and thus, sampled degrees of nodes tend to be smaller than original degrees. As random walk moves in the linear fashion, the connectivity in the sampled subgraph was also quite sparse due to under-sampling of edges. This under-sampling of edges caused overestimation of shortest path lengths in sampled subgraphs. Hence, this conventional wisdom of selecting nodes in an unbiased manner (e.g., uniformly at random) may not yield representative subgraphs that match the properties of the original graph. So, we presented our new sampling strategy, *Induced Subgraph Random Walk Sampling (ISRW)*, which tries to overcome the problem of undersampling of edges in *SRW*. We applied graph [induction](https://en.wikipedia.org/wiki/Induced_subgraph) step to *SRW* to select additional edges between sampled nodes with the aim to restore connectivity and bring the structure closer to that of the original graph.
```sh 
  sampled_subgraph = random_walk_induced_graph_sampling(complete_graph, nodes_to_sample)
```
- **Snowball Sampling (SB) :** Snowball Sampling is a variant of [Breadth First Search](https://en.wikipedia.org/wiki/Breadth-first_search) where there is limit on the number of neighbors 'k' that are added to the sample. Begin from a random set of nodes of size 'k'. After that each of the new 'k' nodes are added that make the second sampling stage. This continues until the sample size is reached.
```sh 
  sampled_subgraph = snowball(complete_graph, nodes_to_sample, k) 
```
- **ForestFire Sampling (FF) :** Randomly pick a seed node and begin “burning” outgoing links and the corresponding nodes. If a link gets burned, the node at the other endpoint gets a chance to burn its own links, and so on recursively.
```sh 
  sampled_subgraph = forestfire(complete_graph, nodes_to_sample) 
```
- **Metropolis Hastings Random Walk Sampling (MHRW) :** This is very similar to random walk sampling except for the fact that a node is selected randomly in graph with probability 'p'.
```sh  
  sampled_subgraph = mhrw(complete_graph, nodes_to_sample, nodes) 
```
- **Induced Metropolis Hastings Random Walk Sampling (Induced-MHRW) :** This is the improvement in MHRW sampling by appling [induction](https://en.wikipedia.org/wiki/Induced_subgraph) step to add additional edges.
```sh  
  sampled_subgraph = induced_mhrw(complete_graph, nodes_to_sample, nodes) 
```
- **Total Induction Edge Sampling (TIES) :** In this approach, nodes are selected in pairs by sampling edges in the same manner as the classic [edge sampling](https://docs.lib.purdue.edu/cgi/viewcontent.cgi?article=2743&context=cstech) approach. The key difference between this approach and normal edge sampling is in the induced graph step; augments the edges selected by the edge sampling step by including other edges between the set of sampled nodes.
```sh 
  sampled_subgraph = ties(complete_graph, nodes_to_sample, phi)
```
  
  

### Pre-requisite
The Graph Sampling package requires [Python](https://www.python.org/downloads/) 2.7, 3.4, 3.5, 3.6 or newer. If you don't have the pre-installed python in your system, please follow up the python link to download it. This package also requires [Networkx](https://networkx.github.io/documentation/latest/install.html) 2.1 or newer which helps to create the graphs and also perform manipulations on them.

### Installing the development version
If you have Git installed on your system, then it is also possible to install the development version of Graph Sampling package by running these commands on your terminal:
```sh
$ git clone https://github.com/Ashish7129/Graph-Sampling.git
$ cd Sampling
$ pip install -e .
```
Or you can install the current release of Graph Sampling package with pip. Please download the zip file and locate it into the current folder and then run the following command for installing the graph sampling package into your system:
```sh
$ pip install Sampling
```
 
### Usage

After installing the package, you can use the package by writing the following command:
```sh
>>> import Sampling 
```
### Example
Check out the file test.py, which helps you to understand the procedure of executing different functions along with their type and number of arguments. For example, snowball sampling fuction is excecuted as follows:
```sh
>>> object = Sampling.Snowball()             
>>> sampled_subgraph = object.snowball(G,size,k) 
```
*The object is the instance of the class Snowball. The class having the snowball function has 3 parameters as :*
  - G : Original Graph / Whole Graph, 
  - size: number of nodes to sample and 
  - k: initial set of k nodes.


