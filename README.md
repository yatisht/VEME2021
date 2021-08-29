# VEME2021

## Install UShER via conda
```
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge

conda install usher
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

