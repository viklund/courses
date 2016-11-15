---
layout: default
title:  'Exercise: Assembly Validation'
---

## Exercise: Assembly Validation

**Questions**

If you have questions about the lab after the course, you are welcome to contact me: martin.norling@nbis.se

## Gene Space

Finding genes and other genomic regions is generally the domain of annotation, not assembly – we can cheat a bit though!
Making a rough estimate of the gene content compared to what is expected from a complete assembly can be a hint at the state of the assembly (at least of the assembled gene space).

### BUSCO

Busco, Benchmarking Universal Single-Copy Orthologs, is a program that attempts to estimate how well an assembly covers the gene space of a particular linage. Load the BUSCO module with:

```
module load bioinfo-tools BUSCO/1.22
```

Run `BUSCO –h` to get information on how to run. You will want to run Busco using 

Running BUSCO takes quite a lot of time, so we will use a script designed to behave like BUSCO, but runs a lot quicker and serves up pre-computed results.
BUSCO is BLAST based, and looks for a set of core genes assumed to be present in basically every organism (of the selected type).

## Whole Genome Alignment

When a reference is available, we can use whole genome alignment to compare the assemblies and look for differences and similarities.

### MUMmer

The program we will use to look do whole genome alignment is called `nucmer` and is from the MUMmer package. To use nucmer, load the `MUMmer/3.23` module. Running nucmer is fairly straight forward, type `nucmer -h` to get some help, and then run with your assembly, the provided reference from yesterday (from the illumina assembly part) and use the `-p` flag to set output name.

This will produce a mapping between the reference and the assembly, but it won't be sorted, so the output will be very noisy. You can use the `show-tiling` command to sort the delta file like this:
```
show-tiling [filename].delta > [filename].tiling
```

#### Dotplots

There is a built-in tool called `mummerplot` that can help us plot the output, but it requires a certain version of GNUplot



### Mauve

For the final part we will use a program called Mauve, that you'll have to run on your own machine! Start by downloading Mauve for your operating system from the [Mauve website](http://darlinglab.org/mauve/).

You will also need to download the assemblies that you wish to look at, as well as the reference to you local machine using `scp`.

Start Mauve, and select `File` -> `Align with ProgressiveMauve`, and select the sequences that you wish to align. You can give it multiple sequences, but I'd recommend doing pairwise alignments. Once the alignment is done you can run `Tools` -> `Move Contigs` to have mauve recursively move the contigs around to match better each other better.

