---
status: testing
---

Workflows Exercise 1.4: Handling a DAG That Fails
=========================

The objective of this exercise is to help you learn how DAGMan deals with job failures. DAGMan is built to help you recover from such failures.

Background
----------

DAGMan can handle a situation where some of the nodes in a DAG fail. DAGMan will run as many nodes as possible, then create a rescue DAG making it easy to continue when the problem is fixed.

Breaking Things
---------------

Recall that DAGMan decides that a jobs fails if its exit code is non-zero. Let's modify our montage job so that it fails. Work in the same directory where you did the last DAG. Edit montage.sub to add a `-h` to the arguments. It will look like this, with the -h at the beginning of the highlighted line:

```hl_lines="2"
executable              = /usr/bin/montage
arguments               = -h tile_0_0.ppm tile_0_1.ppm tile_1_0.ppm tile_1_1.ppm -mode Concatenate -tile 2x2 mandle-from-dag.jpg
transfer_input_files    = tile_0_0.ppm,tile_0_1.ppm,tile_1_0.ppm,tile_1_1.ppm
output                  = montage.out
error                   = montage.err
log                     = montage.log
request_memory          = 1GB
request_disk            = 1GB
request_cpus            = 1
queue
```

Submit the DAG again:

``` console
username@learn $ condor_submit_dag goatbrot.dag
-----------------------------------------------------------------------
File for submitting this DAG to HTCondor           : goatbrot.dag.condor.sub
Log of DAGMan debugging messages                 : goatbrot.dag.dagman.out
Log of HTCondor library output                     : goatbrot.dag.lib.out
Log of HTCondor library error messages             : goatbrot.dag.lib.err
Log of the life of condor_dagman itself          : goatbrot.dag.dagman.log

Submitting job(s).
1 job(s) submitted to cluster 236889.
-----------------------------------------------------------------------
```

Use watch to watch the jobs until they finish. In a separate window, use `tail --lines=500 -f goatbrot.dag.dagman.out` to watch what DAGMan does.

``` console
07/27/22 15:11:46 (D_ALWAYS) ******************************************************
07/27/22 15:11:46 (D_ALWAYS) ** condor_scheduniv_exec.236889.0 (CONDOR_DAGMAN) STARTING UP
07/27/22 15:11:46 (D_ALWAYS) ** /usr/bin/condor_dagman
07/27/22 15:11:46 (D_ALWAYS) ** SubsystemInfo: name=DAGMAN type=DAGMAN(9) class=CLIENT(2)
07/27/22 15:11:46 (D_ALWAYS) ** Configuration: subsystem:DAGMAN local:<NONE> class:CLIENT
07/27/22 15:11:46 (D_ALWAYS) ** $CondorVersion: 9.11.0 2022-07-19 BuildID: 597572 PackageID: 9.11.0-0.597572 RC $
07/27/22 15:11:46 (D_ALWAYS) ** $CondorPlatform: x86_64_CentOS7 $
07/27/22 15:11:46 (D_ALWAYS) ** PID = 1651969
07/27/22 15:11:46 (D_ALWAYS) ** Log last touched time unavailable (No such file or directory)
07/27/22 15:11:46 (D_ALWAYS) ******************************************************
07/27/22 15:11:46 (D_ALWAYS) Using config source: /etc/condor/condor_config
07/27/22 15:11:46 (D_ALWAYS) Using local config sources:
07/27/22 15:11:46 (D_ALWAYS)    /var/run/condor/pids.conf
07/27/22 15:11:46 (D_ALWAYS)    /var/run/condor/master_pid_6051.conf
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/condor_config.principals
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/condor_config.hosts
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/condor_config.global
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/condor_config.policy
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/condor_config.x86_64_CentOS7
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/condor_config.security
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/hosts/learn.chtc.wisc.edu.local
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/include/schedd.conf
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/include/facility_wid.conf
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/include/scitokens_credmon.conf
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/include/owned_by_chtc.conf
07/27/22 15:11:46 (D_ALWAYS)    /var/run/condor/condor_config.startd_offline_gpus
07/27/22 15:11:46 (D_ALWAYS)    /etc/condor/condor_config.flightworthy_testing
```
Below is where DAGMan realizes that the montage node failed:

