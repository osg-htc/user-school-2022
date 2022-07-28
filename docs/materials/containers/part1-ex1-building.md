---
status: testing
---

<style type="text/css">
  pre em { font-style: normal; background-color: yellow; }
  pre strong { font-style: normal; font-weight: bold; color: \#008; }
</style>

# Containers/GPUs Exercise 1.1: Building a Singularity Container

The objective of this exercise is to learn the very basics of building your
first Singularity image.


## Getting a Sylabs Cloud Account

In this exercise we will use Singularity's equivalent of DockerHub,
called Sylabs Cloud, to build and publish our image.  To get started,
first sign up for a free account at
[https://cloud.sylabs.io/](https://cloud.sylabs.io/)

## Image Definition File

The image definition file contains the instructions on how to build
the image. Usually there is a base image, and a set of commmands
which will set up the environment as you want it. Examples
of commands include:

  * Package system tools (apt/yum/...) to install/upgrade specific
    packages
  * Langauage package tools (pip/...) to install language specific
    modules
  * Steps to build software from source

You can find a full description of the image defintion format
in the [Singularity manual](https://docs.sylabs.io/guides/3.8/user-guide/definition_files.html).

In Sylabs Cloud, click on `Remote Builder` in the top navigation bar.
Remove the default definition file, and paste the following in:

``` file
Bootstrap: docker
From: opensciencegrid/osgvo-ubuntu-20.04:latest

%post
    apt-get update -y
    apt-get install -y \
            python3-pip \
            python3-cowsay
    python3 -m pip install numpy
```

This definition file installs a few packages from apt (that is,
Ubuntu prepared packages), and one package via pip. Using
package from the vendor is great as those packages are
generally well tested. However, not everything is packaged
by the OS vendors, and sometimes you need specific versions
of a package and then using language tools like pip, or 
building from source, can be good solutions.

The definition file above has two problems we have to
address before building. The first problem is that there
is no Ubuntu package for `python3-cowsay`. The second
problem (albeit minor), is that numpy might be better
installed with apt. The fix here is to swap and the names
of those two. Replace `python3-cowsay` with 
`python3-numpy` and replace `numpy` with `cowsay`.
The final definition file should look like:


``` file
Bootstrap: docker
From: opensciencegrid/osgvo-ubuntu-20.04:latest

%post
    apt-get update -y
    apt-get install -y \
            python3-pip \
            python3-numpy
    python3 -m pip install cowsay
```

The last step is to name the *Repository*. The naming should
start with your project, a slash, and then a name of your
choosing. For example, if your project field says `bob`,
you could name the repository `bob/first-image`.

Now hit `Build` and watch the image build.

Once the build is complete, the repository should be changed
to "public". This is important as it will allow us to
download the image directly to login04/login05 in the 
next exercise. Click on `Dashboard` on the top, find
your repository in the list, and click it. In the middle
you will see a `Project Visibility` box. Click 
`Make Public`.




