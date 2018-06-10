# DNA-seq

:notebook: My notes for DNA-seq analysis. :+1: 


### 

There are many tools for variant calling, but GATK from the Broad Institute is considered one of the best, and is widely used.
The following are the main steps in the variant calling pipeline.

1) Alignment
2) Mark/Remove Duplicates
3) Local Re-alignment/Base Recalibration
4) Call variants
5) Filter variants
6) Callset refinement 
7) Annotate variants


<img src="https://software.broadinstitute.org/gatk/img/BP_workflow_3.6.png" width="564" height="306"/>

1) Alignment

   Align fastq files to reference genome. Convert to BAM format and sort.

2) Mark Duplicates

   Mark duplicate fragments or remove them altogether. This process identifies read pairs that are likely to have originated from duplicates of the same original DNA fragments through some artifactual processes (e.g. PCR duplicates). They are considered to be non-independent observations so they will be ignored in downstream analyses. 
   
3) Recalibrate Bases

   Base Quality Score Recalibration (BSQR) is a machine learning method to adjust base quality scores, which are confidence scores emitted by the sequencer for each base. It detects and corrects for systematic errors made by the sequencer when it estimates the quality score of each base call. It "builds a model of covariation based on the data and a set of known variants, then it adjusts the base quality scores in the data based on the model". *Datasets to use: dbSNP (>132), Mills indels, and 1KG indels.*
   
4) Call variants

   GATK calls variants via "local de-novo assembly of haplotypes in an active region". Firstly, run the `HaplotypeCaller` tool on individual BAM files. This will produce genotype likelihood information for each sample for each locus. The output from this step will be the intermediate individual-sample files `*.g.vcf` which you will then use to perform Joint variant calling with the `GenotypeGVCFs` tool. This will call variants jointly on all your samples.
   
5) Filter variants
   - Variant Quality Score Recalibration (VQSR)

   Calculates a new well-calibrated quality score (the VQSLOD value) using "machine learning algorithms to learn from each dataset what is the annotation profile of good variants vs. bad variants". Uses a training dataset of known variants (e.g. hapmap, 1000G) to learn how to recognise good variants. It's a two step process: VariantRecalibrator followed by ApplyRecalibration. May not be able to use with small sequencing experiments - "This tool is expecting thousands of variant sites in order to achieve decent modeling with the Gaussian mixture model." *Datasets to use: dbSNP (>132) and Mills indels.* 
   
   Alternatively, if you have a small data-set you can use hard filtering. Suggested parmaters are discussed [here](https://software.broadinstitute.org/gatk/documentation/article.php?id=3225).
   
   
6) Callset refinement

   "The goal of the Genotype Refinement workflow is to use additional data to improve the accuracy of genotype calls and to filter genotype calls that are not reliable enough for downstream analysis." (Optional, according to GATK.)

   

7) Annotate variants

   After filtering your variants, you now want to prioritise the ones most likely to be functional and relevant to your disease. Here is a great review paper: [A practical guide to filtering and prioritizing genetic variants](https://www.biotechniques.com/BiotechniquesJournal/2017/January/A-practical-guide-to-filtering-and-prioritizing-genetic-variants/biotechniques-365454.html?pageNum=1)

   The most widely used tools are curently: SnpEff, ANNOVAR, and VEP.

### File Formats

The output of a variant caller is a vcf file. It describes variation in the genome.  Information here is from the [official documentation.](http://samtools.github.io/hts-specs/VCFv4.3.pdf)

It contains 2 parts: a meta-information/header section and a record section. The lines of the meta-information/header section start with ##.

The header line names the 8 fixed, mandatory columns. There are often many more optional columns. These columns are as follows: 

1. **#CHROM**
2. **POS**
3. **ID** - The rs number if it a dbSNP variant
4. **REF** - Reference base
5. **ALT** - Alternate base
6. **QUAL** - The Phred-scaled probability that a REF/ALT polymorphism exists at this site given sequencing data, i.e. −10log<sub>10</sub> prob(call in ALT is wrong). E.g. value of 10 indicates a 1 in 10 chance of error, while a value of 100 indicates a 1 in 10^10 chance. High QUAL -> high confidence calls.
7. **FILTER** - 'PASS' if the variant passed all filters. '.' if no filtering has been applied. Otherwise, a semicolon-separated list of codes for filters that fail.
8. **INFO** - Additional information, represented as ID=VALUE. The exact format of each INFO sub-field should be specified in the header.

<img src="https://github.com/cobri/DNA-seq/blob/master/VCF_INFO.png?raw=true" width="600" height="340"/>



9. **FORMAT** - Optional sample-level annotations. Colon-separated data in this field corresponds to the types specified in the format. The first sub-field must always be the genotype (GT) if it is present. There are several common standard keywords:
    * GT : Genotype. 
      - 0/0 - homozygous reference
      - 0/1 - heterozygous
      - 1/2 - heterozygous for the two ALT allelles
      - 1/1 - homozygous first ALT
      - 2/2 - homozygous second ALT
    * DP : read depth at this position for this sample
    * FT : sample genotype filter indicating if this genotype was “called”
    * GL : genotype likelihoods comprised of comma separated floating point log10-scaled likelihoods for all possible genotypes given the set of alleles defined in the REF and ALT fields.
    * HQ : haplotype qualities, two comma separated phred qualities (Integers)



### Reproducibility

In the interst of reproducibility, many automated 'pipelines' or 'workflows' for anaylsing next-gen sequencing data have been developed. There are various pros and cons of each, but some poopular systems include:

* bcbio-nextgen
* snakemake
* nextflow

However, while these are often used by large sequencing cores, bash/python scripts still remain widely used by individual bioinformaticians.
