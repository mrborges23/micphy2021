## Bayesian Polymorphism-aware phylogenetic models

In this session, we will perform Bayesian phylogenetic inference with the polymorphism-aware phylogenetic models. We will learn about the virtual PoMos and how we can use them to account for nucleotide usage bias likely present in our data sets. 

* &#8600; Theoretical_part

For this session, you will need **RevBayes**, but we will also need **R** or RStudio with the package **Rcpp** installed. In this tutorial, we will infer the evolutionary history of 10 fruit fly populations. The data was taken from FlyPop, and in this tutorial, we will be using a toy example of only 10 000 sites and ten populations. 

First of all, create a folder called "Session_3" and download the following files into it.

* ```weighted_sampled_method.cpp```
* ```counts_to_pomo_states_converter.R```
* ```fruit_fly_counts.txt```
* ```fruit_fly_pomotwo_alignment.txt```
* ```fruit_fly_pomothree_alignment.txt```
* ```virtual_pomo_script.Rev```

Inside this folder, create two more: one called **data** and another called **output**.

1. The first step in this tutorial is to convert the allelic counts into PoMo states. As we will be using the virtual PoMos Two and Three, the virtual population sizes are, respectively, 2 and 3. We will be using the sampled-weighted strategy to correct for sampling errors. This script is implemented in **C++**, and we will run it using **R** and the package **Rcpp**.  Open the ```counts_to_pomo_states_converter.R``` file and make the appropriate changes to obtain your PoMo alignments suited for PoMoTwo and Three. 

```r
count_file <- "count_file.txt"     # count file
n_alleles  <- 4                    # the four nucleotide bases A, C, G and T
N          <- 10                   # virtual population size

alignment <- counts_to_pomo_states_converter(count_file,n_alleles,N)
```

2. The open the ```virtual_pomo_script.Rev``` file. 