```console
07/27/22 15:14:12 (D_ALWAYS) Event: ULOG_EXECUTE for HTCondor Node montage (236895.0.0) {07/27/22 15:14:09}
07/27/22 15:14:12 (D_ALWAYS) Number of idle job procs: 0
07/27/22 15:14:17 (D_ALWAYS) Currently monitoring 1 HTCondor log file(s)
07/27/22 15:14:17 (D_ALWAYS) Event: ULOG_JOB_TERMINATED for HTCondor Node montage (236895.0.0) {07/27/22 15:14:13}
07/27/22 15:14:17 (D_ALWAYS) Number of idle job procs: 0
07/27/22 15:14:17 (D_ALWAYS) Node montage job proc (236895.0.0) failed with status 1.
07/27/22 15:14:17 (D_ALWAYS) DAG status: 2 (DAG_STATUS_NODE_FAILED)
07/27/22 15:14:17 (D_ALWAYS) Of 5 nodes total:
07/27/22 15:14:17 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 15:14:17 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 15:14:17 (D_ALWAYS)     4       0        0       0       0          0        1
07/27/22 15:14:17 (D_ALWAYS) 0 job proc(s) currently held
07/27/22 15:14:17 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeAvg = 0.008020679155985514; EventCycleTimeCount = 30.0; EventCycleTimeMax = 0.1734399795532227; EventCycleTimeMin = 3.790855407714844E-05; EventCycleTimeStd = 0.03267003963035863; EventCycleTimeSum = 0.2406203746795654; LogProcessCycleTimeAvg = 0.001571587153843471; LogProcessCycleTimeCount = 7.0; LogProcessCycleTimeMax = 0.002900123596191406; LogProcessCycleTimeMin = 0.0008997917175292969; LogProcessCycleTimeStd = 0.0006338591087992796; LogProcessCycleTimeSum = 0.0110011100769043; SleepCycleTimeAvg = 5.004259451230367; SleepCycleTimeCount = 30.0; SleepCycleTimeMax = 5.006263017654419; SleepCycleTimeMin = 5.000571966171265; SleepCycleTimeStd = 0.001743289792563769; SleepCycleTimeSum = 150.127783536911; SubmitCycleTimeAvg = 0.007244017816358997; SubmitCycleTimeCount = 31.0; SubmitCycleTimeMax = 0.1727950572967529; SubmitCycleTimeMin = 1.311302185058594E-05; SubmitCycleTimeStd = 0.03207315501357425; SubmitCycleTimeSum = 0.2245645523071289; ]
07/27/22 15:14:17 (D_ALWAYS) ERROR: the following job(s) failed:
07/27/22 15:14:17 (D_ALWAYS) ---------------------- Job ----------------------
07/27/22 15:14:17 (D_ALWAYS)       Node Name: montage
07/27/22 15:14:17 (D_ALWAYS)            Noop: false
07/27/22 15:14:17 (D_ALWAYS)          NodeID: 4
07/27/22 15:14:17 (D_ALWAYS)     Node Status: STATUS_ERROR
07/27/22 15:14:17 (D_ALWAYS) Node return val: 1
07/27/22 15:14:17 (D_ALWAYS)           Error: Job proc (236895.0.0) failed with status 1
07/27/22 15:14:17 (D_ALWAYS) Job Submit File: montage.sub
07/27/22 15:14:17 (D_ALWAYS)  HTCondor Job ID: (236895.0.0)
07/27/22 15:14:17 (D_ALWAYS) PARENTS: g1 g2 g3 g4 WAITING: 0 CHILDREN:
07/27/22 15:14:17 (D_ALWAYS) ---------------------------------------	<END>
07/27/22 15:14:17 (D_ALWAYS) Aborting DAG...
07/27/22 15:14:17 (D_ALWAYS) Writing Rescue DAG to goatbrot.dag.rescue001...
07/27/22 15:14:17 (D_ALWAYS) Removing submitted jobs...
07/27/22 15:14:17 (D_ALWAYS) Removing any/all submitted HTCondor jobs...
07/27/22 15:14:17 (D_ALWAYS) Running: /usr/bin/condor_rm -const DAGManJobId==236889
07/27/22 15:14:17 (D_ALWAYS) Note: 0 total job deferrals because of -MaxJobs limit (0)
07/27/22 15:14:17 (D_ALWAYS) Note: 0 total job deferrals because of -MaxIdle limit (1000)
07/27/22 15:14:17 (D_ALWAYS) Note: 0 total job deferrals because of node category throttles
07/27/22 15:14:17 (D_ALWAYS) Note: 0 total PRE script deferrals because of -MaxPre limit (20) or DEFER
07/27/22 15:14:17 (D_ALWAYS) Note: 0 total POST script deferrals because of -MaxPost limit (20) or DEFER
07/27/22 15:14:17 (D_ALWAYS) Note: 0 total HOLD script deferrals because of -MaxHold limit (20) or DEFER
07/27/22 15:14:17 (D_ALWAYS) DAG status: 2 (DAG_STATUS_NODE_FAILED)
07/27/22 15:14:17 (D_ALWAYS) Of 5 nodes total:
07/27/22 15:14:17 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 15:14:17 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 15:14:17 (D_ALWAYS)     4       0        0       0       0          0        1
07/27/22 15:14:17 (D_ALWAYS) 0 job proc(s) currently held
07/27/22 15:14:17 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeAvg = 0.008020679155985514; EventCycleTimeCount = 30.0; EventCycleTimeMax = 0.1734399795532227; EventCycleTimeMin = 3.790855407714844E-05; EventCycleTimeStd = 0.03267003963035863; EventCycleTimeSum = 0.2406203746795654; LogProcessCycleTimeAvg = 0.001571587153843471; LogProcessCycleTimeCount = 7.0; LogProcessCycleTimeMax = 0.002900123596191406; LogProcessCycleTimeMin = 0.0008997917175292969; LogProcessCycleTimeStd = 0.0006338591087992796; LogProcessCycleTimeSum = 0.0110011100769043; SleepCycleTimeAvg = 5.004259451230367; SleepCycleTimeCount = 30.0; SleepCycleTimeMax = 5.006263017654419; SleepCycleTimeMin = 5.000571966171265; SleepCycleTimeStd = 0.001743289792563769; SleepCycleTimeSum = 150.127783536911; SubmitCycleTimeAvg = 0.007244017816358997; SubmitCycleTimeCount = 31.0; SubmitCycleTimeMax = 0.1727950572967529; SubmitCycleTimeMin = 1.311302185058594E-05; SubmitCycleTimeStd = 0.03207315501357425; SubmitCycleTimeSum = 0.2245645523071289; ]
07/27/22 15:14:17 (D_ALWAYS) Wrote metrics file goatbrot.dag.metrics.
07/27/22 15:14:17 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeAvg = 0.008020679155985514; EventCycleTimeCount = 30.0; EventCycleTimeMax = 0.1734399795532227; EventCycleTimeMin = 3.790855407714844E-05; EventCycleTimeStd = 0.03267003963035863; EventCycleTimeSum = 0.2406203746795654; LogProcessCycleTimeAvg = 0.001571587153843471; LogProcessCycleTimeCount = 7.0; LogProcessCycleTimeMax = 0.002900123596191406; LogProcessCycleTimeMin = 0.0008997917175292969; LogProcessCycleTimeStd = 0.0006338591087992796; LogProcessCycleTimeSum = 0.0110011100769043; SleepCycleTimeAvg = 5.004259451230367; SleepCycleTimeCount = 30.0; SleepCycleTimeMax = 5.006263017654419; SleepCycleTimeMin = 5.000571966171265; SleepCycleTimeStd = 0.001743289792563769; SleepCycleTimeSum = 150.127783536911; SubmitCycleTimeAvg = 0.007244017816358997; SubmitCycleTimeCount = 31.0; SubmitCycleTimeMax = 0.1727950572967529; SubmitCycleTimeMin = 1.311302185058594E-05; SubmitCycleTimeStd = 0.03207315501357425; SubmitCycleTimeSum = 0.2245645523071289; ]
07/27/22 15:14:17 (D_ALWAYS) **** condor_scheduniv_exec.236889.0 (condor_DAGMAN) pid 1651969 EXITING WITH STATUS 1
```

