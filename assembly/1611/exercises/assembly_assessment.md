---
layout: default
title:  'Exercise: Assembly Assessment'
---

# Exercise: Assembly Assessment

**Questions**

If you have questions about the lab after the course, you are welcome to contact me: martin.norling@nbis.se

## Introduction

In this exercise we will look further into assembly assessment. We will focus on illumina assemblies, as the tools for these assemblies are more mature, but many things can be used for pacbio as well.

Start with one assembly and go through all the steps, then continue with the other assemblies if you have the time.

## Read Mapping

As the  assemblers we used for illumina assembly didn't provide read mappings, we need to map the reads back on the assemblies. We will do this with BWA, The Burrows-Wheeler Aligner. We will need to do this for each assembly. These parts can be stored in a script, like in the illumina assembly exercise, or run directly on the command line - the choice is yours!

To run BWA, we need to load the `bioinfo-tools`, `bwa/0.7.13`, and `samtools/1.3` modules.

The first thing you need to do to get a mapping is to index the target sequence, in our case this is the assembly.

```
$ bwa index <assembly.fasta>
```

We can then map the raw reads to the assembly using the "mem" algorithm. Run `$ bwa mem` to get some help on running BWA, then map the the reads to the assembly like:

```
$ bwa mem -t 8 assembly.fasta reads.fastq >assembly_lib_type.sam
```

This will produce a SAM file. These are bulky plain-text files, so we will sort and convert it into a BAM file, which are more efficient. You can convert the mapping using samtools like:

```
$ samtools view -bS assembly_lib_type.sam -o assembly_lib_type.bam
```

We can now sort the BAM file to get a file that can be more efficiently parsed:

```
$ samtools sort assembly_lib_type.bam assembly_lib_type.sorted
```

And finally we can index the sorted BAM file:

```
$ samtools index assembly_lib_type.sorted.bam
```

Now that we have a sorted, indexed BAM file, we can also remove the SAM file, and the unsorted BAM file to save some space and keep the project neat!

### QAtools

### REAPR

## KMER analysis

### KAT
