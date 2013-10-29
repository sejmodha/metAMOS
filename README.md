[![githalytics.com alpha](https://cruel-carlota.pagodabox.com/50642308f8120a4dd17e617e46cc21b1 "githalytics.com")](http://githalytics.com/treangen/metAMOS)
# MetAMOS v1.3 "Candied Bacon"

Last updated: September 18th, 2013

***********************************************************************************
We are happy to announce version 1.3, a.k.a. Candied Bacon

## NEWS
       1. Updated 64-bit frozen Linux binary now available (fixed taxonomy dir issue)
       2. Generic module support officialy supported for Assemble and Annotate step
       3. Spades & Kraken added
       4. Added prelim support for program workflows (core, optional, deprecated).
       5. Added prelim support for new MultiAlign step
       6. Numerous bug fixes

*on deck: kmher, MaSuRCA, Ray Meta, and Viritas

-----------------------------------------------------------------------------------
## **MetAMOS single file binary**

In attempt to further simplify the MetAMOS installation process, we are happy to announce the availability of a 'frozen' MetAMOS binary for Linux-x68_64 platforms. Along with this binary comes a significantly reduced list of prerequisites:

* Java 1.5 (or newer)
* Perl 5.8.8 (or newer)
* 64-bit *nix OS

You can download a frozen binary with all BLAST DBs [here](http://www.cbcb.umd.edu/confcour/temp/metAMOS_binary.tar.gz) 

*Note: this is a big file to download and will take awhile to finish (~13GB compressed, 27GB unzipped)

Alternatively, you can download a frozen binary with NO BLAST DBs [here](http://www.cbcb.umd.edu/confcour/temp/metAMOS_binary_noblastdbs.tar.gz) 

*Note: ~2GB compressed, 4GB unzipped

The revised installation procedure is simply:

1. Download tarball
2. Extract
3. Run a test script or two

Mac OSX equivalent will be soon appearing [here](http://www.cbcb.umd.edu/confcour/temp/metAMOS_binary_noblastdbs_osx.tar.gz) 

----------------------------------------------------------------------------------

## SUMMARY
        A) HARDWARE REQUIREMENTS
        B) SOFTWARE REQUIREMENTS
        C) INSTALLING MetAMOS
        D) QUICK START
        E) TEST SUITE
        F) EXAMPLE OUTPUT
        G) CONTACT
        H) CITING

----------------------------------------------------------------------------------
### A) HARDWARE REQUIREMENTS

MetAMOS was designed to work on any standard 64bit Linx
environment. To use MetAMOS for tutorial/teaching purposes, a minimum
of 8 GB RAM is required. To get started on real data sets a minimum of
32 GB of RAM is recommened, and anywhere from 64-1000 GB may be
necessary for larger datasets. In our experience, for most 50-100
million read datasets, 64 GB is a good place to start (128 GB of memory now
available on High Memory Instance at Amazon Elastic Compute Cloud ). 

----------------------------------------------------------------------------------
### B) SOFTWARE REQUIREMENTS

The main prequisite software is python2.6+. Once python2.6+ is installed, there
should not be any other major prerequisites as most everything that is
needed is distributed with MetAMOS inside of the /Utilities
directory. Depending on your platform/Linux distribution, you might need to download and install the following BEFORE running INSTALL.py:

1. git
2. gcc
3. automake
4. python-tools
5. python-devel
6. zlib-devel
7. numpy
8. freetype, freetype-devel
9. libpng-devel
10. matplotlib
11. R

Additionally, there is some software that MetAMOS can
incorporate into its pipeline that we are not allowed to distribute,
such as MetaGeneMark. To get a license to use MetaGeneMark, please
visit: http://exon.gatech.edu/license_download.cgi.

----------------------------------------------------------------------------------
### C) INSTALLING MetAMOS

