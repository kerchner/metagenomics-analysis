---
source: md
title: "Diversity tackled by R "
teaching: 40
exercises: 10
questions:
- "How can I obtain the abundance of the reads?"
- "How can I use R to explore diversity?"
objectives:
- "Comprehend which libraries are required for metagenomes diversity analysis."  
- "Grasp how a phyloseq object is made"
- "Undestand the needed commands to create a phyloseq object for analysis"
keypoints:
- "The library `phyloseq` manages metagenomics objects and computes alpha diversity."  
- "Transform your named matrixes into Phyloseq objects using `pyhloseq(TAX, OTU)`."
- "Use `help()` to discover the capabilities of libraries."
- "The library `ggplot2` allow publication-quality plotting in R."
---

# First plunge into diversity
*Look at your fingers, controlled by the mind can do great things. But imagine if each one have a little brain of its own, with 
different ideas, desires, and fears ¡How wonderful things will be made out of an artist such as that!* 
	-Ode for multidisciplinarity

Species diversity, is the number of species that are represented in a certain 
community. 
Once we know the taxonomic composition of our metagenomes, we can do diversity analyses. 
Here we will talk about the two most used diversity metrics, diversity α (within one metagenome) and β (between metagenomes).   

- α Diversity: Represents the richness (e.g. number of different species) and species' abundance. It can be measured by calculating richness, 
 and eveness, or by using a diversity index, such as Shannon's, Simpson's, Chao's, etc.  
  ![image](https://user-images.githubusercontent.com/67386612/118978296-c4735080-b93c-11eb-8421-3294b21c9c44.png)
###### Figure 1. Alpha diversity represented by fishes in a pond. Here, alpha diversity is represented at its simplest way: Richness
 
- β Diversity: It is the difference (measured as distance) between two or more metagenomes. 
It can be measured with metrics like Bray-Curtis dissimilarity, Jaccard distance or UniFrac, to name a few.  
![image](https://user-images.githubusercontent.com/67386612/119004501-3f943100-b954-11eb-82fe-cc91235072f2.png)
###### Figure 2. Alpha and Beta diversity represented by fishes in a pond.

For this lesson we will use Phyloseq, an R package specialized in metagenomic analysis. We will use it along with Rstudio to analyze our data. 
[Rstudio cloud](https://rstudio.cloud/) and select "GET STARTED FOR FREE"

## α diversity  

|-------------------+-----------------------------------------------------------------------------------------------------------------|   
| Diversity Indices |                             Description                                                                         |   
|-------------------+-----------------------------------------------------------------------------------------------------------------|   
|      Shannon (H)  | Estimation of species richness and species evenness. More weigth on richness.                                   |   
|-------------------+-----------------------------------------------------------------------------------------------------------------|   
|    Simpson's (D)  |Estimation of species richness and species evenness. More weigth on evenness.                                    |                           
|-------------------+-----------------------------------------------------------------------------------------------------------------|   
|      ACE          | Abundance based coverage estimator of species richness.                                                         |   
|-------------------+-----------------------------------------------------------------------------------------------------------------|   
|     Chao1         | Abundance based on species represented by a single individual (singletons) and two individuals (doubletons).    |            
|-------------------+-----------------------------------------------------------------------------------------------------------------|   
 

- Shannon (H): 

| Variable             |  Definition   |     
:-------------------------:|:-------------------------:  
<img src="https://render.githubusercontent.com/render/math?math=H=-\sum_{i=1}^{S}p_i\:ln{p_i}"> | Definition
<img src="https://render.githubusercontent.com/render/math?math=S"> | Number of OTUs  
<img src="https://render.githubusercontent.com/render/math?math=p_i">|  The proportion of the community represented by OTU i   

- Simpson's (D) 

| Variable             |  Definition |   
:-------------------------:|:-------------------------:  
<img src="https://render.githubusercontent.com/render/math?math=D=\frac{1}{\sum_{i=1}^{S}p_i^2}">| Definition   
<img src="https://render.githubusercontent.com/render/math?math=S"> | Total number of the species in the community   
<img src="https://render.githubusercontent.com/render/math?math=p_i" align="middle"> | Proportion of community represented by OTU i    
  
- Chao1  
  
| Variable             |  Desription |  
:-------------------------:|:-------------------------:  
<img src="https://render.githubusercontent.com/render/math?math=S_{chao1}=S_{Obs}+\frac{F_1(F_1-1)}{2(F_2 + 1)}">  | Definition  
<img src="https://render.githubusercontent.com/render/math?math=F_1,F_2">|Count of singletons and doubletons respectively    
<img src="https://render.githubusercontent.com/render/math?math=S_{chao1}=S_{Obs}">| The number of observed species    

## β diversity  
Diversity β measures how different two or more communities are, either in their composition (diversity)
or in the abundance of the organisms that compose it (abundance). 
- Bray-Curtis dissimilarity: Emphasis on abundance. Measures the differences 
from 0 (equal communities) to 1 (different communities)
- Jaccard distance: Based on presence / absence of species (diversity). 
It goes from 0 (same species in the community) to 1 (no species in common)
- UniFrac: Measures the phylogenetic distance; how alike the trees in each community are. 
There are two types, without weights (diversity) and with weights (diversity and abundance)  

It is easy to visualize using PCA, PCoA or NMDS analysis.

# Creating lineage and rank tables  

In this lesson we will use RStudio to analize two microbiome samples from CCB, you don't have to install anything, 
you already have an instance on the cloud ready to be used.   

For this purpose, we will use packages in R. A package is a family of code units (functions, classes, variables) that 
implement a set of related tasks. Importing a package is like getting a piece of lab equipment out of a storage locker 
and setting it up on the bench. Packages provide additional functionality to the basic R code, much like a new piece 
of equipment adds functionality to a lab space.

Packages like Quiime2, MEGAN, Vegan or Phyloseq in R allows us to obtain these diversity indexes by 
manipulating taxonomic-assignation data.  In this lesson, we will use Phyloseq. In order to do so, we need to generate 
an abundance matrix from the Kraken output files. One program widely used for this purpose is kraken-biom.

But before we face our first storm in this code sea, let's learn one useful tool in RStudio.

## The terminal in RStudio
RStudio has an integrated terminal that use the same language as we learn in the UNIX lessons. As well, R's terminal 
is an interface that executes programs, and is better to deal with long data sets than a visual interface.  

You can also known in which directory you are standing in the reminal,by using `pwd`. 

Let's explore the content of some of our data files. In order to do it, we have to move to 
the  folder where our taxonomic-data files are: 
~~~
$ cd /home/dcuser/dc_workshop/taxonomy
~~~~
{: .bash}

First, we will visualize the content of our directory by the `ls` command.  
~~~
$ ls
~~~~
~~~
JC1A.error  JC1A.kraken  JC1A.report  JP4D.error  JP4D.kraken  JP4D.report
~~~
{: .output}

Files `.kraken` and `kraken.report`are the output of the Kraken program, we can see a few lines of the file `.kraken` 
using the command `head`.   
~~~
$ head JP4D.kraken  
~~~
{: .bash}
~~~
U       k141_55805      0       371     0:337
U       k141_0  0       462     0:428
U       k141_55806      0       353     0:319
U       k141_55807      0       296     0:262
C       k141_1  953     711     0:54 1224:2 0:152 28211:2 0:15 953:3 0:449
U       k141_2  0       480     0:446
C       k141_3  28384   428     0:6 1286:2 0:8 28384:14 0:11 1:3 0:350
U       k141_4  0       302     0:268
U       k141_5  0       714     0:680
U       k141_6  0       662     0:628
  
~~~
{: .output}

This information may be confused, let's take out our cheatsheet to understand some of its components:
|------------------------------+------------------------------------------------------------------------------|  
| column                       |                              Description                                     |  
|------------------------------+------------------------------------------------------------------------------|  
|   C                          |  Classified or unclassified                                                  |  
|------------------------------+------------------------------------------------------------------------------|  
|    k141_0                    |FASTA header of the read(contig)                                              |                
|------------------------------+------------------------------------------------------------------------------|  
|  1365647                     | Tax id                                                                       |  
|------------------------------+------------------------------------------------------------------------------|  
|    416                       |Read length                                                                   |           
|------------------------------+------------------------------------------------------------------------------|  
|  0:6 1286:2 0:8 28384:14 0:11|kmers hit to a taxonomic id E.g. 0:1 root 6 hit, 1286 has 2 hits, etc.        |           
|-------------------+-----------------------------------------------------------------------------------------|  

There are other set of files with `.report` suffix. This is an output with the same information as the one found
in the `.kraken` files, but in a more human-readable sintax:
~~~
$ head JP4D.report  
~~~
{: .bash}
~~~
 75.95  77818   77818   U       0       unclassified
 24.05  24642   5       R       1       root
 24.01  24597   0       R1      131567    cellular organisms
 24.01  24597   562     D       2           Bacteria
 19.88  20365   581     P       1224          Proteobacteria
 15.16  15530   494     C       28211           Alphaproteobacteria
  7.92  8116    7       O       204455            Rhodobacterales
  7.84  8033    945     F       31989               Rhodobacteraceae
  1.44  1476    51      G       1060                  Rhodobacter
  0.92  943     711     S       1063                    Rhodobacter sphaeroides
  ~~~
{: .output}


## kraken-biom 
Kraken-biom is a program that creates BIOM tables from the Kraken output 
[kraken-biom](https://github.com/smdabdoub/kraken-biom)

Lets take a look at the different flags that kraken-biom have:

~~~
$ kraken-biom -h                  
~~~
{: .bash}
~~~
usage: kraken-biom [-h] [--max {D,P,C,O,F,G,S}] [--min {D,P,C,O,F,G,S}]
                   [-o OUTPUT_FP] [--otu_fp OTU_FP] [--fmt {hdf5,json,tsv}]
                   [--gzip] [--version] [-v]
                   kraken_reports [kraken_reports ...]

Create BIOM-format tables (http://biom-format.org) from Kraken output
(http://ccb.jhu.edu/software/kraken/)...
~~~
{: .output}
By a close look at the first output lines, it is noticeable that we need a specific output
from Kraken: `kraken.report`s. 

With the next command, we are going to create a table in [Biom](https://biom-format.org/) format called `cuatroc.biom`:
~~~
$ kraken-biom JC1A.report JP4D.report --fmt json -o cuatroc.biom
~~~
{: .bash}

If we inspect our folder, we will see that the "cuatroc.biom" file has been created, this is 
a biom object which contains both, the abundances of each OTU and the identificator of each OTU.
With this result, we are ready to return to RStudio's console and beggin to mannipulate or 
taxonomic-data.

#  Manipulating lineage and rank tables with phyloseq  

## Load required packages  

Phyloseq is a library with tools to analyze and plot your metagenomics tables. Let's install [phyloseq](https://joey711.github.io/phyloseq/) (This instruction might not work on certain versions of R) and other libraries required for its execution:  

~~~
$ if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

$ BiocManager::install("phyloseq") # Install phyloseq

$ install.packages(c("ggplot2", "readr", "patchwork")) #install ggplot2 and patchwork to   chart publication-quality plots. readr to read rectangular datasets.
~~~
{: .language-r}  

Once the libraries are installed, we must make them available for this R session. Now load the libraries (a process needed every time we began a new work 
in R and we are going to use phyloseq):

~~~
$ library("phyloseq")
$ library("ggplot2")
$ library("readr")
$ library("patchwork")
~~~
{: .language-r}

  
### Load data with the number of reads per OTU and taxonomic labels for each OTU.  

Let's procced to create the phyloseq object by the `import_biom` command:
~~~
merged_metagenomes <- import_biom("cuatroc.biom")
~~~
{: .language-r}

Now, we can inspect the result by asking what class is the object created, and 
doing a close inspection of some of its content:
~~~
$ class(merged_metagenomes)
$ View(merged_metagenomes@tax_table@.Data)
~~~
{: .language-r}
~~~
[1] "phyloseq"
attr(,"package")
[1] "phyloseq"
~~~
{: .output}
![image](https://user-images.githubusercontent.com/67386612/119017138-4e80e080-b960-11eb-8465-737d6197c775.png)
###### Figure 3. Table of the OTU data from our `merged_metagenomes` object.

The "class" command indicate that we already have our phyloseq object. Also, 
inside the tax_table we see that it looks just like the one created in the
last episode of the lesson. Let's get rid of some of the innecesary characters 
in the OTUs identificator and put name to the taxonomic ranks:

~~~
$ merged_metagenomes@tax_table@.Data <- substring(merged_metagenomes@tax_table@.Data, 4)
$ colnames(merged_metagenomes@tax_table@.Data)<- c("Kingdom", "Phylum", "Class", "Order", "Family", "Genus", "Species")
~~~
{: .language-r}

Finally, we can review our object and see that both datasets 
(i.e. JC1A and JP4D) are in the our object.
>If you look at our Phyloseq object, you will see that there are more data types 
>that we can use to build our object(?phyloseq), as a phylogenetic tree and metadata 
>concerning our samples. These are optional, so we will use our basic
>phyloseq object for now composed of the abundances of specific OTUs and the 
>names of those OTUs.  
{: .callout}


### Plot diversity estimates at desired taxonomic resolution

We want to know how is the bacterial diversity, so, we will prune all of the 
non-bacterial organisms in our metagenome. To do this we will make a subset 
of all bacterial groups and save them.
~~~
$ merged_metagenomes <- subset_taxa(merged_metagenomes, superkingdom == "Bacteria")
~~~
{: .language-r}

Now let's look at some statistics of our metagenomes:

~~~
merged_metagenomes
sample_sums(merged_metagenomes)
summary(merged_metagenomes@otu_table@.Data)
~~~
{: .language-r}

By the output of the sample_sums command we can see how many reads they are
in the library. Also, the Max, Min and Mean outout on summary can give us an
idea of the eveness. Nevertheless, to have a more visual representation of the
diversity inside the samples (i.e. α diversity) we can now look at a ggplot2
graph created using Phyloseq:

~~~
p = plot_richness(merged_metagenomes, measures = c("Observed", "Chao1", "Shannon")) 
p + geom_point(size=5, alpha=0.7)
~~~
{: .language-r}

![image](https://user-images.githubusercontent.com/67386612/112223149-23dc0b00-8bef-11eb-8651-677a5713a5bb.png)
###### Figure 4. Alpha diversity indexes for both samples

Each of these metrics can give insight of the distribution of the OTUs inside 
our samples. For example. Chao1 diversity index gives more weight to singletons
and doubletons observed in our samples. While Shannon is a entrophy index 
remarking the impossiblity of taking two reads out of the metagenome "bag" 
and that these two will belong to the same OTU.


> ## Exercise 1
> 
> Use the help from plot_richness to discover other ways to plot diversity estimates using Phyloseq
> and use another index to show the α diversity in our samples.
> 
>> ## Solution
>> 
>> `?plot_richness` or `help("plot_richness")`
>> 
>>One of the widely α diversity indexes used is Simpson diversity index, as an example
>>of solution, here it is the plot with an extra metric, which is Simpson α index:  
>> `p = plot_richness(merged_metagenomes, measures = c("Observed", "Chao1", "Shannon", "Simpson"))`
>> ![image](https://user-images.githubusercontent.com/67386612/119062049-66298a80-b99b-11eb-94f8-197f03dbe194.png)
>>
>> 
> {: .solution}
{: .challenge}  
  
  

  
> ## `.discussion`
>
> How much did the α diversity can be changed by elimination of singletons
> and doubletons?
{: .discussion}
  
  
                             
{% include links.md %}