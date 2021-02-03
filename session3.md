## **Session 3**. Bayesian polymorphism-aware phylogenetic inference

In this session, we will perform Bayesian phylogenetic inference with the polymorphism-aware phylogenetic models. We will learn about the virtual PoMos and how we can account for nucleotide usage bias likely present in our data sets while infering phylogenetic trees. 

* &#8600; Theoretical_part

In this tutorial, we will infer the evolutionary history of 12 great apes populations. The data was taken from Prado-Maritinez (2012), and in this tutorial, we will be using a toy example of only 1000 sites. 

First of all, create a folder called "Session_3" and download the following files into it.

* &#8600;```weighted_sampled_method.cpp```
* &#8600;```counts_to_pomo_states_converter.R```
* &#8600;```great_apes_1000.cf```

* &#8600;```great_apes_pomotwo_alignment.txt```
* &#8600;```great_apes_pomothree_alignment.txt```

* &#8600;```great_apes_pomotwo.Rev```
* &#8600;```great_apes_pomothree.Rev```

Inside this folder, create two more: one called **data** and another called **output**.


### Loading the data

The first step in this tutorial is to convert the allelic counts into PoMo states. As we will be using the virtual PoMos Two and Three, the virtual population sizes are, respectively, 2 and 3. We will be using the sampled-weighted strategy to correct for sampling errors. This script is implemented in **C++**, and we will run it using **R** and the package **Rcpp**.  Open the ```counts_to_pomo_states_converter.R``` file and make the appropriate changes to obtain your PoMo alignments suited for PoMoTwo and PoMoThree. 

```r
count_file <- "count_file.txt"     # count file
n_alleles  <- 4                    # the four nucleotide bases A, C, G and T
N          <- 10                   # virtual population size

alignment <- counts_to_pomo_states_converter(count_file,n_alleles,N)
```

Place the produced alignments inside the **data** folder.

Open the ```great_apes_pomothree.Rev``` file using an appropriate text editor. First load in the PoMo alignment using the ```readCharacterDataDelimited()``` function. This functions requires you to input the number of expected states: these are 10 for PoMoTwo and 16 for PoMoThree.

```
data <- readCharacterDataDelimited("data/great_apes_pomothree_naturalnumbers.txt", stateLabels=16, type="NaturalNumbers", delimiter=" ", headers=FALSE)
```

Information about the alignment can be obtained by typing ```data``` (o whatever name you have identified the alignment).

```
>data
NaturalNumbers character matrix with 12 taxa and 1000 characters
================================================================
Origination:                   
Number of taxa:                12
Number of included taxa:       12
Number of characters:          1000
Number of included characters: 1000
Datatype:                      NaturalNumbers
```

Next we will specify some useful variables based on our dataset: these include, for example, the number of species and the taxa. We will need that taxon information for setting up different parts of our model.

Additionally, we set up a variable that holds all the moves and monitors for our analysis. Recall that moves are algorithms used to propose new parameter values during the MCMC simulation. Monitors print the values of model parameters to the screen and/or log files during the MCMC analysis.

```
moves    = VectorMoves()  
monitors = VectorMonitors()
```

### Setting up the model

Estimating an unrooted tree under the virtual PoMos requires specification of two main components: 
* the PoMo model, which in our case is PoMoTwo or PoMoThree 
* the tree topology and branch lengths.

A given PoMo model is defined by its corresponding instantaneous-rate matrix, ```Q```. PoMoTwo and PoMoThree have three free parameters in common: the population size ```N```, the allele frequencies ```pi``` and the exchangeabilities ```rho```. PoMoThree additionally includes the allele fitnesses ```phi```, as it accounts for selection. 


```N``` is taken as a fixed variable and we set it to 10 to simplify the analyses. For the great apes, we could have fixed this value to 10 000, or even consider it follows a certain distribution. 

```
# population size
N <- 10
```


Since ```pi```, ```rho``` and ```phi``` are stochastic variables, we need to specify a move to propose updates to it. A good move on variables drawn from a Dirichlet distribution (i.e., ```pi```) is the ```mvBetaSimplex```. This move randomly takes an element from the simplex, proposes a new value for it drawn from a Beta distribution, and then rescales all values of the simplex to sum to 1 again. 

```
# allele frequencies
pi_prior <- [1,1,1,1]
pi ~ dnDirichlet(pi_prior)
moves.append( mvBetaSimplex(pi, weight=2) )
```

The ```rho``` and ```phi``` parameters must be a positive-real number and a natural choice as the prior distribution is the exponential distribution. Again, we need to specify a move for this new stochastic variable and a simple scaling move ```mvScale``` typically works. The weight option inside the moves specifies how often the move will be applied either on average per iteration or relative to all other moves. 

