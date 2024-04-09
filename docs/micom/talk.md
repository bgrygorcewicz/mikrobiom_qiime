<!-- .slide: data-background="assets/isb/data-midnight.jpg" class="dark" -->

# Predicting personalized pathogen engraftment with metabolic models


### Alex Carr, Gibbons Lab

<img src="assets/isb/logo.png" width="40%">

from the *ISB Microbiome Course 2023*

<br>
<div class="footer">
<a href="https://creativecommons.org/licenses/by-sa/4.0/"><i class="fa fa-bullhorn"></i>CC-BY-SA</a>
<a href="https://gibbons.isbscience.org/"><i class="fa fa-globe"></i>gibbons.isbscience.org</a>
<a href="https://github.com/gibbons-lab"><i class="fa fa-github"></i>gibbons-lab</a>
<a href="https://twitter.com/avcarrology"><i class="fa fa-twitter"></i>@avcarrology</a>
</div>

Note:

Hello everyone, thank you for joining us for the second day of this years ISB Microbiome Course. My name is Alex, I'm a senior graduate student in the Gibbons lab and I'll be the instructor for today's portion of the course. The focus of todays course is personalized predictions of microbiome mediated responses, using metabolic modeling.

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

Let's get the slides first (use your computer, phone, TV, fridge)

*https://gibbons-lab.github.io/isb_course_2023/micom*

Note:

Before we get started, let's make sure you're set up with the course notes. You can open these slides using the url shown here, which should also be in the email that was sent to you prior to the course. The TAs will also be posting this link in the Slack channel, so you can find it there as well.

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Quick reminder :clock:

<img src="assets/materials.png" width="80%">

Note:

As a reminder, as with yesterday, this course will be taught across three modules. The presentation slides we're currently looking at will contain explanations of the logic and methods used in the course, as well as links to pertinent materials related to the material included in the course. The analysis and modeling, as well as visualizations of results, will take place in the course notebook, hosted in Google Collab. You can navigate to the notebook using the link on the next slide, or from the email sent to you before the course. Finally, the Slack chat is available to you at any time during the course for questions or comments. So if you have any questions, don't hesitate to post them there and the TAs will be happy to help you out.

---
<!-- .slide: data-background="var(--primary)" class="dark" -->

## Modeling pathogen engraftment: will it stick?

Note:

For this next part of the course, we'll revisit the theme of this years course: Predicting personalized pathogen engraftment with metabolic models

---

## <i>Clostridioides difficile</i> (C. diff) is a leading cause of GI track infections

<img src="assets/cdiff-global.jpeg" width="50%">


<div class="footnote">

Rodriguez-Palacios et al. https://doi.org/10.3389/fmed.2020.00009

</div>

Note:

Clostridioides difficile or C. diff is a leading cause of morbidity and mortality globally. In the US alone CDI affects nearly 500k individuals annually, ~6% of which are fatal. As you can see in this graffic CDI disproportionately affects the US, Australia, parts of Europe and parts of Africa.

---

## <i>C. diff</i> colonization and the transition to infection

More of us have more <i>C. diff</i> in the gut than you might expect

<img src="assets/cdiff-colonization.png" width="70%">

<div class="footnote">

Crobach, M. et al. https://doi.org/10.1128/cmr.00021-17

</div>

Note:

While CDI is a serious issue, C. diff isn't a bad guy in every context. In adults estimates suggest 4-15% may be asymptomatic carriers
of C. diff, while in infants the rate of colonization is much higher at around 30%. Infection develops following disruptions to the microbiome, such as antibiotic use and diarrhea. In these context C. diff spores allow it to persist following disruption and without competition C. diff can bloom, leading to CDI.

---

## Predicting <i>C. diff</i> colonization with MICOM

<img src="assets/micom-cdiff-workflow.png" width="40%">


Note:

We want to be able to predict C. diff engraftment with MICOM. To do so we'll be using the following workflow. As a reminder, we'll be working with samples from healthy FMT donors and individuals who had CDI and recieved an FMT. We'll import the abundance tables generated in yesterdays tutorial and inject all them with 10% C. diff. A sort of in-silico invasion assay. We'll then estimate growth rates and fluxes and use this information to asses the susceptibility of our samples to invasion. We'll also use the flux predictions to understand what niches C. diff might be occupying when it invades.

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Let's set up our Notebook!