DAGMan notices that one of the jobs failed because its exit code was non-zero. DAGMan ran as much of the DAG as possible and logged enough information to continue the run when the situation is resolved. Do you see the part where it wrote the rescue DAG?

Look at the rescue DAG file. It's called a partial DAG because it indicates what part of the DAG has already been completed.

``` console
username@learn $ cat goatbrot.dag.rescue001
# Rescue DAG file, created after running
#   the goatbrot.dag DAG file
# Created 7/27/2022 20:14:17 UTC
# Rescue DAG version: 2.0.1 (partial)
#
# Total number of Nodes: 5
# Nodes premarked DONE: 4
# Nodes that failed: 1
#   montage,<ENDLIST>

DONE g1
DONE g2
DONE g3
DONE g4
```

From the comment near the top, we know that the montage node failed. Let's fix it by getting rid of the offending `-h` argument. Change montage.sub to look like:

``` file
executable              = /usr/bin/montage
arguments               = tile_0_0.ppm tile_0_1.ppm tile_1_0.ppm tile_1_1.ppm -mode Concatenate -tile 2x2 mandle-from-dag.jpg
transfer_input_files    = tile_0_0.ppm,tile_0_1.ppm,tile_1_0.ppm,tile_1_1.ppm
output                  = montage.out
error                   = montage.err
log                     = montage.log
request_memory          = 1GB
request_disk            = 1GB
request_cpus            = 1
queue
```

