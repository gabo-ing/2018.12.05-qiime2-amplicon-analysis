Amplicon analysis using QIIME2

This file is a backup of the commands/pipeline used to analyse soil microbiomes. This project is based on sequencing using primers 341F-806R (16s) and ITS V3-V4.

Based on QIIME2 documentation and QIIME2 2018.11. This requires installation of QIIME2 and activation (source activate qiime2-2018.11)

Common to both 16s and ITS V3-V4.

Input Files: FASTQ data with the Casava 1.8 demultiplexed format, Paired end (2x300bp, Illumina MiSeq). Two files per sample.

Step 1: Create QIIME2 artifact from FASTQ files (16s). Same command was used for ITS (just changing filenames). Note that QIIME2 is able to import .fastq.gz files

Create manifest file (text file will do) and save it somewhere:

	sample-id,absolute-filepath,direction
	sample1,$PWD/dir/(sample-name-1)_16S_R1.fastq.gz,forward
	sample1,$PWD/dir/(sample-name-1)_16S_R2.fastq.gz,reverse
	....
	sampleN,$PWD/dir/(sample-name-N)_16S_R1.fastq.gz,forward
	sampleN,$PWD/dir/(sample-name-N)_16S_R2.fastq.gz,reverse

Import files:

	qiime tools import --type 'SampleData[PairedEndSequencesWithQuality]' --input-path (manifest-file) --output-path (filename).qza --input-format PairedEndFastqManifestPhred33

Step 2: Remove 16s primers by trimming, using primer length as reference (Forward 17bp, reverse 21bp). In this case, DADA2 is used. Trunc parameter was set at 240bp. Optional: number of threads

	qiime dada2 denoise-paired --i-demultiplexed-seqs (filename).qza --p-trim-left-f 17 --p-trim-left-r 21 --o-table table-(filename).qza --o-representative-sequences represent-(filename).qza --o-denoising-stats denoising-stats-(filename).qza --p-trunc-len-f 240 --p-trunc-len-r 240 --p-n-threads 6

Step 3: Select reference database for taxonomic analysis and download suitable classifiers. In this case, I used: Silva 132 QIIME-compatible release from https://forum.qiime2.org/t/silva-132-classifiers/3698

Step 4: Perform taxonomic analysis and visualize barplots*

	qiime feature-classifier classify-sklearn --i-classifier classifier.qza --i-reads represent-(filename).qza --o-classification taxonomy.qza
	
	qiime metadata tabulate --m-input-file taxonomy.qza --o-visualization taxonomy.qzv
	
	qiime taxa barplot --i-table table-(filename).qza --i-taxonomy taxonomy.qza --m-metadata-file metadata.tsv --o-visualization taxa-bar-plots.qzv
	
* To be added: creation of custom classifiers and circumventing QIIME2 issue with space trailing at taxonomy file.

Step 5: Perform Heatmap Analysis

	optional: collapse table to taxonomic level
	
	qiime taxa collapse --i-table table.qza --i-taxonomy taxonomy.qza --p-level (1 to 7) --o-collapsed-table table-collapsed.qza
	
	qiime feature-table heatmap --i-table taxonomy-collapsed.qza --m-metadata-file (metadata).tsv --m-metadata-column Samplegroup --o-visualization heatmap-euclidean.qzv --p-metric euclidean
	
	* distance is optional, I used euclidean
