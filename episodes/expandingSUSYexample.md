---
title: "Expanding the SUSY analysis"
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

Now that you've mastered the basics of creating simple Snakemake workflows and submitting them to REANA, let's dive deeper into more complex analyses. This next example will illustrate how to expand our SUSY analysis to perform more sophisticated tasks.

## A more real SUSY analysis

In real-world analysis workflows, processing often involves multiple datasets. Typically, we identify these datasets using CERN's Data Access System (DAS) and then extract a list of files for further analysis. These files are then processed individually before being merged for subsequent steps. This workflow often involves additional CERN tools like [Rucio](https://rucio.cern.ch/) for dataset discovery and manual steps, like using `hadd` to merge ROOT files. However, Snakemake and REANA offer a powerful solution to automate and streamline these tasks. Let's explore how we can leverage their capabilities to handle this complex scenario.

:::::::::::::: callout

## Remember

There isn't a one-size-fits-all approach to creating workflows, just as there isn't a single way to perform an analysis. The decisions made in this tutorial are primarily for illustrative purposes. While there may be more efficient or optimized ways to structure this workflow, we've chosen this approach to clearly demonstrate the core concepts involved.

::::::::::::::::::::::


Let's look at the Snakefile:

```
output_dir = "$REANA_WORKSPACE"
analysis_container = "docker://gitlab-registry.cern.ch/cms-analysis/analysisexamples/snakemake-reana-examples/cmsreana_susyexample:latest"

# Define the final target rule
rule all:
    input:
        expand("output/datacards/{sample}.root", sample=config["samples"])


# Rule for dataset
rule dataset:
    output:
        "output/dataset_{sample}.txt"
    resources:
        voms_proxy=True,
        rucio=True
    params:
        sample = "{sample}"
    container: "docker://docker.io/reanahub/reana-auth-rucio:1.1.1"
    shell:
        """
        source datasets.sh {params.sample}   ### this is a workaround to get the dataset
        mkdir -p {output_dir}/output/
        rucio whoami
        rucio list-file-replicas cms:$dataset --pfns --protocols root --rses "(tier=2|3)&(country=US|country=DE)" | head -n 5 > {output_dir}/output/dataset_{params.sample}.txt
        head {output_dir}/output/dataset_{params.sample}.txt
        """


# Rule for skimming
rule skimming:
    input: 
       "output/dataset_{sample}.txt"
    output:
        "output/skimming/{sample}/files.txt"  
    resources:
        voms_proxy=True,
        kerberos=True,
        # compute_backend="htcondorcern"
    params:
        selection = '"(nMuon>0&&nTau>0&&HLT_IsoMu24)"',
        N = 100,
        sample = '{sample}'
    container: analysis_container
    shell:
        """
        mkdir -p {output_dir}/output/skimming/{params.sample}
        cd /code/CMSSW_13_0_10/src 
        source /cvmfs/cms.cern.ch/cmsset_default.sh
        cmsenv
        export X509_CERT_DIR=/cvmfs/grid.cern.ch/etc/grid-security/certificates
        echo "Processing files listed in: {input}"
        while IFS= read -r file; do
            echo "Processing file: $file"
            python3 PhysicsTools/NanoAODTools/scripts/nano_postproc.py \
                {output_dir}/output/skimming/{params.sample}/ "$file" \
                --bi /code/CMSSW_13_0_10/src/SUS_ex/Analysis/scripts/keep_in.txt \
                --bo /code/CMSSW_13_0_10/src/SUS_ex/Analysis/scripts/keep_out.txt \
                -c {params.selection} \
                -I SUS_ex.Analysis.DiTau_analysis analysis_mutaumc \
                -N {params.N} --prefetch
        done < {output_dir}/{input}
        find {output_dir}/output/skimming/{params.sample}/ -type f -name "*root" > {output_dir}/output/skimming/{params.sample}/files.txt
        head {output_dir}/output/skimming/{params.sample}/files.txt
        """


# Rule for hadd 
rule hadd:
    input:
        "output/skimming/{sample}/files.txt"
    output:
        "output/skimming/{sample}_Skim.root"
    container: analysis_container
    shell:
        """
        mkdir -p {output_dir}/output/skimming
        cd /code/CMSSW_13_0_10/src && \
        source /cvmfs/cms.cern.ch/cmsset_default.sh && \
        cmsenv && \
        python3 PhysicsTools/NanoAODTools/scripts/haddnano.py \
                        {output_dir}/output/skimming/{wildcards.sample}_Skim.root \
                        $(cat {output_dir}/{input})
        """


# Rule for datacarding
rule datacarding:
    input:
        "output/skimming/{sample}_Skim.root"
    output:
        "output/datacards/{sample}.root"
    params:
        year = "2022postEE",
        sample = '{sample}'
        # sample = lambda wildcards: wildcards.sample
    container: analysis_container
    shell:
        """
        mkdir -p {output_dir}/output/datacards
        cd /code/CMSSW_13_0_10/src/SUS_ex/Analysis2 && \
        source /cvmfs/cms.cern.ch/cmsset_default.sh && \
        cmsenv && \
        ./FinalSelection_mutau.exe {params.year} {output_dir}/{input} {output_dir}/{output} {params.sample} {params.sample}
        """
```






:::::: keypoints
 - keypoint 1
 - keypoint 2
::::::