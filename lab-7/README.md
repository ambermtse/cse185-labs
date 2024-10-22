# Lab 7: Phylogenetics and COVID-19

Skills: Multiple sequence alignment, Phylogenetics, Installing tools, Finding data


## Intro

COVID-19 is caused by a novel type of coronavirus, a class of viruses that can cause disease ranging from the common cold to more severe conditions like MERS or SARS. In this lab, we'll analyze the genome multiple strains of the novel coronavirus (SARS-CoV-2), and use comparative genomics techniques to explore how the virus relates to other types of coronaviruses.

Our primary goal will be to reconstruct the phylogenetics analysis from [A Novel Coronavirus from Patients with Pneumonia in China, 2019](https://www.nejm.org/doi/full/10.1056/NEJMoa2001017), one of the earliest reports of the full SARS-CoV-2 genome. Specifically, we'll be producing something like their [Figure 4b](https://www.nejm.org/doi/full/10.1056/NEJMoa2001017).


This tree shows the relationship between multiple SARS-CoV-2 genomes and other coronavirus strains, including SARS, MERS, and coronavirus strains isolated from bats.

Unfortunately, they don't give us a whole lot of methods info to go on. Here is a snippet from the relevant methods part: "Multiple-sequence alignment of the 2019-nCoV and reference sequences was performed with the use of Muscle. Phylogenetic analysis of the complete genomes was performed with RAxML (13) with 1000 bootstrap replicates and a general time-reversible model used as the nucleotide substitution model." Not a whole lot of details, but with enough detective work we'll still be able to construct a similar tree!

## Overview

Our overall goal will be to compare genomes of different virus species. Whereas in previous labs, we usually started from raw reads (in fastq format), for this lab, we'll work with existing assemblies for the viral genomes of interest and skip doing the assembly ourselves. If you choose to do the extra credit part, you'll try your hand at starting from the raw reads and using your own assembly in the phylogenetics analysis.

In this lab, we'll go through:
1. Obtaining genome assemblies from NCBI and raw reads from SRA.
2. Performing multiple sequence alignment.
3. Building a phylogenetic tree to explore the evolutionary relationship between virus strains.
4. Visualizing phylogenetic trees.

### Summary of tools covered
In this lab we'll be using or referring to the following tools:

* [mafft](https://mafft.cbrc.jp/alignment/software/): for performing multiple sequence alignment
* [RaxML](https://github.com/stamatak/standard-RAxML): for building phylogenetics trees

# 1. Downloading the data

Our first step will be to download the assembled genomes for the viruses we'd like to compare. To help you get started, we've provided (based on manually copying from the figure above...) the NCBI accession numbers for the genomes to compare:

```
~/public/lab7/lab7_accessions.txt
```

This is just a text file, with one accession per line, where each accession is unique to a virus strain. These accessions, and brief descriptions, are also listed here: https://docs.google.com/spreadsheets/d/1p1JpKKj1lUmGqrq2fdnX-FHvv7wt-jIdyY4qrRXyT9I/edit?usp=sharing

To see info about a certain accession, you can go to NCBI using a link like: https://www.ncbi.nlm.nih.gov/nuccore/AY508724.1

You'll see for instance that this genome is from "SARS coronavirus NS-1, complete genome" (the original SARS from the early 2000s). You can also scroll through the entire genome sequence since it's so short!


Our goal in this first section is to download the genomes for each of these accessions into one big fasta file, which we will need to input into the tools we use below. 

# 2. Performing multiple sequence alignment

Now that we've collected all the genome sequences we need in a single fasta file, we'll want to compare them to each other. A typical and critical step in order to do so is to create a "multiple sequence alignment" between them so we can compare nucleotides at specific bases across strains.

The NEJM paper used a tool called [MUSCLE](https://www.ebi.ac.uk/jdispatcher/msa/muscle) for this. We'll deviate from their methods and use an alternative tool called [mafft](https://mafft.cbrc.jp/alignment/software/), since we found that it runs quite a bit faster. `mafft` (and `MUSCLE`) take as input a fasta file with multiple genomes, and outputs a new fasta-like file showing the alignment between those genomes.


# 3. Building a tree

Now, we're ready to build our tree. We'll use a tool called RaxML (the same one used in the NEJM paper). You can find the RaxML manual here: https://github.com/stamatak/standard-RAxML/blob/master/manual/NewManual.pdf. 

We will first build a maximum likelihood tree, and annotate the branches with confidence values, as was done in the NEJM figure. Even though they didn't give us much detail, we do know:
* They used bootstrapping to annotate support for each split. Their methods say they did 1000 bootstraps. We'll do fewer to save time. Our solution used 100. But you might want to try with fewer to make sure things are working first.
* They used a "general time-reversible model". (Hint look for "GTRCAT" in the RaxML manual).

We'll actually need to use multiple RaxML commands:
* The first will find the maximum likelihood tree based on our mafft alignment.
* The second will perform the bootstrap search. This one can take a while. You might want to use `nohup`.
* The third will draw the bipartitions (bootstrap values) on the best tree generated by the first command. This should be fast.

# 4. Visualize the tree

Finally, visualize your tree. We recommend you use one of multiple online tools for viewing trees from Newick files. e.g.:

* [iTOL](https://itol.embl.de/upload.cgi)
* [ETE Treeview](http://etetoolkit.org/treeview/)


# 5. Summarize your findings

In the cell below:
* Briefly summarize the methods you used
* Include your tree
* Summarize your main findings


First, I downloaded all the assembled genomes for the SARS, MERS and SARS-CoV-2 viruses we will compare into a single fasta file, and used mafft to do a multiple sequence alignment to compare all the genome sequences. Next, I ran RaxML using the general time-reversible model GTRCAT to find the maximum likelihood tree based off of our multiple sequence alignment from mafft, perform 100 bootstrap searches to see how many times the same branch is observed when repeatedly generating phylogentic trees on our alignment data, and then draw bipartitions on the best tree generated using our bootstrap values. To visualize the tree, I inputed the newick file that was outputed from the drawing bipartition RaxML command into the online iTOL. Below is my resulting phylogenetic tree:

![tree](figures/my_COVID-19_tree.png)

According to the phylogenetic tree, COVID-19 (SARS-CoV2) is most similar to some Bat SARS-like coronaviruses, being separated by 2 internal nodes. COVID-19, SARS, and MERS related genomes all form 3 separate clades and are visually distinct from one another. In the SARS genomes, the human SARS virus and some Bat SARS-like viruses are genetically similar and have close internal nodes. The same can be said for the human MERS genome and the Bat virus genomes (HKU4-1 and HKU5-1). The common colds like OC43 and HKU1 are genetically similar to each other, and are the most genetically dissemilar to COVID-19, SARS, and MERS genomes. 



