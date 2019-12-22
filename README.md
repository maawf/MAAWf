#Introduction

MAAWf is a fully automated analytics workflow platform for microbiota based on web technology that makes it easy for clinical and basic medical workers to analyze the composition and functionality of microbiomes in environments and visualize visual results. MAAWf is available by visiting the website www.maawf.com.

MAAWf's key functions include analysis of species composition, gene abundance, metabolic pathway abundance, pathway coverage, ARGs analysis, and CAZy analysis of WMS data; OTU clustering, diversity, intergroup difference analysis, macrogenomic functional gene prediction, metabolic pathways and other functional analysis of 16S data. MAAWf supports online interaction and local deployment.

##Online analysis

Web-based MAAWf tool is now available at http://www.maawf.com.


##Local analysis

For local deployment, please try to download source code and files (Linux):

wget http://www.maawf.com/maawf.tar.gz


##Local deployment of MAAWf
v2019.12.6

###Environment and Setup
MAAWf was developed in the Linux (Ubuntu 16.04) environment and can run on a Docker Linux systems and can be compiled using code such as R, Python, Shell, PHP, etc. MAAWf  has no special requirements for hardware architecture, but it is recommended to use a high-capacity memory server to run to improve computing performance. Because MAAWf's workflows are run as Docker containers, we highly recommend using Docker to deploy MAAWf locally, while MAAWf supports local visual interface installation and normal command-line installation, following detailed installation instructions based on the Ubuntu system. 

1.	To start with, install Docker first
//update system
$apt-get update
//setup Docker
$apt-get install -y docker.io
//Run Docker service
$service docker start

2.	Download MAAWf source code
$wget http://www.maawf.com/maawf.tar.gz
$tar xzf maawf.tar.gz
$mkdir -p /home/myspace/username
#Then, move all files under the ~/maawf folder to /home/myspace/username and you can see the example file structure as follows:
username
├── 16s_pipeline
├── ARGs-OAP
├── html
└── metagenome_pipeline

3.	Down load all supporting containers
$docker pull suntoa/16s_pipeline
$docker pull suntoa/16s_pipeline_picrust 
$docker pull suntoa/16s_pipeline_lefse 
$docker pull suntoa/megahit
$docker pull suntoa/prokka
$docker pull suntoa/EMBOSS
$docker pull suntoa/cd-hit
$docker pull suntoa/salmon
$docker pull suntoa/r3.6.1
$docker pull suntoa/metagenome_pipeline

4.	Setup all supporting containers
$docker run -dit --name="16s_pipeline" -v /home/myspace/username/:/suntoa suntoa/16s_pipeline
$docker run -dit --name="16s_pipeline_picrust" -v /home/myspace/username/:/suntoa $suntoa/16s_pipeline_picrust docker run -dit --name="16s_pipeline_lefse" -v /home/myspace/username/:/suntoa suntoa/16s_pipeline_lefse
$docker run -dit --name=" megahit " -v /home/myspace/username/:/suntoa suntoa/megahit
$docker run -dit --name=" prokka " -v /home/myspace/username/:/suntoa suntoa/prokka
$docker run -dit --name=" emboss" -v /home/myspace/username/:/suntoa suntoa/emboss
$docker run -dit --name=" cd-hit " -v /home/myspace/username/:/suntoa suntoa/cd-hits
$docker run -dit --name=" salmon " -v /home/myspace/username/:/suntoa suntoa/salmon
$docker run -dit --name=" r3.6.1" -v /home/myspace/username/:/suntoa suntoa/r3.6.1
$docker run -dit --name="metaphlan_pipeline" -v /home/myspace/username/:/suntoa suntoa/metagenome_pipeline

5.	Install php5.6 and Apache2 on the Ubuntu host
$sudo apt-get install software-properties-common
$sudo add-apt-repository ppa:ondrej/php 
$sudo apt-get update
$sudo apt-get -y install php5.6 php5.6-mcrypt php5.6-mbstring php5.6-curl php5.6-cli php5.6- mysql php5.6-gd php5.6-intl php5.6-xsl php5.6-zip

6.	Modifying the root of apache's website
$cd /etc/apache2/sites-available vim 000-default.conf
Change the DocumentRoot directory to the directory where the html is located, such as /home/myspace/username/html, and restart apache
$sudo apache2ctl -k restart

7.	Create Docker user groups and give users of the www-data user who perform php Docker permissions
#Creat a new docker  group
$sudo groupadd docker
#www-data  add docker  group
$sudo usermod -aG docker www-data
#Restart docker  service
$sudo systemctl restart docker
#Set www-data user Docker  privilege
$sudo /etc/sudoers

