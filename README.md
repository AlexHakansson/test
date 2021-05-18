
# Kallisto With Downsampling Docker Markdown

This process takes 3 different programs. Kallisto, seqtk and Python


## Download

A docker file for kallisto was found online that will install kallisto and 
python along with their dependcies.

```RUN apt-get update  && apt-get install -y \
		build-essential \
		cmake \
		python \
		python-pip \
		python-dev \
		hdf5-tools \
		libhdf5-dev \
		hdf5-helpers \
		libhdf5-serial-dev \
		git \
		apt-utils

# install kallisto from source

WORKDIR /docker
RUN git clone https://github.com/pachterlab/kallisto.git 
WORKDIR /docker/kallisto
RUN mkdir build
WORKDIR /docker/kallisto/build 
RUN cmake .. && \
	make && \
	make install
```
	
	
	
seqtk can be installed with	


`RUN apt-get update && apt-get install -y \
  seqtk

CMD ["/usr/bin/seqtk"]`
	
Alternatively kallisto and seqtk can be installed with anaconda with:

`conda install kallisto`

`conda install seqtk`

## The pipeline

ds_and_kal.py is run to generate the bash file to first downsample the fastq
files and then run kallisto on the downsampled file.

The funciton can be run using the following command:

`python ds_and_kal.py <fastq_dir> <down_sample_num>`

whare fastq_dir is the directory with the fastq files to process and
down_sample_num is the number of reads to downsample to or the proportion of
reads to downsample if it is less than one.

To make things simpler, the main funciton of ds_and_kal.py has predetermined the
location of the kallisto index file, intermediate downsampling folder, andg
the kallisto results directory. The kallisto index file location may need to be
changed and the other files can be changed as necessary. The main function of 
ds_and_kal.py can be seen below.

`
# kallisto index file. change location for your machine.
kalfile = "~/kallisto_index/Human_GRC38_cdna.idx"
#location to save intermediate downsampled fastq files
ds_dir = "ds_fastq"
if not os.path.exists(ds_dir):
    os.mkdir(ds_dir)
#location for kallisto results to be saved
kal_dir = "kal_results"
if not os.path.exists(kal_dir):
    os.mkdir(kal_dir)
#bash script to be run
job_file = "ds_kal_job.sh"

make_full_job(sys.argv[1], ds_dir, kal_dir,sys.argv[2], kalfile, job_file)
`


