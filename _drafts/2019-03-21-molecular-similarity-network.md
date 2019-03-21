---
title:  "Molecular similarity network with visualised structures"
date: 2019-03-21
permalink: /posts/2019/03/molecular-similarity-network/
categories: 
  - short
tags:
  - python
  - jupyter notebook
---

Analogous to a Sequence Similarity Network (SSN), molecular similarity network visualises Tanimoto similarity between molecules. iwatobi shows [here](https://iwatobipen.wordpress.com/2019/03/19/draw-molecular-network-on-jupyter-notebook-with-rdkit-and-cytoscape-js-rdkit-cytoscape/) and [here](https://iwatobipen.wordpress.com/2019/03/20/draw-molecular-network-on-jupyter-notebook-with-rdkit-and-cytoscape-js-2-rdkit-cytoscape/) using Python packages RDKit, networkx, and cyjupyter.

Here is [my own Jupyter notebook](files/molecular_similarity_network.ipynb) with a little bit of refactoring to improve the readability (I rarely use RDKit so this is more for my benefit, really). Instead of SMILES in the graph, I use the compound names instead. Here's how 20 compounds would look like:

<p align="center">
  <img src="images/msn.png" height="200px"/>
</p>

The only problem I see with this is scalability. For around 20 molecules or so this approach is good. More than that, the graph is too crowded with molecular structures and the clustering would not look obvious.

