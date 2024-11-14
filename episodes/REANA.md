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

REANA offers flexibility in workflow management by supporting multiple systems like [CWL](https://www.commonwl.org/), [Serial](https://docs.reana.io/running-workflows/supported-systems/serial/), (Yadage)[https://yadage.readthedocs.io/en/latest/], and Snakemake. While there's a growing adoption of Snakemake within the LHC community due to its large external user base and strong support, REANA remains agnostic to the chosen workflow system. However, due to its popularity and powerful features, we will primarily focus on Snakemake throughout this tutorial.


### A REANA workflow

While a Snakemake worflow is defined in a snakefile, in REANA one needs to create a REANA file to include all the parameters that the snakemake workflow will need. 

Let's retake the previous SUSY example, and try to run it using REANA. 




:::::: keypoints
 - keypoint 1
 - keypoint 2
::::::