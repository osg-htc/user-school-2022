---
status: testing
---

<style type="text/css">
  pre em { font-style: normal; background-color: yellow; }
  pre strong { font-style: normal; font-weight: bold; color: \#008; }
</style>

# Containers/GPUs Exercise 1.2: Running a Singularity Job

The objective of this exercise is to run a Singularity job on OSG.
Several concepts from earlier in the week will be used.


## Downloading the Image

For this exercise, we need a downloaded image (.sif file) of
the image we build in the previous exercise. Navigate back
to [https://cloud.sylabs.io/](https://cloud.sylabs.io/) and
find your image in the repository. On that page, there will be 
a `singularity pull` command listed. Run that command on 
`login05.osgconnect.net`. Example:


``` console
$ singularity pull library://bob/bob/first-image
INFO:    Downloading library image
703.1MiB / 703.1MiB [==========================================================] 100 % 59.9 MiB/s 0s
$ ls *.sif
first-image.sif
```

If you get a persmission denied, you probably forgot to make the
image public in the previous step.


## Testing the Image Locally

You can test the image on the access point by using
`singularity shell`. This will drop you in to the container, and
automatically mount $HOME so that you can access your files, which
is really nice when you have to test or build inside the container
environment, but want to keep the files after exiting the 
container. Run:

``` console
$ singularity shell first-image.sif
```

You should see the prompt change to indicate that you are inside
the container. Let's test our new container by starting Python,
import cowsay, and use the cowsay library:

``` console
Singularity> python3
Python 3.8.10 (default, Sep 28 2021, 16:10:42) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cowsay
>>> cowsay.cow('Hello OSG User School!')
  ______________________
| Hello OSG User School! |
  ======================
                      \
                       \
                         ^__^
                         (oo)\_______
                         (__)\       )\/\
                             ||----w |
                             ||     ||
```

You can exit python and the container by pressing Ctrl-D
twice.


## Putting Together a Job

At this point, you should have put together enough jobs to make this
familiar. First we need an executable. Name this file `cowsay.py`:

``` file
#!/usr/bin/python3

import cowsay
cowsay.cow('OSG User School')
```

We then need a submit file - the only real differenc in this one
is the addition of `+SingularityImage` which specifies which image
to use:

```
+SingularityImage = "./first-image.sif"

request_cpus = 1
request_memory = 1GB
request_disk = 5GB

executable   = cowsay.py
output       = $(Cluster).$(Process).out
error        = $(Cluster).$(Process).err
log          = $(Cluster).log

queue 1
```

Run the job and verify the output.

You an also try running the job without the `+SingularityImage` line and see what happens.



