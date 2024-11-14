---
title: "Running workflows in REANA"
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


REANA is a powerful platform designed to streamline and enhance the reproducibility of scientific research, particularly in high-energy physics analysis. It enables researchers to define, execute, and share complex data analysis workflows, ensuring that their work is transparent, verifiable, and easily replicated. By leveraging containerization technology and cloud computing resources, REANA simplifies the management of computational environments, data dependencies, and execution pipelines. This tutorial will guide you through the essential steps of using REANA, from creating your first workflow to deploying it on a remote computing infrastructure. REANA offers a user-friendly approach to reproducible research, empowering you to focus on scientific discovery while automating the underlying infrastructure.

![](https://docs.reana.io/images/reana-platform-20181202.png)

While REANA is primarily a tool for reproducible analysis, it effectively functions as a comprehensive analysis facility, seamlessly integrating various CERN resources. This means that essential CERN tools, including EOS, CVMFS, Kerberos, Rucio, VOMS-proxy, HTCondor, can be directly accessed within the REANA platform, streamlining the analysis process and simplifying resource management.

## Understanding the Basics

REANA offers flexibility in workflow management by supporting multiple systems like [CWL](https://www.commonwl.org/), [Serial](https://docs.reana.io/running-workflows/supported-systems/serial/), [Yadage](https://yadage.readthedocs.io/en/latest/), and Snakemake. While there's a growing adoption of Snakemake within the LHC community due to its large external user base and strong support, REANA remains agnostic to the chosen workflow system. However, due to its popularity and powerful features, we will primarily focus on Snakemake throughout this tutorial.


### Defining a workflow for REANA

While a Snakemake worflow is defined in a snakefile, in REANA one needs to create a REANA file to include all the parameters that the snakemake workflow will need. 

Let's retake the previous SUSY example, and try to run it using REANA. First we need to write a `reana.yaml` file:

```YAML
version: 0.9.3
inputs:
  directories:
    - SUSY/
  parameters:
    input: inputs.yaml
  files:
    - Snakefile
workflow:
  type: snakemake
  file: Snakefile
  resources:
    cvmfs:
      - cms.cern.ch
workspace:
  retention_days:
    output/*: 30
outputs:
  directories:
    - output/
```

The `reana.yaml` file acts as a blueprint for your REANA workflow, defining essential information for execution.

 - `version` (optional): Since REANA is under development, specifying the version used (e.g., `0.9.3` at the time of writing) can be helpful for troubleshooting.
 - `inputs`: This section defines the files, folders, and parameters your workflow requires. Remember that we need to upload all necessary files before the workflow runs. 
   - `directories` and `files`: Specify the files and folders to be uploaded to the REANA platform.
   - `parameters`: In this example, workflow parameters are defined in a separate
`inputs.yaml` file, so we reference the input file here. You can also define parameters directly within reana.yaml.
 - `workflow`: This crucial section tells REANA about the type of workflow you're using. 
    - `type`: we are using `snakemake`, but REANA supports CWL, Serial or Yadage. ([More here](https://docs.reana.io/running-workflows/supported-systems/)).
    - `file`: This defines the location of your workflow script.
    - `resources`: Define any **global** resources required for your workflow execution (detailed information available at [here](https://docs.reana.io/advanced-usage/)). Remember that you can also define dedicated rule resources in the snakefile.
 - `workspace` (optional): Here, you can set options like `retention_days` to specify how long specific folders should be retained after workflow completion.
 - `outputs`: This section informs REANA which files or folders should be made available for download after successful workflow execution. These can be individual files or entire directories.

### Running a workflow in REANA

Let's get familiar with the steps necessary to run our workflow in REANA. First, activate the REANA environment, and then remember to set these variables:

```
export REANA_SERVER_URL=https://reana.cern.ch
export REANA_ACCESS_TOKEN=xxxxxxxxxxxxxxxxxxxxxxx
```

This needs to be done every time you start a session. Then, the REANA client contains a similar validation than Snakemake's dry-run, we can run:

```BASH
reana-client validate -f reana.yaml
```

```OUTPUT
Building DAG of jobs...
[WARNING] Building DAG of jobs...
Job stats:
job            count
-----------  -------
all                1
datacarding        1
skimming           1
total              3

[WARNING] Job stats:
job            count
-----------  -------
all                1
datacarding        1
skimming           1
total              3

==> Verifying REANA specification file... /srv/reana.yaml
  -> SUCCESS: Valid REANA specification file.
==> Verifying REANA specification parameters... 
  -> SUCCESS: REANA specification parameters appear valid.
==> Verifying workflow parameters and commands... 
  -> SUCCESS: Workflow parameters and commands appear valid.
==> Verifying dangerous workflow operations... 
  -> WARNING: Operation "cd /" found in step "skimming" might be dangerous.
  -> WARNING: Operation "cd /" found in step "datacarding" might be dangerous.
```

This step verifies first if the snakefile contains a workflow that can be run, and second it verifies that the inputs in the `reana.yaml` file are correct. If everything looks ok, we can create a workflow called `test_SUSY` within the platform:

```BASH
reana_client create -w test_SUSY -f reana.yaml
```

Remember that this step will only create the workflow within REANA, you can verify it by looking at [https://reana.cern.ch/](https://reana.cern.ch/) or by running:

```BASH
reana-client status -w test_SUSY
```

The next step is to upload the files the workflow needs:

```BASH
reana-client upload -w test_SUSY 
```

and finally we can make it run:

```BASH
reana-client start -w test_SUSY 
```

Again, you can check the status of your jobs via the REANA website or with `reana-client status -w test_SUSY`.


::::::::::::: challenge

### There must be a better way!

Absolutely there is!
While there can be specific circunstances where one can split these steps, there is a REANA command to create, upload and start your workflow. You can try:

```BASH
reana-client run -w test_SUSY -f reana.yaml
```

::::::::::::::::::::::::::::::::

:::::: keypoints
 - keypoint 1
 - keypoint 2
::::::