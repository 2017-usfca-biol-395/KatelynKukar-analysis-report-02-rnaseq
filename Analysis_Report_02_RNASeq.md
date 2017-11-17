Analysis Report 2: Understanding Genetic Expression within Female Lung Cancer Patients
================
Katelyn Kukar
November 15, 2017

Introduction
============

Genes encode the proteins which dictate cellular functions within organisms. Each step in the flow of information from DNA to RNA to protein provides the cell with integral information that works to regulate functionality by adjusting the amount and type of proteins manufactured. The functionality of genes has been found to be unique among individual samples, with one study stating gene expression levels were variable based on the “underlying genetics of the \[samples\] they were collected from” (Abeyta *et al.*, 2004). Gene expression also gives essential information regarding cellular responses during diseased states and the communication of these genes to the cells to increase organismal immunity. Immune responses are heightened by a combination of genes when the cell is under distress, and understanding the unique integration of genes in molecular pathways can thoroughly improve the efficacy of combination treatments (Feld *et al.*, 2007). By profiling the expression of genes based on these genetically inherited differences, disease treatments can be crafted on an individual, patient basis.

Studies behind benign and malignant tumor formation are of increased interest for gene therapy studies, since they offer a control mechanism for the up and down regulation of essential genes under normal or uncontrolled cellular growth conditions. “Clients with very advanced cancers refractory to conventional treatment indicate that \[gene therapy\] can specifically mediate tumor regression with low toxicity” (Roth and Cristiano, 1997). Therefore, understanding how the organism responds to the inheritance of a single functionally defective gene through the regulation of defense genes is imperative to stopping tumor growth in patients prior to the removal of the defective gene through vector therapy (Roth and Cristiano, 1997).

Lung cancer has traditionally been associated with smoke inhalation from carcinogenic substances which activate a downstream pathway to accelerate the development of cancer (Gazdar *et al.*, 2004). Lung cancer is specifically caused by epidermal growth factor receptor genes, a receptor tyrosine kinase that can be modulated through inhibition with EGFR synthetic inhibition therapy (getfitnib) (Paez *et al.*, 2004). However, these methods are not conducive to long term treatments, due to the sensitivity predictions and high mutation rates of cancerous cells (Paez *et al.*, 2004). Within this study, we evaluate the representative genes discovered within currently smoking, previously smoking, and never-smoking female patients and their associations with the human body, specifically EGFR. We predict that the most prevelant genes with associations to Epidermal Growth Factor Receptors will be from smoking tissue samples from patients in a more advanced stage of cancer.

Methods
=======

Sample origin and sequencing
----------------------------

Seo et al. (Seo *et al.*, 2012) collected 200 fresh surgical specimen (preserved with LN2) of primary lung adenocarcinoma from patients who underwent major lung resection. 87 of these cancer tissues with driver tissues that were not detected by screening tests, through Sanger sequencing for *EGFR* and *KRAS* mutation and fluorescence in situ hybridization \[FISH\] for *EML4-ALK* fusion, were analyzed by trascriptome sequencing for the paired tissue samples. For RNA-seq the RNAiso Plus kit from Takara was used, followed by RNeasy MinElute from Qiagen to purify the samples. RNA quality was assessed using RNA 6000 NanoLabchip on a 2100 Bioanalyzer from Agligent, with all samples matched to exome sequencing from normal lung genomic DNA. Samples were sequencing with Illumina TruSeq SBS Kit v3 on a HiSeq 2000 sequencer at Illumina Incorporated. Seo et al generated "14,038,673,860 paired-end 101-bp-long reads from RNA sequencing of 164 samples (87 cancer and 77 corresponding normal tissues). On average, the RNA sequencing throughputs were 9.77 and 7.38 Gbp for cancer and normal tissues, respectively. In the whole-exome sequencing of normal tissues, we obtained 32.96-fold read depth per tissue for regions targeted by the exome capture platform used in this study" (Seo *et al.*, 2012). Seo et al.aligned their RNA and exome sequencing reads through NCBI human reference genome using GSNAP, and also aligned RNA sequence reads to a cDNA set obtained from public databases.

Later, Li et al. (Li *et al.*, 2015) utilized RNA sequencing data from 68 lung adenocarcinoma patients with validated smoking status from Gene Expression Omnibus (GEO), collected originally from Seo et al. (2012). An out group was also collected for non-smoking patients from a separate GEO accession number.

Computational
-------------

We used the metadata originally retrieved from Seo et al. and re-purposed by Li et al. to create a relative study on the genes found within cancerous tissues from never, previously, and currently smoking patients.

