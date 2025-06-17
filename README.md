# Extracting_Human_Exon_Coordinates_from_GTF

# Computational Practice: Extracting Gene Coordinates From a Genome file.

# Objective
In this lab, you will access a high-performance computing environment, download genomic annotation data from ENSEMBL, and extract specific features (exon coordinates) using command-line tools. These are common computational biology tasks.

# Learning Objectives
* HPC cluster access and resource allocation
* Navigating Linux file systems
* Downloading biological data from public repositories
* Parsing genomic annotation files (GTF format)
* Basic data extraction using command-line tools

# Instructions

## Step 1: Access the Palmetto2 Cluster
Open your web browser and navigate to https://ondemand.rcd.clemson.edu/
Log in with your Clemson credentials
You will be directed to the Palmetto OnDemand dashboard

## Step 2: Request an Interactive Jupyter Session
From the top navigation menu, click on Interactive Apps → Jupyter Notebook
Configure your session with the following specifications:
```
Number of resource chunks: 1
CPU cores per chunk: 4
Memory per chunk: 32gb
GPUs per chunk: 0
Walltime requested: 12:00:00 (12 hours)
QOS: Select appropriate QOS (e.g., regular)
Partition: Choose an appropriate partition (consult cluster documentation)
```
Click Launch to submit your job request
Wait for your job to start (this may take a few minutes)

## Step 3: Access Terminal in Jupyter
When your session is ready, click Connect to Jupyter
In the Jupyter interface, click the New button in the upper right
Select Terminal from the dropdown menu

## Step 4: Set Up Working Environment
```
# Navigate to your scratch space
cd /scratch/$USER

# Create a new directory for this assignment
mkdir -p human_exon_coordinates

# Navigate to the new directory
cd human_exon_coordinates

# Check your current location to confirm
pwd
```

## Step 5: Download the Human GTF File from ENSEMBL
```
# Download the latest GTF file for human genome 
wget https://ftp.ensembl.org/pub/current_gtf/homo_sapiens/Homo_sapiens.GRCh38.110.gtf.gz

# Check the file size to confirm successful download
ls -lh
```

## Step 6: Extract Exon Coordinates
```
# Uncompress the GTF file
gunzip Homo_sapiens.GRCh38.110.gtf.gz

# Extract chromosome, start, and stop coordinates for all exons
# Format: chromosome, start, end
awk '$3 == "exon" {print $1, $4, $5}' Homo_sapiens.GRCh38.110.gtf > human_exons_coordinates.txt

# Look at the first few lines of the extracted coordinates
head human_exons_coordinates.txt

# Count the number of exons extracted
wc -l human_exons_coordinates.txt

# Create a more structured output with tab-separated values and a header
echo -e "chromosome\tstart\tend" > human_exons_coordinates_tsv.txt
awk '$3 == "exon" {print $1"\t"$4"\t"$5}' Homo_sapiens.GRCh38.110.gtf >> human_exons_coordinates_tsv.txt

# View the formatted output
head human_exons_coordinates_tsv.txt
```

## Step 7: Enhanced Analysis (Optional Extensions)
```
# Count exons per chromosome
awk '$3 == "exon" {print $1}' Homo_sapiens.GRCh38.110.gtf | sort | uniq -c | sort -nr > exons_per_chromosome.txt

# Calculate average exon length
awk '$3 == "exon" {sum += $5-$4+1; count++} END {print "Average exon length: " sum/count " bp"}' Homo_sapiens.GRCh38.110.gtf

# Find the largest and smallest exons
awk '$3 == "exon" {print $1, $4, $5, $5-$4+1}' Homo_sapiens.GRCh38.110.gtf | sort -k4,4nr | head -1 | awk '{print "Largest exon: " $1 ":" $2 "-" $3 " (" $4 " bp)"}'
awk '$3 == "exon" {print $1, $4, $5, $5-$4+1}' Homo_sapiens.GRCh38.110.gtf | sort -k4,4n | head -1 | awk '{print "Smallest exon: " $1 ":" $2 "-" $3 " (" $4 " bp)"}'
```

# Questions for Reflection
* What is the biological significance of exons in gene structure, and why might extracting their coordinates be important for downstream genomic analyses like variant annotation or RNA-seq analysis?
* How does the GTF (Gene Transfer Format) file structure enable efficient parsing of specific genomic features, and what advantages does the tab-delimited format provide over other annotation formats?
* When you calculated the average exon length and identified the largest/smallest exons, what biological factors might explain the dramatic variation in exon sizes across the human genome?
* Why is it crucial to work with the most current genome assembly (GRCh38.110) when extracting genomic coordinates, and what problems could arise from using outdated annotation versions in medical genomics applications?

# Generative AI Prompts For a Deeper Dive

## Prompt 1: GTF File Structure and Genomic Annotation Standards
```
I'm working with GTF files for genomic annotation analysis and need to understand the format more deeply. Could you explain:
1. The complete structure of GTF files - all 9 columns and their biological significance
2. How GTF differs from other annotation formats like GFF3, BED, and GenBank
3. The hierarchical relationship between genes, transcripts, exons, and CDSs in GTF files
4. How to extract complex features like alternative splicing patterns and isoform-specific exons
5. Best practices for validating GTF file integrity and handling edge cases in parsing
6. How modern genome browsers and analysis tools utilize GTF annotations
Please include examples of parsing commands for extracting different feature types and explain how coordinate systems work in genomic annotations.
```
## Prompt 2: Exon Biology and Computational Analysis Applications
```
I've extracted exon coordinates from the human genome and want to understand their broader biological and computational significance. Could you provide insights on:
1. The biological role of exons in gene expression and protein coding
2. How exon size distribution relates to gene function and evolutionary constraints
3. Computational applications that rely on accurate exon coordinates (variant calling, RNA-seq, splice site prediction)
4. Methods for analyzing exon conservation across species and identifying functionally important regions
5. How alternative splicing affects exon usage and how to computationally detect splice variants
6. Integration of exon coordinates with other genomic datasets (ChIP-seq, ATAC-seq, Hi-C)
7. Clinical applications where exon coordinate accuracy is critical (precision medicine, genetic diagnostics)
Please explain how exon coordinate data connects to real-world medical genomics workflows and research applications.
```
# Additional Resources
ENSEMBL Documentation (https://useast.ensembl.org/info/website/upload/gff.html)
GTF Format Specification (https://genome.ucsc.edu/FAQ/FAQformat.html#format4)
Palmetto2 User Guide (https://docs.rcd.clemson.edu/palmetto/index.html)
