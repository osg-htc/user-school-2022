---
status: testing
---

<style type="text/css">
  pre em { font-style: normal; background-color: yellow; }
  pre strong { font-style: normal; font-weight: bold; color: \#008; }
</style>

# Workflows Exercise 1.1: Coordinating a Set of Jobs With a Simple DAG

The objective of this exercise is to learn the very basics of running a set of jobs as a single DAG workflow. We'll
first show what it looks like to submit a workflow that contains a single job..

## What is DAGMan?

In short, DAGMan lets you submit complex sequences of jobs as long as they can be expressed as a directed acylic graph.
For example, you may wish to run a large parameter sweep but before the sweep run you need to prepare your data.  After
the sweep runs, you need to collate the results.  This might look like this, assuming you want to sweep over five
parameters:

![simple DAG](files/osgus22-workflows-part1-ex1-simple-dag.gif)

DAGMan has many abilities, such as throttling jobs, recovery from failures, and more.  More information about DAGMan can
be found at [in the HTCondor manual](https://htcondor.readthedocs.io/en/latest/users-manual/dagman-workflows.html).

## Submitting a Simple DAG

For our job, we will return briefly to the `sleep` program.

``` file
executable              = /bin/sleep
arguments               = 4
log                     = simple.log
output                  = simple.out
error                   = simple.error
request_memory = 1GB
request_disk = 1GB
request_cpus = 1
queue
```

We are going to get a bit more sophisticated in submitting our jobs now.  Let's have three windows open.  In one window,
you'll submit the job.  In another you will watch the queue, and in the third you will watch what DAGMan does.

First we will create the most minimal DAG that can be created: a DAG with just one node.  Put this into a file named
`simple.dag`.

``` file
Job Simple simple.sub
```

In your first window, submit the DAG:

``` console
username@learn $ condor_submit_dag simple.dag
-----------------------------------------------------------------------
File for submitting this DAG to HTCondor           : simple.dag.condor.sub
Log of DAGMan debugging messages                 : simple.dag.dagman.out
Log of HTCondor library output                     : simple.dag.lib.out
Log of HTCondor library error messages             : simple.dag.lib.err
Log of the life of condor_dagman itself          : simple.dag.dagman.log

Submitting job(s).
1 job(s) submitted to cluster 236874.
-----------------------------------------------------------------------
```

In the second window, check the queue (what you see may be slightly different):

``` console
username@learn $ condor_q -nobatch -wide:80

-- Schedd: learn.chtc.wisc.edu : <128.104.100.148:9618?... @ 07/27/22 14:53:44
 ID        OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD
236874.0   username        7/27 14:53   0+00:00:13 R  0    0.4 condor_dagman -p
236875.0   username        7/27 14:53   0+00:00:00 I  0    0.0 sleep 4

2 jobs; 0 completed, 0 removed, 1 idle, 1 running, 0 held, 0 suspended
```

In the third window, watch what DAGMan does (what you see may be slightly different):

``` console
username@learn $ tail -f --lines=500 simple.dag.dagman.out
07/27/22 14:53:31 (D_ALWAYS) ******************************************************
07/27/22 14:53:31 (D_ALWAYS) ** condor_scheduniv_exec.236874.0 (CONDOR_DAGMAN) STARTING UP
07/27/22 14:53:31 (D_ALWAYS) ** /usr/bin/condor_dagman
07/27/22 14:53:31 (D_ALWAYS) ** SubsystemInfo: name=DAGMAN type=DAGMAN(9) class=CLIENT(2)
07/27/22 14:53:31 (D_ALWAYS) ** Configuration: subsystem:DAGMAN local:<NONE> class:CLIENT
07/27/22 14:53:31 (D_ALWAYS) ** $CondorVersion: 9.11.0 2022-07-19 BuildID: 597572 PackageID: 9.11.0-0.597572 RC $
07/27/22 14:53:31 (D_ALWAYS) ** $CondorPlatform: x86_64_CentOS7 $
07/27/22 14:53:31 (D_ALWAYS) ** PID = 1650410
07/27/22 14:53:31 (D_ALWAYS) ** Log last touched time unavailable (No such file or directory)
07/27/22 14:53:31 (D_ALWAYS) ******************************************************
07/27/22 14:53:31 (D_ALWAYS) Using config source: /etc/condor/condor_config
07/27/22 14:53:31 (D_ALWAYS) Using local config sources:
07/27/22 14:53:31 (D_ALWAYS)    /var/run/condor/pids.conf
07/27/22 14:53:31 (D_ALWAYS)    /var/run/condor/master_pid_6051.conf
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/condor_config.principals
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/condor_config.hosts
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/condor_config.global
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/condor_config.policy
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/condor_config.x86_64_CentOS7
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/condor_config.security
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/hosts/learn.chtc.wisc.edu.local
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/include/schedd.conf
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/include/facility_wid.conf
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/include/scitokens_credmon.conf
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/include/owned_by_chtc.conf
07/27/22 14:53:31 (D_ALWAYS)    /var/run/condor/condor_config.startd_offline_gpus
07/27/22 14:53:31 (D_ALWAYS)    /etc/condor/condor_config.flightworthy_testing
07/27/22 14:53:31 (D_ALWAYS) config Macros = 365, Sorted = 365, StringBytes = 85642, TablesBytes = 13292
07/27/22 14:53:31 (D_ALWAYS) CLASSAD_CACHING is ENABLED
07/27/22 14:53:31 (D_ALWAYS) Daemon Log is logging: D_ALWAYS D_ERROR D_STATUS
07/27/22 14:53:31 (D_ALWAYS) DaemonCore: No command port requested.
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_USE_STRICT setting: 1
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_VERBOSITY setting: 3
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_DEBUG_CACHE_SIZE setting: 5242880
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_DEBUG_CACHE_ENABLE setting: False
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_SUBMIT_DELAY setting: 0
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MAX_SUBMIT_ATTEMPTS setting: 6
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_STARTUP_CYCLE_DETECT setting: False
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MAX_SUBMITS_PER_INTERVAL setting: 100
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_AGGRESSIVE_SUBMIT setting: False
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_USER_LOG_SCAN_INTERVAL setting: 5
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_QUEUE_UPDATE_INTERVAL setting: 300
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_DEFAULT_PRIORITY setting: 0
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_SUPPRESS_NOTIFICATION setting: True
07/27/22 14:53:31 (D_ALWAYS) allow_events (DAGMAN_ALLOW_EVENTS) setting: 114
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_RETRY_SUBMIT_FIRST setting: True
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_RETRY_NODE_FIRST setting: False
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MAX_JOBS_IDLE setting: 1000
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MAX_JOBS_SUBMITTED setting: 0
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MAX_PRE_SCRIPTS setting: 20
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MAX_POST_SCRIPTS setting: 20
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MAX_HOLD_SCRIPTS setting: 20
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MUNGE_NODE_NAMES setting: True
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_PROHIBIT_MULTI_JOBS setting: False
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_SUBMIT_DEPTH_FIRST setting: False
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_ALWAYS_RUN_POST setting: False
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_CONDOR_SUBMIT_EXE setting: /usr/bin/condor_submit
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_USE_DIRECT_SUBMIT setting: True
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_DEFAULT_APPEND_VARS setting: False
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_ABORT_DUPLICATES setting: True
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_ABORT_ON_SCARY_SUBMIT setting: True
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_PENDING_REPORT_INTERVAL setting: 600
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_AUTO_RESCUE setting: True
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MAX_RESCUE_NUM setting: 100
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_WRITE_PARTIAL_RESCUE setting: True
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_DEFAULT_NODE_LOG setting: @(DAG_DIR)/@(DAG_FILE).nodes.log
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_GENERATE_SUBDAG_SUBMITS setting: True
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_MAX_JOB_HOLDS setting: 100
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_HOLD_CLAIM_TIME setting: 20
07/27/22 14:53:31 (D_ALWAYS) ALL_DEBUG setting: D_CAT
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_DEBUG setting:
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_SUPPRESS_JOB_LOGS setting: False
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_REMOVE_NODE_JOBS setting: True
07/27/22 14:53:31 (D_ALWAYS) DAGMAN will adjust edges after parsing
07/27/22 14:53:31 (D_ALWAYS) argv[0] == "condor_scheduniv_exec.236874.0"
07/27/22 14:53:31 (D_ALWAYS) argv[1] == "-Lockfile"
07/27/22 14:53:31 (D_ALWAYS) argv[2] == "simple.dag.lock"
07/27/22 14:53:31 (D_ALWAYS) argv[3] == "-AutoRescue"
07/27/22 14:53:31 (D_ALWAYS) argv[4] == "1"
07/27/22 14:53:31 (D_ALWAYS) argv[5] == "-DoRescueFrom"
07/27/22 14:53:31 (D_ALWAYS) argv[6] == "0"
07/27/22 14:53:31 (D_ALWAYS) argv[7] == "-Dag"
07/27/22 14:53:31 (D_ALWAYS) argv[8] == "simple.dag"
07/27/22 14:53:31 (D_ALWAYS) argv[9] == "-Suppress_notification"
07/27/22 14:53:31 (D_ALWAYS) argv[10] == "-CsdVersion"
07/27/22 14:53:31 (D_ALWAYS) argv[11] == "$CondorVersion: 9.11.0 2022-07-19 BuildID: 597572 PackageID: 9.11.0-0.597572 RC $"
07/27/22 14:53:31 (D_ALWAYS) argv[12] == "-Dagman"
07/27/22 14:53:31 (D_ALWAYS) argv[13] == "/usr/bin/condor_dagman"
07/27/22 14:53:31 (D_ALWAYS) Workflow batch-id: <236874.0>
07/27/22 14:53:31 (D_ALWAYS) Workflow batch-name: <simple.dag+236874>
07/27/22 14:53:31 (D_ALWAYS) Workflow accounting_group: <>
07/27/22 14:53:31 (D_ALWAYS) Workflow accounting_group_user: <>
07/27/22 14:53:31 (D_ALWAYS) Warning: failed to get attribute DAGNodeName
07/27/22 14:53:31 (D_ALWAYS) DAGMAN_LOG_ON_NFS_IS_ERROR setting: False
07/27/22 14:53:31 (D_ALWAYS) Default node log file is: </home/username/osg/workflows/./simple.dag.nodes.log>
07/27/22 14:53:31 (D_ALWAYS) DAG Lockfile will be written to simple.dag.lock
07/27/22 14:53:31 (D_ALWAYS) DAG Input file is simple.dag
07/27/22 14:53:31 (D_ALWAYS) Parsing 1 dagfiles
07/27/22 14:53:31 (D_ALWAYS) Parsing simple.dag ...
07/27/22 14:53:31 (D_ALWAYS) Adjusting edges
07/27/22 14:53:31 (D_ALWAYS) Dag contains 1 total jobs
07/27/22 14:53:31 (D_ALWAYS) Bootstrapping...
07/27/22 14:53:31 (D_ALWAYS) Number of pre-completed nodes: 0
07/27/22 14:53:31 (D_ALWAYS) MultiLogFiles: truncating log file /home/username/osg/workflows/./simple.dag.nodes.log
07/27/22 14:53:31 (D_ALWAYS) DAG status: 0 (DAG_STATUS_OK)
07/27/22 14:53:31 (D_ALWAYS) Of 1 nodes total:
07/27/22 14:53:31 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 14:53:31 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 14:53:31 (D_ALWAYS)     0       0        0       0       1          0        0
07/27/22 14:53:31 (D_ALWAYS) 0 job proc(s) currently held
07/27/22 14:53:31 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeCount = 0.0; EventCycleTimeSum = 0.0; LogProcessCycleTimeCount = 0.0; LogProcessCycleTimeSum = 0.0; SleepCycleTimeCount = 0.0; SleepCycleTimeSum = 0.0; SubmitCycleTimeCount = 0.0; SubmitCycleTimeSum = 0.0; ]
07/27/22 14:53:31 (D_ALWAYS) Registering condor_event_timer...
07/27/22 14:53:32 (D_ALWAYS) Submitting HTCondor Node Simple job(s)...
```

<span style="color:RED">**Here's where the job is submitted**</span>

```file
07/27/22 14:53:32 (D_ALWAYS) Submitting node Simple from file simple.sub using direct job submission
07/27/22 14:53:32 (D_ALWAYS) Submit warning: Submit:0:the line 'RETRY = 0' was unused by DAGMAN. Is it a typo?
|Submit:0:the line 'JOB = Simple' was unused by DAGMAN. Is it a typo?
07/27/22 14:53:32 (D_ALWAYS) 	assigned HTCondor ID (236875.0.0)
07/27/22 14:53:32 (D_ALWAYS) Just submitted 1 job this cycle...
07/27/22 14:53:32 (D_ALWAYS) DAG status: 0 (DAG_STATUS_OK)
07/27/22 14:53:32 (D_ALWAYS) Of 1 nodes total:
07/27/22 14:53:32 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 14:53:32 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 14:53:32 (D_ALWAYS)     0       0        1       0       0          0        0
07/27/22 14:53:32 (D_ALWAYS) 0 job proc(s) currently held
07/27/22 14:53:32 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeCount = 0.0; EventCycleTimeSum = 0.0; LogProcessCycleTimeCount = 0.0; LogProcessCycleTimeSum = 0.0; SleepCycleTimeCount = 0.0; SleepCycleTimeSum = 0.0; SubmitCycleTimeAvg = 0.05731511116027832; SubmitCycleTimeCount = 1.0; SubmitCycleTimeMax = 0.05731511116027832; SubmitCycleTimeMin = 0.05731511116027832; SubmitCycleTimeStd = 0.05731511116027832; SubmitCycleTimeSum = 0.05731511116027832; ]
07/27/22 14:53:37 (D_ALWAYS) Currently monitoring 1 HTCondor log file(s)
```

<span style="color:RED">**Here's where DAGMan noticed that the job is running**</span>

```file
07/27/22 14:55:22 (D_ALWAYS) Event: ULOG_EXECUTE for HTCondor Node Simple (236875.0.0) {07/27/22 14:55:18}
07/27/22 14:55:22 (D_ALWAYS) Number of idle job procs: 0
```

<span style="color:RED">**Here's where DAGMan noticed that the job finished.**</span>

```file
07/27/22 14:55:22 (D_ALWAYS) Event: ULOG_JOB_TERMINATED for HTCondor Node Simple (236875.0.0) {07/27/22 14:55:22}
07/27/22 14:55:22 (D_ALWAYS) Number of idle job procs: 0
07/27/22 14:55:22 (D_ALWAYS) Node Simple job proc (236875.0.0) completed successfully.
07/27/22 14:55:22 (D_ALWAYS) Node Simple job completed
07/27/22 14:55:22 (D_ALWAYS) DAG status: 0 (DAG_STATUS_OK)
07/27/22 14:55:22 (D_ALWAYS) Of 1 nodes total:
07/27/22 14:55:22 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 14:55:22 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 14:55:22 (D_ALWAYS)     1       0        0       0       0          0        0
07/27/22 14:55:22 (D_ALWAYS) 0 job proc(s) currently held
07/27/22 14:55:22 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeAvg = 0.002710472453724254; EventCycleTimeCount = 22.0; EventCycleTimeMax = 0.05800700187683105; EventCycleTimeMin = 3.695487976074219E-05; EventCycleTimeStd = 0.01235157343544432; EventCycleTimeSum = 0.05963039398193359; LogProcessCycleTimeAvg = 0.0008540153503417969; LogProcessCycleTimeCount = 2.0; LogProcessCycleTimeMax = 0.0009989738464355469; LogProcessCycleTimeMin = 0.0007090568542480469; LogProcessCycleTimeStd = 0.0002050022711569886; LogProcessCycleTimeSum = 0.001708030700683594; SleepCycleTimeAvg = 5.00527053529566; SleepCycleTimeCount = 22.0; SleepCycleTimeMax = 5.005910873413086; SleepCycleTimeMin = 5.00182318687439; SleepCycleTimeStd = 0.0007872599017242631; SleepCycleTimeSum = 110.1159517765045; SubmitCycleTimeAvg = 0.002509210420691449; SubmitCycleTimeCount = 23.0; SubmitCycleTimeMax = 0.05731511116027832; SubmitCycleTimeMin = 1.382827758789062E-05; SubmitCycleTimeStd = 0.01194727151749822; SubmitCycleTimeSum = 0.05771183967590332; ]
```

<span style="color:RED">**Here's where DAGMan noticed that all the work is done.**</span>

```file
07/27/22 14:55:22 (D_ALWAYS) All jobs Completed!
07/27/22 14:55:22 (D_ALWAYS) Note: 0 total job deferrals because of -MaxJobs limit (0)
07/27/22 14:55:22 (D_ALWAYS) Note: 0 total job deferrals because of -MaxIdle limit (1000)
07/27/22 14:55:22 (D_ALWAYS) Note: 0 total job deferrals because of node category throttles
07/27/22 14:55:22 (D_ALWAYS) Note: 0 total PRE script deferrals because of -MaxPre limit (20) or DEFER
07/27/22 14:55:22 (D_ALWAYS) Note: 0 total POST script deferrals because of -MaxPost limit (20) or DEFER
07/27/22 14:55:22 (D_ALWAYS) Note: 0 total HOLD script deferrals because of -MaxHold limit (20) or DEFER
07/27/22 14:55:22 (D_ALWAYS) DAG status: 0 (DAG_STATUS_OK)
07/27/22 14:55:22 (D_ALWAYS) Of 1 nodes total:
07/27/22 14:55:22 (D_ALWAYS)  Done     Pre   Queued    Post   Ready   Un-Ready   Failed
07/27/22 14:55:22 (D_ALWAYS)   ===     ===      ===     ===     ===        ===      ===
07/27/22 14:55:22 (D_ALWAYS)     1       0        0       0       0          0        0
07/27/22 14:55:22 (D_ALWAYS) 0 job proc(s) currently held
07/27/22 14:55:22 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeAvg = 0.002710472453724254; EventCycleTimeCount = 22.0; EventCycleTimeMax = 0.05800700187683105; EventCycleTimeMin = 3.695487976074219E-05; EventCycleTimeStd = 0.01235157343544432; EventCycleTimeSum = 0.05963039398193359; LogProcessCycleTimeAvg = 0.0008540153503417969; LogProcessCycleTimeCount = 2.0; LogProcessCycleTimeMax = 0.0009989738464355469; LogProcessCycleTimeMin = 0.0007090568542480469; LogProcessCycleTimeStd = 0.0002050022711569886; LogProcessCycleTimeSum = 0.001708030700683594; SleepCycleTimeAvg = 5.00527053529566; SleepCycleTimeCount = 22.0; SleepCycleTimeMax = 5.005910873413086; SleepCycleTimeMin = 5.00182318687439; SleepCycleTimeStd = 0.0007872599017242631; SleepCycleTimeSum = 110.1159517765045; SubmitCycleTimeAvg = 0.002509210420691449; SubmitCycleTimeCount = 23.0; SubmitCycleTimeMax = 0.05731511116027832; SubmitCycleTimeMin = 1.382827758789062E-05; SubmitCycleTimeStd = 0.01194727151749822; SubmitCycleTimeSum = 0.05771183967590332; ]
07/27/22 14:55:22 (D_ALWAYS) Wrote metrics file simple.dag.metrics.
07/27/22 14:55:22 (D_ALWAYS) DAGMan Runtime Statistics: [ EventCycleTimeAvg = 0.002710472453724254; EventCycleTimeCount = 22.0; EventCycleTimeMax = 0.05800700187683105; EventCycleTimeMin = 3.695487976074219E-05; EventCycleTimeStd = 0.01235157343544432; EventCycleTimeSum = 0.05963039398193359; LogProcessCycleTimeAvg = 0.0008540153503417969; LogProcessCycleTimeCount = 2.0; LogProcessCycleTimeMax = 0.0009989738464355469; LogProcessCycleTimeMin = 0.0007090568542480469; LogProcessCycleTimeStd = 0.0002050022711569886; LogProcessCycleTimeSum = 0.001708030700683594; SleepCycleTimeAvg = 5.00527053529566; SleepCycleTimeCount = 22.0; SleepCycleTimeMax = 5.005910873413086; SleepCycleTimeMin = 5.00182318687439; SleepCycleTimeStd = 0.0007872599017242631; SleepCycleTimeSum = 110.1159517765045; SubmitCycleTimeAvg = 0.002509210420691449; SubmitCycleTimeCount = 23.0; SubmitCycleTimeMax = 0.05731511116027832; SubmitCycleTimeMin = 1.382827758789062E-05; SubmitCycleTimeStd = 0.01194727151749822; SubmitCycleTimeSum = 0.05771183967590332; ]
07/27/22 14:55:22 (D_ALWAYS) **** condor_scheduniv_exec.236874.0 (condor_DAGMAN) pid 1650410 EXITING WITH STATUS 0
```

Now verify your results:

``` console
username@learn $ cat simple.log
000 (236875.000.000) 2022-07-27 14:53:32 Job submitted from host: <128.104.100.148:9618?addrs=128.104.100.148-9618+[2607-f388-107c-501-216-3eff-feb2-bc11]-9618&alias=learn.chtc.wisc.edu&noUDP&sock=schedd_6051_0fa9>
    DAG Node: Simple
...
040 (236875.000.000) 2022-07-27 14:55:17 Started transferring input files
	Transferring to host: <128.104.58.123:9618?addrs=128.104.58.123-9618+[2607-f388-1086-0-67d-7bff-fe41-62ea]-9618&alias=e1540.chtc.wisc.edu&noUDP&sock=slot1_4_9756_bc06_56915>
...
040 (236875.000.000) 2022-07-27 14:55:17 Finished transferring input files
...
001 (236875.000.000) 2022-07-27 14:55:18 Job executing on host: <128.104.58.123:9618?addrs=128.104.58.123-9618+[2607-f388-1086-0-67d-7bff-fe41-62ea]-9618&alias=e1540.chtc.wisc.edu&noUDP&sock=startd_7130_2a59>
...
006 (236875.000.000) 2022-07-27 14:55:22 Image size of job updated: 35
	0  -  MemoryUsage of job (MB)
	0  -  ResidentSetSize of job (KB)
...
040 (236875.000.000) 2022-07-27 14:55:22 Started transferring output files
...
040 (236875.000.000) 2022-07-27 14:55:22 Finished transferring output files
...
005 (236875.000.000) 2022-07-27 14:55:22 Job terminated.
	(1) Normal termination (return value 0)
		Usr 0 00:00:00, Sys 0 00:00:00  -  Run Remote Usage
		Usr 0 00:00:00, Sys 0 00:00:00  -  Run Local Usage
		Usr 0 00:00:00, Sys 0 00:00:00  -  Total Remote Usage
		Usr 0 00:00:00, Sys 0 00:00:00  -  Total Local Usage
	0  -  Run Bytes Sent By Job
	33128  -  Run Bytes Received By Job
	0  -  Total Bytes Sent By Job
	33128  -  Total Bytes Received By Job
	Partitionable Resources :    Usage  Request Allocated
	   Cpus                 :                 1         1
	   Disk (KB)            :       49  1048576   1336536
	   IoHeavy              :                           0
	   Memory (MB)          :        0     1024      1024

	Job terminated of its own accord at 2022-07-27T19:55:22Z with exit-code 0.
...
```

Looking at DAGMan's various files, we see that DAGMan itself ran as a job (specifically, a "scheduler" universe job).

``` console
username@learn $ ls simple.dag.*
simple.dag.condor.sub  simple.dag.dagman.out  simple.dag.lib.out  simple.dag.nodes.log
simple.dag.dagman.log  simple.dag.lib.err     simple.dag.metrics

username@learn $ cat simple.dag.condor.sub
# Filename: simple.dag.condor.sub
# Generated by condor_submit_dag simple.dag
universe	= scheduler
executable	= /usr/bin/condor_dagman
getenv		= True
output		= simple.dag.lib.out
error		= simple.dag.lib.err
log		= simple.dag.dagman.log
remove_kill_sig	= SIGUSR1
+OtherJobRemoveRequirements	= "DAGManJobId =?= $(cluster)"
# Note: default on_exit_remove expression:
# ( ExitSignal =?= 11 || (ExitCode =!= UNDEFINED && ExitCode >=0 && ExitCode <= 2))
# attempts to ensure that DAGMan is automatically
# requeued by the schedd if it exits abnormally or
# is killed (e.g., during a reboot).
on_exit_remove	= (ExitSignal =?= 11 || (ExitCode =!= UNDEFINED && ExitCode >=0 && ExitCode <= 2))
copy_to_spool	= False
arguments	= "-p 0 -f -l . -Lockfile simple.dag.lock -AutoRescue 1 -DoRescueFrom 0 -Dag simple.dag -Suppress_notification -CsdVersion $CondorVersion:' '9.11.0' '2022-07-19' 'BuildID:' '597572' 'PackageID:' '9.11.0-0.597572' 'RC' '$ -Dagman /usr/bin/condor_dagman"
environment	= _CONDOR_SCHEDD_ADDRESS_FILE=/var/lib/condor/spool/.schedd_address;_CONDOR_MAX_DAGMAN_LOG=0;_CONDOR_SCHEDD_DAEMON_AD_FILE=/var/lib/condor/spool/.schedd_classad;_CONDOR_DAGMAN_LOG=simple.dag.dagman.out
queue
```

If you want to clean up some of these files (you may not want to, at least not yet), run:

``` console
username@learn $ rm simple.dag.*
```

## Challenge

- What is the scheduler universe? Why does DAGMan use it?