An initial script was made to install the necessary tools and download sequnecing files for RNA-seq analysis on Ubunutu 16.04 with an sra-toolkit. Once the toolkit was applied to a path it was installed and run against NCBI to excise the column of sample run ids for downloading.

Another script was made to install the biomartr package to download a reference genome, transcripts, and annotation for the human genome. The source library was fun and then biocLite to get the genome, CDS, RNA and Gff lines.

Next, the paired-end downloaded sra files needed to be converted to fastq files. The sra-toolkit was again applied to convert the files in a for loop and split the files in forward and reverse reads utilizing fastq-dump.

To later create an alignment of the seqeunces given an index of kmers was required. Sailfish is a computational method that quantifies the abundance of annotated RNA isoforms from the RNA-seq analysis. Sailfish does this by looking at k-mers which refer to all the possible subsequneces from the reads (all possible isoforms) (Patro *et al.*, 2014).

Next quality checks were applied on the fastq files and output was saved. Quality checks are important to see if the original sequencing data sets have aggregious problems to inhibit analyses later on.

Once the quality of the sequences were evaluated then the paired end reads were trimmed with trimmomatic to remove insufficient read frames. This required TruSeq3-PE.fa file with Illumnia adaptors with strict parameters to trim reads that drop below 20 in a window size of 4 absed on fastqc evaluation. Another script was run after this to ensure all files were correctly trimmed and none were missed over by the code. This utilized a for loop to determine how variants of the original output were decided, and if not already trimmed then the first script was rerun on these missed files (Bolger *et al.*, 2014).

Returning to the k-mer based index sailfish was run on the trimmed sequences to align them from forward and reverse reads. In a for loop these samples were aligned for each file (Patro *et al.*, 2014).

Next a script to construct a transcript to gene ID mappling table was made using a Genbank ID to Gene names list which applied the sequences to known gene seqeunces already within Genbank. This file was transferred into human-readable gene names for analysis with perl and the removal of the beginning output to only leave required information (NCBI).

From the table made the genbank ID to gene name table was parsed to relable the rws to add a transcript ID and a gene name. This was done with sailfish (NCBI).

Lastly, an R script was made to read in all of the "transcript counts made by sailfish individually on each sample, aggregate those counts (which are at the transcript level) to the gene level, normalize them based on length and read depth, and then build a table with rows as genes and columns as samples, with each cell being the normalized count for that gene in that sample. Then, we want to join in the two other metadata files we have,from SRA and from the supplementatry table from the original manuscript that has information like patient age and smoking behavior. We have to do some cleaning along the way, and the gathered (melted) output file is rather big so we only include interesting columns, and write out the output in compressed csv format as well as binary RData format" (Zimmerman 2017). Once this was made troubleshooting was used to check the upload before the data was applied within R.

Within R the table was read in with the SRA sample metadata and the table was then manipulated to change the column names to be usable in graphical representations with human-readable information. Vectors were created for the sailfish quant files to the reconstructed table. The table was melted and gathered to include all the counts for all the samples by genes with the metadata tables while discarding redundant files. This table lastly had to be zipped to fit within GitHubs regulations (Patro *et al.*, 2014).

The code tolower was used to make each column name lower case before sub-setting the data to include only female participants. Using dplyr a primary table was crafted to show the number of patients within each cancer stage and their smoking status. Next, the data was subsetted again to look only at the top 5 genes of interest with the highest mean counts. Dplyr was used again to filter the data to look at the mean count of the 5 top genes as a table. These genes were summarized in a bar plot for reference. Once summarized the genes of interest were coded to become a vector to be used in accordance with smoking status and cancer stage. Boxplots were made utilizing this vector through a ggplot and dplyr syntax.

Results
=======

Once the metadata was organized as a column seperated value spreadsheet, with associated vectors, it could be loaded in for direct analysis. We apply multiple R source libraries to graphically represent all RNA sequence data to analysize. We utilize R to count and quanitfy many of the gene levels based on known phenotypic traits, in order to, evaluate each gene type found in reference to one another within the female population tested.

Tabular Results
---------------

Since female participants are primarily of interest the data was filtered to highlight this gender only. The tables represent quantified results from the female pariticipant cancer tissue samples.

| Cancer Stage |  Current Smoker|  Never Smoker|  Previous Smoker|
|:-------------|---------------:|-------------:|----------------:|
| 1A           |               0|            15|                1|
| 1B           |               0|             9|                1|
| 2A           |               0|             2|                0|
| 3A           |               1|             0|                1|
| 3B           |               0|             2|                0|
| 4            |               0|             2|                0|

**Table 1**: Females that never smoke seem to be more likely to have cancer at any stage.