:computer: Let's switch to the notebook

<br>

<a href="https://colab.research.google.com/github/gibbons-lab/isb_course_2023/blob/main/micom_2023.ipynb"
   target="_blank">Click me to open the notebook!</a>

Note:

Before we jump into the material, let's follow the link to the notebook and familiarize ourselves over there.

---

# Functional analyses

Tries to predict what the microbiome *does* from sequencing data.

Uses gene/transcript/protein/metabolite abundances (metagenomics, metatranscriptomics, proteomics or metabolomics).

Gene content yields metabolic *capacity* or *potential*.


Note:

Yesterday, we used QIIME2 to analyze sequencing data, starting from raw sequences and ultimately generating a table of amplicon sequence variants or ASVs. We also assigned taxonomy to our ASVs and used diversity metrics to compare samples. Today we'll look at functional analyses of the gut microbiome. That is, rather than looking at what populates the microbiome, we'll be determining what is happening in the microbiome, which is not always fully explained by taxonomy alone. When we first say functional analysis, a few things might jump into mind, like some of the 'omics methods listed here. Most commonly, to determine the function of the microbiome, one will do a metagenomic study, from which you can see what genes are present in the community. Mapping these genes to a reference database is simple and from there we could infer what reactions might take place in the system, and put together a network of reactions taking place. There is a caveat, though, in that gene abundance data doesn't always tell the full story. That's because gene abundance really only reports the metabolic capacity of a community. If you imagine a microbial community with really high abundance of starch degradation genes, you might infer that a lot of starch degradation pathways are active. But if the host doesn't eat any starch, then clearly this inference is wrong. Gene abudances tell you what *could* happen, not necessarily what is happening.

---

<!-- .slide: data-background="var(--secondary)" class="dark" -->

# Genes abundances are cool but not what you really care about*

<div class="footnote">

hot take :fire:

</div>


Note:

Alright, so genes abundances and other 'omic approaches are cool, but they don't necessarily tell the whole story. So, today we'll explore an alternative approach to analyzying the function of the microbiome: predicting metabolic flux. Fluxes will tell us more about the actual metabolic behavior of the microbiome than abundances can.

---

## Fluxes

<img src="assets/fluxes.png" width="45%">
<video width="45%" autoplay loop>
  <source src="assets/fluxes.mp4" type="video/mp4">
</video>

<div class="footnote">

