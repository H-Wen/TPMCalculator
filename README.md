TPMCalculator
=============

[![install with bioconda](https://img.shields.io/badge/install%20with-bioconda-brightgreen.svg?style=flat)](http://bioconda.github.io/recipes/tpmcalculator/README.html) [![Anaconda-Server Badge](https://anaconda.org/bioconda/tpmcalculator/badges/downloads.svg)](https://anaconda.org/bioconda/tpmcalculator) [![Anaconda-Server Badge](https://anaconda.org/bioconda/tpmcalculator/badges/license.svg)](https://anaconda.org/bioconda/tpmcalculator)

TPMCalculator quantifies mRNA abundance directly from the alignments by parsing BAM files. 
The input parameters are the same GTF files used to generate the alignments, and one or 
multiple input BAM file(s) containing either single-end or paired-end sequencing reads. 
The TPMCalculator output is comprised of four files per sample reporting the TPM values 
and raw read counts for genes, transcripts, exons and introns respectively.

## Reference

 * Roberto Vera Alvarez, Lorinc Sandor Pongor, Leonardo Mariño-Ramírez, David Landsman; TPMCalculator: one-step software to quantify mRNA abundance of genomic features, Bioinformatics, , bty896, https://doi.org/10.1093/bioinformatics/bty896
 
## Conda/Bioconda

TPMCalculator is available on Bioconda: https://bioconda.github.io/recipes/tpmcalculator/README.html

## NIH Biowulf

NIH Biowulf users can load TPMcalculator as a module: https://hpc.nih.gov/apps/TPMCalculator.html    

## Requirements

### BAMTools

Clone the BAMTools repository from GitHub: https://github.com/pezmaster31/bamtools

Compile it on this way and set the environment variables for TPMCalculator:

    cd bamtools
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=../ ..
    make
    make install
    cd ..
    export BAMTOOLS_DIR=`pwd`
    export CPPFLAGS="-I $BAMTOOLS_DIR/include/bamtools/"
    export LDFLAGS="-L $BAMTOOLS_DIR/lib64 -Wl,-rpath,$BAMTOOLS_DIR/lib64"

That's it. BAMTools was compiled and the env variables were set for compiling
TPMCalculator.

## Installation

After the installation of BAMTools go to the TPMCalculator folder and do make:

    make

A bin folder will be created with the TPMCalculator executable.

## Docker

Use provided [Dockerfile](https://raw.githubusercontent.com/ncbi/TPMCalculator/master/Dockerfile) 
based on the [BioContainers](https://biocontainers.pro/) base image.

    docker build -t biocontainers/tpmcalculator:0.0.1 https://raw.githubusercontent.com/ncbi/TPMCalculator/master/Dockerfile

    docker run -v /path_to_data:/data --user=yourUID:your:GID biocontainers/tpmcalculator:0.0.1 TPMCalculator -g /data/path_to_GTF/genes.gtf -b /data/path_to_bam/sample1.bam

## [CWL](https://github.com/common-workflow-language)

A CWL tool definition is also provided [tpmcalculator.cwl](https://raw.githubusercontent.com/ncbi/TPMCalculator/master/tpmcalculator.cwl)

Use it like this:

    cwl-runner tpmcalculator.cwl --out_stderr=test.stderr --out_stdout=test.stdout -g genes.gtf -b sample_1.bam

## Usage

Usage: ./bin/TPMCalculator -g GTF_file [-d BAM_files_directory|-b BAM_file] 

    ./bin/TPMCalculator options:

        -v    Print info
        -h    Display this usage information.
        -g    GTF file
        -d    Directory with the BAM files
        -b    BAM file
        -k    Gene key to use from GTF file. Default: gene_id
        -t    Transcript key to use from GTF file. Default: transcript_id
        -c    Smaller size allowed for an intron created for genes. Default: 16. We recommend to use the reads length
        -p    Use only properly paired reads. Default: No. Recommended for paired-end reads.
        -q    Minimum MAPQ value to filter out reads. Default: 0. This value depends on the aligner MAPQ value.
        -o    Minimum overlap between a reads and a feature. Default: 8.
        -e    Extended output. This will include transcript level TPM values. Default: No.
        -a    Print out all features with read counts equal to zero. Default: No.

## Description

The model to describe the genomic features used for a gene is created from the GTF provided 
by the user. TPMCalculator performs two transformations which are executed on the genomic 
coordinates generating regions for the genes that include the exons and “pure” intron 
regions as shown in Figure S1. The first transformation creates overlapped exons for 
all alternative spliced forms of the genes. A single gene model is generated with unique 
exons and introns which includes the sequence of all exonic regions. The second transformation 
process creates a list of pure intron regions that replace those generated by the first 
transformation. We should indicate that only the intron regions are modified to generate 
regions not overlapped by exons of other genes. Reporting TPM values for these unique 
introns allows further identification of alternative splicing events like intron retention.
Additionally, a set of non-overlapped gene features (exons and introns) are generated and 
used for TPM calculation.

![Gene model](https://github.com/ncbi/TPMCalculator/raw/master/doc/Gene_model.png)

## Validation

* [UCSC hg19](https://github.com/ncbi/TPMCalculator/wiki/Validation#validation-using-ucsc-hg19-genome-annotation)
* [Gencode v25](https://github.com/ncbi/TPMCalculator/wiki/Validation#validation-using-gencode-v25-genome-annotation)

For more detailed description and instalation guide lines see https://github.com/ncbi/TPMCalculator/wiki/

## Credits

Roberto Vera Alvarez
Email: veraalva@ncbi.nlm.nih.gov

Lorinc Pongor
Email: pongorlorinc@gmail.com

Leonardo Mariño-Ramírez
Email: marino@ncbi.nlm.nih.gov

David Landsman
Email: landsman@ncbi.nlm.nih.gov

# Public Domain notice

## National Center for Biotechnology Information.

This software is a "United States Government Work" under the terms of the United States
Copyright Act. It was written as part of the authors' official duties as United States
Government employees and thus cannot be copyrighted. This software is freely available
to the public for use. The National Library of Medicine and the U.S. Government have not
placed any restriction on its use or reproduction.

Although all reasonable efforts have been taken to ensure the accuracy and reliability
of the software and data, the NLM and the U.S. Government do not and cannot warrant the
performance or results that may be obtained by using this software or data. The NLM and
the U.S. Government disclaim all warranties, express or implied, including warranties
of performance, merchantability or fitness for any particular purpose.

Please cite NCBI in any work or product based on this material.