| genename |  mean\_count|
|:---------|------------:|
| EEF1A1   |    149021.94|
| SFTPB    |    135305.42|
| SFTPA2   |     66520.77|
| CD74     |     58994.05|
| MIR6723  |     58916.57|

Visualization of tabular data is shows quanitfication of gene counts in reference to one another for the 5 most common genes from all female samples.

Graphical Results
-----------------

**Table 2**: The most highly expressed genes for females includes *SFTPB* and *EEF1A1*.

![](Analysis_Report_02_RNASeq_files/figure-markdown_github-ascii_identifiers/make-barplot-of-highly-expressed-genes-1.png)

**Figure 1**: Here we visualize the mean counts of the top five most prevalent genes revealed within the entire sample pool.

From Figure 1 results we visualize *EEF1A1* to be the gene of most interest.

![](Analysis_Report_02_RNASeq_files/figure-markdown_github-ascii_identifiers/make-boxplot-of-highly-expressed-genes-by-cancer_stage-1.png)

**Figure 2**: Here we visualize the gene counts for the top 5 most prevalent genes categorized by cancer stage.

From Figure 2, we can see *EEF1A1* has the highest count per gene in stages 1A, 2A, 3B, and 4. We see *SFTPB* is the highest in stage 1B, and *SFTPA2* is the highest in stage 3A. *SFTPB* has a boxplot that has a much larger spread in stage 1B than any other spreads seen throughout all stages.

![](Analysis_Report_02_RNASeq_files/figure-markdown_github-ascii_identifiers/make-boxplot-of-highly-expressed-genes-by-smoking_status-1.png)

**Figure 3**: Here we visualize the gene counts for the top 5 most prevalent genes categorized by smoking status.

From Figure 3, we can see *EEF1A1* has the largest statistical spread in patients that have never smoked, while we see *SFTPA2* has the largest spread in previously smoking patients. *SFTPB* has a noticeable spread in both never and previously smoking patients, but more outliers are seen in never smoking patients. Currently smoking patients have expression in *EEF1A1* and *MIR6723*.

Discussion
==========

The development of lung cancer and propogation of malignant tumors has traditionally been associated with smoke inhalation from carcinogenic substances that are known to accelerate cellular growth via receptor tyrosine kinases. However, the creation of proteins from downstream specific genes that activate cancerous cells remains mostly unknown. Within this study, we specifically evaluate the genes disvcovered in smoking, never-smoking, and previously smoking female malignant tumor samples to identify the most prevelant gene types. From our gene profiling we can conclude how the most prevelant gene types are associated with the human body and their potential relationship with Epidermal Growth Factor Receptors. We originally hypothesized the most likely genes of interest would be related to Epidermal Growth Factor Receptors in smoking patients with an advanced stage of adenocarcinoma.

Within Table 1, we deduce that a majority of the patients with varying cancer stages fall under the never smoking category. Though we see most of these subsetted patients are within the preliminary stages of adenocarcinoma, this raises questions due to the comparable few cases of cancer in current and previously smoking populations. We also can see that the most advanced stages of cancer are associated with never smoking patients, instead of current or previous smokers. To evaluate these discrepancies we look at the specific genes associated with smoking type in each sample. Figure 3 showcases a represenation of the prevelance of the five most relevant genes based on read counts. We conclude that in never smoking patients the *EEF1A1* has the largest spread of data.

Through the utilization of the Online Mendelian Inheritance in Man tool, presented by NCBI, we deduce that *EEF1A1* is the Eukaryotic Translation Elongation Factor 1-Alpha 1 which is associated with cervical cancer normally found in individuals with the Human Papilloma Virus (HPV). It is recorded by the World Health Organization that 70% of Cervical Cancer is directly related to HPV because the decreased immunity leads to mutation of normal cells on the cervix. The American Cancer Society denotes that cervical cancer comes in two relative forms with 9 out of 10 being from squamous cells in the exocervix, while the remaining types are adenocarcinomas from gland cells. These cervical adenocarcinomas have become more common in the past 20-30 years and we can propose that the high percentage of non-smoking individuals with this form of cancer is due to a relation they have with HPV. Currently, cervical cancer is the third leading cancer in Korea in HPV tested women (NCBI). We also see an increase of expression of *EEF1A1* in previously smoking patients which can be related to HPV or the activation of tyrosine kinases through carcinogenic inhalation. From this information we can predict *EEF1A1* is a gene used in the growth and propogation of cancerous tissues, with specific correlations to cervical cancer in women with HPV.

