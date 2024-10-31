---
title: Introduction
---

In this tutorial we will use `Snakemake` and `REANA`. To set up both packages, you can follow this instructions.


## Software Setup

### Snakemake

We will run some jobs locally. To use Snakemake, we can do the following:

::::::::::::::::::::::::::::::::::::::: discussion

#### Recommended

We can use singularity and the official snakemake container to run our jobs. This is the simplest solution if you dont want/can install the official package. Other advantage is that one can keep the analysis environment clean. 

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

### Linux

Use Terminal

::::::::::::::::::::::::

## Gitlab repository

<!--
FIXME: place any data you want learners to use in `episodes/data` and then use
       a relative link ( [data zip file](data/lesson-data.zip) ) to provide a
       link to it, replacing the example.com link.
-->
<!-- Download the [data zip file](https://example.com/FIXME) and unzip it to your Desktop -->
FIXME