8.	Modify the PHP upload file size limit
In order to analyze big data, you need to remove the limit on the size of uploaded files. Make the same changes to /etc/php/5.6/apache2/php.ini and /etc/php/5.6/cli/php.ini 
$vim /etc/php/5.6/apache2/php.ini 
$vim /etc/php/5.6/cli/php.ini
The modifications are as follows, and the specific limit parameters can be adjusted as user’s preference:
max_execution_time = 600
max_input_time = 600 
memory_limit = 32m 
file_uploads = on 
upload_tmp_dir = /tmp 
upload_max_filesize = 8000m 
post_max_size = 8000m
//Restart apache
$sudo apache2ctl -k restart

9.	Command line instructions
9.1      WMS command line
wms_process.sh 
The script executes the WMS pipeline, and outputs the classification profile, functional annotation, ARGs annotation, ARGs abundance maps, and PCoA visual analysis results, CAZy abundance matrix, and Cazy clustering heat map.
wms_process.sh --arg[1] --arg[2] --arg[3] --arg[4] --arg[5] --arg[6] --arg[7] --arg[8]

--arg[1]  WMS fastq data folder
--arg[2]  Trimmomatic parameters: sliding window size [Default=4]
--arg[3]  Average nucleotie length threshold [Default=20]
--arg[4]  A minimum read length threshold [Default=50]
--arg[5]  The taxonomic level to display: 'a' : all taxonomic levels; 'k' : kingdoms (Bacteria and Archaea) only; 'p': phyla only; 'c' : classes only; 'o' : orders only; 'f': families only; 'g' : genera only; 's' : species only; [Default='s']
--arg[6]   Blastx aligned length truncation value [Default=25]
--arg[7]   Blastx aligned evalue truncation [Default=1e-07]
--arg[8]   Blastx aligned identity value [Default=80]


9.2   16SrRNA command line
16s_process_map_validate.sh
This script checks map.txt for errors.
16s_process.sh
The script executes 16S pipeline, including preprocessing of sequences, OTU clustering, Alpha and Beta diversity analysis, LEfSe analysis, volcano map and functional annotation between samples.
9.2.1 16s_process_map_validate.sh --arg[1]
--arg[1]  map.txt folder
9.2.2 16s_process.sh --arg[1] --arg[2] --arg[3] --arg[4] --arg[5] --arg[6] --arg[7] --arg[8] --arg[9] --arg[10] --arg[11] --arg[12] --arg[13] --arg[14] --arg[15] --arg[16] --arg[17] --arg[18] --arg[19] --arg[20] --arg[21] --arg[22] --arg[23] --arg[24] --arg[25] --arg[26] --arg[27] 

--arg[1]  16s_data folder
--arg[2]  Minimum allowable overlapping base pairs [advise: 8]
--arg[3]  Maximum allowable difference in overlapping area% [advise: 10]
--arg[4]  Maximum unacceptable Phred Mass Score [advise: 19]
--arg[5]  Reference database: Silva119 and Greengene, includinig SILVA119 and Greengene (version 13_5) [Default= Greengene]
--arg[6]  The taxonomic level for OTU abundance bar chart : 2: phylum levels; 3: class level; 4: order level; 5: family level; 6: genus level [Default=6]
--arg[7]  16s data input format: raw_data: 16s original sequencing double-end fastq data; otu_txt: OTU table in .txt format generated by QIIME; otu_biom: OTU table in .biom format generated by QIIME
--arg[8]  Alpha diversity indicators, including Shannon ,Simpson, Inverse Simpson, Chao1, ACE, all [Default=all]
--arg[9]  Beta diversity distance matrix algorithm, including manhattan, euclidean, canberra, clark, bray, kulczynski, jaccard, gower, altGower, morisita, horn, mountford, raup, binomial, chao, cao, mahalanobis [Default=bray]
--arg[10]    The sum of each sample value is normalized to 1M: Yes,No[Default=Yes]
--arg[11]    Alpha value of Kruskal-Wallis test between classes [Default=0.05]
--arg[12]    Alpha value of Wilcoxon test paired between two subclasses [Default=0.05]
--arg[13]    LDA score threshold [Default= 4]
--arg[14]    LEfSe result output format: png, pdf, svg[Default=png]
--arg[15]    Set the dpi resolution of the LEfSe result output [Default=300]
--arg[16]    Phylogenetic graph output format: png,pdf,svg[Default= png]
--arg[17]    Set the dpi resolution of the phylogenetic graph output [Default= 300]
--arg[18]	Output format: png, pdf [Default=png]
--arg[19]   Annotation method: 1, 2. 1 for PICRUSt, 2 for Tax4Fun [Default=1]
--arg[20]   GreenGenes version of standardized copy number: 13_5, 18may2012 [Default=13_5]
--arg[21]   GreenGenes version predicting metagenomics: 13_5 , 18may2012 [Default=13_5]
--arg[22]   Whether to get the NSTI value of each sample: Yes,No [Default=Yes]
--arg[23]   Types of functional predictions: ko,cog,rfam [Default=ko]
--arg[24]   KEGG pathway hierarchy: 1,2,3 [Default=2]
--arg[25]   Type of PICRUSt output: biom, txt [Default=biom]
--arg[26]   Whether to predict the abundance of functional genes: Yes, No [Default=Yes] 
--arg[27] Output format of Wilcoxon rank sum test for pathway abundance for the second-level KEGG metabolic pathway: png, pdf [Default=png]

