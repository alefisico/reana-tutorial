---
title: Introduction
---

This tutorial is your comprehensive guide to workflow automation. You'll learn:

 * The fundamentals of [Snakemake](https://snakemake.readthedocs.io/en/stable/) and its role in streamlining data analysis pipelines
 * How to construct and execute basic Snakemake workflows locally
 * The benefits of cloud-based workflow execution with the [REANA](https://reana.cern.ch/) platform
 * How to migrate your local Snakemake workflows to the REANA environment

To set up Snakemake and REANA packages, you can follow these instructions.


## Snakemake setup

To install snakemake [here](https://snakemake.readthedocs.io/en/stable/getting_started/installation.html) is the official documentation on how to install it in your machine. Since we will run some jobs locally in your machine, we can use snakemake as:

::::::::::::::::::::::::::::::::::::::: discussion

### Recommended

We can use singularity and the official snakemake container to run our jobs. This is the simplest solution if you dont want/can install the official package in your machine. Other advantage is that one can keep the analysis environment clean this way.

An example in lxplus:
```BASH
export APPTAINER_BINDPATH=/afs,/eos,/cvmfs,/cvmfs/grid.cern.ch/etc/grid-security:/etc/grid-security  ## this is optional (if needed)
apptainer shell -B ${PWD}:/srv --pwd /srv docker://snakemake/snakemake /bin/bash
```
One can include these lines in a bash script.

:::::::::::::::: spoiler

### Simpler solution

Another simpler solution is to include a bash function in your `.bashrc` or `.bash_aliases`. For `.bash_aliases`:
```BASH
run_snakemake ()
{ 
    local base_command="export APPTAINER_BINDPATH=/afs,/eos,/cvmfs,/cvmfs/grid.cern.ch/etc/grid-security:/etc/grid-security && apptainer exec -B ${PWD}:/srv --pwd /srv docker://snakemake/snakemake";
    if [[ $# -eq 0 ]]; then
        echo "Usage: run_snakemake <command> [arguments]";
        return 1;
    fi;
    local command="$1";
    shift;
    local run_snakemake_cmd="$base_command $command $@";
    eval "$run_snakemake_cmd"
}
```
Reload your `.bashrc` and then you can just run it from anywhere as
```
run_snakemake snakename --help
```

::::::::::::::::::::::::


:::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::: spoiler

### Using conda

Conda is a popular package and environment management system. To install Snakemake using conda, you can use the following command in your terminal:
```BASH
conda create -c conda-forge -c bioconda -n snakemake snakemake
```

::::::::::::::::::::::::

To test that everything works, you can run the following command:
```
snakemake --help
```
:::::::::::::::::::::::instructor
The rest of the tutorial will assume that the bash function exists. So better to emphasize to do that.
:::::::::::::::::::::::


## REANA setup

REANA is a platform where you can submit your jobs. Think about it more of an Analysis Facility. To create an account you can follow the [oficial documentation](https://docs.reana.io/getting-started/first-example/) or just go to [https://reana.cern.ch](https://reana.cern.ch) to create your **access token**. This token is important since you need to use it everytime that you want to submit jobs to REANA. This step can take some minutes, depending on how busy the REANA team are approving their request.

You can submit jobs to REANA from _any machine_. If you want to use lxplus, you just need to activate the following environment:
```BASH
source /afs/cern.ch/user/r/reana/public/reana/bin/activate
```

After this, you can make a test that your REANA account works by running:
```BASH
export REANA_SERVER_URL=https://reana.cern.ch
export REANA_ACCESS_TOKEN=xxxxxxxxxxxxxxxxxxx
reana-client ping
```

:::::::::::::::: spoiler

### Using a container

In my opinion, using a container is the easiest way to interact with the REANA cluster from _any machine_. To do that you can use:
```BASH
apptainer run --env REANA_SERVER_URL=https://reana.cern.ch --env REANA_ACCESS_TOKEN=xxxxxxxxxxxxxxxxx --bind ${PWD}:/srv --pwd /srv  docker://docker.io/reanahub/reana-client:0.9.3 ping
```
You can save this in a bash script for convenience, or in your `.bashrc` as:
```BASH
reana_client ()
{ 
    local base_command="apptainer run --env REANA_SERVER_URL=https://reana.cern.ch --env REANA_ACCESS_TOKEN=xxxxxxxxxxxxxxxx --bind ${PWD}:/srv --pwd /srv  docker://docker.io/reanahub/reana-client:0.9.3";
    if [[ $# -eq 0 ]]; then
        echo "Usage: reana_client <command> [arguments]";
        return 1;
    fi;
    local command="$1";
    shift;
    run_reana_cmd="$base_command $command $@";
    eval "$run_reana_cmd"
}
```
and, after reloading your `.bashrc`, then simply:
```BASH
reana-client ping
```
::::::::::::::::::::::::