```
# exchangeabilities
for (i in 1:6){
  rho[i] ~ dnExponential(10.0)
  moves.append(mvScale( rho[i], weight=2 ))
}

# fitness coefficients
gamma ~ dnExponential(1.0)
moves.append(mvScale( gamma, weight=2 ))
phi := [1.0,gamma,gamma,1.0]
```

The functions ```fnReversiblePoMoTwo4N()``` and ```fnReversiblePoMoThree4N()``` will create an instantaneous-rate matrix.

```
# rate matrix
Q := fnReversiblePoMoThree4N(N,pi,rho,phi)
```

The tree topology and branch lengths are stochastic nodes in our phylogenetic model. We will assume that all possible labeled, unrooted tree topologies have equal probability. In the case of our unrooted tree topology, for example, we can use both a nearest-neighbor interchange move (mvNNI) and a subtree-prune and regrafting move (mvSPR).

```
# topology
topology ~ dnUniformTopology(taxa)
moves.append( mvNNI(topology, weight=2*n_taxa) )
```

Next, we have to create a stochastic node representing the length of each of the ```2*n_taxa−3``` branches in our tree. We can do this using a for loop. In this loop, we can create each of the branch-length nodes and assign each move.

```
# branch lengths
for (i in 1:n_branches) {
   branch_lengths[i] ~ dnExponential(10.0)
   moves.append( mvScale(branch_lengths[i]) )
}
```

Finally, we combine the tree topology and branch lengths. We do this using the ```treeAssembly()``` function, which applies the value of the ith member of the ```branch_lengths``` vector to the branch leading to the ith node in topology. Thus, the psi variable is a deterministic node:

```
psi := treeAssembly(topology, branch_lengths)
```

We have fully specified all of the parameters of our phylogenetic model:
* the tree with branch lengths;
* the PoMo instantaneous-rate matrix Q;
* the type of character data.

Collectively, these parameters comprise a distribution called the phylogenetic continuous-time Markov chain, and we use the ```dnPhyloCTMC``` constructor function to create this node. This distribution requires several input arguments:
```
sequences ~ dnPhyloCTMC(psi,Q=Q,type="NaturalNumbers")
```

Once the PhyloCTMC model has been created, we can attach our sequence data to the tip nodes in the tree. Note that although we assume that our sequence data are random variables, they are realizations of our phylogenetic model. For the purposes of inference, we assume that the sequence data are *clamped* to their observed values.

```
sequences.clamp(data)
```

When this function is called, RevBayes sets each of the stochastic nodes representing the tips of the tree to the corresponding nucleotide sequence in the alignment. This essentially tells the program that we have observed data for the sequences at the tips.

Finally, we wrap the entire model in a single object. To do this, we only need to give the ```model()``` function a single node.
```
pomo_model = model(pi)
```

## Setting, running and summarizing the MCMC simulation

For our MCMC analysis, we need to set up a vector of monitors to record the states of our Markov chain. irst, we will initialize the model monitor using the mnModel function. This creates a new monitor variable that will output the states for all model parameters when passed into a MCMC function. We will sample every 10th iterate.

```
monitors.append( mnModel(filename="output/great_apes_pomothree.log", printgen=10) )
```

The mnFile monitor will record the states for only the parameters passed in as arguments. We use this monitor to specify the output for our sampled trees and branch lengths. Again, we sample every 10th iterate.

```
monitors.append( mnFile(filename="output/great_apes_pomothree.trees", printgen=10, psi) )
```

Finally, create a screen monitor that will report the states of specified variables to the screen with mnScreen. This monitor mostly helps us to see the progress of the MCMC run.

```
monitors.append( mnScreen(printgen=10) )
```

With a fully specified model, a set of monitors, and a set of moves, we can now set up the MCMC algorithm that will sample parameter values in proportion to their posterior probability. The ```mcmc()``` function will create our MCMC object. Furthermore, we will perform and combine two independent MCMC runs to ensure proper convergence.

```
pomo_mcmc = mcmc(pomo_model, monitors, moves, nruns=2, combine="mixed")
```

Now, run the MCMC. 

```
pomo_mcmc.run( generations=10000 )
```

When the analysis is complete, you will have the monitored files in your output directory. Programs like **Tracer** (Rambaut and Drummond 2011) allow to evaluate mixing and non-convergence. Look at the file called ```output/great_apes_pomothree.log``` in **Tracer**. There you see the posterior distribution of the continuous parameters.

Apart from the continuous parameters, we need to summarize the trees sampled from the posterior distribution. RevBayes can summarize the sampled trees by reading in the tree-trace file:

```
trace = readTreeTrace("output/great_apes_pomothree.trees", treetype="non-clock", burnin= 0.2)
```

The ```mapTree()``` function will summarize the tree samples and write the maximum a posteriori tree to file:

```
map_tree = mapTree(treetrace,"output/primates_cytb_JC_MAP.tree")
```

Look at the file called ```output/primates_cytb_JC_MAP.tree``` in FigTree.


## Some questions
