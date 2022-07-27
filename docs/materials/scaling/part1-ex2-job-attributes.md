Exercise 1.2: Investigating Job Class Ad
==================================================

The objective of this exercise is to make user made aware of the 'Job Class Ad' that is available on HTCondor. When a job is submitted to HTCondor a set of 'Job Class Ad' gets added to the job. The 'Job Class Ad' stores information about various aspects of the job e.g.- which machine is running your job, current memory,disk usage of the job etc. 

Setup
-----

Before we start please make sure that you are logged into your login node- `login05.osgconnect.net`
The shell script that we will be using follows. Copy that and save it as `simple.sh`. 

```file
#!/bin/bash

SLEEPTIME=$1

hostname
pwd
whoami

for i in {1..5}
do 
 echo "performing iteration $i"
 sleep $SLEEPTIME
done
```
We have created a basic job submission that submits the file.
```file 
universe = vanilla
log = logs/$(Cluster)_$(Process).log
error = logs/$(Cluster)_$(Process).err
output = $(Cluster)_$(Process).out

executable = simple.sh

should_transfer_files = YES
when_to_transfer_output = ON_EXIT

request_cpus = 1
request_memory = 1GB
request_disk = 1GB

# set arguments, queue a normal job
arguments = 600
queue 1

# queue a job that will go on hold
transfer_input_files = test.txt
queue 1

# queue a job that will never start
request_memory = 40TB
queue 1
```

Save the submit file and submit the jobs using `condor_submit`. When the job starts running, try using following command.

```condor_q -l <JobId>```

Replace the `<JobId>` with your job's JobId. What do you see? You should see a list of data printed out in your screen. These are the `Job Class Ads`. 
Details of all the different attribute can be found in the [HTcondor Manual](https://htcondor.readthedocs.io/en/latest/classad-attributes/job-classad-attributes.html) 

The list has many information about your jobs. We can look at some of the specific `Job Class Ad` using the `-af` option.
Some of the most useful Job Class Ad that we can look at are 
`LastRemoteHost`,`MATCH_EXP_JOBGLIDEIN_ResourceName`,`exitcode`, `holdreasoncode`, `NumJobStarts`, `MemoryUsage`, 
`NumHoldsByReason`, `NumJobStarts`,`MachineAttrGLIDEIN_ResourceNameN`

Point to be noted that the `MachineAttrGLIDEIN_ResourceNameN` itself is not a `Job Class Ad` that you can ask HTCondor to return. This `N` represent an integer eg. `MachineAttrGLIDEIN_ResourceName0`

You can look at some of the `Job Class Ad` using `condor_q <JobId> -af <jobclassad>`. Replace the `<JobId>` with your job's Id and the `<jobclassad>` with your desired `Job Class Ad`.
Remember, you can use many `Job Class Ad` in your command and the same command can be used in the case of `condor_history` as well.

* `LastRemoteHost` contains the information/host name where your jobs ran the latest 
* `MATCH_EXP_JOBGLIDEIN_ResourceName` contains the information which machine ran your jobs, 
* `exitcode` can be used to get an idea about whether your job ran successfully or not. For most of the cases an **exitcode=0** means that your job ran successfully. 
* `holdreasoncode` contains the code of different reasons for which your jobs may go on hold. The details of the code can be found [here](https://htcondor.readthedocs.io/en/latest/classad-attributes/job-classad-attributes.html?highlight=HoldReasonCode#job-classad-attributes)
* `MemoryUsage` shows the current memory used by your job.
* `NumHoldsByReason` The value of this attribute is a (nested) classad containing a count of how many times a job has been placed on hold grouped by the reason the job went on hold
* `NumJobStarts` contain the information about how many time the job started. if the value is 1 that means the job ran only on one site.
* `MachineAttrGLIDEIN_ResourceNameN` indicates the name of the machine where the job ran. The integer value of **N** represent the execution attempt number. 0 is the recent execution and the highest integer value is the oldest execution attempt.

You can look for these `Job Class Ad` in the jobs in `condor_history` too.
