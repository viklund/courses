---
layout: default
title:  'Exercise: Illumina Assembly'
---

## Exercise: Illumina Assembly

In this exercise you will assemble genomes de novo using commonly used assembly software. You will work with Illumina data of _Saccharomyces cerevisiae S288c_, data that was downloaded from the NCBI Short Read Archive (SRA) and subsampled to make faster, crappier assemblies. Due to time-restrictions we are forced to stick to assemblers that run quickly, but if you have an assembly project of your own you are encouraged to try other assemblers too. Remember that no assembler is best for all projects; it is recommended to try several.

**Questions**

If you have questions about the lab after the course, you are welcome to contact me: martin.norling@nbis.se

### Setup

If you're not already logged in, start by logging in to UPPMAX using the earlier supplied instructions and be sure to log in to your reserved node.

Before you start working with the assemblers you need to setup a project structure to have easy access to the data, results, and run scripts.

To make certain you are in your home folder, type: `cd`.

Make a directory for all of today’s exercises using `mkdir illumina_assembly`, and enter it using `cd illumina_assembly`. Data in your home directory is backed up, so this is a good place to store scripts and notes, but you have a storage quota and the drive isn't as fast as using scratch or glob, so don't store data or runs here. Instead create a second project directory in glob; `cd ~/glob`, `mkdir illumina_assembly`, `cd illumina_assembly`. 

Now make a copy of the folder which includes the read data using `cp -r /proj/g2016024/nobackup/illumina_assembly/data .`
Also create an empty folder to store assemblies with `mkdir assemblies`. Now go back to your original project directory and soft-link the glob directories into the main project. `cd ~/illumina_assembly`, `ln -s ~/glob/illumina_assembly/data`, `ln -s ~/glob/illumina_assembly/assemblies`. Finally create a file called "README" where you keep notes of your project status, results, and notes. 

### Running Assemblers

You are now ready to proceed to working with the first assembly program. Here are instructions for running a number of assemblers, and you might not have time to run all of them, so select some that you like and run them in that order!

We will run assemblers by making a script for each assembler, allowing us to re-run whatever command we used at a later time if needed. Our base script will look like this:

```bash
#!/bin/bash

# Load modules here
module load bioinfo-tools
# module load <assembler module>

# make variables pointing to our data
PE_DATA="$PWD/data/S_cerevisiae_S288c_pe.fastq.gz"
MP_DATA="$PWD/data/S_cerevisiae_S288c_mp.fastq.gz"

#enter the assemblies directory
cd assemblies

#make an output directory for the assembly
mkdir -p assembler_name

#name the output
OUTPUT="S_cerevisiae_S288c"

date +"Starting assembly at %Y-%m-%d, %H:%M:%S"

# this is where we run the assembler


date +"Assembly finished at %Y-%m-%d, %H:%M:%S"
```

The instructions for running ABySS will include detailed instructions on how to write the assembly script, while the remaining assemblers will require you to do more by yourself.

#### ABySS 

_Runtime approximately 15 minutes_

