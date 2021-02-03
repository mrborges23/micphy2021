## **Session 3**. Bayesian polymorphism-aware phylogenetic inference

In this session, we will perform Bayesian phylogenetic inference with the polymorphism-aware phylogenetic models. We will learn about the virtual PoMos and how we can account for nucleotide usage bias likely present in our data sets while infering phylogenetic trees. 

* &#8600; Theoretical_part

In this tutorial, we will infer the evolutionary history of 12 great apes populations. The data was taken from Prado-Maritinez (2012), and in this tutorial, we will be using a toy example of only 1000 sites. 

First of all, create a folder called "Session_3" and download the following files into it.

* ```weighted_sampled_method.cpp```
* ```counts_to_pomo_states_converter.R```
* ```great_apes_1000.cf```

* ```great_apes_pomotwo_alignment.txt```
* ```great_apes_pomothree_alignment.txt```

* ```great_apes_pomotwo.Rev```
* ```great_apes_pomothree.Rev```

Inside this folder, create two more: one called **data** and another called **output**.

1. The first step in this tutorial is to convert the allelic counts into PoMo states. As we will be using the virtual PoMos Two and Three, the virtual population sizes are, respectively, 2 and 3. We will be using the sampled-weighted strategy to correct for sampling errors. This script is implemented in **C++**, and we will run it using **R** and the package **Rcpp**.  Open the ```counts_to_pomo_states_converter.R``` file and make the appropriate changes to obtain your PoMo alignments suited for PoMoTwo and PoMoThree. 
```r
count_file <- "count_file.txt"     # count file
n_alleles  <- 4                    # the four nucleotide bases A, C, G and T
N          <- 10                   # virtual population size

alignment <- counts_to_pomo_states_converter(count_file,n_alleles,N)
```
Place the produced alignments inside the **data** folder.

2. Now, open the ```great_apes_pomothree.Rev``` file using an appropriate text editor. First load in the PoMo alignments using the ```readCharacterDataDelimited()``` function. This functions requires you to input the number of expected states: these are 10 for PoMoTwo and 16 for PoMoThree.
```
data <- readCharacterDataDelimited("data/great_apes_pomothree_naturalnumbers.txt", stateLabels=16, type="NaturalNumbers", delimiter=" ", headers=FALSE)
```
Information about the alignment can be obtained by typing ```data``` (o whatever name you have identified the alignment).
```
>data
NaturalNumbers character matrix with 12 taxa and 5000 characters
================================================================
Origination:                   
Number of taxa:                12
Number of included taxa:       12
Number of characters:          5000
Number of included characters: 5000
Datatype:                      NaturalNumbers
```

3. Next we will specify some useful variables based on our dataset. The variable data has member functions that we can use to retrieve information about the dataset. These include, for example, the number of species and the taxa. We will need that taxon information for setting up different parts of our model.

4. Additionally, we set up a (vector) variable that holds all the moves for our analysis. Recall that moves are algorithms used to propose new parameter values during the MCMC simulation. Similarly, we set up a variable for the monitors. Monitors print the values of model parameters to the screen and/or log files during the MCMC analysis.

5. Estimating an unrooted tree under the virtual PoMos requires specification of two main components: (1) the PoMo model and (2) the Tree Topology and Branch Lengths. A given substitution model is defined by its corresponding instantaneous-rate matrix, Q. PoMoTwo and PoMoThree have three free parameters in common: the population size, the allele frequencies and the exchagebilities. PoMoThree additionally has the allele fitnesses, as it accounts for selection. The functions fnReversiblePoMoTwo4N() and fnReversiblePoMoThree4N() will create an instantaneous-rate matrix for a character with n states.


The weight specifies how often the move will be applied either on average per iteration or relative to all other moves. 


6. The tree topology and branch lengths are stochastic nodes in our phylogenetic model. We will assume that all possible labeled, unrooted tree topologies have equal probability. Some types of stochastic nodes can be updated by a number of alternative moves. Different moves may explore parameter space in different ways, and it is possible to use multiple different moves for a given parameter to improve mixing (the efficiency of the MCMC simulation). In the case of our unrooted tree topology, for example, we can use both a nearest-neighbor interchange move (mvNNI) and a subtree-prune and regrafting move (mvSPR). These moves do not have tuning parameters associated with them, thus you only need to pass in the topology node and proposal weight.




7. Finally, we combine the tree topology and branch lengths. We do this using the ```treeAssembly()``` function, which applies the value of the ith member of the br_lens vector to the branch leading to the ith node in topology. Thus, the psi variable is a deterministic node:


We have fully specified all of the parameters of our phylogenetic model—the tree topology with branch lengths, and the substitution model that describes how the sequence data evolved over the tree with branch lengths. Collectively, these parameters comprise a distribution called the phylogenetic continuous-time Markov chain, and we use the dnPhyloCTMC constructor function to create this node. This distribution requires several input arguments:

the tree with branch lengths;
the instantaneous-rate matrix Q;
the type of character data.


Once the PhyloCTMC model has been created, we can attach our sequence data to the tip nodes in the tree.

Note that although we assume that our sequence data are random variables—they are realizations of our phylogenetic model—for the purposes of inference, we assume that the sequence data are “clamped” to their observed values. When this function is called, RevBayes sets each of the stochastic nodes representing the tips of the tree to the corresponding nucleotide sequence in the alignment. This essentially tells the program that we have observed data for the sequences at the tips.

Finally, we wrap the entire model in a single object to provide convenient access to the DAG. To do this, we only need to give the model() function a single node. With this node, the model() function can find all of the other nodes by following the arrows in the graphical model:


The mnFile monitor will record the states for only the parameters passed in as arguments. We use this monitor to specify the output for our sampled trees and branch lengths.

monitors.append( mnFile(filename="output/primates_cytb_JC.trees", printgen=10, psi) )
Finally, create a screen monitor that will report the states of specified variables to the screen with mnScreen:

monitors.append( mnScreen(printgen=100, TL) )
This monitor mostly helps us to see the progress of the MCMC run.


