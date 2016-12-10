#Genome Assembly Summary Report
#All files in directory "Assembly"
#For answers, less file "Assembly/READMEs_Summaries_Plots/GenomeAssembly_SummaryReport.txt"
#For work see below

#Methods used to summarize the D. melanogaster Genome Assembly

#Preparing the environment and getting Genome Assembly Data from FlyBase
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

#Calculating total number of nucleotides
##deletes headers, leaving only sequences
grep -v "^>" Assembly.txt > SepSeq.txt 
##deletes line returns from sequences
tr -d '\n' < SepSeq.txt > AllSeq.txt 
##counts the total number of sequences
wc -L AllSeq.txt > NucNumber.txt 
##removes OverAllSeq
tr -d 'NucNumber' < NucNumber.txt > NucNumber2.txt 
##show answer: 143726002 nucleotides 
less NucNumber2.txt 

#Calculating number of Ns
##counts number of Ns, -o omits lines and only outputs matches
grep -o 'N' AllSeq.txt | wc -l > NNumber.txt 
##shows answer: 1152978 Ns
less NNumber.txt 

#Calculating total number of sequences
##gets only the headers, deleting the sequences
grep ">" Assembly.txt > Headers.txt 
##counts the number of headers
grep -c ">" Headers.txt > SeqNumber.txt 
##shows answer: 1870 sequences
less SeqNumber.txt 

#Splitting Data by < or > 100kbp
##gets just sequences under 100,000bp
faFilter maxSize=100000 Assembly Under100
##gets just sequences over 100,000bp
faFilter minSize=100000 Assembly Over100 

#Calculating total nucleotides (>100kbp)
##deletes headers, leaving only sequences
grep -v ">" Over100.txt > OverSepSeq.txt 
##deletes line returns from sequences
tr -d '\n' < OverSepSeq.txt > OverAllSeq.txt
##counts the total number of sequences
wc -L OverAllSeq.txt > OverNucNumber.txt 
##removes OverAllSeq
tr -d 'OverAllSeq' < OverNucNumber.txt > OverNucNumber2.txt
##shows answer: 137547960
less OverNucNumber2.txt 

#Calculating total nucleotides (<100kbp)
##deletes headers, leaving only sequences
grep -v "^>" Under100.txt > UnderSepSeq.txt 
##deletes line returns from sequences
tr -d '\n' < UnderSepSeq.txt > UnderAllSeq.txt 
##counts the total number of sequences
wc -L UnderAllSeq.txt > UnderNucNumber.txt 
##removes UnderAllSeq
tr -d 'UnderAllSeq' < UnderNucNumber.txt > UnderNucNumber2.txt 
##shows answer: 6178042 nucleotides
less UnderNucNumber2.txt 

#Calculating total number of Ns (>100kbp)
##counts number of Ns, -o omits lines and only outputs matches
grep -o 'N' OverAllSeq.txt | wc -l > OverNNumber.txt 
##shows answer: 490385
less OverNNumber.txt 

#Calculating total number of Ns (<100kbp)
##counts number of Ns, -o omits lines and only outputs matches
grep -o 'N' UnderAllSeq.txt | wc -l > UnderNNumber.txt 
##shows answer 662593 Ns
less UnderNNumber.txt 

#Calculating total number of sequences (>100kbp)
##gets only the headers, deleting the sequences
grep ">" Over100.txt > OverHeaders.txt 
##counts the number of headers
grep -c ">" OverHeaders.txt > OverSeqNumber.txt 
##shows answer: 7
less OverSeqNumber.txt 

#Calculating total number of sequences (<100kbp)
##gets only the headers, deleting the sequences
grep ">" Under100.txt > UnderHeaders.txt 
##counts the number of headers
grep -c ">" UnderHeaders.txt > UnderSeqNumber.txt 
##shows answer: 1863 sequences
less UnderSeqNumber.txt 


#Bash Script to display data all nice and such 
##write bash script in nano 
nano bash_GenomeAssembly.sh
##make script executable
chmod +x bash_GenomeAssembly.sh
##run bash script and save output
bash bash_GenomeAssembly.sh > GenomeAssemblyReport.txt

##Bash script follows
#!/usr/bin/env bash
echo "There are $(bioawk '{print}' SeqNumber.txt) sequences in this assembly"
echo "There are $(bioawk '{print}' NucNumber2.txt) nucleotides in this assembly" 
echo "There are $(bioawk '{print}' NNumber.txt) Ns in this assembly"
echo "There are $(bioawk '{print}' OverSeqNumber.txt) sequences larger than 100,000bp in this assembly" 
echo "There are $(bioawk '{print}' OverNucNumber2.txt) nucleotides in the sequences larger than 100,000bp in this assembly" 
echo "There are $(bioawk '{print}' OverNNumber.txt) Ns in the sequences larger than 100,000bp in this assembly" 
echo "There are $(bioawk '{print}' UnderSeqNumber.txt) sequences shorter than 100,000bp in this assembly"
echo "There are $(bioawk '{print}' UnderNucNumber2.txt) nucleotides in the sequences shorter than 100,000bp within this assembly" 
echo "There are $(bioawk '{print}' UnderNNumber.txt) Ns in the sequences shorter than 100,000bp within this assembly"
