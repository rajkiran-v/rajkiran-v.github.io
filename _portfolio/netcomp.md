---
header:
  overlay_image: /assets/images/code.jpg
  caption: "Photo credit: [**Marcus Spiske**](https://unsplash.com)"
permalink: /portfolio/netcomp/
date: 2018-01-04
toc: true
toc_label: "Contents"
---

# NetComp: Python Network Comparison

*(NetComp source code is available [on GitHub][3].)*

As I worked on my research on [network data analysis][1], it became clear that
there was a need for a Python library that implemented the analytical tools I
was interested in. The ubiquitous [NetworkX][2] package contains quite a few
metrics, but since it is such a popular package, it does not implement research
algorithms until they reach a high level of maturity. I decided to build
[NetComp][3] in order to bridge this gap. You can get it locally via

	pip install netcomp
	
## Usage

In this demo, we'll compare two Erdos-Renyi random graphs, and then compare an
Erdos-Renyi graph to a planted partition graph. We expect that the distance
between the latter pair should be greater than the distance between the former,
as they are drawn from distinct models. We'll use graphs of size 100, and set
the parameters for the planted partition graphso that it has the same volume as
the Erdos-Renyi graph.

	>>> import netcomp as nc
	... import networkx as nx
	
	>>> G1 = nx.erdos_renyi_graph(100,0.1)
	... G2 = nx.erdos_renyi_graph(100,0.1)
	... G3 = nx.planted_partition_graph(2,50,0.19,0.01)
	
	>>> A1,A2,A3 = [nx.adjacency_matrix(G) for G in [G1,G2,G3]]
	
	>>> d0 = nc.lambda_dist(A1,A2,kind='laplacian_norm')
	... d1 = nc.lambda_dist(A1,A3,kind='laplacian_norm')
	
	>>> print({:.03f}.format(d1/d0))
	
	3.548
	
The first principal eigenvalue of the adjacency matrix is a signature for
two-community structure. Therefore, we expect performance to improve when we
compare using only the first principal eigenvalue. Let's check:
	
	>>> d0 = nc.lambda_dist(A1,A2,kind='laplacian_norm',k=2)
	... d1 = nc.lambda_dist(A1,A3,kind='laplacian_norm',k=2)
	
	>>> print({:.03f}.format(d1/d0))
	
	37.409
	
Performance increases by an order of magnitude. This sort of comparison can be
done using the adjacency or Laplacian spectrum (set using the `kind` keyword
argument), or via non-spectral distances. A complete list of included graph
distances is provided below.

## Design Principles

The guiding principles behind the library are:

- **Flexibility.** Graphs can be input as either dense NumPy or sparse SciPy
  arrays/matrices.
  
- **Speed.** All algorithms must run in linear or near-linear time, and have
  efficient implementations leveraging sparse data structures when appropriate.
  
I opted to utilize arrays as the fundamental data structure of the library,
rather than creating a custom `Graph` class or extending that of NetworkX. This
has some drawbacks in terms of flexibility; NetworkX supports arbitrary vertex
labels and edge properties, whereas using an array implicitly assumes integer
vertex labels. The benefit of such an approach, however, is that we avoid the
costly conversion to and from arrays, which is the format in which networks are
often encountered in application.

## Distances Included in Library

The graph distances included are:

- **Resistance Distance.** Compares networks by looking at differences in their
  resistance structure. Also included is the renormalized resistance distance,
  which extends this metric to disconnected networks and networks of different
  sizes. Our paper studying the properties of the renormalized resistance
  distance can be found [on the arXiv][5].

- **DeltaCon.** Another popular distance, [DeltaCon][4] is comparable to the resistance
  distance. It uses an alternative method of calculating node affinities, and
  then looks compares the resulting structure for the two networks in question. 
  
- **NetSimile.** [NetSimile][7] examines the statistics of a variety of local network
  measures, and compares statistical properties (mean, standard deviation, etc.)
  of these measures. We implement it as originally proposed, but this method is
  highly extensible, and can include any network measure of interest.

- **Spectral Distances.** These distances compare graphs via looking at the
  spectrum (eigenvalues) of the various matrix representations of the graph. The
  most common matrix representation of the graph is the adjacency matrix, but
  this method is also implemented on the Laplacian, both in its normalized and
  unnormalized forms.

- **Graph Edit Distance.** Trivial to calculate, and implemented in NetworkX, but
  we often use it as a baseline, so I included it.
  
Note that the vertex affinity matrices calculated by the resistance distance and
DeltaCon are themselves directly accessible. Also useful is a helper function
`_eigs` which calculates the `k` largest (or smallest) eigenvalues of a
symmetric matrix, using the appropriate method depending on the input data type
(sparse or dense) and the number of eigenvalues to be calculated.[^fnote1]

## Future Work

The library is still in alpha, and is incomplete in its implementation of the
various algorithms. In particular, it lacks the fast implementation of the
resistance distance and DeltaCon, both of which are linear-time approximations
of the quadratic-time exact algorithms. The resistance distance approximation in
particular will rely on proper implementation of an
[algebraic multigrid solver][6] for linear systems, which will be an involved
process to build. I look forward to working on it.

[^fnote1]: This function must be imported directly via `from netcomp.linalg
    import _eigs`

[1]: /portfolio/networks

[2]: https://networkx.github.io/

[3]: https://www.github.com/peterewills/netcomp

[4]: https://arxiv.org/abs/1304.4657

[5]: https://arxiv.org/abs/1707.07362

[6]: http://pyamg.github.io/

[7]: https://arxiv.org/abs/1209.2684
