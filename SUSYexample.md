---
title: "A simple analysis from SUSY"
teaching:
exercises:
---

:::::: questions
 - question 1
 - question 2
::::::

:::::: objectives
 - objective 1
 - objective 2
::::::

After learning the basics about Snakemake using dummy processes, let's use a simple example from a SUSY analysis to expand the Snakemake capabilites. 

For this part of the tutorial, let's clone the following repository:
```BASH
git clone ssh://git@gitlab.cern.ch:7999/cms-analysis/analysisexamples/snakemake-reana-examples/cmsreana_susyexample.git
```

In this example, we'll demonstrate a basic use case: skimming a nanoAOD file and generating histograms suitable for CMS Combine. While the specific details of this analysis aren't crucial, this example will highlight REANA's potential for more complex and sophisticated workflows (more about REANA in the next episode). 

## A simple SUSY analysis

Let's discuss the content of the file `Snakefile`:

```

output_dir = "."

# Define the final target rule
rule all:
    input:
        config["output_file"]

# Rule for skimming
rule skimming:
    input:
        config["nanoaod_file"]
    output:
        "output/skimming/DY_Skim.root"
    params:
        selection = '"(nMuon>0&&nTau>0&&HLT_IsoMu24)"',
        N = 1000
    container:
        "docker://gitlab-registry.cern.ch/cms-analysis/analysisexamples/snakemake-reana-examples/cmsreana_susyexample:latest"
    shell:
        """
        mkdir -p {output_dir}/output/skimming
        cd /code/CMSSW_13_0_10/src && \
        source /cvmfs/cms.cern.ch/cmsset_default.sh && \
        cmsenv && \
        python3 PhysicsTools/NanoAODTools/scripts/nano_postproc.py \
            {output_dir}/output/skimming {output_dir}/{input} \
            --bi /code/CMSSW_13_0_10/src/SUS_ex/Analysis/scripts/keep_in.txt \
            --bo /code/CMSSW_13_0_10/src/SUS_ex/Analysis/scripts/keep_out.txt \
            -c {params.selection} \
            -I SUS_ex.Analysis.DiTau_analysis analysis_mutaumc \
            -N {params.N}
        """

# Rule for datacarding
rule datacarding:
    input:
        "output/skimming/DY_Skim.root"
    output:
        config['output_file']
    params:
        year = "2022postEE"
    container:
        "docker://gitlab-registry.cern.ch/cms-analysis/analysisexamples/snakemake-reana-examples/cmsreana_susyexample:latest"
    shell:
        """
        mkdir -p {output_dir}/output/datacards
        cd /code/CMSSW_13_0_10/src/SUS_ex/Analysis2 && \
        source /cvmfs/cms.cern.ch/cmsset_default.sh && \
        cmsenv && \
        ./FinalSelection_mutau.exe {params.year} {output_dir}/{input} {output_dir}/{output} DY DY
        """

```

The provided example demonstrates a Snakemake workflow with two rules: 
skimming and datacarding. One can notice that this workflow relies on some CMSSW packages, as well as CVMFS environments. While the specific details of these rules might be analysis-specific, we can focus on the general concepts of `params` and `container` to enhance workflow flexibility and reproducibility.

`container`:

 * Encapsulating Environment: By specifying a container image, you create a self-contained environment for your workflow, which can be **reproducible**. This ensures that the execution environment, including specific software versions and dependencies, is consistent across different systems. 
 * Simplifying Setup: Using containers eliminates the need for complex installations and configuration on the host system. You can simply pull the container image and run the workflow.

`params`:

 * Parameterizing Rules: The params option allows you to pass parameters to your shell commands, making your rules more flexible and adaptable to different input data or configuration settings.
 * Leveraging Configuration Files: By defining parameters in a configuration file, you can easily modify the behavior of your workflow without changing the Snakefile itself.


:::::::::: callout

#### Containers: The Key to Reproducible Results

Using containerized environments is highly recommended for achieving reproducible research outcomes. Containers offer several advantages:

 * Isolation and Consistency: They create a self-contained environment with specific software versions and dependencies, guaranteeing consistent execution across different computing platforms. This eliminates potential issues arising from variations in the host system's configuration.
 * Simplified Setup: Containers eliminate the need for complex installations and environment configuration on the user's machine. By pulling the pre-built container image, users can readily execute the workflow without worrying about compatibility or missing software.
 * Enhanced Sharing: Sharing containerized workflows is straightforward as they encapsulate the entire execution environment. This facilitates collaboration and streamlines research efforts.

Curious about the specific software included in the container used for this analysis? You can delve deeper by examining the Dockerfile located in the repository here: [link to Dockerfile](https://gitlab.cern.ch/cms-analysis/analysisexamples/snakemake-reana-examples/cmsreana_susyexample/-/blob/master/Dockerfile?ref_type=heads).

::::::::::::::::::


Let's validate the workflow:

```BASH
snakemake --snakefile Snakefile --configfile inputs.yaml --dry-run
```

You can try to run the `--dag` and/or `--rulegraph` commands to visualize what you will run. If it does like everything is correct, you can try to run it:

```BASH
snakemake --snakefile Snakefile --configfile inputs.yaml
```

::::::::::: challenge

### `--use-apptainer`

While we've covered the core concepts of Snakemake, there are numerous additional flags that can be used to customize and optimize your workflows. One such flag is `--use-apptainer`, which is essential when running rules within containerized environments.

By using `--use-apptainer`, Snakemake ensures that the specified container is utilized for each rule's execution, providing a consistent and isolated environment for your workflow. Let's try our example with

```BASH
snakemake --snakefile Snakefile --configfile inputs.yaml --use-apptainer
```
::::::::::::::::


:::::: spoiler

If the previous Snakemake command didn't execute as expected, it's likely due to the requirement for access to CERN tools like CVMFS, EOS, or VOMS-proxy. While Snakemake offers some mechanisms to incorporate these tools, REANA provides a more seamless and efficient solution.

In the next episode, we'll delve deeper into REANA and explore how it simplifies the execution of complex workflows, especially those involving CERN-specific tools and resources.

::::::::::::::::::::


:::::: keypoints
 - keypoint 1
 - keypoint 2
::::::