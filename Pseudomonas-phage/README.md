# README 
Mining the SRA database for Pseudomons phage 

#### Input sequence
Pseudomonas phage PAKP1 genome (contigs.fa)

#### Search SRA 
Pseudomas phage genome was imported as reference genome and searched against all of SRA using SerachSRA \
The results included bam files for each dataset searched against in SRA, which 111155 files  

#### Filtering  
Filtered the bam files to select for \ 
- alignment length more than 100 bp using sam_len script from another git repository https://github.com/linsalrob/sam \
  `for f in */; do cd $f; for i in *.bam; do sam_len 100 $i $i-filtered.bam; done; cd ..;  done ` 
 - number of hits < 10 \
    - `for f in */; do cd $f; for i in *-filtered.bam; do echo -n "$i: " >> ../samtools_count; echo ``samtools view -c "$i"`` >>../samtools_count; done` 
    - `sed -e 's/bam: /\t/g' samtools_count| awk '{ if ($2 > 10) { print } }'| cut -f 1 > moreThan10Hits.txt` \
    - `for f in */; do  cd $f; for i in ``cat ../../moreThan10Hits.txt``; do  cp $i.bam /vol_b/subset/.; done; cd ..;  done` \
  
This resulted in only 4 datasets after filtering. \

#### Visualization 
Using Anvi'o, upload data to Jetstream \ 

- `anvi-script-reformat-fasta contigs.fa -o contigs-fixed.fa -l 0 --simplify-names`
- `mv contigs-fixed.fa contigs.fa` 
- Create anvio contigs database \
  `anvi-gen-contigs-database -f contigs.fa -o contigs.db -n 'pseudomonas_datbase'`
- CONVERT BAM files to SAM files \
  `samtools view -h -o output.sam input.bam` \
  for loop: changing all bam files to sam files \
  `for dir in *.bam;  do samtools view -h -o  $dir.sam $dir; done` 
- CHANGE CONTIG (TO MATCH BAM FILES) \
  `for dir in *.bam.sam ; do sed -e 's/NC_015294.2/c_000000000001/g' $dir>$dir.new.sam;  done` 
- CONVERT SAM back to BAM \
  `for dir in *.bam.sam.new.sam; do samtools view -h -o $dir.bam $dir; done`
- Profiling BAM files \
  write a for loop to sort bam files \
  `for dir in *.bam (.bam.sam.new.sam.bam); do anvi-init-bam $dir -o $dir.2.bam; done` 
- create anvi-profiles \
`anvi-profile -i $dir -c /home/sruthi12/contigs.db `
- MERGE \
`anvi-merge */PROFILE.db -o SAMPLES-MERGED -c /home/sruthi12/contigs.db`
- visualize data \
  `anvi-interactive -p SAMPLES-MERGED/PROFILE.db -c /home/sruthi12/contigs.db`
 
#### Image from Anvi'o showing the results 
This image shows the 4 datasets identified to contain the Pseudomonas phage, and the coverage of the reads against the phage genome. \

<img src="https://github.com/NCGAS/CEWiT-REU-Identifying-datasets-in-SRA-using-Jetstream/blob/master/Pseudomonas-phage/pseudomas%20phage.png" alt="drawing" style="width:200px;"/>


#### Conclusion
The SRR1518980 datsaet, was previously characterized as an unclassified virus, which through this workflow we can predict is Pseudomonas phage PAK P1. 



