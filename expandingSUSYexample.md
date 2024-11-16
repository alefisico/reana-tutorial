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








:::::: keypoints
 - keypoint 1
 - keypoint 2
::::::