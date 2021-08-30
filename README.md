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

## Summarize the mutation-annotate tree (MAT)

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

* Getting amino acid translation of mutations
```
wget http://hgdownload.soe.ucsc.edu/goldenPath/wuhCor1/bigZips/genes/ncbiGenes.gtf.gz
gunzip ncbiGenes.gtf.gz
matUtils summary -i public-2021-08-25.all.masked.nextclade.pangolin.pb.gz -T 4 -g ncbiGenes.gtf -f wuhCor1.fa -t coding_mutations.tsv
```

## Extracting subtrees from a MAT to view in Auspice/Newick viewers
* Store the sequence names of the samples of interest in a new file (`samples_of_interest.txt` in the example below) 
```
grep ">" example1_typical_use_case.fasta | cut -d">" -f2 > samples_of_interest.txt 
```

* Extract subtree of samples provided as a Newick file (`samples_of_interest.nh` in the example below)
```
matUtils extract -i example_1_placed.pb -T 4 -s samples_of_interest.txt -t samples_of_interest.nh 
```

* Extract subtree of samples provided as a JSON file (`samples_of_interest.json` in the example below)
```
matUtils extract -i example_1_placed.pb -T 4 -s samples_of_interest.txt -j samples_of_interest.json 
```

* Extract subtree(s) of size 50 in the context of the samples provided and output the JSON files 
```
matUtils extract -i example_1_placed.pb -T 4 -K samples_of_interest.txt:50  
```

* Extract the clade corresponding to clade AY.2 and  output the Newick and JSON files 
```
matUtils extract -i example_1_placed.pb -T 4 -c "AY.2" -t AY2.nh -j AY2.json -o AY2.pb
```

## Converting a MAT to be viewable in Taxodium
* Download metadata files for the comprehensive tree
```
wget https://hgdownload.soe.ucsc.edu/goldenPath/wuhCor1/UShER_SARS-CoV-2/2021/08/25/public-2021-08-25.metadata.tsv.gz
gunzip public-2021-08-25.metadata.tsv.gz
```

* Use the metadata to convert a MAT to taxodium pb file
```
matUtils extract -i public-2021-08-25.all.masked.nextclade.pangolin.pb.gz -l taxodium_public-2021-08-25.pb -M public-2021-08-25.metadata.tsv -g ncbiGenes.gtf -f wuhCor1.fa 
```

## Optimize a MAT for parsimony
```
matOptimize -i AY2.pb -T4 -o AY2_optimized.pb
```

* To view the optimized MAT in Auspice 
```
matUtils extract -i AY2_optimized.pb -T 4 -j AY2_optimized.json 
```

## Check for recombination ancestry in the samples of interest
```
ripples -i example_1_placed.pb -T 4 -s samples_of_interest.txt 
```
