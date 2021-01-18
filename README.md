# Scbean.VIPCCA
[![Documentation Status](https://readthedocs.org/projects/vipcca/badge/?version=latest)](https://vipcca.readthedocs.io/en/latest/?badge=latest)
![PyPI](https://img.shields.io/pypi/v/scbean?color=blue)

Variational inference of probabilistic canonical correlation analysis (VIPCCA) was implemented in a python package scbean, providing a range of single-cell data analysis including dimension reduction, remvoing batch-effects, transfer well-annotated celltype labels from scRNA-seq onto scATAC-seq cells by learning from the integrated data. It's efficient and scalable for large-scale datasets with more than 1 million cells. We will also provide more fundamental analyses for multi-modal data and spatial resoved transcriptomics in the future. The output can be easily used for downstream data analyses such as clustering, identification of cell subpopulations, differential genen expression, visualization using either [Seurat](https://satijalab.org/seurat/) or [Scanpy](https://scanpy-tutorials.readthedocs.io).


### Create conda environment
For more information about how to manage conda environment, please see its [documentation](https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/environments.html).
```shell
$ conda create -n scbean python=3.8
$ conda activate scbean
```


### Install scbean from pypi

```shell
$ pip install scbean
```

### Install scbean from GitHub source code
```shell
$ git clone https://github.com/jhu99/scbean.git
$ cd ./scbean/
$ pip install .
```

**Note**: 

- Please make sure that the `pip` is for python>=3.8. The current release depends on tensorflow with version 2.4.0. Install tenserfolow-gpu if gpu is avialable on the machine.

- If there is a need to run large data sets, we provide version 1.1.1 (depending on tensorflow 1.15.1), which uses sparseTensor to reduce memory usage.
```shell
$ pip install scbean==1.1.1
```

### Usage

For detailed guide about the usage of scbean, the tutorial and documentation were provided [here](https://vipcca.readthedocs.io/en/latest/).

#### Quick start

Download the [data](http://141.211.10.196/result/test/papers/vipcca/data.tar.gz) of the following test code.

```python
import scbean.model.vipcca as vip
import scbean.tools.utils as tl
import scbean.tools.plotting as pl

# If your script depends on a specific backend you can use the use() function:
import matplotlib
matplotlib.use('TkAgg')

# read single-cell data.
adata_b1 = tl.read_sc_data("./data/mixed_cell_lines/293t.h5ad", batch_name="293t")
adata_b2 = tl.read_sc_data("./data/mixed_cell_lines/jurkat.h5ad", batch_name="jurkat")
adata_b3 = tl.read_sc_data("./data/mixed_cell_lines/mixed.h5ad", batch_name="mixed")

# tl.preprocessing include filteration, log-TPM normalization, selection of highly variable genes.
adata_all= tl.preprocessing([adata_b1, adata_b2, adata_b3])

# Construct VIPCCA with .
handle = vip.VIPCCA(
							adata_all,
							res_path='./results/CVAE_5/',
							split_by="_batch",
							epochs=100,
							lambda_regulizer=5,
							)

# Training and integrating multiple single-cell datasets. The output results include cell representation in reduced dimensional space and recovered gene expression.
adata_integrate=handle.fit_integrate()

# Visualization
pl.run_embedding(adata_integrate, path='./results/CVAE_5/',method="umap")
pl.plotEmbedding(adata_integrate, path='./results/CVAE_5/', method='umap', group_by="_batch",legend_loc="right margin")
pl.plotEmbedding(adata_integrate, path='./results/CVAE_5/', method='umap', group_by="celltype",legend_loc="on data")
```