The next most prevelant gene within our tested samples after *EEF1A1* is *SFTPB*; as seen in Table 2 and Figure 1. Individuals with *SFTPB* expression are mostly in stage 1B of cancer and either never or previously smoking patients. OMIM from NCBI categorizes *SFTPB* as Surfactant, Pulmonary-Associated Protein B. Knowing adrenocarcinoma samples within the study were specifically extracted from glandular structure formed malignant tumors within the lung indicates a plausible link to this pulmonary protein. *SFTPB* specifically makes proteins that are packaged in cellular structures as lamellar bodies, found in specialized lung cells (NCBI). *SFTPB* is explained by NCBI to suppress lung cancer progression by inhibiting secretory phospholipases meaning it would be expected to be expressed in individuals with less advanced stages of cancer due to their bodies heightened immune response. *SFTPB* is also known to now be used as a Biomarker for Lung Cancer Prediction since its increased expression is only seen in response to growth of cancer cells (NCBI). By understnading the importance of *SFTB* in regards to adrenocarcinoma we can conclude it's increased expression is relevant in the female tissues it is observed in. However, we need to see if this gene is up or down regulated to see if it is creating proteins to prevent excess growth in already cancerous tissues or if it is mechanistically blocked.

After *SFTPB* and *EEF1A1* three remaining genes show increased expression within the female tissue samples: 1. *SFTPA2*: surfactant, pulmonary-associated protein A2 also important in supressing lung cancer progression (OMIM). 2. *MIR6723*: an miRNA, which could be used in regulatory pathway alterations (OMIM). 3. *CD74*: an antigen that is associated with inflammation and cancer within humans to increase cell proliferation of cancer cells (OMIM).

Lastly, in regards to Epidermal Growth Factor, we see an affect induced by *EEF1A1*. Since *EEF1A1* is a known elongation factor it functions within the cell to recruit tRNAs to Ribsomsomes during protein sysnthesis, menaing it is ubiquitious expressed. Moreover, *EEF1A1* participates in several processes required for cell growth and proliferation, including; cytoskeleton organization, mitotic apparatus formation, and signal transduction (NCBI). From this information we can conclude that *EEF1A1* helps promote cancerous tissue growth. We can support this notion in Figure 1 where the most expressed gene is *EEF1A1* in more advanced cancerous stages (3A, 3B, 4).

Overall, we can conclude that though our hypothesis towards the correlation of Epidermal Growth Factor with both smoking status and cancer stage is inconclusive, we do observe a correlation between cancer stage and Epidermal Growth Factor Regulatory genes alone. We also see a correlation with cancerous tissues and assumed preventative protein synthesis through repressor genes. We assume we do not see a correlation with smoking status due to the HPV prevelance in the female population in Korea, which requires more testing to statistially quantify. In future tests we hope to look at how HPV changes expression of genes that simultaneously promote or regulate cancer cell growth. We also hope to look at the up or down regulation of the top five genes as a holsitic unit in cancerous cells, and if the cancerous cells are some how mechanistically blocking preventative growth proteins or not.

Sources Cited
=============

Abeyta,M.J. *et al.* (2004) Unique gene expression signatures of independently-derived human embryonic stem cell lines. *Human molecular genetics*, **13**, 601–608.

Bolger,A.M. *et al.* (2014) Trimmomatic: A flexible trimmer for illumina sequence data. *Bioinformatics*, **30**, 2114–2120.

Feld,J.J. *et al.* (2007) Hepatic gene expression during treatment with peginterferon and ribavirin: Identifying molecular pathways for treatment response. *Hepatology*, **46**, 1548–1563.

Gazdar,A.F. *et al.* (2004) Mutations and addiction to egfr: The achilles ‘heal’of lung cancers? *Trends in molecular medicine*, **10**, 481–486.

Li,Y. *et al.* (2015) RNA-seq analysis of lung adenocarcinomas reveals different gene expression profiles between smoking and nonsmoking patients. *Tumor Biology*, **36**, 8993–9003.

Paez,J.G. *et al.* (2004) EGFR mutations in lung cancer: Correlation with clinical response to gefitinib therapy. *Science*, **304**, 1497–1500.

Patro,R. *et al.* (2014) Sailfish enables alignment-free isoform quantification from rna-seq reads using lightweight algorithms. *Nature biotechnology*, **32**, 462–464.

Roth,J.A. and Cristiano,R.J. (1997) Gene therapy for cancer: What have we done and where are we going? *Journal of the National Cancer Institute*, **89**, 21–39.

Seo,J.-S. *et al.* (2012) The transcriptional landscape and mutational profile of lung adenocarcinoma. *Genome research*, **22**, 2109–2119.
