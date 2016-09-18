---
layout: default
title:  'RNAseq'
---




# RNA-seq data processing and analysis tutorial

RNA-seq has become a powerful approach to study the continually changing cellular transcriptome. Here, one of the most common questions is to identify genes that are differentially expressed between two conditions, e.g. controls and treatment. The **main** exercise in this tutorial will take you through a basic bioinformatic analysis pipeline to answer just that, it will show you how to find differentially expressed (DE) genes. Briefly,

* in the **main exercise**, we will,
  * check the quality of the raw reads with [FastQC](#fastqc)
  * map the reads to the reference genome using [Star](#star)
  * convert between SAM and BAM files format using [Samtools](#samtools)
  * assess the post-alignment reads quality using [QualiMap](#qualimap)
  * count the reads overlapping with genes regions using [featureCounts](#featurecounts)
  * build statistical model to find DE genes using edgeR from a [prepared R script](#descript)

As discussed during the lecture, RNA-seq experiment does not end with a list of DE genes. If you have time after completing the main exercise, try one (or more) of the bonus exercises. The bonus exercises can be run independently of each other, so choose the one that matches your interest.

* In the **bonus section** you can find additional exercises
  * **BEx. 01 Functional annotation** how to put DE genes in the biological context of functional annotations
  * **BEx. 02 Exon usage** how to perform analysis of differential exon usage and study alternative splicing
  * **BEx. 03 _De novo_ transcriptome assembly**  how to assembly transcriptome if no reference is present
  * **BEx. 04 Visualisation** how to view RNA-seq bam files and present DE results with graphics


# Data description

The data you will be using in this exercise is from the recent paper [YAP and TAZ control peripheral myelination and the expression of laminin receptors in Schwann cells. Poitelon et al. Nature Neurosci. 2016](http://www.nature.com/neuro/journal/v19/n7/abs/nn.4316.html). In the experiments performed in this study, YAP and TAZ were knocked-down in Schwann cells to study myelination, using the sciatic nerve in mice as a model.

Myelination is essential for nervous system function. Schwann cells interact with neurons and the basal lamina to myelinate axons using receptors, signals and transcription factors. Hippo pathway is an evolutionary conserved pathway involved in cell contact inhibition, and it acts to promote cell proliferation and inhibits apoptosis. The pathway integrates mechanical signals (cell polarity, mechanotransduction, membrane tension) and gene expression response. In addition to its role in organ size control, the Hippo pathway has been implicated in tumorigenesis, for example its deregulation occurs in a broad range of human carcinomas. Transcription co-activators YAP and TAZ are two major downstream effectors of the Hippo pathway, and have redundant roles in transcriptional activation.

The material for RNA-seq was collected from 2 conditions (wt and YAP(kd)TAZ(kd)), each in 3 biological replicates (see table below).


|  Accession  | Condition | Replicate |
| --- | ----------- | --------- |
| SRR3222409 |  KO | 1 |
| SRR3222410 |  KO | 2 |
| SRR3222411 |  KO | 3 |
| SRR3222412 |  WT  | 1 |
| SRR3222413 |  WT  | 2 |
| SRR3222414 |  WT  | 3 |


For the purpose of this tutorial,  that is to shorten the time needed to run various bioinformatics steps, we have down-sampled the original files. We randomly sampled, without replacement, 25% reads from each sample, using fastq-sample from the [fastq-tools](http://homes.cs.washington.edu/~dcjones/fastq-tools/) tools.

# Bioinformatics: processing raw sequencing files
Reading manuals, trying different tools/options, finding solutions to problems are daily routine work for bioinformaticians. By now you should have some experience with using command line and various bioinformatic tools, so in order to feel like a pro we encourage you to try your own solutions to the problems below, before checking the solution key. Click to see the suggested answers to compare them with your own solutions. Discuss with person next to you and ask us when in doubt. Remember that there are more than one way to skin a cat. Have fun!

## Preparing a working directory
To get going, let's book a node, create a working folder in the _glob_ directory and link the raw sequencing files .fastq.gz

* :computer: **Book a node.** As for other tutorials in this course we have reserved half a node per person. If you have not done it yet today book a node now as otherwise you will take away resources from your fellow course participants.
 <details>
 <summary>:key: Click to see how to book a node</summary>
 {% highlight shell %}
 salloc -A g2016017 -t 08:00:00 -p core -n 8 --no-shell --reservation=g2016017_4 &
 {% endhighlight %}
 </details>

* :computer: **Create a folder** named _transcriptome_ for your project in your _glob_ directory. **Create  a sub-folder**  called _DATA_.
 <details>
 <summary>:key: Click to see suggested commands</summary>
 {% highlight shell %}
 cd ~/glob
 mkdir transcriptome
 mkdir transcriptome/DATA
 {% endhighlight %}
 </details>

* :computer: **Sym-link** the .fastq.gz files located in /sw/courses/ngsintro/rnaseq_2016/DATA/p25. :bulb: A great chance to practice your bash loop skills.
<details>
<summary>:key: Click to see suggested commands</summary>
{% highlight shell %}
cd ~/glob/transcriptome/DATA/
for i in /sw/courses/ngsintro/rnaseq_2016/DATA/p25/*; do ln -s $i; done
{% endhighlight %}
</details>

## <a name="fastqc"></a> FastQC: quality check of the raw sequencing reads
After receiving raw reads from a high throughput sequencing centre it is essential to check their quality. Why waste your time on data analyses of the poor quality data? FastQC provide a simple way to do some quality control check on raw sequence data. It provides a modular set of analyses which you can use to get a quick impression of whether your data has any problems of which you should be aware before doing any further analysis.

* :mag: **Read** more on [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/). Can you figure out how to run it on Uppmax?

* :computer: **Create** _fastqc_ folder in your _transcriptome_ directory. **Navigate to _fastqc_ folder**.
<details>
<summary>:key: Click to see suggested commands</summary>
{% highlight shell %}
cd ~/glob/transcriptome
mkdir fastqc
cd fastqc
{% endhighlight %}
</details>

* :computer: **Load** _bioinfo-tools_ and _FastQC_ modules
<details>
<summary>:key: Click to see suggested commands</summary>
{% highlight shell %} 
module load bioinfo-tools 
module load FastQC/0.11.5
{% endhighlight %}
</details>

* :computer: **Run** FastQC on all the .fastq.gz files located in the _transcriptome/DATA_. **Direct the output** to the  _fastqc_ folder. :bulb: Check the FastQC option for input and output files. :bulb: The bash loop comes handy again.
<details>
<summary>:key: Click to see suggested commands</summary>
{% highlight shell %}
for i in ~/glob/transcriptome/DATA/* 
do 
fastqc $i -o ~/glob/transcriptome/fastqc/ 
done
{% endhighlight %}
</details>

* :mag: **Open** the FastQC for the proceeded sample. **Go back** to the [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) website and **compare** your report with [Example Report for the Good Illumina Data](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/good_sequence_short_fastqc.html) and [Example Report for the Bad Illumina Data](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/bad_sequence_fastqc.html) data.

* :open_mouth: Discuss whether you'd be happy when receiving this very data from the sequencing facility.

## <a name="star"></a> STAR: aligning reads to a reference genome
After verifying that the quality of the raw sequencing reads is acceptable we can map the reads to the reference genome. There are many mappers/aligners available, so it may be good to choose one that is adequate for your type of data. Here, we will use a software called STAR (Spliced Transcripts Alignment to a Reference) as it is good for generic purposes, fast, easy to use and has been shown to outperform many of the other tools when aligning 2x76bp paired-end data (2012). Before we begin mapping, we need to obtain genome reference sequence (.fasta file) and a corresponding annotation file (.gtf) and build a STAR index. Due to time constrains, we will practice on chromosome 11 only. Then we will use the prepared index for the entire genome to do the actual mapping.

### Accessing reference genome and genome annotation file
It is best if the reference genome (.fasta) and annotation (.gtf) files come from the same source to avoid potential naming conventions problems. It is also good to check in the manual of the aligner you use for hints on what type of files are needed to do the mapping.

* :mag: **Check** [STAR](https://github.com/alexdobin/STAR) manual what files are needed for the mapping.

* :open_mouth: What is the idea behind building STAR index? What files are needed to build one? Where do we take them from? Could one use a STAR index that was generated before?


* :computer: **Create** the _reference_ sub-folder in _transcriptome_ directory
<details>
<summary>:key: Click to see how to create the directory </summary>
{% highlight shell %}
mkdir ~/glob/transcriptome/reference
{% endhighlight %}
</details>

* :computer: **Download** the reference genome .fasta file for chromosome 11, mouse and the corresponding genome annotation .gtf file from Ensmeble webite.
<details>
<summary>:key: Click for the link to the Ensemble website </summary>
http://www.ensembl.org/info/data/ftp/index.html
</details>
</details>
<details>
<summary>:key: Click to see file names to be downloaded </summary>
*Mus\_musculus.GRCm38.dna.chromosome.11.fa*: chromosome 11 reference; *Mus\_musculus.GRCm38.85.gtf*: genome annotation
</details>
<details>
<summary>:key: Click to see how to transfer files from your computer to Uppmax </summary>
Mac/Linux:
{% highlight shell %}
scp Mus\_musculus.GRCm38.dna.chromosome.11.fa Mus\_musculus.GRCm38.85.gtf username@milou.uppmax.uu.se:~/glob/transcriptome/reference
{% endhighlight %}
Windows: try [WinSCP](https://winscp.net/eng/index.php) or other secure file transfer software
</details>

You should now have *Mus\_musculus.GRCm38.dna.chromosome.11.fa* and *Mus\_musculus.GRCm38.85.gtf* in the sub-folder _reference_

### preparing index

* :computer: **Create _indexChr11_ sub-folder** in the _transcriptome_ directory
<details>
<summary>:key: Click to see how to create directory</summary>
mkdir ~/glob/transcriptome/indexChr11; cd ~/glob/transcriptome/indexChr11;`
</details>


* :computer: **Load STAR module** on Uppmax. :bulb: Use _module spider star_ to check which version of STAR are available and load the latest one.
<details>
<summary>:key: Click to see how to load module</summary>
{% highlight shell %}
module load star/2.5.1b
{% endhighlight %}
</details>


* :computer: **Build STAR index** for chromosome 11 using the downloaded reference .fasta and gene annotation .gtf files. :bulb: Check STAR manual for details
<details>
<summary>:key: Click again to see suggested commands</summary>
{% highlight shell %}  
star --runMode genomeGenerate --runThreadN 8 --genomeDir ~/glob/transcriptome/indexChr11 --genomeFastaFiles ~/glob/transcriptome/reference/Mus_musculus.GRCm38.dna.chromosome.11.fa --sjdbGTFfile ~/glob/transcriptome/reference/Mus_musculus.GRCm38.85_chr11.gtf  `
{% endhighlight %}
</details>

* :computer: **Sym-link STAR index** to for the entire genome into the _transcriptome_ directory. The index for the whole genome was prepared for us before class in the very same way as for the chromosome 11 in steps above. It just requires more time (ca. 4h) to run. The index can be found here: */sw/courses/ngsintro/rnaseq\_2016/index*

<details>
<summary>:key: Click again to see how to link the index</summary>
{% highlight shell %}
cd ~/glob/transcriptome/
ln -s /sw/courses/ngsintro/rnaseq_2016/index
{% endhighlight%}
</details>

### mapping
Now we are ready to map our reads to the reference genome, via STAR index.
* :computer: **Create _star_ sub-folder** in the _transcriptome_ directory. **Create sub-sub-folder named _SRR3222409_** to save the mapping results for the sample SRR3222409.

<details>
<summary>:key: Click to see how to create folders </summary>
{% highlight shell %}
mkdir ~/glob/transcriptome/star
mkdir ~/glob/transcriptome/star/SRR3222409
{% endhighlight %}
</details>

* :computer: **Map reads** to the reference genome for SRR3222409 sample. Do not forget that we are working with paired-end reads so each sample has two matching reads file. **Check** the STAR manual for the parameters to:
 * use index for the entire genome
 * to read in zipped .fastq.gz files for both forward and reverse reads
 * to run the job on the 8 allocated cores  
 * to direct the mapping results to the _SRR3222409_ sub-sub folder
 * to give the results prefix _SRR3222409_

 <summary>:key: Click to see how to write the mapping command with the above parameters</summary>
 {% highlight shell %}
 star --genomeDir ~/glob/transcriptome/index/complete --readFilesIn ~/glob/transcriptome/DATA/SRR3222409_1.fastq.gz ~/glob/transcriptome/DATA/SRR3222409_2.fastq.gz --runThreadN 8 --readFilesCommand zcat --outFileNamePrefix ~/glob/transcriptome/star/SRR3222409/SRR322409_
 {% endhighlight%}
  </details>

* :computer: **Map or copy over**. Map the remaining samples in the analogous way. Running short of time? Copy over the results that we have prepared for you before the class. They are here: */sw/courses/ngsintro/rnaseq\_2016/main/Star*
<details>
<summary>:key: Click to see how to copy results, sample by sample</summary>
{% highlight shell %}  
cp -r /sw/courses/ngsintro/rnaseq_2016/main/star/SRR3222410/ ~/glob/transcriptome/star/
{% endhighlight%}
</details>

<details>
<summary>:key: Click to see how to copy results using bash loop</summary>
{% highlight shell %}
for i in SRR3222411 SRR3222412 SRR3222413 SRR3222414
do
cp -r /sw/courses/ngsintro/rnaseq_2016/main/star/$i ~/glob/transcriptome/star/
done
{% endhighlight%}
</details>

###<a name="samtools"></a> Samtools: converting between SAM and BAM
Before we proceed further with our data processing, let's convert our mapped reads from STAR, saved in the default .SAM text format, into the binary .BAM format. Why? BAM files take less space so it is easier to store them and they are the most commonly required file format for many of the down-stream bioinformatics tools. In addition, they can be sorted and indexed shortening the time needed to proceed them in comparison with .SAM format. Also, then they will be ready for exploration in IGV, the Integrative Genomic Viewer.

* :mag: **Read** through [Samtools](http://www.htslib.org/doc/samtools.html) documentation and see if you can figure it out how to:
* convert SAM into BAM
* sort BAM files
* index BAM files

* :computer: **Create _bams_ sub-folder** in _transcriptome_, **navigate to _bams_ sub-folder** and **load samtools module**
<details>
<summary>:key: Click to see the suggested commands, file by file</summary>
`mkdir ~/glob/transcriptome/bams; cd ~/glob/transcriptome/bams; module load samtools`
</details>


* :computer: **Sym-link** in _bams_ sub-folder all the SAM files containing the mapped reads, as created during the Star mapping step. :bulb: You can use the bash loop if you apply wild cards (slightly more advanced but try first before looking at the answer key)
<details>
<summary>:key: Click to see the suggested commands, sample by sample</summary>
`ln -s ~/glob/transcriptome/star/SRR3222409/SRR3222409_Aligned.out.sam`
</details>
<details>
<summary>:key: Click to see the suggested commands, using bash loop</summary>
`for i in ~/glob/transcriptome/star/**/*.sam; do ln -s $i; done`
</details>


* :computer: **Convert SAM to BAM**: for the first sample *SRR3222409\_Aligned.out.sam* into *SRR3222409\_Aligned.out.bam*
<details>
<summary>:key: Click to see the suggested commands</summary>
`samtools view -bS SRR3222409_Aligned.out.sam > SRR3222409_Aligned.out.bam`
</details>


* :computer: **Convert SAM to BAM** for the remaining samples or **copy them over**. You can find the BAMs prepared for your in */sw/courses/ngsintro/rnaseq_2016/main/bams/out*
<details>
<summary>:key: Click to see how to copy over sample by sample</summary>
`cp /sw/courses/ngsintro/rnaseq_2016/main/bams/out/SRR3222410_Aligned.out.bam ~/glob/transcriptome/bams/`
</details>
<details>
<summary>:key: Click to see how to copy over using a bit more advanced bash loop</summary>
`for i in SRR3222411 SRR3222412 SRR3222413 SRR3222414; do cp "/sw/courses/ngsintro/rnaseq_2016/main/bams/out/"$i"_Aligned.out.bam" ~/glob/transcriptome/bams/; done`
</details>


* :computer: **Sort BAM file** sort *SRR3222409\_Aligned.out.bam* file and save it as *SRR3222409\_Aligned.out.sorted.bam* in the *bams* sub-folder
<details>
<summary>:key: Click to see how to sort BAM file</summary>
`samtools sort ~/glob/transcriptome/bams/SRR3222409_Aligned.out.bam ~/glob/transcriptome/bams/SRR3222409_Aligned.out.sorted`
</details>



* :computer: **Sort BAM files** for the remaining samples or **copy them over**. You can find the BAMs prepared for your in */sw/courses/ngsintro/rnaseq_2016/main/bams/out*, ending with *sorted.bam*
<details>
<summary>:key: Click to see how to copy over sample by sample</summary>
`cp /sw/courses/ngsintro/rnaseq_2016/main/bams/out/SRR3222410_Aligned.out.sorted.bam ~/glob/transcriptome/bams/`
</details>
<details>
<summary>:key: Click to see how to copy over using a bit more advanced bash loop. It is the same as above, just the file extension is different. </summary>
`for i in SRR3222411 SRR3222412 SRR3222413 SRR3222414; do cp "/sw/courses/ngsintro/rnaseq_2016/main/bams/out/"$i"_Aligned.out.sorted.bam" ~/glob/transcriptome/bams/; done`
</details>


* :computer: **Index the sorted BAM files**
<details>
<summary>:key: Click to see how to sort BAM file, sample by sample</summary>
`samtools index ~/glob/transcriptome/bams/SRR3222410_Aligned.out.sorted.bam`
</details>
<details>
<summary>:key: Click to see how to sort BAM file, using bash loop</summary>
`for i in *.sorted.bam; do samtools index $i; done`
</details>



###<a name="qualimap"></a>QualiMap: post-alignment quality control
Some important quality aspects, such as saturation of sequencing depth, read distribution between different genomic features or coverage uniformity along transcripts, can be measured only after mapping reads to the reference genome. One of the tools to perform this post-alignment quality control is QualiMap. QualiMap examines sequencing alignment data in SAM/BAM files according to the features of the mapped reads and provides an overall view of the data that helps to the detect biases in the sequencing and/or mapping of the data and eases decision-making for further analysis.

* :mag: **Read** through [QuliMap](http://qualimap.bioinfo.cipf.es/doc_html/intro.html) documentation and see if you can figure it out how to run it to assess post-alignment quality on the RNA-seq mapped samples. The tool is already installed on Uppmax and available as QuliMap module


* :computer: **Create QualiMap** sub-folder in _transcriptome_ directory, **navigate to _qualimap_ sub-folder** and **load QualiMap/2.2 module**
<details>
<summary>:key: Click to see the suggested commands</summary>
`mkdir ~/glob/transcriptome/qualimap; cd ~/glob/transcriptome/qualimap/; module load QualiMap/2.2`
</details>


* :computer: **Run QualiMap** for the fist sample on sorted BAM file *SRR3222409\_Aligned.out.sorted.bam* **directing** the results to *~/glob/transcriptome/qualimap/SRR3222409* folder. :bulb: QualiMap creates the folder if you specify the right parameter
<details>
<summary>:key: Click to see the suggested commands</summary>
`qualimap rnaseq -bam ~/glob/transcriptome/bams/SRR3222409_Aligned.out.sorted.bam -gtf ~/glob/transcriptome/reference/Mus_musculus.GRCm38.81.gtf --outdir ~/glob/transcriptome/qualimap/SRR3222409`
</details>


* :computer: **Run QualiMap** for the remaining sorted BAM files or **copy the results over**. These can be found */sw/courses/ngsintro/rnaseq\_2016/main/qualimap*
<details>
<summary>:key: Click to see how to copy over the results, sample by sample</summary>
`cp -r /sw/courses/ngsintro/rnaseq_2016/main/qualimap/SRR3222410 ~/glob/transcriptome/qualimap/`
</details>
<details>
<summary>:key: Click to see how to copy over the results, using bash loop</summary>
`for i in SRR3222409 SRR3222410 SRR3222411 SRR3222412 SRR3222413 SRR3222414; do cp -r "/sw/courses/ngsintro/rnaseq_2016/main/qualimap"/$i ~/glob/transcriptome/qualimap/; done
`
</details>


* :open_mouth: **Check the QualiMap results**. What do you think? Are the samples of good quality? How can you tell?


###<a name="featurecounts"></a> featureCounts: counting reads
After ensuring mapping quality we can count the reads to obtain a raw count table. We could count the reads by hand, opening the BAM in the IGV along the genome annotation file, and counting the reads overlapping with the regions of interest. This of course would take forever for the entire genome but it is never a bad idea to see how the data look like for the selected few genes of interest. For get the counts for the entire genome one can use many of the already available tools doing just that. Here we will use featureCounts, an ultrafast and accurate read summarization program, that can count mapped reads for genomic features such as genes, exons, promoter, gene bodies, genomic bins and chromosomal locations.

* :mag: **Read** [featureCounts](http://bioinf.wehi.edu.au/featureCounts/) documentation and see if you can figure it out how to run summarize paired-end reads and count fragments overlapping with exonic regions.


* :computer: **Create featurecounts** sub-folder in the _transcriptome_ directory and **navigate** there.
<details>
<summary>:key: Click to see how...</summary>
`mkdir ~/glob/transcriptome/featurecounts; ~/glob/transcriptome/featurecounts`
</details>


* :computer: **Load featureCounts** module. :bulb: featureCounts is available on Uppmax as part of the _subread_ package
<details>
<summary>:key: Click to see how to load featureCounts</summary>
`module load subread`
</details>


* :computer: **Sym-link** SAM files generated by Star and located in *~glob/transcriptome/star*
<details>
<summary>:key: Click to see sym-link files, sample by sample</summary>
`ln -s ~/glob/transcriptome/star/SRR3222409/SRR3222409_Aligned.out.sam`
</details>
<details>
<summary>:key: Click to see sym-link files, using bash loop</summary>
`for i in ~/glob/transcriptome/star/**/*.sam; do ln -s $i; done`
</details>


* :computer: **Run featureCounts** on the SAM files, **counting** fragments overlapping exon regions and **saving** the count tables as _tableCounts_. :bulb: The libraries are un-stranded and you can proceed all the samples in one go.
<details>
<summary>:key: Click to see how to run featureCounts on all samples</summary>
`featurecounts]$ featureCounts -p -a ~/glob/transcriptome/reference/Mus_musculus.GRCm38.81.gtf -t gene -g gene_id -s 0 -o tableCounts *.sam`
</details>


* :open_mouth: **Have a look** at the _tableCounts_ and _tableCounts.summary_. Can you figure out what these files contain? Do you think that counting work? How can you tell?


### MultiQC: combining QC measures across all the samples
* :mag: **Read** more on [MultiQC](http://multiqc.info). Can you figure out why this tool has become very popular? Can you figure out how to combine FastQC, Star, QualiMap and featureCounts results for all the samples into interactive report?


* :computer: **Navigate** to _transcriptome_ directory and *load module MultiQC/0.6*
<details>
<summary>:key: Click to see how </summary>
`cd ~/glob/transcriptome; module load MultiQC/0.6`
</details>


* :computer: **Run** MultiQC
<details>
<summary>:key: Click to see how to run MultiQC</summary>
`multiqc .`
</details>


* :open_mouth: **Transfer** the MultiQC report to your computer and have a look at it.  What can you notice?

###<a name="descript"></a> Differential expression
As mentioned during the lecture, the best way to perform differential expression is to use one of the statistical packages, within **R environment**, that were specifically designed for analyses of read counts arising from RNA-seq, SAGE and similar technologies. Here, we will one of such packages called [edgeR](https://bioconductor.org/packages/release/bioc/html/edgeR.html). Learning R is beyond the scope of this course so we prepared basic ready to run scripts from a command line scripts to find DE genes between Ko and Wt.

* :computer: **Create** _DE_ sub-folder in the _transcriptome_ directory and **navigate** there
<details>
<summary>:key: Click to see how </summary>
`mkdir ~/glob/transcriptome/DE; cd ~/glob/transcriptome/DE`
</details>


* :computer: **Load R module and R packages**
<details>
<summary>:key: Click to see how </summary>
`module load R/3.3.0; module load R_packages/3.3.0`
</details>


* :computer: **Sym-link** _tableCounts_ as created by featureCounts and **sym-link** the prepared gene annotation file *tableCounts_annotation.txt* prepared by us before the class and located: */sw/courses/ngsintro/rnaseq\_2016/main/DE* as well as **sym-link** R script located in the same directory
<details>
<summary>:key: Click to see how </summary>
`ln -s ~/glob/transcriptome/featurecounts/tableCounts; ln -s /sw/courses/ngsintro/rnaseq_2016/main/DE/tableCounts_annotations.txt; ln -s /sw/courses/ngsintro/rnaseq_2016/main/DE/diffExp.R `
</details>


* :computer: **Run diffExp.R script**
<details>
<summary>:key: Click to see how </summary>
`Rscript diffExp.R`
</details>
A file *results\_DE.txt* should be created in the _DE_ sub-folder


* :open_mouth: **Copy over** to your computer **open** the *results\_DE.txt*
 file in Excel or alike. Given FDR value of 0.05, how many DE genes are there? How many up and down-regulated? What are the top changes? How does it change when we only look at the DE that have minimum log-fold-change 1?




# Bonus exercise: from differential expression to biological knowledge


## Introduction to functional annotation
In this part of the exercise we will address the question which biological processes are affected in the experiment; in other words we will functionally annotate the results of the analysis of differential gene expression (performed in the main part of the exercise). We will use [Gene Ontology (GO) term](http://geneontology.org/) and [reactome pathway](http://reactome.org/) annotations.

When performing this type of analysis one has to keep in mind that the analysis is only as accurate as the annotation available for your organism, so if working with non-mainstream model organisms which do have most of the annotations only electronically inferred (as opposed to direct evidence), the results may not be fully reflecting the actual situation.

There are many methods to approach the question which biological processes and pathways are overrepresented amongst the differentially expressed genes, compared to all genes included in the analysis. They use several types of statistical tests (e.g. hypergeometric test, Fisher's exact test), and many have been developed with microarray data in mind. Not all of these methods are appropriate for RNA-seq data, which as you remember from the lecture, exhibit length bias in power of detection of differentially expressed genes (i.e. longer genes, which tend to gather more reads, are more likely to be detected as "differentially expressed" than shorter genes, solely because of the length).

We will use the R / Bioconductor package [goseq](http://bioconductor.org/packages/release/bioc/html/goseq.html), specifically designed to work with RNA-seq data. This package provides methods for performing Gene Ontology  and pathway analysis of RNA-seq data, taking length bias into account.

In this part, we will use the same data as in the main workflow. The starting point of the exercise is the file with results of the differential expression produced in the main part of the exercise.

This module can be performed on Uppmax, or on your local computer if you have installed [R statistical programming language](https://cran.r-project.org/) and (optionally) a graphical interface to R such as [RStudio](https://www.rstudio.com/).

## Libraries to install and load if exercise is performed locally

* :computer: If you prefer to use your local computer for this exercise, you need to **install packages** used in the exercise. You can do it by pasting the following two commands in R session:
 `source("http://bioconductor.org/biocLite.R")
 biocLite(c("goseq","GO.db","reactome.db","org.Mm.eg.db"))`

* To perform the exercise you will need data included in the following location at Uppmax:
 `/sw/courses/ngsintro/rnaseq_2016/bonus/funannot`


*  You will need to copy the directory to your working space, whether working on Uppmax:
 <details>
 <summary>:key: Click to see an example command</summary>
 `cp -r /sw/courses/ngsintro/rnaseq_2016/bonus/funannot ./`
 </details>
 or on your local computer:
 <details>
 <summary>:key: Click to see an example of command</summary>
 `scp -r YOUR_LOGIN@milou.uppmax.uu.se:/sw/courses/ngsintro/rnaseq_2016/bonus/funannot ./`
 </details>


## Workflow

OBS: the following part is not yet ready, the R packages are not installed in the R_packages module! I installed the libraries in my own R library.

 * :computer: **Load R and R modules** required in the exercise:
 <details>
 <summary>:key: Click to see an example of command</summary>
 `module load R/3.3.0; module load R_packages/3.3.0`
 </details>


* :computer: **Enter** the exercise working directory:
 <details>
<summary>:key: Click to see how</summary>
`cd /funannot`
</details>
and you are ready to start the exercise.


* :computer: **To perform the functional annotation** you can use a wrapper script *annotate\_de\_results.R*, which is executed as in the main exercise:
<details>
<summary>:key: Click to see how</summary>
`script annotate_de_results.R`
</details>
The results will be saved in the directory *GO\_react\_results*.

Alternatively, you can open the script in RStudio (or a text editor such as Atom or Sublime) and execute each step of the script in a live R session. This way you will be able to "see inside" the script and try to follow the individual steps.

## Interpretation
* The results are saved as tables in the directory *GO\_react\_results*.

* The columns of the results tables are:

 |  category  | over_represented_pvalue | under_represented_pvalue | numDEInCat | numInCat  | term | ontology |

* You can view the tables in a text editor, and try to find GO terms and pathways relevant to the experiment using a word search functionality.

* If you are performing the exercise in an intercative R session (on your local computer or on Uppmax), the results are collected in the following objects:

 `go.dn.adj
go.up.adj
react.dn.adj.annot
react.up.adj.annot`

  You can explore the results either by printing them all on the screen

* <details>
<summary>:key: Click to see examples of commands</summary>
`go.dn.adj`
</details>


* or by performing a string search using grep.
* <details>
<summary>:key: Click to see examples of commands</summary>
`grep("myelin", go.dn.adj$term,  value=T)`
</details>
* <details>
<summary>:key: Click to see examples of commands</summary>
`grep("neuro", go.dn.adj$term,  value=T)`
</details>


* :open_mouth: Do you think the functional annotation reflects the biology of the experiments we have just analysed?


# Bonus exercise: exon usage

## Introduction to differential exon usage

Multi-exon genes are affected by alternative
splicing and thus, can express a variety of different
transcripts from the same genomic sequence. Differences in the relative expression of these isoforms between tissues and species occur naturally between cell types and allow cells to adapt to the environment.

It is important to distinguish differential transcript usage from gene-level differential expression (which was covered in the main part of the exercise) and from transcript-level differential expression. DTU considers changes in the **proportions** of the isoforms of a gene that are expressed as opposed to changes of the individual transcript levels.

Although the main units of interest are the transcripts, it has been difficult to obtain accurate and precise transcript-level expression estimates due to the extensive overlap between different transcripts. To circumvent that, number of methods have been developed that instead of estimating expression levels of transcripts, analyse levels of transcript building blocks (exons).  Hence, differential exon usage can be viewed as a proxy to the differential transcript usage.

Note that DEU is a more general concept than alternative splicing, since it also includes changes in the usage of alternative transcript start sites and polyadenylation sites, which can cause differential usage of exons at the 5' and 3' boundary of transcripts.

The Bioconductor package **DEXSeq** implements a method to test for differential exon usage (DEU) in comparative
RNA-Seq experiments. By differential exon usage, we mean changes in the **relative** usage
of exons caused by the experimental condition. The relative usage of an exon is defined as number of transcripts from the gene that contain this exon vs. number of all transcripts from the gene.

In this exercise we will use reads mapped to chromosome 11 only (performing this analysis on entire data set is quite time consuming and requires considerable computing power). The starting point are files with reads summarised per each annotated exon, prepared beforehand by us.

This module can be performed on Uppmax, or on your local computer if you have installed [R statistical programming language](https://cran.r-project.org/) and (optionally) a graphical interface to R such as [RStudio](https://www.rstudio.com/).

## Libraries to install and load if exercise is performed locally

* :computer: If you prefer to use your local computer for this exercise, you need to **install packages** used in the exercise. You can do it by pasting the following two commands in R session:

  `source("http://bioconductor.org/biocLite.R")
 biocLite("DEXSeq")`

* In addition you may need to install X11 app ([XQuartz](https://www.xquartz.org/) on MacOS) to produce the html report.

* To perform the exercise you will need data included in the following location at Uppmax:

  `/sw/courses/ngsintro/rnaseq_2016/bonus/exon`


* You will need to copy the directory to your working space, whether working on Uppmax:
 <details>
<summary>:key: Click to see an example command</summary>
`cp -r /sw/courses/ngsintro/rnaseq_2016/bonus/exon ./`
</details>
or on your local computer:
<details>
<summary>:key: Click to see an example of command</summary>
`scp -r YOUR_LOGIN@milou.uppmax.uu.se:/sw/courses/ngsintro/rnaseq_2016/bonus/exon ./`
</details>


## Workflow

OBS!!! this part is not tested at all due to problems with DEXSeq installation on milou. if you have that package installed, you can test it though, it works locally. I hope the html report rendering will work on Uppmax (requires x11 app on macs)

OBS: the following part is not yet ready, the R packages are not installed in the R_packages module! I installed the libraries in my own R library.

* :computer: **Load R and R modules** required in the exercise:
<details>
<summary>:key: Click to see how</summary>
`module load R/3.3.0; module load R_packages/3.3.0`
</details>


* **Enter** the exercise working directory:
<details>
<summary>:key: Click to see how</summary>
`cd /exon`
</details>


* :computer: To perform the functional annotation you can use a wrapper script *deu.R*,
<details>
<summary>:key: Click to see how</summary>
`Rscript deu.R`
</details>


* Alternatively, you can open the script in RStudio (or a text editor such as Atom or Sublime) and execute each step of the script in a live R session. This way you will be able to "see inside" the script and try to follow the individual steps.

* The results in html format are saved in the directory ***DEXSeqReport***. For detailed description of the individual report sections please consult [DEXSeq user manual](http://bioconductor.org/packages/release/bioc/vignettes/DEXSeq/inst/doc/DEXSeq.pdf). Additionally, a table with significant exons ***deu\_signif\_exons.txt*** is saved in the working directory.

* :open_mouth: How many differentially used exons were identified in the data? Do you think this result makes sense?


# Bonus exercise: transcriptome assembly

# Bonus exercise: further data analyses and visualization

# Introduction<a id="orgheadline1"></a>

Data visualisation is important to be able to clearly convey results,
but can also be very helpful as tool for identifying issues and
noteworthy patterns in the data. In this part you will use the bam
files you created earlier in the RNA-seq lab and use IGV (Integrated
Genomic Viewer) to visualize the mapped reads and genome
annotations. In addition we will produce high quality plots of both
the mapped read data and the results from differential gene
expression.

# IGV<a id="orgheadline2"></a>

If you are already familiar with IGV you can load the mouse genome and
at least one bam file from each of the treatments that you created
earlier. The functionality of IGV is the same as if you look at
genomic data, but there are a few of the features that are more
interesting to use for RNA-seq data.

[Integrated genomics viewer](http://software.broadinstitute.org/software/igv/) from Broad Institute is a nice graphical
interface to view bam files and genome annotations. It also has tools
to export data and some functionality to look at splicing patterns in
RNA-seq data sets. Even though it allows for some basic types of
analysis it should be used more as a nice way to look at your mapped
data. Looking at data in this way might seem like a daunting approach
as you can not check more than a few regions, but in in many cases it
can reveal mapping patterns that are hard to catch with just summary
statistics.

For this tutorial you can chose to run IGV directly on your own computer
or on uppmax. If you chose to run it on your own computer you will
have to download some of the bam files (and the corresponding index
files) from upppmax. If you have not yet installed IGV you also
have to get a copy of the program. 

<details>
  <summary>:key: Click to see how to transfer files from uppmax</summary>

    scp scp username@milou.uppmax.uu.se:~/glob/bamfile.bam .

</details>

If you instead choose to run on uppmax you need to make sure that you
log on in a way so that the generated graphics are exported via the
network to your screen. This can be done in two different ways. The
first method requires you to log in to uppmax with the following
command.

    ssh -Y username@milou.uppmax.uu.se
    ssh -Y computenode

These two steps makes sure that any graphical interface that you start
on your compute node, will be exported to your computer. Note that as
the graphics is exported over the network it can be fairly slow in
redrawing windows and the experience can be fairly poor.

The second and superior way is to go to [Milou-gui](https://milou-gui.uppmax.uu.se/main/)

Once you log into this interface you will have a linux desktop
interface in a browser window. This interface is running on the login
node so if you want to do any heavy lifting you need to login to your
reserved compute node also here. This is done by opening a terminal in
the running linux envirolnment and log on to your compute node as before
NB! If you have no active reservation you have to do that first.

    ssh -Y computenode

Once at the compute node we can load necessary modules and open an IGV
session.

    module load bioinfo-tools
    module load IGV/2.3.40
    igv-core

This should start the IGV so that it is visible on your screen. If not please try to
reconnect to uppmax or consider running IGV locally as that is often
the fastest and most convinient solution. 

Once we have the program running you select the genome that you would
like to load. As seen in the image below. 

![img](Figures/IGV-genome.png)

Note that if you are working with a genome that are not part of the
available genomes in IGV, one can create genome files from within
IGV. Please check the manual of IGV for more information on that.

To open your bam files click on File and chose the option "Load from
file&#x2026;" select your bam file and make sure that you have a .bai index
for that bam file in the same folder. You can repeat this and open
multiple bam files in the same window, which makes it easy to compare
samples. For every file you open a number of panels are opened that
visualize the data in different ways. The first panel named "Coverage"
summarize the coverage of basepairs in the window you have zoomed
to. The second that ends with the name "Junctions" show how reads were
spliced to map, eg. reads that stretch over multiple exons are split
and mapped one part in one exon and the next in another exon. The
third panel shows the reads as they are mapped to the genome. If one
right click with the mouse on the read panel there many options to
group and color reads. 

To see actual reads you have to zoom in until the reads are
drawn on screen. If you have a gene of interest you can also use
the search box to directly go to that gene. 

If you for example search for the gene "Mocs2" you should see a decent
amount of reads mapping to this region. For more detailed information
on the splice reads you can instead of just looking at the splice
panel right click on the read panel and select "Sashimi plots" This
will open a new window showing in an easy readable fashion how reads
are spliced in mapping and you will also be able to see that there are
differences in between what locations reads are spliced. This can
hence represents reads that originate from different isoforms of the
gene.

To try some of the features available in IGV you can try to address the following
questions. 

Q1: Are the reads you mapped from a stranded or unstranded library?

Q2: Pick a gene from the toplist of most significant genes from the DE
analysis and search for it using the search box in IGV. Would you say that
the pattern you see here confirms the gene as differentially expressed
between treatments?

Q3: One can visualize all genes in a given pathway using the gene list
option under "Regions" in the menu. Would you agree with what they
state in the paper about certain pathways being down-regulated. If you need
hints for how to proceed see [Gene List tutorial at Broad](http://software.broadinstitute.org/software/igv/gene_list_view).

# Closing remarks
It is not possible to learn RNA-seq data processing and analysis in one day... The good news is that there are many available tools and well-written tutorial with examples to learn from. In this tutorial we have covered the most important data processing steps that may be enough when the libraries are good. If not, there is plenty of trouble-shooting that one can try before discarding the data. And once the count table are in place, the biostatistical and data mining begins. There are no well-defined solutions here, all depends on the experiment and questions to be asked, but we strongly advise learning R. Not only to use the specifically designed statistical packages to analyze NGS count data, but also to be able to handle the data and results as well as to generate high-quality plots. There is no better way of learning than to try...


# About authors
Thomas Källman :neckbeard:, Agata Smialowska :smiling_imp:, Olga Dethlefsen :angel: @ NBIS, National Bioinformatics Infrastructure, Sweden