10   Instructions for using local command line cases
10.1 WMS workflow case
//Prepare WMS raw data
$cd /home/myspace/username/html/Microbiome_analysis_website/metagenome_upload/
$mkdir metagenome

The input files should be packed in a .zip format that consist of all metagenomic sequences, and two meta-data i.e., args_meta_data.txt and cazy_meta_data.txt. Pair-ended fastq files must be named after samplename_1.fq and samplename_2.fq for forward and reverse reads, respectively:

metagenome
├── A1-T1_1.fq
├── A1-T1_2.fq
├── B1-T1_1.fq
├── B1-T1_2.fq
├── args_meta_data.txt
└── cazy_meta_data.txt

args_meta_data.txt consists of three columns: sample ID, Name, Category. Sample ID is the unique sample number starts from 1 to N. Name is the sample name, which must be the same as the uploaded file name. Category is the grouping of samples. 
SampleID	Name	Category
1	A1-T1	A
2	B1-T1	B

cazy_meta_data.txt consists of two columns: #SampleID and GroupID. #SampleID is the unique sample name, and must be the same as the uploaded file name. GroupID is the grouping of samples.
#SampleID	GroupID
A1-T1	A
B1-T1	B

$cd /home/myspace/ username /html/process_shell

10.1.1 WMS pipeline (arg[1]~[8])
$sh wms_process.sh metagenome 4 20 50 ‘s’ 25 1e-07 80	
//Please note that ’/’ is not required before the file folder metagenome.


10.2 16S Workflow Case
//Prepare 16S raw data
$cd /home/myspace/ username /html/Microbiome_analysis_website/16s_upload/
$mkdir 16s

MAAWf supports 16S rDNA data in three formats: sequence, OTU (.txt), OTU (.biom). For sequence, the input files should be packed in a .zip format that containin 16S rDNA sequences and a mapping file named "map.txt". Paired-end fastq files must be named after samplename_1.fastq and samplename_2.fastq, and the file structure is shown below: 
16s
├── 16s_data
│   ├── A1T1_1.fastq
│   ├── A1T1_2.fastq
│   ├── B1T1_1.fastq
│   └── B1T1_2.fastq
└── map.txt
For OTU(.txt), the input named “otu_table.txt” must be an OTU file in txt format generated by QIIME and a mapping file named "map.txt". The file structure is shown below:
16s
├── otu_table.txt
└── map.txt
For OTU(.biom), The input named “otu_table.biom” must be an OTU file in biom format generated by QIIME and a mapping file named "map.txt". The file structure is shown below:
16s
├── otu_table.biom
└── map.txt

map.txt consists of five columns: #SampleID, BarcodeSequence, LinkerPrimerSequence, SampleType and Description. #SampleID is the sample name, which must be the same as the uploaded file name. BarcodeSequence consists of barcoded sequences for each sample. LinkerPrimerSequence is a common primer for sequencing. SamplyeType is the grouping of samples. The Description is any information relevant to the sample, but each sample descpription must be unique. The sample is as follows:
#SampleID	BarcodeSequence	LinkerPrimerSequence	SampleType	Description
A1T1	TACGCTGCTATCCTCT	TCGTATCCTCT...WGCAG	tumor	A1T1
B1T1	ATGCGCAGTATCCTCT	TCGTATCCTCT...WGCAG	normal	B1T1

$cd /home/myspace/username/html/process_shell

10.2.1 Verify that the map file is correct
$sh 16s_process_map_validate.sh  16s  //note: 16s folder does not need to add ’/’

10.2.2 16S pipeline (arg[1]~[27])
$bash 16s_process.sh 16s 8 10 19 Greengene 6 raw_data all bray Yes 0.05 0.05 4 png 300 png 300 png 1 13_5 13_5 Yes ko 2 biom Yes png
//Please note that ’/’ is not required before the file folder 16s.

###Local use of MAAWf
Open a browser (Firefox, Google Chrome, or Internet Explorer) and type “localhost” to startup the MAAWf graphical interface.




