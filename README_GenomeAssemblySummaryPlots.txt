#Genome Assembly Summary Plots
#All files in directory "Assembly"
#For Answers, see .png plots in "READMEs_Summaries_Plots"
#For work see below (some files may have been created in "README_GenomeAssemblySummaryReport.txt"

#Prepare environment and download data
##loads bioawk,etc
module load jje/jjeutils/0.1a
##loads faSize, etc
module load jje/kent/2014.02.19
##gets Genome Assemlby file
wget ftp://ftp.flybase.net/genomes/Drosophila_melanogaster/dmel_r6.13_FB2016_05/fasta/dmel-all-chromosome-r6.13.fasta.gz 
##unzips files
gunzip dmel-all-CDS-r6.12.fasta.gz 
##renames file Assembly
mv dmel-all-CDS-r6.12.fasta.gz > Assembly.txt

#Generating Sequence Length Distribution File
##gets just headers from full assembly file
grep ">" Assembly.txt > Headers.txt 
##gets just the field with the length
awk '{ print $7 }' Headers.txt > Lengths.txt 
##removes everything but the numerical values for the lengths
tr -d "length=;" < Lengths.txt > LengthsNum.txt 
##groups lengths from longest to shortest
sort -rn LengthsNum.txt > SortLength.txt	
##adds Length Header
echo $'Length' | cat - SortLength.txt > SortLengthHead.txt 

#Generating Sequence Length Distribution Plot
##loads R
module load R 
##opens R
R 
##loads ggplot
library(ggplot2) 
##loads data into R
LengthDistribution <- read.table("SortLengthHead.txt", header = TRUE) 
##loads data into ggplot
LD = ggplot(data = LengthDistribution) 
##makes labeled histogram out of data, with a log scale and bin sizes of 1000
LD + geom_histogram(mapping = aes(x=Length), bins = 1000) + scale_x_log10() + ggtitle("Sequence Length Distibution (Dmel)") + xlab("Sequence Length (bp)")
##saves the plot as 6' x 6' file
ggsave("SequenceLengthDistributionPlot.png", width = 6, height = 6) 

#Generating Sequence GC% Distribution File
##gives the GC content of each
bioawk -c fastx '{ print ">"$name; print gc($seq) }' Assembly.txt > GC.txt	
##gets just the GC% of each sequence
grep -v ">" GC.txt > GCval.txt 
##sorts GC content from highest to lowest
sort -rn GCval.txt > GCsort.txt 
##shows answer
less GCsort.txt	

#Generating Sequence GC% Distribution Plot
##loads R
module load R 
##opens R
R 
##loads ggplot
library(ggplot2)
##loads GCsort data into R
GCDistribution = read.table("GCsort.txt") 
##loads data into ggplot
GCD = ggplot(data = GCDistribution)
##makes labeled histogram with bin sizes of 0.01%
GCD + geom_histogram(mapping = aes(x=V1), binwidth = 0.01) + ggtitle("Sequence GC% Distribution (Dmel)") + xlab("Sequence GC Content (%)")
##saves plot as a 6' x 6' png file
ggsave("SequenceGCDistributionPlot.png", width = 6, height = 6)

#Generating Genome Size CDF File
##loads plotCDF2
module load jje/jjeeutils 
##Adds Dmel Assembly ID
bioawk '{print $1 "\t Dmel"}' < SortLength.txt > CDFFile.txt 
##Adds Length and Assembly Headers to list of sorted Lengths
echo $'Length\tAssembly' | cat - CDFFile.txt > CDFHead.txt

#Generating Genome Size CDF Plot
##creates CDF from file with headers Length and Assembly
plotCDF2 CDFHead.txt > GenomeSizeCDFPlot.png 

