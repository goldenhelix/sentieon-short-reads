# Sentieon Short Reads

This repository contains workflows for short-read genomic analysis using Sentieon's genomics tools. For detailed information about Sentieon's tools and algorithms, please refer to the [Sentieon Manual](https://support.sentieon.com/manual/).

## Prerequisite

Run the **Download Genomic Reference Resources** task to download and prepare the required local reference sequence and model files. These resources must be available on your system before running any of the workflows.

Before downloading, use [Workspace Settings](./manage/settings) to specify the target location with the `RESOURCE_PATH` variable.

## Overview

This repository focuses exclusively on short-read analysis workflows and includes:

1. Germline analysis workflow that combines alignment and variant calling
2. Somatic analysis task for tumor-normal paired samples or tumor-only samples

## Germline Analysis

### Germline Alignment and Variant Calling Workflow

The `germline_align_call.workflow.yaml` file defines a workflow that performs two sequential steps:

1. **Alignment with BWA-MEM**: Aligns FASTQ files to a reference genome
2. **Variant Calling with DNAscope**: Calls variants from the aligned BAM files

This workflow is designed to process samples from a directory structure where FASTQ files follow the pattern `*_*.fastq.gz` or `*_*.fq.gz` where the sample name is the characters up to the first `_`. All files with this prefix will be aligned together, supporting the reads being spread across multiple latest (i.e. L001, L002 etc).

#### Workflow Parameters

- **Output Folder**: Directory where results will be stored
- **Machine Learning Model**: Model to use for variant calling (default: None)
- **Reference File**: FASTA file for alignment and variant calling; defaults to variables `${RESOURCES_PATH}/${REFERENCE_PATH}` set in the workspace settings

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
- **Reference File**: FASTA file for alignment (defaults to workspace reference if not provided)
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
- **Reference File**: FASTA file for variant calling (defaults to workspace default if not provided)
- **Machine Learning Model**: Platform-specific model for optimization
- **Advanced Options**:
  - PCR Free Sample
  - Output GVCF

## Somatic Analysis

The **Somatic Alignment and Variant Calling** workflow (`somatic_align_call.workflow.yaml`) performs both alignment and variant calling for somatic samples. The alignment step treats every sample independently, generating BAM/CRAM files for each. The workflow then relies on the relationship between tumor and normal samples as defined in the workspace **SampleCatalog**. 

A dedicated step in the workflow generates a manifest (batch parameter file) that pairs tumor samples with their matched normal samples based on the SampleCatalog. If a tumor sample does not have a normal sample specified in the SampleCatalog, it will be processed in tumor-only mode for variant calling.

### Somatic Alignment and Variant Calling Workflow

The `somatic_align_call.workflow.yaml` file defines a multi-stage workflow:

1. **Alignment with BWA-MEM**: Aligns each sample's FASTQ files independently.
2. **Generate Batch Parameter File**: Scans the aligned samples and uses the SampleCatalog to pair tumor and normal samples, creating a manifest for downstream analysis.
3. **Variant Calling with TNscope**: Performs variant calling using Sentieon's TNScope algorithm, using the manifest to process tumor-normal pairs or tumor-only samples as appropriate.

#### Key Features

- Independent alignment of all samples
- Automatic pairing of tumor and normal samples using SampleCatalog relationships
- Tumor-only variant calling if no normal sample is specified
- High sensitivity for detecting somatic variants
- Optimized for cancer genomics applications

#### Workflow Parameters

- **Input Folder**: Directory containing FASTQ files for alignment
- **Output Folder**: Directory for alignment and variant calling results
- **Reference File**: FASTA file for alignment and variant calling (defaults to workspace default if not provided)
- **SampleCatalog**: Used to define tumor/normal relationships
