# CEWiT-REU-Identifying-datasets-in-SRA-using-Jetstream

This project was done by National Center for Genome Analysis Support ([NCGAS](https://ncgas.org/)), and two undergraduate studnets, Haley Leffeler, and Sruthi Ganapaneni who worked with us for a year as part of the Center of Exceller for Women in Tech ([CEWiT](http://cewit.indiana.edu/)) [REU program](http://cewit.indiana.edu/students/REU/index.shtml). Over the last  year the students focused on developing a workflow to search Sequence Read Arhive ([SRA](https://www.ncbi.nlm.nih.gov/sra)) to identify datasets that contain a sequence/genome of interest. This project is also in collaboration with [Rob Edward's lab](https://edwards.sdsu.edu) from [San Diego State University](https://www.sdsu.edu/) who have previously developed a gateway to [search SRA](https://www.searchsra.org/) that is available to the community. 

### Main objective of the project 
The objective of this project was to develop a workflow to search SRA datasets using the developed gateway SearchSRA to identify datasets that contain the sequence of interest. The workflow focused on filtering the results from searchSRA to filter and visualize the results to confirm the presence of the sequence. The resulting datasets that do contain the sequence of interest can now be added to the research project for analysis as needed. 

### Common steps included in the workflow 
The general steps inlcuded in the workflow is shown in the below figure

![alt text](https://github.com/NCGAS/CEWiT-REU-Identifying-datasets-in-SRA-using-Jetstream/blob/master/SRApaper-method.png "Workflow steps")

### Steps to run this workflow
#### Input sequence
This is the sequence of interest or a genome that you would like to identify in other datasets in SRA.

#### Search SRA 
To search SRA, we used the gateway [SearchSRA gateway](https://www.searchsra.org/)
    - First register for an account, if you dont have an account already
    - Create a project - enter project name, project description. 
    - Create an experiment - experiment name description, project the experiment should belong to, application-"Search-SRA". Then click "Continue" 
    - Choose reference file- Import the input sequence 
    - For the option "Select existing Search IDs File OR Upload your own below" - you can select to serahc against only "[Human Microbiome Project](https://www.hmpdacc.org/ihmp/) datasets in SRA", "[TARA ocean project datasets](https://oceans.taraexpeditions.org/en/m/about-tara/les-expeditions/tara-oceans/), or "All-SRA-metagenomes"
    - Click "Save and Launch" to start the job
  Documentation on how to use the gateway is also available [here](https://www.searchsra.org/pages/documentation).

Once the searchSRA job is completed, download the output "results.txt". This file contains a link where the results are stored, and other information.     

For example, \
Here is the format of the results.txt file \
results_url = http://IPaddress/results/c800788d-3146-4ad6-8529-aa76d83d6694/results.zip \
results_size = 548M \
total_compute_hours = 120.49 
    - Download the results.zip file from the link, either by copy pasting the link in a browser or running the command \
        `wget http://IPaddress/results/c800788d-3146-4ad6-8529-aa76d83d6694/results.zip` \
    - Once the result.zip is downloaded, unzip the file \
        `unzip results.zip` \
The results directory, contains a set of subdirectories listed as 1,2,3,.... Each of the subdirectories contains a set of files with    extension *.bam* and *.bam.bai*.  

#### Filtering  
Filter the bam files to include only those that, 
1. have a alignment length of more than 100bp 
    - This was done using the code available in another git repository https://github.com/linsalrob/sam. 
    - The command run was in the results file, 
        `for f in */; do cd $f; for i in *.bam; do sam_len 100 $i $i-filtered.bam; done; cd ..;  done ` 
    - The above code enters every subdirectory in results file (cd $f), and runs the sam_len command on every bam file one by one. The resulting filtered bam files are saved with the filename "SRR/ERR/DRR...-filtered.bam 
2. have more than 10 hits at least \
In the results file again, run the following command \
    - First, I used samtools count to count the number of hits per sample and save it to a new file. 
    `for f in */; do cd $f; for i in *-filtered.bam; do echo -n "$i: " >> ../samtools_count; echo ``samtools view -c "$i"`` >>../samtools_count; done` \
    This outputs the file, samtools_count which has the format., (S/E/D)RR ID: number of hits 
    - Then create a subset list with lines from samtools_count that have more than 10 hits, using the command 
    `sed -e 's/bam: /\t/g' samtools_count| awk '{ if ($2 > 10) { print } }'| cut -f 1 > moreThan10Hits.txt` 
    - Now make a subset bam file with the filetered hits 
        Make a directory called subset to save all the filtered bam files \
        `mkdir subset` \
        Then from the list copy over the bam files to the new directory made. \
        `for f in */; do  cd $f; for i in ``cat ../../moreThan10Hits.txt``; do  cp $i.bam /vol_b/subset/.; done; cd ..;  done` \

Great! Now we have the filered bam files that potentially have the input sequence 
 
#### Visualization 
This final step is to quickly visualize the filtered bam files against the input sequence to assess the coverage, regions of the input sequence. In some cases this step has also helped us further filter the bam files (shown in Haley's project, add link).  

For this step we picked [Anvi'o](http://merenlab.org/software/anvio/), an analysis and visualization platform for omics data. This platform will extend the data to not just confirming datasets do contain the genome, but also explore the results further. 