To download the software, go [here](https://github.com/treangen/metAMOS/archive/Release1.3.zip).
You can also browse the [repository](https://github.com/treangen/MetAMOS/tree/Release1.3)
and click on Downloads. Once downloaded, simply unpack the files and
open the MetAMOS directory. Once inside the MetAMOS directory, run:

python INSTALL.py

This will download and install any external dependencies (or they can
be refused by answering NO), which may take minutes or hours to
download depending on your connection speed. If all dependencies are
downloaded (including optional ones), this will take quite awhile to
complete (plan on a few hours to 2 days).

----------------------------------------------------------------------------------
### D) QUICK START

Before you get started using MetAMOS a brief review of its design will
help clarify its intended use. MetAMOS gas two main components:

1.  initPipeline
2.  runPipeline

The first component, initPipeline, is for creating new projects and
also initiliazing sequence libraries. Currently interleaved &
non-interleaved fasta, fastq, and SFF files are supported. The file-type flags (-f, -q, and -s)
must be specified before the file. Once specified, they remain in effect
until a different file type is specified.

usage: initPipeline -f/-q -1 file.fastq.1 -2 file.fastq.2 -d projectDir -i 300:500 

options: -s -c -q, -f, -1, -2, -d, -m, -i

* -1: either non-paired file of reads or first file in pair, can be list of multiple separated by a comma
* -2: second paired read file, can be list of multiple separated by a comma
* -c:  fasta file containing contigs
* -d: output project directory (required)
* -f: boolean, reads are in fasta format (default is fastq)
* -h: display help message
* -i: insert size of library, can be list separated by commas for multiple libraries
* -l: SFF linker type
* -m: interleaved file of paired reads
* -o: reads are in outtie orientation (default innie)
* -q: boolean, reads are in fastq format (default is fastq)
* -s/--sff: boolean, reads are in SFF format (default is fastq)

For example, to input a:

(non-interleaved fastq, single library)
*initPipeline -q -1 file.fastq.1 -2 file.fastq.2 -d projectDir -i 300:500

(non-interleaved fasta, single library)
*initPipeline -f -1 file.fastq.1 -2 file.fastq.2 -d projectDir -i 300:500

(interleaved fastq, single library)
*initPipeline -q -m file.fastq.12  -d projectDir -i 300:500

(interleaved fastq, multiple libraries)
*initPipeline -q -m file.fastq.12,file2.fastq.12  -d projectDir -i 300:500,1000:2000

(interleaved fastq, multiple libraries, existing assembly)
*initPipeline -q -m file.fastq.12,file2.fastq.12 -c file.contig.fa -d projectDir -i 300:500,1000:2000

The second component, runPipeline, takes a project directory as input and runs the following steps by default:

1. Preprocess
2. Assemble
3. FindORFs
4. FindRepeats
5. Abundance
6. Annotate
7. FunctionalAnnotation
8. Scaffold
9. Propagate 
10. FindScaffoldORFs
11. Classify 
12. Postprocess

usage info:

usage: runPipeline [options] -d projectdir

   -h = <bool>:   print help [this message]
   -j = <bool>:   just output all of the programs and citations then exit (default = NO)
   -v = <bool>:   verbose output? (default = NO)
   -d = <string>: directory created by initPipeline (REQUIRED)

[options]: [pipeline_opts] [misc_opts]

[pipeline_opts]: options that affect the pipeline execution

Pipeline consists of the following steps:

  Preprocess, Assemble, FindORFS, MapReads, Abundance, Annotate,
  Scaffold, Propagate, Classify, Postprocess

Each of these steps can be referred to by the following options:

*   -f = <string>: force this step to be run (default = NONE)
*   -s = <string>: start at this step in the pipeline (default = Preprocess)
*   -e = <string>: end at this step in the pipeline (default = Postprocess)
*   -n = <string>: step to skip in pipeline (default=NONE)

For each step you can fine-tune the execution as follows

[Preprocess]

*   -t = <bool>:   filter input reads? (default = NO)
*   -q = <bool>:   produce FastQC quality report for reads with quality information (fastq or sff)? (default = NO)


[Assemble]

*   -a = <string>: genome assembler to use (default = SOAPdenovo)
		  this can also be a comma-separated list of assembler (for example: soap,velvet)
		  in this case, all selected assemblers will be run and the best selected for subsequent analysis
*   -k = <kmer size>: k-mer size to be used for assembly (default = 31)
*   -o = <int>:    min overlap length


[MapReads]

*   -m = <string>: read mapper to use? (default = bowtie)
*   -i = <bool>:   save bowtie (i)ndex? (default = NO)
*   -b = <bool>:   create library specific per bp coverage of assembled contigs (default = NO)


[FindORFS]

*   -g = <string>: gene caller to use (default=FragGeneScan)
*   -l = <int>:    min contig length to use for ORF call (default = 300)
*   -x = <int>:    min contig coverage to use for ORF call (default = 3X)


[Annotate]

*   -c = <string>: classifier to use for annotation (default = FCP)
*   -u = <bool>:   annotate unassembled reads? (default = NO)


[Classify]

*   -z = <string>: taxonomic level to categorize at (default = class)

[misc_opts]: Miscellaneous options

*   -r = <bool>:   retain the AMOS bank?  (default = NO)
*   -p = <int>:    number of threads to use (be greedy!) (default=1)
*   -4 = <bool>:   454 data? (default = NO)

For example, to enable read filtering:

*   -t

and to enable meta-IDBA as the assembler:

*   -a metaidba

And to use PhyloSift to annotate:

*   -c phylosift

Any single step in the pipeline can be skipped by passing the
following parameter to runPipeline:

*   -n,--skipsteps=Step1,..

MetAMOS reruns steps based on timestamp information, so if the input
files for a step in the pipeline hasn't changed since the last run, it
will be skipped automatically. However, you can forefully run any step
in the pipeline by passing the following parameter to runPipeline:

*   -f,--force=Step1,..

MetAMOS stores a summary of the input libraries in pipeline.ini 
in the working directory. The pipeline.conf file stores the list 
of programs available to MetAMOS. Finally, pipeline.run stores the 
selected parameters and programs for the current run. MetAMOS also stores 
detailed logs of all commands executed by the pipeline in Logs/COMMANDS.log 
and a log for each step of the pipeline in Logs/<STEP NAME>.log

Upon completion, all of the final results will be stored in the
Postprocess/out directory. A component, create_summary.py, takes
this directory as input and as output, generates an HTML page with
with summary statistics and a few plots. An optional component, create_plots.py,
takes one or multiple Posprocess/out directories as input and generates
comparative plots.


----------------------------------------------------------------------------------
### E) Test suite

We have developed a set of scripts for testing the various features of
MetAMOS. All of these regression test scripts are available inside the
/Test directory and include all necessary datasets to run them. Here
is a brief listing of the test scripts we currently include:

* ./Test/test_create.sh (Test initPipeline)
* ./Test/run_test.sh (Vanilla test)
* ./Test/test_phylosift.sh (Test phylosift)
* ./Test/test_minimus.sh (Test Minimumus)

*Test Preprocess filtration of non-interleaved fastq files
./Test/test_filter_noninterleaved_fastq.s

*Test Newbler (if available)
./Test/test_newbler.sh

*Test CA (fasta)
./Test/test_ca_fasta.sh

*Test CA (fastq)
./Test/test_ca.sh

*Test SOAPdenovo
./Test/test_soap.sh

*Test MetaVelvet
./Test/test_metavelvet.sh

*Test SparseAssembler
./Test/test_sparse.sh

*Test Velvet
./Test/test_velvet.sh

*Test FCP
./Test/test_fcp.sh

*Test Spades
./Test/test_spades.sh

#Test BLAST
./Test/test_blast.sh

..more on the way!

----------------------------------------------------------------------------------
### F) Example output

MetAMOS generates an interactive webpage once a run successfully completes:
http://treangen.github.io/metAMOS/example/html/summary.html

This includes summary statistics and taxonomic information based on Krona
Krona publication: Ondov BD, Bergman NH, Phillippy AM.. Interactive
metagenomic visualization in a Web browser. BMC Bioinformatics. 2011
Sep 30;12:385.  PMID: 21961884

Additionally, since MetAMOS stores all of its results in an AMOS bank,
the assemblies can be visualized with Hawkeye.

----------------------------------------------------------------------------------
### G) BUGS

If you encounter any problems/bugs, please check the known issues pages:
https://github.com/treangen/MetAMOS/issues?direction=desc&sort=created&state=open
to see if it has already been documented.

If not, please report the issue either using the contact information below or 
by submitting a new issue online. Please include information on your run,
any output produced by runPipeline, as well as the pipeline.* files and the 
Log/<LAST_STEP> file (if not too large).

### H) CONTACT

Who to contact to report bugs, forward complaints, feature requests:

Todd Treangen: treangen@gmail.com
Sergey Koren: sergek@umd.edu

----------------------------------------------------------------------------------
### I) CITE

Treangen TJ*, Koren S*, Sommer DD, Liu B, Astrovskaya I, Ondov B,
Darling AE, Phillippy AM, Pop M.  MetAMOS: a modular and open source
metagenomic assembly and analysis pipeline. Genome Biol. 2013 Jan
15;14(1):R2. PMID: 23320958.

url: http://genomebiology.com/content/pdf/gb-2013-14-1-r2.pdf

*Indicates both authors contributed equally to this work