Now we can re-submit our original DAG and DAGMan will pick up where it left off. It will automatically notice the rescue DAG. If you didn't fix the problem, DAGMan would generate another rescue DAG.

``` console
username@learn $ condor_submit_dag goatbrot.dag

Running rescue DAG 1
-----------------------------------------------------------------------
File for submitting this DAG to HTCondor           : goatbrot.dag.condor.sub
Log of DAGMan debugging messages                 : goatbrot.dag.dagman.out
Log of HTCondor library output                     : goatbrot.dag.lib.out
Log of HTCondor library error messages             : goatbrot.dag.lib.err
Log of the life of condor_dagman itself          : goatbrot.dag.dagman.log

Submitting job(s).
1 job(s) submitted to cluster 236903.
-----------------------------------------------------------------------

username@learn $ tail -f goatbrot.dag.dagman.out
07/27/22 15:35:32 (D_ALWAYS) ******************************************************
07/27/22 15:35:32 (D_ALWAYS) ** condor_scheduniv_exec.236903.0 (CONDOR_DAGMAN) STARTING UP
07/27/22 15:35:32 (D_ALWAYS) ** /usr/bin/condor_dagman
07/27/22 15:35:32 (D_ALWAYS) ** SubsystemInfo: name=DAGMAN type=DAGMAN(9) class=CLIENT(2)
07/27/22 15:35:32 (D_ALWAYS) ** Configuration: subsystem:DAGMAN local:<NONE> class:CLIENT
07/27/22 15:35:32 (D_ALWAYS) ** $CondorVersion: 9.11.0 2022-07-19 BuildID: 597572 PackageID: 9.11.0-0.597572 RC $
07/27/22 15:35:32 (D_ALWAYS) ** $CondorPlatform: x86_64_CentOS7 $
07/27/22 15:35:32 (D_ALWAYS) ** PID = 1653758
07/27/22 15:35:32 (D_ALWAYS) ** Log last touched 7/27 15:14:17
07/27/22 15:35:32 (D_ALWAYS) ******************************************************
07/27/22 15:35:32 (D_ALWAYS) Using config source: /etc/condor/condor_config
...
```

<span style="color:RED">**Here is where DAGMAN notices that there is a rescue DAG**</span>

```hl_lines="3"
07/27/22 15:35:32 (D_ALWAYS) Parsing 1 dagfiles
07/27/22 15:35:32 (D_ALWAYS) Parsing goatbrot.dag ...
07/27/22 15:35:32 (D_ALWAYS) Adjusting edges
07/27/22 15:35:32 (D_ALWAYS) Found rescue DAG number 1; running goatbrot.dag.rescue001 in combination with normal DAG file
07/27/22 15:35:32 (D_ALWAYS) ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
07/27/22 15:35:32 (D_ALWAYS) USING RESCUE DAG goatbrot.dag.rescue001
07/27/22 15:35:32 (D_ALWAYS) Dag contains 5 total jobs
```

<span style="color:RED">**Shortly thereafter it sees that four jobs have already finished.**</span>

```console
07/27/22 15:35:32 (D_ALWAYS) Bootstrapping...
07/27/22 15:35:32 (D_ALWAYS) Number of pre-completed nodes: 4
07/27/22 15:35:32 (D_ALWAYS) MultiLogFiles: truncating log file /home/username/osg/workflows/./goatbrot.dag.nodes.log
07/27/22 15:35:32 (D_ALWAYS) DAG status: 0 (DAG_STATUS_OK)
07/27/22 15:35:32 (D_ALWAYS) Of 5 nodes total:
07/27/22 15:35:32 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 15:35:32 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 15:35:32 (D_ALWAYS)     4       0        0       0       1          0        0
07/27/22 15:35:32 (D_ALWAYS) 0 job proc(s) currently held
07/27/22 15:35:32 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeCount = 0.0; EventCycleTimeSum = 0.0; LogProcessCycleTimeCount = 0.0; LogProcessCycleTimeSum = 0.0; SleepCycleTimeCount = 0.0; SleepCycleTimeSum = 0.0; SubmitCycleTimeCount = 0.0; SubmitCycleTimeSum = 0.0; ]
07/27/22 15:35:32 (D_ALWAYS) Registering condor_event_timer...
```