[ABySS](http://www.bcgsc.ca/platform/bioinfo/software/abyss), Assembly By Short Sequences, is a _de novo_, parallel, paired-end sequence assembler that is designed for short reads. Official instructions for running the assembler can be found [here](https://github.com/bcgsc/abyss#assembling-a-paired-end-library). 

Start the project by creating a new script called "run_abyss.sh" in your project directory, and copy the contents of the above script into it. Also update the paths and assembler name, and include the `abyss/1.9.0` module. When you are done the result should look like this:

```bash
#!/bin/bash

# Load modules here
module load bioinfo-tools
module load abyss/1.9.0

# make variables pointing to our data and output directory
PE_DATA="$PWD/data/S_cerevisiae_S288c_pe.fastq.gz"
MP_DATA="$PWD/data/S_cerevisiae_S288c_mp.fastq.gz"

#enter the assemblies directory
cd assemblies

#make an output directory for the assembly
mkdir -p assembler_name
cd assembler_name

#name the output
OUTPUT="S_cerevisiae_S288c"

date +"Starting assembly at %Y-%m-%d, %H:%M:%S"

# this is where we run the assembler

date +"Assembly finished at %Y-%m-%d, %H:%M:%S"
```

The program we will invoke is called `abyss-pe`, and we will run it with a kmer-size of 37 (or some other value you choose), as well as 8 threads. Paired-end libraries can be added to the assembly with the `in="<pe-data>"` argument, and mate-pairs can be added using the `in="<mp-data>"` argument. Choose if you wish to use all of the data or just a sub-set, and then add this command to the script.

You should end up with something like:

```bash
#!/bin/bash

# Load modules here
module load bioinfo-tools
module load abyss/1.9.0

# make variables pointing to our data and output directory
PE_DATA="$PWD/data/S_cerevisiae_S288c_pe.fastq.gz"
MP_DATA="$PWD/data/S_cerevisiae_S288c_mp.fastq.gz"

#enter the assemblies directory
cd assemblies

#make an output directory for the assembly
mkdir -p assembler_name
cd assembler_name

#name the output
OUTPUT="S_cerevisiae_S288c"

date +"Starting assembly at %Y-%m-%d, %H:%M:%S"

# this is where we run the assembler
abyss-pe name=$OUTPUT k=37 np=8 in="$PE_DATA" mp="$MP_DATA"

date +"Assembly finished at %Y-%m-%d, %H:%M:%S"
```

Now run `$ chmod +x run_abyss.sh` to make the script executable.
To run the assembly you can run `$ ./run_abyss.sh >log.abyss`. This will use all your cores though, so while you should start and see that the script runs, you might wish to wait until lunch for the full run. If you have several assemblies prepared you can run them in order like: `$ ./run_abyss.sh >log.abyss && ./run_soapdenovo.sh >log.soapdenovo && ...`. 

#### SOAP denovo


_Runtime approximately 15 minutes_

[SOAP](http://soap.genomics.org.cn/soapdenovo.html), Short Oligonucleotide Analysis Package, includes SOAPdenovo, a novel short-read assembly method that can build a de novo draft assembly for the human-sized genomes. 

Start the project by creating a new script called "run_soapdenovo.sh" in your project directory, and copy the contents of the above script into it. Also update the paths and the assembler name, and include the `soapdenovo/2.04-r240` module.

SoapDeNovo requires a config file that you need to create yourself using a text editor like nano:

```
nano soap_denovo.config
```

We need to enter at least the paired-end library, and the mate-pairs if you wish. The config file will need the full paths to your data, so use the following command to find it:
```
$ find $PWD/data -name "*.fastq.gz"
```

The paired-end library should look like this:

```
[LIB]

avg_ins=500

reverse_seq=0

asm_flags=3

rank=1

p=[paired_end_data]
```

And the mate-pair library should look like:
```
[LIB]

avg_ins=3000

reverse_seq=0

asm_flags=3

rank=2

p=[mate_pair_data]
```

Exit and save the file by ctrl-x (if using nano) and answer yes when asked to save.

Start SoapDeNovo using the `SOAPdenovo-63mer` command. Use the `-p 8` flag to use 8 threads and the `-o` flag to set an output prefix, and - if you wish - you can also use one or more of the following flags, `-F` (fill gaps in scaffold), `-R` (resolve repeats by reads), or any other flag you find interesting in the documentation.

SoapDeNovo also comes with a GapCloser utlity that tries to improve the assemblies by closing gaps in the scaffolds. If you wish to use it, you can add it to she script. You can run it like:

```
GapCloser -b soap_denovo_.config -a [output prefix].scafSeq -o [output prefix].new.scafSeq -t 8 > log.gapcloser
```

#### SPAdes

**Runtime approximately 15 minutes**

[SPAdes](http://spades.bioinf.spbau.ru/release3.6.1/manual.html), St. Petersburg genome assembler, is intended for both standard isolates and single-cell MDA bacteria assemblies. Official instructions for running the assembler can be found [here](http://spades.bioinf.spbau.ru/release3.6.1/manual.html#sec3.2). 

Start the project by creating a new script called "run_spades.sh" in your project directory, and copy the contents of the above script into it. Also update the the paths and the assembler name, and include the `spades/3.9.0` module.

Spades is very easy to run in the basic configuration. Usually you want to run with the `--careful` flag, but this will take too long for this exercise. It will take around 15 mins anyway. What you DO want to do is to use the `--pe1-12` flag for paired-end data, and `--mp1-12` flag for mate-pairs. You should also use the `-t` flag to set number of threads to *8*, and the `-o` flag to set *output name*. You may use other flags too if you want to!

### Super-quick evaluation

Most of the assembly evaluation will be made later, but for now, we will run a single program - QUAST - to get some basic statistics.

You can put this in a script if you wish to, but it's also fine to run directly from the command line, as there is no real
reason that you'd wish to go back and look exactly how you ran QUAST. 

Start by loading the `bioinfo-tools` module, and the `quast/3.2` module. Then simply run:

```
$ quast -o illumina_assemblies [contigs from assemblers]
```


