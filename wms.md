# Whole Metagenome sequencing

In this tutorial you'll analyze a sample from Pig Gut Metagenome.

> Pig is a main species for livestock and biomedicine. The pig genome sequence was recently reported. To boost research, we established a catalogue of the genes of the gut microbiome based on faecal samples of 287 pigs from France, Denmark and China. More than 7.6 million non-redundant genes representing 719 metagenomic species were identified by deep metagenome sequencing, highlighting more similarities with the human than with the mouse catalogue. The pig and human catalogues share only 12.6 and 9.3 % of their genes, respectively, but 70 and 95% of their functional pathways. The pig gut microbiota is influenced by gender, age and breed. Analysis of the prevalence of antibiotics resistance genes (ARGs) reflected antibiotics supplementation in each farm system, and revealed that non-antibiotics-fed animals still harbour ARGs. The pig catalogue creates a resource for whole metagenomics-based studies, highly valuable for research in biomedicine and for sustainable knowledge-based pig farming

To speed up the analysis, we'll only use the first 60K reads from the first sample of the study. The full samples are accessible under BioProject [PRJEB11755](http://www.ncbi.nlm.nih.gov/bioproject/308698)

### getting the data and checking their quality

If you are reading this tutorial online and haven't cloned the directory, first download and unzip the data:

```
wget https://github.com/HadrienG/tutorials/blob/master/data/DHN_Pig_60K.fastq.gz
gzip -d DHN_Pig_60K.fastq.gz
```

We'll use FastQC to check the quality of our data. FastQC can be downloaded and
ran on a Windows or LINUX computer without installation. It is available [here](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/)

Start FastQC and select the fastq file you just downloaded with `file -> open`
What do you think about the quality of the reads? Do they need trimming? Is there still adapters
present?  Overrepresented sequences?

If the quality appears to be that good, it's because it was the cleaned reads that were deposited in SRA.
We can directly move to the classification step.

### Taxonomic classification

[Kraken](https://ccb.jhu.edu/software/kraken/) is a system for assigning taxonomic labels to short DNA sequences (i.e. reads)  
Kraken aims to achieve high sensitivity and high speed by utilizing exact alignments of k-mers and a novel classification algorithm (sic).

In short, kraken uses a new approach with exact k-mer matching to assign taxonomy to short reads. It is *extremely* fast compared to traditional
approaches (i.e. Blast)

By default, the authors of kraken built their database based on RefSeq Bacteria, Archea and Viruses. We'll use it for the purpose of this tutorial.

```
wget https://ccb.jhu.edu/software/kraken/dl/minikraken.tgz
tar xzf minikraken.tgz
```

Now run kraken on the reads

`kraken --db minikraken_20141208/ --threads 8 --fastq-input DHN_Pig_60K.fastq > DHN_Pig.tab`

which produces a tab-delimited file with an assigned TaxID for each read. Kraken includes a script called `kraken-report` to transform
this file into a "tree" view with the percentage of reads assigned to each taxa.

`kraken-report --db minikraken_20141208/ DHN_Pig.tab > DHN_Pig_tax.txt`

Open this file and take a look!

### Visualization

We'll visualize the composition of our datasets using Krona.

Get the script to transform the kraken results in a format Krona can understand

```
wget https://raw.githubusercontent.com/norling/metlab/master/pipeline_scripts/kraken_to_krona.py
chmod 755 kraken_to_krona.py
```

Run the script and Krona

```
./kraken_to_krona.py kraken.txt
ktImportText kraken.krona.in
```

And open the generated html file in your browser!