<span style="color:RED">**Here is where DAGMan resubmits the montage job and waits for it to complete.**</span>

```console
07/27/22 15:35:33 (D_ALWAYS) Submitting HTCondor Node montage job(s)...
07/27/22 15:35:33 (D_ALWAYS) Submitting node montage from file montage.sub using direct job submission
07/27/22 15:35:33 (D_ALWAYS) Submit warning: Submit:0:the line 'RETRY = 0' was unused by DAGMAN. Is it a typo?
|Submit:0:the line 'JOB = montage' was unused by DAGMAN. Is it a typo?
07/27/22 15:35:33 (D_ALWAYS) 	assigned HTCondor ID (236904.0.0)
07/27/22 15:35:33 (D_ALWAYS) Just submitted 1 job this cycle...
07/27/22 15:35:33 (D_ALWAYS) DAG status: 0 (DAG_STATUS_OK)
07/27/22 15:35:33 (D_ALWAYS) Of 5 nodes total:
07/27/22 15:35:33 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 15:35:33 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 15:35:33 (D_ALWAYS)     4       0        1       0       0          0        0
07/27/22 15:35:33 (D_ALWAYS) 0 job proc(s) currently held
07/27/22 15:35:33 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeCount = 0.0; EventCycleTimeSum = 0.0; LogProcessCycleTimeCount = 0.0; LogProcessCycleTimeSum = 0.0; SleepCycleTimeCount = 0.0; SleepCycleTimeSum = 0.0; SubmitCycleTimeAvg = 0.05305004119873047; SubmitCycleTimeCount = 1.0; SubmitCycleTimeMax = 0.05305004119873047; SubmitCycleTimeMin = 0.05305004119873047; SubmitCycleTimeStd = 0.05305004119873047; SubmitCycleTimeSum = 0.05305004119873047; ]
07/27/22 15:35:38 (D_ALWAYS) Currently monitoring 1 HTCondor log file(s)
07/27/22 15:35:38 (D_ALWAYS) Reassigning the id of job montage from (236904.0.0) to (236904.0.0)
07/27/22 15:35:38 (D_ALWAYS) Event: ULOG_SUBMIT for HTCondor Node montage (236904.0.0) {07/27/22 15:35:33}
07/27/22 15:35:38 (D_ALWAYS) Number of idle job procs: 1
07/27/22 15:37:29 (D_ALWAYS) Currently monitoring 1 HTCondor log file(s)
07/27/22 15:37:29 (D_ALWAYS) Event: ULOG_EXECUTE for HTCondor Node montage (236904.0.0) {07/27/22 15:37:28}
07/27/22 15:37:29 (D_ALWAYS) Number of idle job procs: 0
07/27/22 15:37:39 (D_ALWAYS) Currently monitoring 1 HTCondor log file(s)
07/27/22 15:37:39 (D_ALWAYS) Event: ULOG_JOB_TERMINATED for HTCondor Node montage (236904.0.0) {07/27/22 15:37:34}
```

<span style="color:RED">**This is where the montage finished.**</span>

```console
07/27/22 15:37:39 (D_ALWAYS) Node montage job proc (236904.0.0) completed successfully.
07/27/22 15:37:39 (D_ALWAYS) Node montage job completed
07/27/22 15:37:39 (D_ALWAYS) DAG status: 0 (DAG_STATUS_OK)
07/27/22 15:37:39 (D_ALWAYS) Of 5 nodes total:
07/27/22 15:37:39 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 15:37:39 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 15:37:39 (D_ALWAYS)     5       0        0       0       0          0        0
07/27/22 15:37:39 (D_ALWAYS) 0 job proc(s) currently held
```

<span style="color:RED">**And here DAGMan decides that the work is all done.**</span>

