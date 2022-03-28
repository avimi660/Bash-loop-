# Bash-loop-
#!/bin/bash -e

#SBATCH -A uoo03398 # Account number
#SBATCH -J annotation_search # Job name	- can be anything
#SBATCH --ntasks 1 # this generally stays as 1 unless you are running stuff in parallel
#SBATCH -c 1 # number of CPUs your job needs
#SBATCH -t 24:00:00 # Amount of	time I want to request 	 
#SBATCH --mem=1G # This is the RAM
#SBATCH -D /nesi/nobackup/uoo03398/michael/mitoproteome_master/mitoproteome_sql_extract # Directory run fr$
#SBATCH --mail-type=ALL	# email	when job starts, ends or dies
#SBATCH --mail-user=avimi660@student.otago.ac.nz #Email	me updates and when job	is finished
#SBATCH -N 1 # how many computers do i need 
#SBATCH --hint=nomultithread #Nesi has hyperthreading, some programs  dont even


for mito_line in `seq 2 1 $no_mito_genes`;
do gene_search_term=`head -n $mito_line mito_genes.txt | tail -n 1 | cut -f 4`;
grep "ID=gene-"$gene_search_term";" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' > temp;
alt_search_terms=`head -n $mito_line mito_genes.txt | tail -n 1 | cut -f 5`;      no_alts=`echo $alt_search_terms | grep -o ";" | wc -l`;
for alt_gene in `seq 1 1 $no_alts`; do alt_gene_name=`echo $alt_search_terms | cut -d ";" -f $alt_gene | sed 's/ //g'`; grep "ID=gene-"$alt_gene_name";" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' >> temp; done;
cat temp | sort | uniq > temp_all_searches;
no_matches=`wc -l temp_all_searches | awk '{ print $1 }'`; echo $gene_search_term $no_matches >> results_matches.txt;
cat temp_all_searches >> gene_location_possum.txt;
rm temp*;
done


# once it has run I need to check if everything has worked properly 
 # QCing to make sure our code has worked as we intended it to. We’ve double checked that results_matches.txt has the same number of genes as in mito_genes.txt . Now we are going to use R to make sure that gene_location_possum.txt, has the same number of total matches as in results_matches.txt
 
 # now I need to load R by typing R in the command line 
 R
 # make a results table to view in R 
 results_table <-  read.table("results_matches.txt")
 # <- is assigning a variable for results_table and remember to use quotes when talking about files. 
 
 # tips - 
 dim(results_table) will give me numbers the first number is the number of rows in the table and the second number is the number of columns. - the number in square brackets first just refers to how many lines of output are generated if there are massive tables. 
 # to pull out a value specifically from the table 
 name_of_table[row_number,column_number] 
 # if i want to pull out a 

