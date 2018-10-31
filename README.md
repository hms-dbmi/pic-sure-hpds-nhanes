# pic-sure-hpds-nhanes
A Self Contained NHANES Research Environment powered by PIC-SURE-HPDS and Jupyter

You must have Docker and docker-compose installed and working to use this tool.


Full Speed Requirements(All data resides in RAM):

3G of RAM available to your Docker engine (docker-machine or otherwise)
10G of hard drive space available to your Docker engine
As many cores as you have available to your Docker engine


Micro-environment Requirements(120 concepts in RAM at a time):

1.5GB of RAM available to your Docker engine (docker-machine or otherwise)
10G of hard drive space available to your Docker engine
One CPU core available to your Docker engine


A note about RAM usage:

The above requirements assume you will be running the example notebook
all the way through and thus will be pulling all the data for all patients
at some point. If you instead plan on doing targeted research on the data
that is related to your research you can get by with much less RAM. A little
more than 1 GB of RAM in both of the above mentioned setups is used by JupyterHub 
to have all the data for all the patients available to your R kernel.

You will notice that the speed of all queries in the example notebook is
roughly the same(really fast) except the last two. This is because the working 
set size of PIC-SURE-HPDS is tuned by the number of patients and number of concepts 
to process at once. For all examples except the last two, all concepts involved in 
the query fit in RAM. The only swapping that is done is on the result set because 
too many patients are involved to fit in the memory configured for HPDS in the 
Micro-environment. For the last two queries in the example, the result set
and the concepts both need to be swapped out of memory multiple times
during processing.


Instructions:

This repo comes with 2 files that can be used to configre the research environment.

.env_full_speed is set to serve queries as fast as possible given the CPU that you have. 

.env_micro is set to use as little RAM as possible and only one core.

Each configuration only processes one large query and one small query at a time. This
is because the processing is CPU-bound and it will use all the CPU you give it access
to until a request completes. This means if you fire 100 queries all at once, it will
serve them in order until it completes all of them. 

For production deployments, the system can be configured to serve multiple requests
at once, but this repository is intended to be used on your laptop or desktop.

The micro environment is very conservative with RAM usage. This environment
will take minutes to return you all the data at once. The performance is directly
related to how many concepts you select in a given query.

The full speed environment is very greedy with CPU usage, but is limited by IO. 
It uses about 2 GB of RAM and giving it more than that will not provide any 
further speed increase. The only way to speed up the full speed environment is 
by getting faster CPUs, more cores, or in the case that you already have a modern
8 core CPU, getting a faster hard drive.


To start the environment in full speed mode just run:

docker-compose up -d


To switch configurations, just copy the desired config's file to .env and run: 

docker-compose up -d


To stop the environment, just run:

docker-compose down


All your changes to the Jupyter Notebooks in the single-user environment are saved
in the jupyter-notebooks folder, so you can stop and restart the environment as many
times as you wish.


