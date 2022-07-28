
# OSG Virtual School Materials

## School Overview and Intro

<!-- View the slides
([PDF](files/osgvsp21-overview.pdf),
[PowerPoint](files/osgvsp21-overview.pptx))
-->

## Intro to HTC and HTCondor Job Execution

View the slides
([PDF](htcondor/files/osgus22-htc-htcondor.pdf),
[PowerPoint](htcondor/files/osgus22-htc-htcondor.pptx))

### Intro Exercises 1: Running and Viewing Simple Jobs (Strongly Recommended)

- [Exercise 1.1: Log in to the local submit machine and look around](htcondor/part1-ex1-login)
- [Exercise 1.2: Experiment with HTCondor commands](htcondor/part1-ex2-commands.md)
- [Exercise 1.3: Run jobs!](htcondor/part1-ex3-jobs.md)
- [Exercise 1.4: Read and interpret log files](htcondor/part1-ex4-logs.md)
- [Exercise 1.5: Determining Resource Needs](htcondor/part1-ex5-request.md)
- [Exercise 1.6: Remove jobs from the queue](htcondor/part1-ex6-remove.md)


### Bonus Exercises: Job Attributes and Handling

- [Bonus Exercise 1.7: Compile and run some C code](htcondor/part1-ex7-compile.md)
- [Bonus Exercise 1.8: Explore `condor_q`](htcondor/part1-ex8-queue.md)
- [Bonus Exercise 1.9: Explore `condor_status`](htcondor/part1-ex9-status.md)

## Intro to HTCondor Multiple Job Execution

View the Slides ([PDF](htcondor/files/osgus22-htc-htcondor-PART2.pdf))

### Intro Exercises 2: Running Many HTC Jobs (Strongly Recommended)

- [Exercise 2.1: Work with input and output files](htcondor/part2-ex1-files.md)
- [Exercise 2.2: Use `queue N`, `$(Cluster)`, and `$(Process)`](htcondor/part2-ex2-queue-n.md)
- [Exercise 2.3: Use `queue from` with custom variables](htcondor/part2-ex3-queue-from.md)
- [Bonus Exercise 2.4: Use `queue matching` with a custom variable](htcondor/part2-ex4-queue-matching.md)


## OSG

View the slides
([PDF](osg/files/osgus22-day2-2-osg-cartwright.pdf),
[PowerPoint](osg/files/osgus22-day2-2-osg-cartwright.pptx))

### OSG Exercises: Comparing CHTC and OSG (Strongly Recommended)

- [Exercise 1.1: Refresher – submitting multiple jobs](osg/part1-ex1-submit-refresher.md)
- [Exercise 1.2: Log in to the OS Pool Access Point](osg/part1-ex2-login-scp.md)
- [Exercise 1.3: Running jobs in OSG](osg/part1-ex3-submit-osg.md)
- [Exercise 1.4: Hardware differences between CHTC and OSG](osg/part1-ex4-hardware-diffs.md)
- [Exercise 1.5: Software differences in OSG](osg/part1-ex5-software-diffs.md)


## Troubleshooting

([PDF](troubleshooting/files/osgus22-htc-troubleshooting.pdf),
[PowerPoint](troubleshooting/files/osgus22-htc-troubleshooting.pptx))

### Troubleshooting Exercises: 

- [Exercise 1.1: Troubleshooting Jobs](troubleshooting/part1-ex1-troubleshooting.md)
- [Exercise 1.2: Job Retry](troubleshooting/part1-ex2-job-retry.md)


## Software

View the slides
([PDF](software/files/osgus22-software.pdf),
[PowerPoint](software/files/osgus22-software.pptx))

### Software Exercises 1: Basic Software and Wrapper Script Use (Strongly Recommended)

- [Exercise 1.1: Work With Downloaded Software](software/part1-ex1-download.md)
- [Exercise 1.2: Use a Wrapper Script To Run Software](software/part1-ex2-wrapper.md)
- [Exercise 1.3: Using Arguments With Wrapper Scripts](software/part1-ex3-arguments.md)

### Software Exercises 2: Specific Software Examples (Pick Two)

