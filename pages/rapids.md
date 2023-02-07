---
title: "Accelerated Data Science"
keywords: Data
sidebar: main_sidebar
permalink: rapids.html
summary: Using GPU accelerated data science library
published: true
---
You need the tf2-rapids-py39 conda environment for this example to work, refer to the [conda page](/conda.html) for details

```python
### Change the visible GPU incase one of them is full
# import os
# os.environ["CUDA_DEVICE_ORDER"]="PCI_BUS_ID"
# os.environ["CUDA_VISIBLE_DEVICES"]="1"
```

## GPU Accelerated Dataframes

### Check the available GPUs

### Jupyter Magic Commands


```python
!nvidia-smi
```

We will use the `%time` magic command command to measure the time it takes to run a single command, and we will use `%%time` to measure the runtime of the entire cell.


```python
from time import sleep

%time sleep(2)
```


```python
%%time
sleep(2)
sleep(1)
```

### GPU Dataframes

#### Reading Data


```python
import cudf # Can be used inplace of pandas in almost all cases
import cupy as cp # Can be used inplace of numpay in almost all cases

import pandas as pd
import numpy as np
```


```python
%time gdf = cudf.read_csv('/data/datasets/rapids/pop_1-03.csv')
```


```python
%time df = pd.read_csv('/data/datasets/rapids/pop_1-03.csv')
```


```python
gdf.shape
```


```python
df.shape
```

#### Writing Data

GPU


```python
%time blackpool_residents = gdf.loc[gdf['county'] == 'BLACKPOOL']
print(f'{blackpool_residents.shape[0]} residents')
```


```python
%time blackpool_residents.to_csv('/data/datasets/rapids/blackpool.csv')
```

CPU


```python
%time blackpool_residents_pd = df.loc[df['county'] == 'BLACKPOOL']
print(f'{blackpool_residents_pd.shape[0]} residents')
```


```python
%time blackpool_residents_pd.to_csv('/data/datasets/rapids/blackpool_pd.csv')
```

### Data Filtering

GPU


```python
%%time
sunderland_residents = gdf.loc[gdf['county'] == 'Sunderland']
northmost_sunderland_lat = sunderland_residents['lat'].max()
counties_with_pop_north_of = gdf.loc[gdf['lat'] > northmost_sunderland_lat]['county'].unique()
```

CPU


```python
%%time
sunderland_residents = df.loc[df['county'] == 'Sunderland']
northmost_sunderland_lat = sunderland_residents['lat'].max()
counties_with_pop_north_of = df.loc[df['lat'] > northmost_sunderland_lat]['county'].unique()
```

### Data Cleaning


```python
%time gdf['county'] = gdf['county'].str.title()
```


```python
%time df['county'] = df['county'].str.title()
```

## GPU Accelerated Graphs


```python
import cugraph as cg # Can be used inplace of networkx

import networkx as nx
```

### Reading the nodes


```python
road_nodes = cudf.read_csv('/data/datasets/rapids/road_nodes_1-06.csv')
road_nodes.head()
```


```python
road_nodes.dtypes
```


```python
road_nodes.shape
```


```python
road_nodes['type'].unique()
```

### Reading the edges


```python
road_edges = cudf.read_csv('/data/datasets/rapids/road_edges_1-06.csv')
road_edges.head()
```


```python
road_edges.dtypes
```


```python
road_edges.shape
```


```python
road_edges['type'].unique()
```


```python
road_edges['form'].unique()
```

### Cleaning the data


```python
road_edges['src_id'] = road_edges['src_id'].str.lstrip('#')
road_edges['dst_id'] = road_edges['dst_id'].str.lstrip('#')
road_edges[['src_id', 'dst_id']].head()
```

### Creating the graph objects


```python
G = cg.Graph()
%time G.from_cudf_edgelist(road_edges, source='src_id', destination='dst_id', edge_attr='length')
```


```python
road_edges_cpu = road_edges.to_pandas()
%time G_cpu = nx.convert_matrix.from_pandas_edgelist(road_edges_cpu, source='src_id', target='dst_id', edge_attr='length')
```

## Multiple GPUs using Dask in one Node


```python
import subprocess # we will use this to obtain our local IP using the following command
cmd = "hostname --all-ip-addresses"

process = subprocess.Popen(cmd.split(), stdout=subprocess.PIPE)
output, error = process.communicate()
IPADDR = str(output.decode()).split()[0]
```

Start the cluster scheduler.
If you are on UOB network or using tunneling you can visit the cluster status page.


```python
from dask_cuda import LocalCUDACluster

cluster = LocalCUDACluster(ip=IPADDR)
cluster
```


```python
# from dask.distributed import SSHCluster

# cluster = SSHCluster(["master", "cn01", "cn02"])
# cluster
```


```python
from dask.distributed import Client, progress

client = Client(cluster)
```


```python
!ls -sh /data/datasets/rapids/pop5x_1-07.csv
```


```python
import dask_cudf # dask ditributed dataferame object
```


```python
ddf = dask_cudf.read_csv('/data/datasets/rapids/pop_1-03.csv', dtype=['float32', 'str', 'str', 'float32', 'float32', 'str'])
```

We can see theat the data on both GPUs does not correspond to the total dataset size. That is because dask creates the task graphs first and then execute it when it is needed.


```python
!nvidia-smi
```

The visualisation will show the parallel task graph and the asigned tasks to each partitioin.


```python
ddf.visualize(format='svg') # This visualization is very large, and using `format='svg'` will make it easier to view.
```


```python
ddf.npartitions
```


```python
mean_age = ddf['age'].mean()
mean_age.visualize(format='svg')
```

The following line will execute the graph.


```python
mean_age.compute()
```


```python
!nvidia-smi
```

And this will presist the data on the GPU.


```python
ddf = ddf.persist()
```


```python
!nvidia-smi
```

This shows that we no longer have operations on our dask graph.


```python
ddf.visualize(format='svg')
```

And since we persisted the data, performaing computations on the dataset is much faster from now on.


```python
ddf['age'].mean().compute()
```


```python
ddf.head() # As a convenience, no need to `.compute` the `head()` method
```


```python
ddf.count().compute()
```


```python
ddf.dtypes
```
