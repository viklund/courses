---
layout: default
title:  'Exercise: Illumina Assembly'
---

## Exercise: Illumina Assembly

In this exercise you will assemble genomes de novo using commonly used assembly software. You will work with Illumina data of _Saccharomyces cerevisiae S288c_, data that was downloaded from the NCBI Short Read Archive (SRA). Due to time-restrictions we are forced to stick to assemblers that run quickly, but if you have an assembly project of your own you are encouraged to try other assemblers too. Remember that no assembler is best for all projects; it is recommended to try several.

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

OUTDIR="assemblies/assembler_name_assembly"

date +"Starting assembly at %Y-%m-%d, %H:%M:%S"

# this is where we run the assembler


date +"Assembly finished at %Y-%m-%d, %H:%M:%S"
```

The instructions for running ABySS will include detailed instructions on how to write the assembly script, while the remaining assemblers will require you to do more by yourself.

#### ABySS 

[ABySS](http://www.bcgsc.ca/platform/bioinfo/software/abyss), Assembly By Short Sequences, is a _de novo_, parallel, paired-end sequence assembler that is designed for short reads. Official instructions for running the assembler can be found [here](https://github.com/bcgsc/abyss#assembling-a-paired-end-library). 

Start the project by creating a new script called "run_abyss.sh" in your project directory, and copy the contents of the above script into it. Also update the assembler name and include the `abyss/1.9.0` module. When you are done the result should look like this:

```bash
#!/bin/bash

# Load modules here
module load bioinfo-tools
module load abyss/1.9.0

# make variables pointing to our data and output directory
PE_DATA="$PWD/data/S_cerevisiae_S288c_pe.fastq.gz"
MP_DATA="$PWD/data/S_cerevisiae_S288c_mp.fastq.gz"

OUTDIR="assemblies/abyss_assembly"

date +"Starting assembly at %Y-%m-%d, %H:%M:%S"

# this is where we run the assembler


date +"Assembly finished at %Y-%m-%d, %H:%M:%S"
```




#### MIRA

#### SOAP denovo

#### Spades

#### Velvet

