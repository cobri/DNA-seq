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



### Reproducibility

In the interst of reproducibility, many automated 'pipelines' or 'workflows' for anaylsing next-gen sequencing data have been developed. There are various pros and cons of each, but some poopular systems include:

* bcbio-nextgen
* snakemake
* nextflow
However, while these are often used by large sequencing cores, bash/python scripts still remain widely used by individual bioinformaticians.
