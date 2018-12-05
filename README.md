Amplicon analysis using QIIME2

This file is a backup of the commands/pipeline used to analyse soil microbiomes. Project A is based on sequencing using primers 341F-806R (16s) and ITS V3-V4. Project B was based on Earth Microbiome Project (sequencing of 16s and 18s).

Based on QIIME2 documentation and QIIME2 2018.11. This requires installation of QIIME2 and activation (source activate qiime2-2018.11)

PROJECT A
-Input Files: FASTQ data with the Casava 1.8 demultiplexed format, Paired end (2x300bp, Illumina MiSeq). Two files per sample.

Step 1: Create QIIME2 artifact from FASTQ files. Note that QIIME2 is able to import .fastq.gz files

Create manifest file (text file will do) and save it somewhere:

	sample-id,absolute-filepath,direction
	sample1,$PWD/dir/(sample-name-1)_16S_R1.fastq.gz,forward
	sample1,$PWD/dir/(sample-name-1)_16S_R2.fastq.gz,reverse
	....
	sampleN,$PWD/dir/(sample-name-N)_16S_R1.fastq.gz,forward
	sampleN,$PWD/dir/(sample-name-N)_16S_R2.fastq.gz,reverse

Import files:

	qiime tools import --type 'SampleData[PairedEndSequencesWithQuality]' --input-path (manifest-file) --output-path (filename).qza --input-format PairedEndFastqManifestPhred33

