---
status: testing
---

<style type="text/css">
  pre em { font-style: normal; background-color: yellow; }
  pre strong { font-style: normal; font-weight: bold; color: \#008; }
</style>

# Containers/GPUs Exercise 1.3: Running a GPU Job

The objective of this exercise is to run a GOU job on OSG.

GPU software stacks are usually more complex, more difficult to
build, and more version sensitive than CPU-only jobs. We recommend
you start with good base images when creating GPU images.

In this exercise we will use the popular TensorFlow package.
[https://www.tensorflow.org/](https://www.tensorflow.org/) desribes TensorFlow as:

> TensorFlow is an open source software library for numerical
> computation using data flow graphs. Nodes in the graph represent
> mathematical operations, while the graph edges represent the
> multidimensional data arrays (tensors) communicated between them. The
> flexible architecture allows you to deploy computation to one or more
> CPUs or GPUs in a desktop, server, or mobile device with a single
> API. TensorFlow was originally developed by researchers and engineers
> working on the Google Brain Team within Google's Machine Intelligence
> research organization for the purposes of conducting machine learning
> and deep neural networks research, but the system is general enough to
> be applicable in a wide variety of other domains as well.

To use TensorFlow, we need a small sample program. We will use a
sample code from the TensorFlow tutorials. Create a .py file with the
following content:

``` file
#!/usr/bin/python3

# http://learningtensorflow.com/lesson10/

import sys
import numpy as np
import tensorflow as tf
from datetime import datetime

tf.debugging.set_log_device_placement(True)

# Create some tensors
a = tf.constant([[1.0, 2.0, 3.0], [4.0, 5.0, 6.0]])
b = tf.constant([[1.0, 2.0], [3.0, 4.0], [5.0, 6.0]])
c = tf.matmul(a, b)

print(c)
```

You can run this on the sumbmit node. You can use `singularity exec`
to execute the code directly, using the TensorFlow image:

``` console
$ singularity exec /cvmfs/singularity.opensciencegrid.org/opensciencegrid/tensorflow-gpu:2.2-cuda-10.1 ./test.py
```

There is a lot of output, but somewhere close to end, TensorFlow will
tell you what hardware it found an (hint: look for "CPU"):

``` file
...Executing op MatMul in device /job:localhost/replica:0/task:0/device:CPU:0
```

## GPU Job

Running GPU jobs on OSG is easy! Most of the time, the only additional
attribute we need to set is `request_gpus = 1`. GPU jobs still need
a CPU, so the section in the submit file should look something like:

``` file
request_cpus = 1
request_gpus = 1
request_memory = 2GB
request_disk = 5GB
```

And we need specify the image to use:

``` file
+SingularityImage = "/cvmfs/singularity.opensciencegrid.org/opensciencegrid/tensorflow-gpu:2.2-cuda-10.1"
```

For TensorFlow, we also recommend a few extra requirements due to how
TensorFlow was built:

``` file
requirements = HAS_AVX2 == True && OSG_HOST_KERNEL_VERSION >= 31000
```

Create a submit file using these attributes, and try running the job. If
successful, you should now see it ran on GPU:

``` file
...Executing op MatMul in device /job:localhost/replica:0/task:0/device:GPU:0
```


