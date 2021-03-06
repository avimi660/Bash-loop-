```
# Bash-loop-
\
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









# new loop: 4th april

zero_match_line_no=`wc -l zero_gene_matches.txt | awk '{ print $1 }'`

for line_no in`seq 1 1 $zero_match_line_no1;
 do echo $line_no;
done 

# to add in later
line_no=42
zero_match_gene=`head -n $line_no zero_matches.txt | tail -n 1 | awk '{ print $1 }'`

product_name=`grep "gene="$zero_match_gene";" GCF_000001405.39_GRCh38.p13_genomic.gff | grep $'\t'CDS$'\t'| head -n 1 | sed ' s/.*;product=//g' | sed 's/;protein_id=.*//g'`

# 5th april
# Shall we change to CDS regions to search for the genes in possums

 # Need to first define our gene_search_term

gene_search_term=COX10

grep "gene="$gene_search_term";" GCF_000001405.39_GRCh38.p13_genomic.gff | grep $'\t'CDS$'\t' > temp.gff

# above we are searching the human genome portion for the term CDS and outputting it to temp gff file


### And then we discovered the product name was still the same between humans and possums

### We’ll work on how we might use this to find more genes
 
# We won’t run the actual loop right now, but we will think about how we set it up to run to save us a bit of time later on. We are going to want to run this on all of our “zero_gene_matches.txt” genes
zero_match_line_no=`wc -l zero_gene_matches.txt | awk '{ print $1 }'`
 for line_no in `seq 1 1 $zero_match_line_no`;
 do echo $line_no;
done

# zero_match_line getting word cont on this file for zero gene matches then piping that commannd to search the input file for the specified pattern 
# We’ll test out our actual code on COX10, which is line 42 in the zero_gene_matches.txt file so…
line_no=42

zero_match_gene=`head -n $line_no zero_gene_matches.txt | tail -n 1 | awk '{ print $1 }'`

product_name=`grep "gene="$zero_match_gene";" GCF_000001405.39_GRCh38.p13_genomic.gff | grep $'\t'CDS$'\t' | head -n 1 | sed 's/.*;product=//g' | sed 's/;protein_id=.*//g'`
 
 ## 6th april we are going to re run the whole loop incorporating search for the description, however we will trouble shoot a bit first
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



##
no_matches=`wc -l temp_all_searches | awk '{ print $1 }'`

if [ `wc -l temp_all_searches | awk '{ print $1 }'` -lt 1 ];
 then product_name=`grep "gene="$gene_search_term";" GCF_000001405.39_GRCh38.p13_genomic.gff | grep $'\t'CDS$'\t' | head -n 1 | sed 's/.*;product=/;product=/g' | sed 's/;protein_id=.*//g'`
 grep "$product_name" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff
 fi
 


grep "$product_name" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep ";Parent=gene-" | sed 's/.";Parent=gene-//g' | sed 's/;.*//g'

# 7th april 
# today we are going to try to get the loop ready to re run incorperating the search for the product description. However we will trouble shoot a bit first before running the whole loop.
# we are using COX10 gene as an example because we know that is hasnt found a match bassed on the gene name. using COX10 and then doing -N we know that it is at line 165 so set the mito_line variable to 165

mito_line=165

#then we run the stuff in our for loop without actually running it as a for loop until the following line which is where we will chuck in our additional code. 

cat temp | sort | uniq > temp_all_searches 

# the first thing we need to do is to run the stuff when there is no matches 
# for our loops we do: for something in something_else; do this_thing; done

#for if statememnts 
if [ this thing is true ]; then this_thing; fi

# for example, the following thing says if the number of lines in our temp_all_searches file is less than 1 (shown by the -lt 1) then echo the gene_search_term name. 

if [ `wc -l temp_all_searches | awk '{ print $1 }'` -lt 1 ]
 then echo $gene_search_term
fi

# OK what is going to be our actual statement 

if [ `wc -l temp_all_searches | awk '{ print $1 }'` -lt 1 ]
 then product_name=`grep "gene="$gene_search_term";" GCF_000001405.39_GRCh38.p13_genomic.gff | grep $'\t'CDS$'\t' | head -n 1 | sed 's/.*;product=/;product=/g' | sed 's/;protein_id=.*//g'`;
 grep "$product_name" nesi/nobackup/uoo03398/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep ";Parent=gene-" | sed 's/.*;Parent=gene-//g' | sed 's/;.*//g' > temp_product_search
fi
 
# for each gene name we have found from the product_search above, we will then look for this in the possum genome for the full gene, and then save these into the temp  file, then make sure we only have uniq matches and then we can keep going with for loop as it was previously written 

