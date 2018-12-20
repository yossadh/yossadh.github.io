---
title:  "Why do simulation?"
date: 2018-12-20
permalink: /posts/2018/12/why-do-simulation/
categories: 
  - short
tags:
  - computational biology
---
Why do we do what we do? — is a question that should be asked by practitioners of any vocation

Michel Cuendet at EMBL in his Molecular Dynamics Simulation (2008) course [summarises](https://embnet.vital-it.ch/CoursEMBnet/Pages3D08/slides/MD_cours_opt.pdf) very well why we do simulations. The following points are taken from his slide:
>### Why we do simulation
>- In some cases, experiment is :
>   - impossible — inside of stars, weather forecast
>   - too dangerous — flight simulation, explosion simulation
>   - expensive — high pressure simulation, windchannel simulation
>   - blind — some properties cannot be observed on very short time-scales and very small space-scales
>- Simulation is a useful complement, because it can:
>   - replace experiment 
>   - provoke experiment 
>   - explain experiment 
>   - aid in establishing intellectual property

To others outside the field, especially my experimental collaborators, I usually highlight these points:
- **blind**: Experiments have no access at the microscopic scale. MD simulations can at least suggest microscopic mechanical desciptions. Needless to say, there has to be some validation, e.g. agreement of some macroscopic observation. 
- **pre– and/or post–experiment**: I like to think this way: simulations can come _before_ as prediction/provocation, or they can come _after_ as explanation. Of course this distinction is not absolute. Computational and experimetal works can go hand-in-hand synergistically, or feedback each other in an iterative loop. (While computational work completely replacing experiment work altogether is probably not so realistic).

  Here is a pertinent quote:
  >**1.3.2.3. Molecular dynamics as prior belief in a Bayesian framework**  
  A recent trend in biomolecular simulations is to deemphasize the predictive capability of molecular dynamics, and focus instead   on potential synergy with experiments. Simulations are thus used as a component of a Bayesian inference task whose goal is to associate a conformational ensemble to a set of experimental observations. When the latter are too sparse or unrestrictive, the problem is underdetermined, and the best one can do is to find structural parameters that are maximally probable given the experimental data. According to Bayes' rule, this probability is proportional to the product of the likelihood function (which measures the probability of each experimental outcome, given the structural parameters) and a prior probability distribution (which reflects our knowledge of each structure before doing any experiments). Several recent lines of research [159] are based on prior probability distributions derived from molecular dynamics, and enable the incorporation of sparse experimental information to solve, for instance, the protein structure prediction problem. This agnostic approach does not require molecular dynamics to be exact in any sense, just to be “not too wrong.”  
  – [Howard, R. J., Carnevale, V., Delemotte, L., Hellmich, U. A., & Rothberg, B. S. (2018). Permeating disciplines: Overcoming barriers between molecular simulations and classical structure-function approaches in biological ion transport. Biochimica et Biophysica Acta - Biomembranes, 1860(4), 927–942.](https://doi.org/10.1016/j.bbamem.2017.12.013)
  
  And the weather forecast metaphor is also useful as illustration of prediction:
  >In several respects, this approach to molecular dynamics calculations is analogous to weather forecasting. To predict tomorrow’s weather today, meteorologists do not run a single simulation. They recognise that they will never know exactly the initial conditions that are required to perform a single fluid dynamics based calculation, in a system of Navier–Stokes equations known to exhibit dynamical chaos. So they perform many simulations with slightly varying initial conditions—i.e. they compute the behaviour of ensembles—in order to make reliable probabilistic predictions of what the weather will look like the next day. Speed too is of the essence here as it is there. Just as the public wishes to know tomorrow’s weather today, not in three weeks time, for the application of free energy calculations in drug development and clinically based personalised medicine.  
– [Coveney, P. V, & Wan, S. (2016). On the calculation of equilibrium thermodynamic properties from molecular dynamics. Physical Chemistry Chemical Physics, 18(44), 30236–30240.](http://dx.doi.org/10.1039/C6CP02349E) 

Another review paper <[Hollingsworth, S. A., & Dror, R. O. (2018). Molecular Dynamics Simulation for All. Neuron, 99(6), 1129–1143.](https://doi.org/10.1016/j.neuron.2018.08.011)> also lists out the kind of information that MD simulation can provide. Here is my point summary:
- assess the mobility or flexibility of various regions of a biomolecule
- test the accuracy of a modeled structure or even to refine it
- determine how a biomolecular system will respond to some perturbation, e.g.:
   - ligand removal
   - mutation
   - phosphorylation and other post-translational modifications
   - different protonation states
   - transmembrane voltage or other mechanical strain
   - salt concentration
   - membrane composition
- structural basis for events that are difficult to address experimentally
- pertaining to drug discovery:
   - relatively inexpensive, although rough, filter for binding en- ergy or stability to winnow down a large pool of candidates to a smaller one that can be tested experimentally
    - generate aqualitativeunderstanding of how a biomolecule or drug works
    - guide lead optimization
    - provide substantially more accurate estimates of ligand binding affinities (free energies) than other computational approaches such as docking
    - generate multiple possible structures for virtual screening
    - help in allosteric drug design
    - assist in the design of drugs with desired binding and dissociation kinetics
