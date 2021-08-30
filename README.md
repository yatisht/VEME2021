# VEME2021

## Install UShER via conda
* Setup the channels
```
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

* Install UShER package
```
conda install usher
```

* Update the package 
```
conda update usher
```

## Download UShER demo files
```
wget https://raw.githubusercontent.com/russcd/USHER_DEMO/master/example_files.zip
unzip example_files.zip
```

## Convert FASTA files to VCF
* Download the reference file
```
wget https://hgdownload.soe.ucsc.edu/goldenPath/wuhCor1/bigZips/wuhCor1.fa.gz
gunzip wuhCor1.fa.gz
```

* Create an MSA of all sequences (including the reference)
```
mafft --thread 4 --auto --keeplength --addfragments example1_typical_use_case.fasta wuhCor1.fa > example_1_msa.fa
```

* Mask problematic sites
```
wget https://raw.githubusercontent.com/W-L/ProblematicSites_SARS-CoV2/master/problematic_sites_sarsCov2.vcf
```

* Generate VCF from MSA
```
faToVcf -maskSites=problematic_sites_sarsCov2.vcf example_1_msa.fa example_1.vcf
```

## Place VCF sequences onto a comprehensive SARS-CoV-2 phylogenetic tree made up of public (GenBank, COG-UK and CNCB) sequences
* Download the comprehensive phylogenetic tree (dated 2021-08-25 in the example below and the subsequent commands)
```
wget http://hgdownload.soe.ucsc.edu/goldenPath/wuhCor1/UShER_SARS-CoV-2/2021/08/25/public-2021-08-25.all.masked.nextclade.pangolin.pb.gz
```

* Place the sequences on the comprhensive global tree using UShER
```
usher -i public-2021-08-25.all.masked.nextclade.pangolin.pb.gz -v example_1.vcf -o example_1_placed.pb -T 4 -k 50 -u -D
```
Files generated to view: `uncondensed-final-tree.nh`, `mutation-paths.txt`, `clades.txt`, `subtree-1.nh`, `subtree-1-mutations.txt`, `example_1_placed.pb`, etc. 

# Tutorials

## Summarize the mutation-annotate tree

* Basic summary
```
matUtils summary -i public-2021-08-25.all.masked.nextclade.pangolin.pb.gz -T 4
matUtils summary -i example_1_placed.pb -T 4
```

* Detailed summary
```
matUtils summary -i public-2021-08-25.all.masked.nextclade.pangolin.pb.gz -T 4 -A -d summary_out/
```
Files to view under the `summary_out` folder.

## Extracting subtrees to view in Auspice

* Getting amino acid translation of mutations
```
wget http://hgdownload.soe.ucsc.edu/goldenPath/wuhCor1/bigZips/genes/ncbiGenes.gtf.gz
gunzip ncbiGenes.gtf.gz
matUtils summary -i public-2021-08-25.all.masked.nextclade.pangolin.pb.gz -T 4 -g ncbiGenes.gtf -f wuhCor1.fa -t coding_mutations.tsv
```