# full loop below 
 
 for mito_line in `seq 2 1 $no_mito_genes`;
do gene_search_term=`head -n $mito_line mito_genes.txt | tail -n 1 | cut -f 4`;
grep "ID=gene-"$gene_search_term";" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' > temp;
alt_search_terms=`head -n $mito_line mito_genes.txt | tail -n 1 | cut -f 5`;      no_alts=`echo $alt_search_terms | grep -o ";" | wc -l`;
for alt_gene in `seq 1 1 $no_alts`; do alt_gene_name=`echo $alt_search_terms | cut -d ";" -f $alt_gene | sed 's/ //g'`; grep "ID=gene-"$alt_gene_name";" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' >> temp;
done;
if [ `wc -l temp_all_searches | awk '{ print $1 }'` -lt 1 ]
 then product_name=`grep "gene="$gene_search_term";" GCF_000001405.39_GRCh38.p13_genomic.gff | grep $'\t'CDS$'\t' | head -n 1 | sed 's/.*;product=/;product=/g' | sed 's/;protein_id=.*//g'`;
 grep "$product_name" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep ";Parent=gene-" | sed 's/.*;Parent=gene-//g' | sed 's/;.*//g' > temp_product_search;
no_product_matches=`wc -l temp_product_search | awk '{ print $1 }'`
 for product_match_line in `seq 1 1 $no_product_matches`;
 do product_match=`head -n $product_match_line temp_product_search | tail -n 1` 
 grep "ID=gene-"$product_match";" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' >> temp;
done
cat temp | sort | uniq > temp_all_searches
fi
no_matches=`wc -l temp_all_searches | awk '{ print $1 }'`;
echo $gene_search_term $no_matches >> results_matches.txt;
sed "s/^/$gene_search_term\t/g" temp_all_searches >> gene_location_possum.txt;
rm temp*;
done


## this was making a section to add into the loop above 
no_product_matches=`wc -l temp_product_search | awk '{ print $1 }'`
 for product_match_line in `seq 1 1 $no_product_matches`;
 do product_match=`head -n $product_match_line temp_product_search | tail -n 1`
 
 grep "ID=gene-"$product_match";" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' >> temp;
done
cat temp | sort | uniq > temp_all_searches;
fi


# copy of alanas script replacing directories with mine. 

no_mito_genes=`wc -l mito_genes.txt | awk '{ print $1 }'`

for mito_line in `seq 2 1 $no_mito_genes`;
# For loop to do stuff...
      do gene_search_term=`head -n $mito_line mito_genes.txt | tail -n 1 | cut -f 4`;
      grep "ID=gene-"$gene_search_term";" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' > temp;
      alt_search_terms=`head -n $mito_line mito_genes.txt | tail -n 1 | cut -f 5`;
      no_alts=`echo $alt_search_terms | grep -o ";" | wc -l`;
                for alt_gene in `seq 1 1 $no_alts`;
                        do alt_gene_name=`echo $alt_search_terms | cut -d ";" -f $alt_gene | sed 's/ //g'`;
                        grep "ID=gene-"$alt_gene_name";" /nesi/nobackup/uoo03398/amichael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' >> temp;
                done;
        cat temp | sort | uniq > temp_all_searches;
        if [ `wc -l temp_all_searches | awk '{ print $1 }'` -lt 1 ]
                then product_name=`grep "gene="$gene_search_term";" GCF_000001405.39_GRCh38.p13_genomic.gff | grep $'\t'CDS$'\t' | head -n 1 | sed 's/.*;product=/;product=/g' | sed 's/;protein_id=$
                grep "$product_name" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep ";Parent=gene-" | sed  's/.*;Parent=gene-//g' | sed 's/;.*//g' > temp_p$
                no_product_matches=`wc -l temp_product_search | awk '{ print $1 }'`;
                for product_match_line in `seq 1 1 $no_product_matches `;
                        do product_match=`head -n $product_match_line temp_product_search | tail -n 1`;
                        grep "ID=gene-"$product_match";" /nesi/nobackup/uoo03398/michael/possum_genome_master/GCF_011100635.1_mTriVul1.pri_genomic.gff | grep $'\t'gene$'\t' >> temp;
                done
                cat temp | sort | uniq > temp_all_searches;
        fi
	no_matches=`wc -l temp_all_searches | awk '{ print $1 }'`;
        echo $gene_search_term $no_matches >> results_matches.txt;
        sed "s/^/$gene_search_term\t/g" temp_all_searches >> gene_location_possum.txt;
        rm temp*;
done