```console
07/27/22 15:37:39 (D_ALWAYS) All jobs Completed!
07/27/22 15:37:39 (D_ALWAYS) Note: 0 total job deferrals because of -MaxJobs limit (0)
07/27/22 15:37:39 (D_ALWAYS) Note: 0 total job deferrals because of -MaxIdle limit (1000)
07/27/22 15:37:39 (D_ALWAYS) Note: 0 total job deferrals because of node category throttles
07/27/22 15:37:39 (D_ALWAYS) Note: 0 total PRE script deferrals because of -MaxPre limit (20) or DEFER
07/27/22 15:37:39 (D_ALWAYS) Note: 0 total POST script deferrals because of -MaxPost limit (20) or DEFER
07/27/22 15:37:39 (D_ALWAYS) Note: 0 total HOLD script deferrals because of -MaxHold limit (20) or DEFER
07/27/22 15:37:39 (D_ALWAYS) DAG status: 0 (DAG_STATUS_OK)
07/27/22 15:37:39 (D_ALWAYS) Of 5 nodes total:
07/27/22 15:37:39 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 15:37:39 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 15:37:39 (D_ALWAYS)     5       0        0       0       0          0        0
07/27/22 15:37:39 (D_ALWAYS) 0 job proc(s) currently held
07/27/22 15:37:39 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeAvg = 0.002316570281982422; EventCycleTimeCount = 25.0; EventCycleTimeMax = 0.05394816398620605; EventCycleTimeMin = 3.695487976074219E-05; EventCycleTimeStd = 0.01076852495354839; EventCycleTimeSum = 0.05791425704956055; LogProcessCycleTimeAvg = 0.001371304194132487; LogProcessCycleTimeCount = 3.0; LogProcessCycleTimeMax = 0.002517938613891602; LogProcessCycleTimeMin = 0.0004379749298095703; LogProcessCycleTimeStd = 0.001056260644337736; LogProcessCycleTimeSum = 0.004113912582397461; SleepCycleTimeAvg = 5.005472593307495; SleepCycleTimeCount = 25.0; SleepCycleTimeMax = 5.005934000015259; SleepCycleTimeMin = 5.005262136459351; SleepCycleTimeStd = 0.0002209916994280902; SleepCycleTimeSum = 125.1368148326874; SubmitCycleTimeAvg = 0.002054883883549617; SubmitCycleTimeCount = 26.0; SubmitCycleTimeMax = 0.05305004119873047; SubmitCycleTimeMin = 1.311302185058594E-05; SubmitCycleTimeStd = 0.01040101214466667; SubmitCycleTimeSum = 0.05342698097229004; ]
07/27/22 15:37:39 (D_ALWAYS) Wrote metrics file goatbrot.dag.metrics.
07/27/22 15:37:39 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeAvg = 0.002316570281982422; EventCycleTimeCount = 25.0; EventCycleTimeMax = 0.05394816398620605; EventCycleTimeMin = 3.695487976074219E-05; EventCycleTimeStd = 0.01076852495354839; EventCycleTimeSum = 0.05791425704956055; LogProcessCycleTimeAvg = 0.001371304194132487; LogProcessCycleTimeCount = 3.0; LogProcessCycleTimeMax = 0.002517938613891602; LogProcessCycleTimeMin = 0.0004379749298095703; LogProcessCycleTimeStd = 0.001056260644337736; LogProcessCycleTimeSum = 0.004113912582397461; SleepCycleTimeAvg = 5.005472593307495; SleepCycleTimeCount = 25.0; SleepCycleTimeMax = 5.005934000015259; SleepCycleTimeMin = 5.005262136459351; SleepCycleTimeStd = 0.0002209916994280902; SleepCycleTimeSum = 125.1368148326874; SubmitCycleTimeAvg = 0.002054883883549617; SubmitCycleTimeCount = 26.0; SubmitCycleTimeMax = 0.05305004119873047; SubmitCycleTimeMin = 1.311302185058594E-05; SubmitCycleTimeStd = 0.01040101214466667; SubmitCycleTimeSum = 0.05342698097229004; ]
07/27/22 15:37:39 (D_ALWAYS) **** condor_scheduniv_exec.236903.0 (condor_DAGMAN) pid 1653758 EXITING WITH STATUS 0
```

Success! Now go ahead and clean up.

Bonus Challenge
---------------

If you have time, add an extra node to the DAG. Copy our original "simple" program, but make it exit with a 1 instead of a 0. DAGMan would consider this a failure, but you'll tell DAGMan that it's really a success. This is reasonable--many real world programs use a variety of return codes, and you might need to help DAGMan distinguish success from failure.

Write a POST script that checks the return value. Check [the HTCondor manual](https://htcondor.readthedocs.io/en/latest/users-manual/dagman-workflows.html#script) to see how to describe your post script.

