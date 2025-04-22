# Extracting_Human_Exon_Coordinates_from_GTF

# Objective
In this assignment, you will learn to access a high-performance computing environment, download genomic annotation data from ENSEMBL, and extract specific features (exon coordinates) using command-line tools.

* Skills Practiced
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
# Deliverables
* A text file with the first 20 lines of your exon coordinates file
* The total number of exons in the human genome (from your analysis)
* A brief report (500-1000 words) answering the following questions:
* What chromosome has the most exons and why might this be the case?
* What is the average exon length in the human genome based on your analysis?
* How could this information be used in designing genetic studies or diagnostics?
* What challenges might arise when trying to map sequencing reads to exon coordinates?
* Generative AI Prompts

## Prompt 1: Understanding Genome Assemblies
```
"I'm working with human genome data and need a comprehensive understanding of genome assemblies. Could you explain:

1. What a genome assembly is and how it's created
2. The difference between reference genomes, draft genomes, and patch releases
3. The evolution of human genome assemblies from GRCh37 (hg19) to GRCh38 (hg38) and the upcoming T2T-CHM13
4. How contigs, scaffolds, and chromosomes relate to each other in an assembly
5. Common file formats used to represent genome assemblies (FASTA, AGP, etc.)
6. The concept of 'N' bases in assemblies and what they represent
7. How genome assemblies handle complex regions like centromeres and telomeres
8. Best practices for converting coordinates between different assembly versions
9. How assembly quality is measured (e.g., N50, contig length)
10. Real-world implications of assembly quality on downstream bioinformatic analyses"
```

## Prompt 2: Representing DNA Information as Coordinates
```
"I'm working on a project involving exon coordinates extracted from a GTF file. Could you help me understand:

1. The concept of genome coordinates and how they provide a reference system for genomic features
2. Different coordinate systems (0-based vs 1-based, interbase) and their applications
3. How genome coordinates relate to the central dogma of molecular biology (DNA → RNA → protein)
4. The relationship between coordinates in GTF/GFF files and actual biological features like genes, exons, and introns
5. How alternative splicing is represented using coordinate systems
6. Challenges in coordinate representation across different reference genomes or assembly versions
7. How genomic variants like SNPs, indels, and structural variants are described using coordinates
8. File formats specialized for storing genomic coordinates (BED, GTF, GFF, VCF)
9. Coordinate transformation in genome browsers and visualization tools
10. Computational methods for efficiently querying and manipulating genomic intervals"
```

# Additional Resources
ENSEMBL Documentation (https://useast.ensembl.org/info/website/upload/gff.html)
GTF Format Specification (https://genome.ucsc.edu/FAQ/FAQformat.html#format4)
Palmetto2 User Guide (https://docs.rcd.clemson.edu/palmetto/index.html)
