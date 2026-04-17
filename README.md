# Bioinformatics Singularity Containers

This repository contains definition files and build instructions for Singularity containers used in NGS pipelines.

## Project Overview
Containers are primarily built from Docker Hub images. The **SpliceAI** container includes a custom modification to handle the Debian Buster EOL (End of Life) repository status, ensuring `apt-get` functionality.

---

## Software Inventory

| Tool | Version | Source |
| :--- | :--- | :--- |
| **Annovar** | 2020Jun08 | bioinfochrustrasbourg/annovar:2018Apr16 |
| **BCFtools** | 1.23 | quay.io/biocontainers/bcftools:1.23 |
| **BWA-MEM2** | 1.22.1 | dceoy/bwa-mem2 |
| **DeepVariant** | 1.9.0 | google/deepvariant:1.9.0 |
| **DeepTrio** | 1.8.0 | google/deepvariant:1.8.0 |
| **FastP** | 0.23.4 | mgibio/fastp:v0.23.4-noble |
| **FastQC** | 0.12.1 | getwilds/fastqc:0.12.1 |
| **GATK4** | 4.6.2.0 | broadinstitute/gatk:4.6.2.0 |
| **GLnexus** | 1.2.7 | edansare/glnexus |
| **Mosdepth** | 0.3.11 | gfanz/mosdepth |
| **MultiQC** | 1.33 | multiqc/multiqc:v1.33 |
| **Peddy** | 0.4.8 | clinicalgenomics/peddy:0.4.8 |
| **Python** | 3.14.2 | python:3.14.2 |
| **Samtools** | 1.23 | staphb/samtools:1.23 |
| **SpliceAI** | Modified | Custom (see below) |
| **VCFTools** | 0.1.16.1 | biocontainers/vcftools:v0.1.16-1 |

---

## Build Instructions

### Standard Builds
Use these commands to build `.sif` images directly from Docker Hub:

```bash
singularity build annovar_2020Jun08.sif docker://bioinfochrustrasbourg/annovar:2018Apr16
singularity build bcftool_1.23.sif docker://quay.io/biocontainers/bcftools:1.23--h3a4d415_0
singularity build bwa-mem2-samtool2.3.1_1.22.1.sif docker://dceoy/bwa-mem2
singularity build deepvariant_1.9.0.sif docker://google/deepvariant:1.9.0
singularity build fastp_0.23.4.sif docker://mgibio/fastp:v0.23.4-noble
singularity build fastqc_0.12.1.sif docker://getwilds/fastqc:0.12.1
singularity build gatk4_4.6.2.0.sif docker://broadinstitute/gatk:4.6.2.0
singularity build mosdepth_0.3.11.sif docker://gfanz/mosdepth
singularity build multiqc_1.33.sif docker://multiqc/multiqc:v1.33
singularity build peddy_0.4.8.sif docker://clinicalgenomics/peddy:0.4.8
singularity build python_3.14.2.sif docker://python:3.14.2
singularity build samtools_1.23.sif docker://staphb/samtools:1.23
singularity build vcftools_0.1.16.1.sif docker://biocontainers/vcftools:v0.1.16-1-deb_cv1
```
### spliceAI modification
```bash
singularity build spliceai.sif docker://davidwu20/spliceai:latest

cat << 'EOF' > spliceai_fixed.def
Bootstrap: localimage
From: spliceai.sif

%post
    # Switch to archive repos for Debian Buster
    echo "deb http://archive.debian.org/debian buster main" > /etc/apt/sources.list
    echo "deb http://archive.debian.org/debian-security buster/updates main" >> /etc/apt/sources.list
    
    # Perform update and install required tools
    apt-get -o Acquire::Check-Valid-Until=false update
    apt-get install -y procps
    rm -rf /var/lib/apt/lists/*
EOF

singularity build spliceai_fixed.sif spliceai_fixed.def
mv spliceai_fixed.sif spliceai.sif
```
### Usage
Run commands within the containers using the following syntax:

```Bash
singularity exec <image_name>.sif <command>
```

---

## Contact

**Deepak Bharti**  
Clinical Bioinformatician, RCSI  
deepakbharti@rcsi.ie

---
