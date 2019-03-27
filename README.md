# CEWiT-REU-Identifying-datasets-in-SRA-using-Jetstream

This project was done by National Center for Genome Analysis Support ([NCGAS](https://ncgas.org/)), and two undergraduate studnets, Haley Leffeler, and Sruthi Ganapaneni who worked with us for a year as part of the Center of Exceller for Women in Tech ([CEWiT](http://cewit.indiana.edu/)) [REU program](http://cewit.indiana.edu/students/REU/index.shtml). Over the last  year the students focused on developing a workflow to search Sequence Read Arhive ([SRA](https://www.ncbi.nlm.nih.gov/sra)) to identify datasets that contain a sequence/genome of interest. This project is also in collaboration with [Rob Edward's lab](https://edwards.sdsu.edu) from [San Diego State University](https://www.sdsu.edu/) who have previously developed a gateway to [search SRA](https://www.searchsra.org/) that is available to the community. 

#### Main objective of the project 
The objective of this project was to develop a workflow to search SRA datasets using the developed gateway SearchSRA to identify datasets that contain the sequence of interest. The workflow focused on filtering the results from searchSRA to filter and visualize the results to confirm the presence of the sequence. The resulting datasets that do contain the sequence of interest can now be added to the research project for analysis as needed. 

#### Common steps included in the workflow 
The general steps inlcuded in the workflow is shown in the below figure

![alt text](https://github.com/NCGAS/CEWiT-REU-Identifying-datasets-in-SRA-using-Jetstream/blob/master/SRApaper-method.png "Workflow steps")

#### Steps to run this workflow
1. Input sequence- This is the sequence of interest or a genome that you would like to identify in other datasets in SRA. 

2. Search SRA - To search SRA, we used the gateway [SearchSRA gateway](https://www.searchsra.org/)
    - First register for an account, if you dont have an account already
    - Create a project - enter project name, project description. 
    - Create an experiment - experiment name description, project the experiment should belong to, application-"Search-SRA". Then click "Continue" 
    - Choose reference file- Import the sequence of interest or genome
    - For the option "Select existing Search IDs File OR Upload your own below" - you can select to serahc against only "[Human Microbiome Project](https://www.hmpdacc.org/ihmp/) datasets in SRA", "[TARA ocean project datasets](https://oceans.taraexpeditions.org/en/m/about-tara/les-expeditions/tara-oceans/), or "All-SRA-metagenomes"
    - Click "Save and Launch" to start the job
  Documentation on how to use the gateway is also available [here](https://www.searchsra.org/pages/documentation).

3. Once the searchSRA job is completed, download the output "results.txt". This file contains a link where the results are stored, and other inforamtion. \    
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

4. Filtering these results to include only those bam files that are   
    - have a alignment length of more than 100bp 
    - have more than 10 hits at least 

