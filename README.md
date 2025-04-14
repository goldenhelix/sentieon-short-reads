# Sentieon Short Reads

This repository contains workflows for short-read genomic analysis using Sentieon's genomics tools. For detailed information about Sentieon's tools and algorithms, please refer to the [Sentieon Manual](https://support.sentieon.com/manual/).

## Overview

This repository focuses exclusively on short-read analysis workflows and includes:

1. Germline analysis workflow that combines alignment and variant calling
2. Somatic analysis task for tumor-normal paired samples

## Germline Analysis

### Germline Alignment and Variant Calling Workflow

The `germline_align_call.workflow.yaml` file defines a workflow that performs two sequential steps:

1. **Alignment with BWA-MEM**: Aligns FASTQ files to a reference genome
2. **Variant Calling with DNAscope**: Calls variants from the aligned BAM files

This workflow is designed to process samples from a directory structure where FASTQ files follow the pattern `*/*-*R1_001.fastq.gz` and automatically extracts sample names from the file paths.

#### Workflow Parameters

- **Output Folder**: Directory where results will be stored
- **Machine Learning Model**: Model to use for variant calling (default: None)
- **Reference File**: FASTA file for alignment and variant calling (default: hg38)

### BWA-MEM Alignment Task

The `bwa.task.yaml` file defines a task that performs alignment using Sentieon's implementation of BWA-MEM.

#### Key Features

- Supports both single-end and paired-end reads
- Optional deduplication of reads
- Configurable machine learning models for platform-specific optimization
- Generates alignment metrics
- Option to output CRAM format instead of BAM

#### Task Parameters

- **Sample Name**: Identifier for the sample
- **Input Folder**: Directory containing FASTQ files
- **Output Folder**: Directory for alignment results
- **Reference File**: FASTA file for alignment
- **Machine Learning Model**: Platform-specific model for optimization
- **Advanced Options**:
  - Use Sample Name as Prefix Filter
  - Paired-end Reads
  - Perform Deduplication
  - Output Metrics
  - Output CRAM

### DNAscope Variant Calling Task

The `dnascope.task.yaml` file defines a task that performs variant calling using Sentieon's DNAscope algorithm.

#### Key Features

- High-accuracy variant calling optimized for germline analysis
- Support for platform-specific machine learning models
- Option to output GVCF format for joint genotyping
- PCR-free library optimization

#### Task Parameters

- **Sample Name**: Identifier for the sample
- **Input File**: BAM or CRAM file from alignment
- **Output Folder**: Directory for variant calling results
- **Reference File**: FASTA file for variant calling
- **Machine Learning Model**: Platform-specific model for optimization
- **Advanced Options**:
  - PCR Free Sample
  - Output GVCF

## Somatic Analysis

### Tumor-Normal Variant Calling Task

The `tnscope.task.yaml` file defines a task that performs somatic variant calling using Sentieon's TNScope algorithm.

#### Key Features

- Designed for tumor-normal paired samples
- High sensitivity for detecting somatic variants
- Optimized for cancer genomics applications
- Can use BAM/CRAM files produced by the BWA-MEM alignment task

#### Task Parameters

- **Tumor Sample Alignments**: BAM or CRAM file for the tumor sample
- **Normal Sample Alignments**: BAM or CRAM file for the matched normal sample
- **Output Folder**: Directory for variant calling results
- **Reference File**: FASTA file for variant calling

## Usage

These workflows and tasks are designed to be run on the Golden Helix server workflow runner. They require a valid Sentieon license configured in the environment.

## Requirements

- Sentieon license
- Docker with the Sentieon image: `registry.goldenhelix.com/public/sentieon:202503`
- Sufficient computational resources (CPU cores and memory as specified in each task)