- [Exercise 2.1: Compiling and Running a Simple Code](software/part2-ex1-compiling.md)
- [Exercise 2.2: Compiling a Research Software](software/part2-ex2-prepackaged.md)
- [Exercise 2.3: Compiling Python and Running Jobs](software/part2-ex3-python.md)
- [Exercise 2.4: Compiling Matlab and Running Jobs](software/part2-ex4-matlab.md)
- [Exercise 2.5: Using Conda Environments](software/part2-ex5-conda.md)

### Software Exercises 3: Using Containers in Jobs (Strongly Recommended)

- [Exercise 3.1: Using Software in a Singularity Container](software/part3-ex1-singularity.md)
still working on 
- [Exercise 3.2: Singularity Examples on OSG Connect](software/part3-ex2-singularity-options.md)

### Bonus Exercises: More With Containers

- [Exercise 4.1: Using Software in a Docker Container](software/part4-ex1-docker.md)
- [Exercise 4.2: Building Your Own Docker Container (Beta)](software/part4-ex2-docker-build.md)


## Data

View the slides
([PDF](data/files/osgus22-data.pdf),
[PowerPoint](data/files/osgus22-data.pptx))

### Data Exercises 1: HTCondor File Transfer (Strongly Recommended)

- [Exercise 1.1: Understanding a job's data needs](data/part1-ex1-data-needs.md)
- [Exercise 1.2: Using data compression with HTCondor file transfer](data/part1-ex2-file-transfer.md)
- [Exercise 1.3: Splitting input](data/part1-ex3-blast-split.md)

### Data Exercises 2: Using Stash (Strongly Recommended)

- [Exercise 2.1: Using a web proxy for shared input](data/part2-ex1-blast-proxy.md)
- [Exercise 2.2: Stash for shared input](data/part2-ex2-stash-shared.md)
- [Exercise 2.3: Stash for shared output](data/part2-ex3-stash-unique.md)

### Bonus Exercises: Shared File Systems

- [Exercise 3.1: Shared filesystems for large input](data/part3-ex1-input.md)
- [Exercise 3.2: Shared filesystems for large output](data/part3-ex2-output.md)

## Scaling Up

View the slides
([PDF](scaling/files/osgus22-scaling.pdf))

### Scaling Up Exercises

-   [Exercise 1.1: Organizing HTC workloads](scaling/part1-ex1-organization.md)

## Extra Topics

### Containers and GPUs

View the slides
([PDF](containers/files/osgus22-containers.pdf),
[PowerPoint](containers/files/osgus22-containers.pptx))

- [Exercise 1.1: Building a Singularity Container](containers/part1-ex1-building.md)
- [Exercise 1.2: Using Containers in Jobs](containers/part1-ex2-running.md)
- [Exercise 1.3: Running a GPU job](containers/part1-ex3-gpus.md)

<!-- BEGIN EXTRA TOPICS THAT ARE NOT READY YET

### Workflows with DAGMan

View the **DRAFT** slides
([PDF](workflows/files/osgus22-bonus-dagman.pdf),[PowerPoint](workflows/files/osgus22-bonus-dagman.pptx))

- [Exercise 1.1: Coordinating set of jobs: A simple DAG](workflows/part1-ex1-simple-dag.md)
- [Exercise 1.2: A brief detour through the Mandelbrot set](workflows/part1-ex2-mandelbrot.md)
- [Exercise 1.3: A more complex DAG](workflows/part1-ex3-complex-dag.md)
- [Exercise 1.4: Handling jobs that fail with DAGMan](workflows/part1-ex4-failed-dag.md)
- [Bonus Exercise 4.5: HTCondor challenges](workflows/part1-ex5-challenges.md)


### Self-checkpointing for long-running jobs

View the **DRAFT** slides
([PDF](checkpoint/files/osgus22-day5-1-selfcheckpointing-DRAFT-20220720.pdf))

-   [Exercise 1.1: **DRAFT**: Trying out self-checkpointing](checkpoint/part1-ex1-checkpointing.md)

<!-- BEGIN EXTRA TOPICS THAT ARE NOT READY YET

### Introduction to Research Computing Facilitation

Slides will be posted here.

END EXTRA TOPICS THAT ARE NOT READY YET -->

