---
layout: default
title:  'Exercise: Contamination Assessment'
---

## Exercise: Contamination Assessment

**Questions**

If you have questions about the lab after the course, you are welcome to contact me: martin.norling@nbis.se

## Running BLAST

## Running BlobTools

In the read preparation you attempted to verify that only reads from the intended organism went into the assembly. We continue this verification here, by classifying the contigs and plotting them on a GC vs. coverage blob plot.
To do the classification – we will need taxonomy information! This can be downloaded (using the command `wget`) from NCBI, at this location: ftp://ftp.ncbi.nlm.nih.gov/pub/taxonomy/taxdump.tar.gz. This file needs to be unpacked, so use the command `$ tar xzf taxdump.tar.gz` to unpack the contents.

We also need a BAM file of mapped reads. You already made on of these in the [assessment tutorial](assembly_assessment) though, so you're set! If not, just go back there for instructions on how to make one.

The third requirement is to actually have a BLAST classification of the contigs. We will use `blastn`, towards the `nt` database. The database is available at `/sw/data/uppnex/blast_databases/nt`. Even though we just ran BLAST, in this case we will need a quite specific command to get a file that’s useful for blobtools, so use this command:

```
blastn -task megablast \
    -query [your assembly file].fasta \
    -db /sw/data/uppnex/blast_databases/nt \
    -outfmt '6 qseqid staxids bitscore std sscinames sskingdoms stitle' \
    -culling_limit 5 \
    -num_threads 8 \
    -evalue 1e-25 \
    -out [output_name].blast.out
```

Now we can finally run blobtools! The uppmax module for blobtools is called `blobtools/0.9.17`.
Blobtools works by using a creating a blob database - then you can query the database to get information and plots. But first - run `blobtools –help` to get more information on how to run blobtools.

Continue by creating a database using `blobtools create`. Use `blobtools create –help` to get help on the syntax. Now supply the **assembly**, **BWA mapping**, and **blast result**, as well as the nodes.dmp and names.dmp from the downloaded taxdump. Name the output based on the assembly you used.

You can now run `blobtools view` to create an output table, and `blobtools blobplot` to create a blobplot. 



    

