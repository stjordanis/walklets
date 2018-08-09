Walklets
============================================
<p align="justify">
Walklets is a multi-scale node embedding algorithm which learns an embedding of approximated adjacency matrix powers up to a given order. Walklet places nodes in an abstract feature space where the vertex features are able to reproduce connectivity patterns in the graph at multiple scales. Embedding is created with an exponential implicit factorization machine. The resulting feature vectors that are extracted in an unsupervised way can be used in downstream machine learning tasks such as edge prediction, node classification and community detection.
</p>

This repository provides an implementation for Walklets as described in the paper:
> Don't Walk, Skip! Online Learning of Multi-scale Network Embeddings
> Bryan Perozzi, Vivek Kulkarni, Haochen Chen, Steven Skiena
> ASONAM, 2017
> https://arxiv.org/abs/1605.02115


### Requirements

The codebase is implemented in Python 2.7.
package versions used for development are just below.
```
networkx          1.11
tqdm              4.19.5
numpy             1.13.3
pandas            0.20.3
```

### Datasets

The code takes an input graph in a csv file. Every row indicates an edge between two nodes separated by a comma. The first row is a header. Nodes should be indexed starting with 0. Sample graphs for the `Facebook Politicians` and `Facebook Food` datasets are included in the  `input/` directory.

### Options

Learning of the embedding is handled by the `src/embedding_clustering.py` script which provides the following command line arguments.

#### Input and output options

```
  --input STR                   Input graph path.                                 Default is `data/politician_edges.csv`.
  --embedding-output STR        Embeddings path.                                  Default is `output/embeddings/politician_embedding.csv`.
  --cluster-mean-output STR     Cluster centers path.                             Default is `output/cluster_means/politician_means.csv`.
  --log-output STR              Log path.                                         Default is `output/logs/politician.log`.
  --assignment-output STR       Node-cluster assignment dictionary path.          Default is `output/assignments/politician.json`.
  --dump-matrices BOOL          Whether the trained model should be saved.        Default is `True`.
  --model STR                   The model type.                                   Default is `GEMSECWithRegularization`.
```


#### Random walk options

```
  --walker STR                  Random walker order (first/second).               Default is `first`.
  --P FLOAT                     Return hyperparameter for second-order walk.      Default is 1.0
  --Q FLOAT                     In-out hyperparameter for second-order walk.      Default is 1.0.
```

#### Skipgram options

```
  --dimensions INT                Number of dimensions.                               Default is 16.
  --random-walk-length INT        Length of random walk per source.                   Default is 80.
  --num-of-walks INT              Number of random walks per source.                  Default is 5.
  --window-size INT               Window size for proximity statistic extraction.     Default is 5.
  --distortion FLOAT              Downsampling distortion.                            Default is 0.75.
  --negative-sample-number INT    Number of negative samples to draw.                 Default is 10.
```

#### Model options

```
  --initial-learning-rate FLOAT   Initial learning rate.                                        Default is 0.001.
  --minimal-learning-rate FLOAT   Final learning rate.                                          Default is 0.0001.
  --annealing-factor FLOAT        Annealing factor for learning rate.                           Default is 1.0.
  --initial-gamma FLOAT           Initial clustering weight coefficient.                        Default is 0.1.
  --lambd FLOAR                   Smoothness regularization penalty.                            Default is 0.0625.
  --cluster-number INT            Number of clusters.                                           Default is 20.
  --overlap-weighting STR         Weight construction technique for regularization.             Default is `normalized_overlap`.
  --regularization-noise FLOAT    Uniform noise max and min on the feature vector distance.     Default is 10**-8.
```

### Examples

The following commands learn a graph embedding and cluster center and writes them to disk. The node representations are ordered by the ID.

Creating a GEMSEC embedding of the default dataset with the default hyperparameter settings. Saving the embedding, cluster centres and the log file at the default path.

```
python src/embedding_clustering.py
```
Creating a DeepWalk embedding of the default dataset with the default hyperparameter settings. Saving the embedding, cluster centres and the log file at the default path.

```
python src/embedding_clustering.py --model DeepWalk
```

Turning off the model saving.

```
python src/embedding_clustering.py --dump-matrices False
```

Creating an embedding of an other dataset the `Facebook Companies`. Saving the output and the log in a custom place.

```
python src/embedding_clustering.py --input data/company_edges.csv  --embedding-output output/embeddings/company_embedding.csv --log-output output/cluster_means/company_means.csv --cluster-mean-output output/logs/company.json
```

Creating a clustered embedding of the default dataset in 32 dimensions, 20 sequences per source node with length 160 and 10 cluster centers.

```
python src/embedding_clustering.py --dimensions 32 --num-of-walks 20 --random-walk-length 160 --cluster-number 10
```