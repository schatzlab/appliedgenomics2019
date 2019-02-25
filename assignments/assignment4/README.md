## Assignment 4: Read mapping and variant calling
Assignment Date: Monday, Feb. 25, 2018  
Due Date: Monday, March 4, 2018 @ 11:59pm  

## Assignment Overview

In this assignment, you will consider the algorithms and statistics to align reads to a reference genome to call SNPs and short indels. You will also perform an experiment to empirically determine the "mappability" of a genomic region. Finally, you will investigate some empirical behavior of the binomial test for heterozygous variant calling. As a reminder, any questions about the assignment should be posted to [Piazza](https://piazza.com/jhu/spring2019/en601749/home). Don't forget to read the **Resources** section at the bottom of the page!


### Question 1. Dynamic Programming [10 pts + 5pts]

- 1a. Compute the edit distance of (a portion of) the human hemoglobin alpha and beta subunits, showing the dynamic programming matrix and the aligned sequences. Assume a fixed unit cost to substitute one amino acid for another and a unit cost for an insertion or deletion. You are allowed to use the language of your choice, including spreadsheets (Excel, Google sheets, etc)

```
    Alpha: 	EALERMFLSFPTTKTYFPHFDLSHGSAQVK
    Beta: 	EALGRLLVVYPWTQRFFESFGDLSTPDAVMGNPKVK
```

- 1b. 5pt BONUS: Notice that the edit distance of GATTTACA and GATACA is 2, but there are multiple possible optimal alignments

```
    GATTTACA			GATTTACA			GATTTACA
    GAT--ACA			GA-T-ACA			GA--TACA
```

Print 5 optimal alignments between the alpha and beta sequences. If there are more than 5, just print the first 5 you find, although make sure they all have the same minimal edit distance. Hint: Instead of just following the pointers while backtracking, write a recursive depth first search to explore all the possible optimal alignments. The recursion should branch whenever there is a tie in the dynamic programming matrix.


### Question 2. Small Variant Analysis [10 pts]

Download chromosome 22 from build 38 of the human genome from here:  
[http://hgdownload.cse.ucsc.edu/goldenPath/hg38/chromosomes/chr22.fa.gz](http://hgdownload.cse.ucsc.edu/goldenPath/hg38/chromosomes/chr22.fa.gz)

Download the read set from here:  
[http://schatzlab.cshl.edu/data/teaching/sample.tgz](http://schatzlab.cshl.edu/data/teaching/sample.tgz)

For this question, you may find this tutorial helpful:  
[http://clavius.bc.edu/~erik/CSHL-advanced-sequencing/freebayes-tutorial.html](http://clavius.bc.edu/~erik/CSHL-advanced-sequencing/freebayes-tutorial.html)

- 2a. Using bowtie2, how many reads align to the chr22 reference? How many reads did not align? How many aligned reads had a mate that did not align (AKA singletons)? Count each read in a pair separately.  
[Hint: Build the index using `bowtie2-build`, align reads using `bowtie2`, analyze with `samtools flagstat`.]

- 2b. How many reads are mapped to the reverse strand? Count each read in a pair separately.   
[Hint: Find out what SAM flags mean [here](https://broadinstitute.github.io/picard/explain-flags.html) and use samtools view.]

- 2c. How many high-quality (QUAL > 20) single nucleotide and indel variants does the sample have? Of the indels, how many are insertions and how many are deletions?  
[Hint:  Identify variants using `freebayes` - sort the SAM file first. Filter using `bcftools filter`, and summarize using `bcftools stats`.]

- 2d. Does the sample have any nonsense or missense mutations?  
[Hint: try the [Variant Effect Predictor](http://useast.ensembl.org/Tools/VEP) using the `Gencode basic transcripts`]


### Question 3. Read Mapping Uncertainty [10 pts]

For the region chr22:21000000-22000000 of the reference sequence for chromosome 22, extract every substring of length 35. Format the substrings as a FASTA file and use read names that indicate the origin. (No need to construct quality values or read pairs: use bowtie2 with `-f` and `-U` respectively). Make a new index and align these "reads" to chr22:21000000-22000000.  
[Hint: On the command line or in a script, load the sequence once and extract substrings in a loop.]

- 3a. How many reads align more than one time to the reference? How many reads did not align?
[Hint: make a fasta file containing just chr22:21000000-22000000 and then extract every substring of length 35]

- 3b. How many reads align correctly? Consider a read aligned correctly if the left end of the alignment is within 5bp of the true location.  
[Hint: Investigate the [SAM file format specification](https://samtools.github.io/hts-specs/SAMv1.pdf).]

- 3c. For 1000bp nonoverlapping windows, plot the average mapping quality of the reads that mapped to this location, regardless of correctness. On the plot, the x-axis is starting coordinate of the bin and y-axis is the value.

- 3d. For 1000bp nonoverlapping windows, plot the fraction of reads that *belonged* there that were *correct*. On the plot, the x-axis is starting coordinate of the bin and y-axis is the fraction


### Question 4. Binomial Distribution [10 pts]

- 4a. For coverage n = 10 to 200, calculate the maximum number of minor allele reads (round down) that would make your one-sided binomial test reject the null hypothesis p=0.5 at 0.05 significance. Plot coverage on the x-axis and (number of reads)/(coverage) on the y-axis.

- 4b. What asymptote does the plot seem to approach? Why is this?


## Packaging

The solutions to the above questions should be submitted as a single PDF document that includes your name, email address, and 
all relevant figures (as needed). Make sure to clearly label each of the subproblems and **give the exact commands** you used for 
solving the question. Submit your solutions by uploading the PDF to [GradeScope](http://www.gradescope.com/). 

If you submit after this time, you will start to use up your late days. Remember, you are only allowed 4 late days for the entire semester!



## Resources

#### [FreeBayes](https://github.com/ekg/freebayes) - Small Variant Identification

```
conda install freebayes
freebayes -f chr22.fa sample.bam > sample.vcf
```

#### [bcftools](https://samtools.github.io/bcftools/bcftools.html) - VCF Summarry

```
conda install bcftools
bcftools filter -e "QUAL<20" sample.vcf > filtered.vcf
bcftools stats filtered.vcf > stats.txt
```

#### [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml) - Short read aligner

```
conda install bowtie2
bowtie2-build chr22.fa chr22
bowtie2 -x chr22 -1 sample/pair.1.fq -2 sample/pair.2.fq > sample.sam
```

While running alignments, if you get an error `Warning: Exhausted best-first chunk memory for read XXXX; skipping read` increase the command-line parameter `--chunkmbs`.

#### [Variant Effect Predictor](http://useast.ensembl.org/Tools/VEP) - Variant Interpretation

Nothing to install, just upload the VCF

