# MicrobialAbundance

## install 

### **1. Download release**, pkgs , refs and test data

#### (1) release:

####  [MicrobialAbundance.zip](https://github.com/BioStaCs-public/MicrobialAbundance/archive/refs/heads/main.zip)

```shell
git clone https://github.com/BioStaCs-public/MicrobialAbundance.git
```

#### (2) pkgs:

####   [gatk-4.6.0.0.zip](https://github.com/broadinstitute/gatk/releases/download/4.6.0.0/gatk-4.6.0.0.zip)

```shell
#download pkgs
YOUR_PATH="/your_custom/"
mkdir -p "${YOUR_PATH}/pkgs"
cd "${YOUR_PATH}/pkgs"
wget https://github.com/broadinstitute/gatk/releases/download/4.6.0.0/gatk-4.6.0.0.zip
unzip gatk-4.6.0.0.zip
```

#### (3) refs and test data: 

https://pan.quark.cn/s/79002f0756ab?entry=webother#/list/share   password: 8T9J

### 2. create conda env

```shell
conda create -n java17
conda activate java17
conda install conda-forge::openjdk=17

conda create -n MicrobialAbundance
conda activate MicrobialAbundance
conda install bioconda::samtools=1.5
conda install bioconda::bwa=0.7.17
conda install bioconda::fastp=0.22.0
conda install python=3.9
conda install pyyaml tqdm pandas
```

### 3. ‌**Edit the configuration file**

#### (1)  ‌‌**Use the `conda env list` command**‌ to display the file paths of our Conda environments on your system.

Execute the following command to view all created virtual environments and their paths:

```shell
conda env list
# or
conda info --envs
```

Example output:

```shell
# conda environments:
base                  /home/user/anaconda3
myenv                 /home/user/anaconda3/envs/myenv
*MicrobialAbundance   ~/.conda/envs/MicrobialAbundance/
java17                ~/.conda/envs/java17/
```

The `*` symbol in the list marks the currently activated environment, and the path column shows the locations of all virtual environments‌

#### (2) **Configure in the following files**

**Configure1：MicrobialAbundance/MicrobialAbundancePipline/configures/MicrobialAbundance_pathes.yaml**

(Use absolute paths if you're not sure how Python handles relative paths.)

```yaml
# MicrobialAbundance_pathes.yaml
# Resource Path Configuration
path:
  # Host reference genomes
  host_fa_hg38: '/path/to/refs/GRCh38/GCA_000001405.15_GRCh38_no_alt_analysis_set.fa'
  host_img_hg38: '/path/to/refs/GRCh38/GCA_000001405.15_GRCh38_no_alt_analysis_set.fa.img'
  host_kmer_hg38: '/path/to/refs/GRCh38/GCA_000001405.15_GRCh38_no_alt_analysis_set.hss'

  host_fa_t2t: '/path/to/refs/t2t/chm13v2.0.fa'
  host_img_t2t: '/path/to/refs/t2t/chm13v2.0.fa.img'
  host_kmer_t2t: '/path/to/refs/t2t/chm13v2.0.hss'
  
  # Microbial database
  microbe_dict: '/path/to/refs/microbiome/BioStaCs_microbes.dict'
  microbe_img: '/path/to/refs/microbiome/BioStaCs_microbes.fa.img'
  taxonomy_file: '/path/to/refs/microbiome/BioStaCs_microbes.db'
  ncid2microbeNames_file: '/path/to/refs/microbiome/BioStaCs_microbeNames_sep=tab.csv'
  microbial_genome_length: '/path/to/refs/microbiome/BioStaCs_GenomeLength.csv'
  tax_id_hierarchy: '/path/to/refs/microbiome/BioStaCs_taxid_hierarchy.txt'
  catalog_genome: '/path/to/refs/microbiome/BioStaCs_catalog_genome.txt'
  
  # Tool paths, 
  java17: '~/.conda/envs/java17/bin/java'
  gatk_4.6_jar: '/your_custom/pkgs/gatk-4.6.0.0/gatk-package-4.6.0.0-local.jar'
  fastp: '~/.conda/envs/MicrobialAbundance/bin/fastp'
```

**Configure2：MicrobialAbundance/MicrobialAbundancePipline/configures/MicrobialAbundance_configure.yaml**

(Single-end data must meet the following requirements: the folder should be named after the sample (sample), and it should contain `sample.fq.gz`, with the suffix required to be `.fq.gz`. The file format should be `sample/sample.fq.gz`. For paired-end data, the file formats should be `sample/sample.R1.fq.gz` and `sample/sample.R2.fq.gz`, with the suffixes required to be `.R1.fq.gz` and `.R2.fq.gz`, respectively.)

```yaml
# MicrobialAbundance_configure.yaml
# Configuration Paths
path:
 tmp_dir : "<project_root>/tmp/"  # Temporary directory for processing files
 input_dir : "<project_root>/input_data/"  # Input data directory
 output_dir : "<project_root>/analysis_output/"  # Output directory

# Runtime Parameters
args:
 thread : 20  # CPU threads allocated per sample
 pool_size : 2  # Number of samples processed concurrently
 mem : "128G"  # Maximum memory allocation
 mem_perthread : "256M"  # Memory per thread in samtools sort

# Analysis Parameters
others:
 species : "human"  # Species selection (human/mouse)
 seq_type : "rna"  # Sequencing type (wgs/rna)
 pair : True  # Paired-end sequencing status
 QC : True  # Enable quality control steps
 host_sequences_removing : True  # Enable host sequence removal
 microbial_taxas_quantification : True  # Enable microbial taxonomy analysis
 match_length_threshold : 0.95  # Alignment length threshold
 score_threshold : 1  # Maximum allowed number of mismatched bases during alignment to the reference genome

# Sample List (Note: Numeric sample IDs require quotes)
samples:
- Sample_1
- Sample_2
- Sample_3
```

### **4. Run pipline on test data**

```shell
YOUR_PATH="/your_custom/"
cd "${YOUR_PATH}/MicrobialAbundance/MicrobialAbundancePipline"
python MicrobialAbundance_start.py
# or
python MicrobialAbundance_start.py -c ./configures/MicrobialAbundance_configure.yaml
```

**Runtime logs can be viewed in the command-line terminal and are backed up in `MicrobialAbundance/MicrobialAbundancePipline/log`**`MicrobialAbundance/MicrobialAbundancePipline/log`
