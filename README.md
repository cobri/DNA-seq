# DNA-seq

My collection of resources for DNA-seq analysis. I needed a place to compile and organise all my notes - so here it is!

### 

There are many tools for variant calling, but GATK from the Broad Institute is considered one of the best, and is widely used.
The following are the main steps in the variant calling pipeline.

1) Alignment
2) Mark/Remove Duplicates
3) Local Re-alignment/Base Recalibration
4) Call variants
5) Filter variants
6) Callset refinement 
6) Annotate variants


<img src="https://software.broadinstitute.org/gatk/img/BP_workflow_3.6.png" width="564" height="306"/>


### File Formats

The output of a variant caller is a vcf file. It describes variation in the genome.  Information here is from the [official documentation.](http://samtools.github.io/hts-specs/VCFv4.3.pdf)

It contains 2 parts: a meta-information/header section and a record section. The lines of the meta-information/header section start with ##.

The header line names the 8 fixed, mandatory columns. There are often many more optional columns. These columns are as follows: 

1. #CHROM
2. POS
3. ID - The rs number if it a dbSNP variant
4. REF - Reference base
5. ALT - Alternate base
6. QUAL - The Phred-scaled probability that a REF/ALT polymorphism exists at this site given sequencing data, i.e. −10log<sub>10</sub> prob(call in ALT is wrong). E.g. value of 10 indicates a 1 in 10 chance of error, while a value of 100 indicates a 1 in 10^10 chance. High QUAL -> high confidence calls.
7. FILTER - 'PASS' if the variant passed all filters. '.' if no filtering has been applied. Otherwise, a semicolon-separated list of codes for filters that fail.
8. INFO - Additional information, represented as ID=VALUE. The exact format of each INFO sub-field should be specified in the header.
9. FORMAT - Sample-level annotations. Colon-separated data in this field corresponds to the types specified in the format. The first sub-field must always be the genotype (GT) if it is present. There are several common standard keywords:
    * GT : genotype, encoded as allele values separated by either of / or |. The allele values are 0 for the reference allele (what is in the REF field), 1 for the first allele listed in ALT, 2 for the second allele list in ALT and so on. 
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
