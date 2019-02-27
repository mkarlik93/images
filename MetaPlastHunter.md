

MetaPlastHunter has two well established pipelines:

•	Full pipeline which contains: Preprocessing (decomplexation, filtering out ribosomal operon reads), DNA sequence mapping (using external mapper bbmap), Taxonomic assignment (MetaPlastHunter core algorithm)

•	Partial pipeline which takes SAM file as an input for taxonomic assignment.


#### Example usage of basic pipeline:

``` bash
MetaPlastHunter --in_1 reads_1.fq  --in_2 reads_2.fq --output xy –settings /home/xy/setttings_metaplasthunter.yaml -C
```


### Installation


#### Necessary dependencies

##### Python 2.7

[networkx](https://networkx.github.io)

[ete3](http://etetoolkit.org)

[pandas](https://pandas.pydata.org)

[pysam](https://pysam.readthedocs.io/en/latest/)


#### Binaries

[bbtools](https://jgi.doe.gov/data-and-tools/bbtools/) that contains bbduk, bbmap, pileup, randomreads programs


To install MetaPlastHunter downloaded from github:

```bash
git clone https://github.com/mkarlik93/MetaPlastHunter.git
```

```bash
cd MetaPlastHunter
```

```bash
python setup.py install
```

### Before the first use


To make it easier whole global settings were put into settings file in yaml format which it’s template you can find here (link).  Please note that your file has to have the same structure as the example file presented below. All mentioned files such as plastid reference database and SILVA ribosomal operon database are ready to download on Figshare and weekly updated.

<pre>
#################################
##     MetaPlastHunter         ##
##      settings file          ##
## Please add full paths below ##
#################################

###### Software ######

Software dependencies:


  bbduk.sh: /opt/bbmap/
  bbmap.sh: /opt/bbmap/
  pileup.sh: /opt/bbmap/
  randomreads.sh: /opt/bbmap/

#### Databases and mapping files ######

Databases and mapping files:

  bbmap_base: /home/karlicki reference_database_no_masked_1209_new.fasta
  seqid2taxid.map: /home/karlicki/seqid2taxid.map
  silva: /home/karlicki/ribosomal_db.fa
  empirical_treshold: /home/karlicki/metaplasthunter_empirical_tresholds.txt

#### Params ####
Params:

  min_number_of_reads: 50
  percentile_treshold: 1
  min_bin_coverage: 0
  bincov4_report: 0
  lca_treshold: 0.00001
  static_coverage_treshold[%]: 1
  min_identity: 0.7
  bincov_len: 100
</pre>


#### Parameter explanation


<pre>
min_number_of_reads -> Minimum number of reads for consider taxa
min_bin_coverage -> Absolute value necessary for calculating %COV threshold
bincov4_report: 0
lca_treshold -> Minimal percent of all assigned reads for taxa report
static_coverage_treshold[%] -> Discard reference genome below this threshold
min_identity -> Minimum identity of reads during mapping step
bincov_len -> bin coverage length it should be average length of read
</pre>

#### NCBI taxonomy

Before the first use [ete3](http://etetoolkit.org) package will download NCBI taxonomy into in your working directory.



#### Building custom database

We strongly believe that made assumptions also can work well for small circular genomes such as mitochondrial.

In case of adding new references to your genome please do it according to following steps:

1.	Add file to the reference database file. Fasta header has to contain only one part such as identity number

2.	Update seqidmap file by adding taxid (you can find it [here](https://www.ncbi.nlm.nih.gov/taxonomy)) and name of the sequence

3.	Run MetaPlastHunter using "--update" option to new record to empirical threshold file

4.	MetaPlastHunter is ready to use!




#### Classic pipeline (all in)




#### External mapper (Starting from SAM file)


MetaPlastHunter also supports usage of external mapper such as [bowtie2](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3322381/), [minimap2](https://github.com/lh3/minimap2). Please note that MetaPlastHunter strongly relies on absolute coverage calculation (absolute completeness) which is calculated after ribosomal operon filtration. So do prefiltration is mandatory.  Please follow presented protocol:

1. Filtration of ribosomal reads - for this crucial step you have two options: ultrafast (exact-kmer matching strategy more info you can find [here](http://seqanswers.com/forums/showthread.php?t=58221)), and normal (classic mapping)


**Acceleration k-mer matching strategy**

compressed_ribosomal_db.fa.gz you will find on Figshare

```bash

bbduk.sh in1=example_1.fastq in2=example_2.fastq outm1=ribo_example_1.fq outm2=ribo_example_2.fq outu1=example_for_bowtie_1.fq  outu2=example_for_bowtie_2.fq  k=31 ref=../compressed_ribosomal_db.fa.gz

```

**Classic mapping approach**


```bash

bbmap.sh in1=example_1.fastq in2=example_2.fastq outm1=ribo_example_1.fq outm2=ribo_example_2.fq outu1=example_for_bowtie_1.fq  outu2=example_for_bowtie_2.fq  k=31 ref=../SILVA_db


```


SILVA_db you will find on Figshare


2. Mapping -> In this step please follow instruction of the mapper. Remember that you need prepare database using provided plastid genomes. Make sure that you have reference base compatible with seqidmap and empirical treshold file.

For example using bowtie2:




3. Use MetaPlastHunter

```bash

MetaPlastHunter -A --in_1 mapped.sam --settings full_path_of_settings_file --output example --threads XX


```