video courtesy of [S. Nayyak](https://twitter.com/Na_y_ak) and [J. Iwasa](https://twitter.com/janetiwasa)

</div>

Note:

Great, so what are fluxes? Rather than abundances, which are a measure of concentration, fluxes are measures of mass conversion, into the system and through internal reactions. Since they represent a rate, rather than a concentration, fluxes are measured in units of concentration per unit time, for instance mmol per hour. In bacterial communities, we typically scale flux by relative abundance, to account for differential metabolic contributions by more- or less-abundant taxa. So why am I claiming that fluxes are more informative than abudances? Well if we look at the example on the right hand side, which shows glucose going into glycolysis and the TCA cycle, we see that the cell in question is importing a ton of glucose, but that glucose is almost immediately being converted into pyruvate. If we were to measure interal glucose levels, they might be super low. This might lead us to incorrectly assume that glucose isn't required for this cell to grow. Obviously this doesn't tell the whole story, though, since glucose is clearly being utilzied. What would be more informative, would be to see the flux of glucose into the cell, and the resulting pathways that are activated and metabolites produced. Clearly, we can learn a lot from looking at fluxes in a system like this. The problem with measuring fluxes, however, is that it is laborious and quite costly. Longitudinal metabolomics can work, in which we measure metabolic abundances across time points, and calculate fluxes between those timepoints, but this takes time, effort and expense and is limited to those metabolites that are included in the metabolic panel. It also often requires growth in a defined medium. Isotopic labeling is another option, wherein we feed the community  isotopically labeled substrate using natural isotobes of carbon or nitrogen, and look for the appearance of those isotobes in other cellular metabolites. However, that can be even more costly and is still limited to just those metabolites being investigated. So what we need is a way to easily and accurately estimate fluxes, without the labor and expense associated with these methods. Luckily, there is already a computational tool for that.

---

<!-- .slide: data-background="var(--secondary)" class="dark" -->

# Flux Balance Analysis (FBA)

Can we infer the most likely fluxes in a biological system?


Note:

To do just that, we can use a method called flux balance analysis, which has become more popular in microbiome research in previous years. This is a computational method that uses genomic information to infer the most likely set of fluxes in a biological system. We can use this to predict the metabolic behavior of a microbe using genome scale metabolic modeling.

---

## The flux cone

<img src="assets/flux_cone.png" width="100%">

Note:

Let's take a look at how FBA works and how we can use that to infer these fluxes. FBA is a powerful computational tool, that allows us to infer fluxes through reactions in a system by reducing the possible solution space of fluxes to a biologically relevant one. FBA makes the critical assumption that the system being modeled, in this case the metabolism of a microbe, is in a state of constant flow, or steady state. This just means that the inflow and outflow of reactions in the systems are equal. A good analogy to this is opening a window in your house: you'll have air flowing in, and air flowing out, but you won't suddenly have more flowing in than out. If you did then your house would expand and pop like a balloon due to the build up of pressure. That doesn't happen. The steady state assumption mimicks this, and is applicable in several biological phenomena. If we look at our example reaction here, we have hydrogen and oxygen combining to form water. In steady state, we can say that stoichiometrically, the amount of hydrogen and oxygen consumed are equal to the amount of water produced. We can represent this mathematically with this first equation - reactants with negative coefficients and products with positive coeffients, balanced to zero. Repeating this for all the reactions in our system and factoring out the fluxes, we end up with a stoichiometric maxtrix designated "S" that represents all of the reactions, as well as an unknown vector of fluxes, designated "v", which we are solving for. To satisfy our assumption of constant flow, we set S dot v equal to zero, which sets up our system of equations. Additionally, we can futher constrain the problem, given known limits for the fluxes. Solving our set of equations leaves us with a flux cone, the space in multidimensional flux space that includes all sets of fluxes satisfying our constraints. You might notice, however, that there are an infinite number of solutions in our flux cone that satisfy these constraints. So, whats next??

---

The goal of FBA is to *reduce* the flux space to a *biologically relevant* one.

Note:

Well, the goal of FBA is to reduce that flux space to one that is biologically relevant, so that it mimics the actual behavior of the system as well as possible. But how do we find an optimal solution, within the possible solution space?


---

## Genome-scale metabolic modeling

<img src="assets/fba.png" width="100%">

Note:

To find that solution, we can look at growth rates. We know that bacteria can only be present in the system if they can grow, otherwise they would be excreted and no longer exist in the system. In practice, this means we can say we want the solution in the flux cone that corresponds with maximum biomass, since this is the generally the biological goal of a growing microbe. Biomass production can also be represented as a flux, since it is in essence a measure of mass conversion. What's great about flux balance analysis is that we can add information from a number of sources to constrain the problem and determine our optimal solution. As we've mentioned, we can use the reactions present in the system to build a stoichiometric matrix, mathematically representing the reactions taking place and how they interact with each other. We can actually infer this whole stoichiometric maxtrix from the the genome of a microbe, by looking at gene abundances to determine what reactions are potentially taking place in the system. Also, as we've mentioned, we make the assumption the system is at steady state. In practice, this means the bacteria is in the exponential growth phase, as the growth rate is constant and biomass production is constant, so we include this assumption to arrive at a possible solution. Additionally reversibility of reactions can be determined by thermodynamics, as many reactions can only occur in one direction thus limiting the space of possible fluxes. Finally, we can  add constraints to the import of metabolites going into a system, which we can leverage to represent the metabolites available to the system to grow. In practice, we can use this to model effects of different diets on the microbiome, adding relevance to the predictions being made by FBA.  Cumulativley these constraints help us find the region in the flux cone that represents maximum biomass, shown here in red.

---

## Selecting biologically relevant fluxes

<img src="assets/reduction.png" width="80%">

For instance, parsimonious FBA reproduces experimental fluxes in <i>E. coli</i> [very well](https://dx.doi.org/10.1038%2Fmsb.2010.47).


Note:

The art of FBA is to then find a single solution in that solution space that is the most biologically relevant. There are several methods available to find a single solution, but today we'll focus on these last two since these are leveraged by MICOM. One such method is minimizing the imports into a system - since the microbiome can be a highly competitive environment, this is biologically intuitive since the most successful residents in the microbiome would not need to rely on importing lots of metabolites to survive, rather subsisting on a smaller subset. Another common approach is parsimonious FBA, called pFBA, which basically minimizes the enzymes required to satisfy the constraints. Again, this is biologically intuitive, since bacteria are efficient beings and don't like to expend more energy than necessary on making enzymes. Really, the method you use is related to the question being asked and the system being modeled - is it competitve, is it resource limiting, etc. I encourage you to look more into these methods if you're interested in conducting FBA on your own.


---

# MICOM

<img src="assets/summary.png" width="100%">

<div class="footnote">

https://micom-dev.github.io/micom

</div>


Note:

To this point, we've focused on making genome scale metabolic models. To make metabolic models of the microbiome community, or metagenome scale metabolic models, we use a tool called MICOM, which extends flux balance analysis into microbial communities. To initially build the models, we need to pass in the relative abundance of bacteria in the sample. Due to sequencing efficiency differences, there may be some bias toward some bacteria over others, but the abundance from sequencing data should be more or less representative of the community. MICOM will then map this abundance data to a database containing hundreds genome-scale metabolic models of common gut microbes. MICOM then uses the reconstructions of those taxa present in the sample to build a massive stoichiometric matrix like the one we discussed earlier that includes not only the internal reactions within each taxon, but also the exchanges between them and external reactions, which in this case is the host. We can then specify a diet that is representative of the food being eaten by the subject of the model and use FBA with an additional regularization step to calculate unique growth rates for all the bacteria based on the process we've outlined previously. Following this, estimates of all the fluxes in the system can be generated based on those growth rates, returning a most likely flux distribution. This give us a huge amount of output data, which we can then use to make detailed and interesting predictions about imports, exports, the inner machinations of bacterial reaction networks, co-dependcies between bacteria, and a testing ground for potential interventions.


---

<img src="assets/overview.png" width="120%">


Note:

Here's the typical workflow for building metabolic models. First we build the models using abundance data and a database of genome scale metabolic models, then apply a medium representing available metabolites, and finally grow the models using a multi-step FBA method that'll we'll cover shortly. Whats neat about MICOM is that are two interfaces in which you can use it, both in python or in Qiime2. Today we'll be using the python interface, as that works in Colab, but you can preform any task with either of the interfaces, either actions like building or growing models or producing visualizations. It's also easy to switch between the two interfaces between steps, as MICOM can readily read Qiime artifacts and vice versa.

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Let's return to the models we've built

:computer: Let's switch to the notebook!

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Community-wide growth is hard :cry:

In a single genome-scale model we only have a single growth rate $\mu$. In a microbial community
we have several $\mu_i$ and a community growth rate

$$
\mu_c = \sum_i a_i\cdot\mu_i
$$

Why is this so hard? Can't we just maximize the community growth rate? Well...

Note:

Great, so now we've covered classic flux balance analysis used for genome-scale metabolic models, and it seems pretty straightforward - maximize the growth rate, and get your answer. So can we just do the same in community models? Not quite. In genome scale models, we have a single growth rate, but in community models, we have multiple growth rates - one for each taxon, as well a community growth rate calculated by multiplying the relative abundance of each taxon by its growth rate and summing them all together. If we try to maximize the community growth rate, you'll find we quickly run into trouble.

---

## Genome-scale metabolic models - well behaved

<img src="assets/gsmm.webp" width="100%">

<div class="footnote">

Schuetz et al. 2012, https://doi.org/10.1126/science.1216882<br>
Harcombe et al. 2013, https://doi.org/10.1371/journal.pcbi.1003091

</div>

Note:

Classical FBA works fairly well when predicting the growth rates of individual bacteria. Here we see the results of a studying that compared empirically measured fluxes with those predicted by FBA across a series of evolved E. coli strains. We see that the predictions agree well with the empirical estimates and the evolved strains are able to achieve growth rates that are 90-95% of the theoretical maximum.

---

## Community-scale metabolic models - pretty rowdy

<img src="assets/csmm.webp" width="100%">

<div class="footnote">

Diener et al. 2023, https://doi.org/10.1128/msystems.01270-22<br>
Senne de Oliveira Lino et al. 2021, https://doi.org/10.1038/s41467-021-21844-7

</div>

Note:

Its when we consider communtities that things start to break down. Classical FBA doesn't do so great here. One of the issues is that given multiple taxa, optimal community biomass may be achieved by various linear combinations of the  growth rates of the individual taxa. That is, there is a non-unique solution space for the growth rates and fluxes. So you might have a solution where the optimal community biomass is achieved by only one organism growing. Additionally, experimental results suggest optimizing community biomass may be a slightly flawed approach, as empiraclly many communities achieve lower overall biomass as diversity increases due to competitive interactions as shown by the figure on the right.

---

## Estimating community wide growth rates with cooperative trade off flux balance analysis (ctFBA)

<img src="assets/growth_cone.png" width="60%">

Note:

This is where cooperative trade off flux balance comes in. This approach is able to generate unique, biologically relevant, solutions to the community growth rate problem posed just a moment ago. Lets try to understand how. First, lets consider the growth curves on the left. As many of us are famililar, bacteria and other organisms typically start at low biomass and low growth rate and increase their biomass until they've consumed all their resources. In doing so, they typically achieve a maximum growth rate somewhere along the way- as shown in the grey region, that eventually returns to zero or close to zero. This growth rate trajectory can be mapped in a growth cone as shown below. Dynamic models typically are able to capture this trajectory while classical FBA can provide a solution somewhere in the optimal region of the growth cone but would not be able to contrain it. ctFBA introduced a biological meaninful contraint by minimizing the distance between the point of zero growth and the theoretical maximum, as shown on the right. This approximates the trajectory expected empiraclly. This constrain is achieved using an L2 style regularization that minimized the sum of the squared growth rates ofthe community. Additionally, ctFBA allows for suboptimal community biomass, which helps balance the tradeoff between individual growth rates with that of community.

---

*Cooperative Tradeoff FBA* allows us to treat metagenome-scale models with the *same*
methods as genome-scale metabolic models (pFBA, minimal media, etc).

---

## But does it work?

<img src="assets/validation.png" width="100%">

<div class="footnote">

https://doi.org/10.1128/mSystems.00606-19

</div>


Note:

The age-old question. Here, we see results from the orignal micom publication validating the ctFBA approach described. On the left we see distributions of individual taxa across a large number of communities and conditions. What we see is that if dont integrate a  regularization step (no L2 parameter minimization), there is a very small number of taxa that can achieve high growth rates, and a majority effectively not growing at all. Obviously, this is not what's actually happening in the microbiome, and this solution is not relevant to the system. If we apply the L2 regularization strategy and require the community to achieve its theoretical maximal growth rate, we see that a lot more taxa are able to grow - that is the L2 reguluarization better distributes growth across the individual communitu members. However, as previously noted, this maximal growth rate might not be achievable with all taxa growing. Lowering the tradeoff value just a little to 0.7, we see that almost all the taxa are able to grow, echoing what we expect to see in the microbial community. This result by itself statisfies our assumption that taxa present in the sample should be able to grow. The team also validated predicted growth rates by comparing them with replication rate, which is generally proportional with growth rate. For these data, the team had metagenomes. Its been shown that coverage profiles of metagenomic reads for fast growing bacteria can be used as a proxy for growth rate. You can do this by measuring the decay in read coverage from the origin of replication. Using this method, the team could estiamte  growth rates in vitro, and compare these to growth rates predicted by MICOM. You can see in this panel on the right that without regularization, or at high tradeoff values with regularization, the predicted growth rate did not correlate with calculated growth rate. Lowering the tradeoff value slightly showed a much stronger correlation with calculated growth rate, thus showing that ctFBA provides a biologically meaninful solution to the community growth rate problem. So, in short, cooperative tradeoff with L2 regularization helps us reach a solution wherein most taxa are able to grow, and the growth rates are a much closer to what you would expect.

---

Easy peasy. What's taking so long then?

<br>

Well, metagenome-scale models are slightly larger... :sweat:

Note:

Awesome, these methods seem great and the math all checks out. So why is it taking so long to run these workflows? Well, typical MICOM models are a bit more complex than the examples I've been using to illustrate the methods, like our two e coli community.

---

<img src="assets/model_gephi.png" width="90%">


Note:

This is a visualization of a standard metagenome-scale metabolic model. Each pink dot represents a metabolite, and each blue dot a reaction, with black lines connecting them. Each of these regions is a single taxon in the community. You can see that the community model contains nearly 70k reactions, over 45k metabolites, and nearly 300k connections. So you can imagine that finding the optimal flux solution in this model takes some computational power, and sometimes a little patience!

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Before we look at our results...

Note:

I know at this point you must be so excited to see the results of the MICOM growth simulation, and your wait is almost over, but first lets take a look at some of the visualizations we'll create with the output from our results. Since MICOM gives us so much great data, it can be a little overwhelming at first, but don't worry. It's actually very easy to make great visualizations in MICOM, or extract the data to use in your favorite visualization package.

---

## Growth Rates

Visualize growth rates of individual taxa per sample

<img src="assets/growthrates.png" width="50%">

Note:

First and foremost, we'll take a look at growth rates of the indivual taxa in each of our models. MICOM has a built in function for this, so it will be very easy to build. Here we have a scatter plot with log scaled growth rate on the y axis, for each taxa on the x axis. This plot is a great way to check that your models are growing correctly at a typical growth rate, like what we see here. It can also tell you which microbes are doing well and have high growth rates in the dietary context you have used to simulate the models. For instance, here we see that prevotella has a high growth rate, and seems to be thriving. We'll see in our results that the growth rates for individual taxa can differ quite a bit. This echoes one of our primary points, which is that context is a major factor in the metabolic activity of the microbiome, and it is important to consider.

---

## The niche space

The context-dependent way in which a microbial taxon uses its environment

<img src="assets/Global-UMAP-cdiff-genuscorr.png" width="75%">


Note:

Next, we'll look at a visualization of the niche space of each taxon in each sample. The niche space represents the context-dependent way in which a taxon uses it's environment - in this case, how it uses the metabolites available to it. Since plotting all the metabolic exchanges wouldn't be possible, we'll use tSNE to make the plot. This technique takes high dimensional data and reduces it to two dimensions, such that similar points are closer together and less similar points are spaced farther apart. This gives us an idea of the metabolic niche each taxon falls into, and how similar or different it is from the other taxa in the community. Like I mentioned, niche space is context dependent. Some taxa change their metabolic behavior depending on what metabolites are present in their environment, allowing them to survive in multiple niche spaces. This adaptation can be helpful for their survival. In example niche plot shown I've used UMAP to accomplish the dimensionality reduction, which is fairly similar to tSNE conceptually. Here I'm shown the results for individual taxa across thousands of samples. Interestingly, what we see is that individual taxa often cluster into one or just a few decrete locations, suggeting the often select from a finite number of metabolic strategies.

---

## Comparative fluxomics

Metabolite exchanges are highly dependent on environmental context and can provide insights into the realized niche of organisms and communitites

<img src="assets/cdiff-heatmap@250x.png" width="80%">

Note:

Finally, we'll take a look at the metabolic imports predicted from the growth of each metabolic model. As shown here, we'll construct a heatmap illustrating the amount of different metabolites produced and exported from taxa in each sample. In this case, each row represents a metabolite, and each column represents one of our samples, with cells colored in a logarithmic color map. While MICOM does have a built in tool to build heatmaps like the one shown here, we'll use this opportunity to take our output and build a visualization in another package, in this case we will use Seaborn. This will additionally allow us to plot the results of growth on both diets on a single plot. Similar to the niche space, the metabolic activity of any taxon in a community, and therefore its exchanges, is dependent on the context in which it grows 

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Let's check out our results and build some visualizations

:computer: Let's switch to the notebook!

---

## Your turn

Lets figure out how to use MICOM investigate the niche occupied by C. diff in our samples.

<img src="assets/coding.gif" width="50%">

Note:

Finally, once we're done looking at our output, it'll be your turn to jump in the drivers seat and explore using MICOM on your own. You can use the cells at the end of the notebook to explore loading a single model, applying a medium, and simulating growth.


---

<!-- .slide: data-background="assets/isb/microbes-azure.jpg" class="dark" -->

### And we are done :clap:

<div style="display: flex; justify-content: space-around; align-items: center;">

<div>

Nick Bohmann <br>
Sean Gibbons <br>
Alyssa Easton <br>
Katherine Ramos Sarmiento <br>
Noa Rappaport <br>
Karl Gaisser <br>
Chloe Herman <br>
Greg Caporaso <br>
Christian Diener

</div>

<div>

Dominic Lewis <br>
Allison Kudla <br>
Audri Hubbard <br>
Joe Myxter <br>
Thea Swanson <br>
Victoria Uhl<br>
Connor Kelly<br>
Shanna Braga<br>
ISB Facilities Team

</div></div>
<br>

# Thanks! :heart:
