---
title: "Let's talk about workflows"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What are the common challenges faced in CMS analysis?
- How can workflow orchestration tools help in capturing the intricate steps involved in producing results in CMS analysis?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Understand the common challenges faced in CMS analysis.
- Learn how workflow orchestration tools can assist in capturing the intricate steps involved in producing results in CMS analysis.

::::::::::::::::::::::::::::::::::::::::::::::::


## The (short) life of a HEX analysis in CMS

Navigating the complexities of CMS analysis can be a daunting task, regardless of your experience level. A common challenge we all face is capturing the intricate steps involved in producing our results.

Let's examine the figure below:

![The life of a HEX analysis](episodes/fig/Analysis_Clemens.png){alt="The life of a HEX analysis"}

This figure illustrates the typical workflow of a CMS analysis, divided into three distinct phases:

 * Collaboration: This encompasses the extensive groundwork laid by the entire research community, over which individual analysts have limited control.
 * Analysis: This is the core phase where analysts like ourselves conduct the actual analysis, utilizing the data and resources provided by the collaboration.
 * Dissemination: This phase focuses on how our results are shared and utilized by others within the scientific community.

While the dissemination phase is crucial, effective methods for sharing and utilizing research outputs already exist.

In this discussion, I will concentrate on the _Analysis_ phase, which lies entirely within our sphere of influence as analysts. A recurring challenge in high-energy physics (HEX) analysis is the lack of reproducibility. Analysts often embark on a similar (simplified) journey:

 * Data Exploration: They begin by analyzing data, generating histograms to visualize distributions.
 * Background Modeling: This crucial step involves developing and implementing models to describe background processes.
 * Systematic Corrections: Numerous corrections are applied to account for various experimental effects.
 * Statistical Analysis: Once the corrected data is obtained, statistical analyses are performed, often utilizing the combine framework.
* Publication: After rigorous peer review, the results are published in scientific journals.

However, this cycle often leads to a significant knowledge gap. When an analyst moves on to a new project or leaves the collaboration, valuable knowledge about the analysis process is lost. Subsequent analysts may struggle to understand (for instance):

 * Code Implementation: How the initial histograms were generated and the underlying code.
 * Software Dependencies: The specific software versions and configurations used.
 * Analysis Workflow: The precise steps involved in applying corrections and performing statistical analyses.
 * Tool Usage: How to effectively utilize tools like RooFit or RooStats for the given analysis.
 * And several other topics.

This lack of documentation and knowledge transfer hinders efficiency and can lead to unnecessary duplication of effort. 

To address some of these reproducibility challenges, the Common Analysis Group (CAT) has developed valuable resources:

 * *Centralized Code Repository*: CAT maintains a central repository for [CMS analysis code](https://gitlab.cern.ch/cms-analysis). This ensures code accessibility and simplifies collaboration among analysts.
 * Statistical Interpretation Tools: CAT is working on a suite of tools for [preserving and publishing the statistical interpretations of results](https://cms-analysis.docs.cern.ch/stats/contributing/). These tools promote consistency and transparency in statistical analyses.

By leveraging these resources, analysts can benefit from existing knowledge and code, reducing redundancy and accelerating the analysis process.

## Reproducibility

Reproducibility is a cornerstone of scientific research, and HEX is no exception.
It ensures that experimental results can be verified independently, bolstering the credibility of scientific findings. 
To achieve reproducibility, researchers must provide detailed descriptions of their methodologies, including the tools, datasets, and parameters used. 
This allows others to replicate the experiments and compare their results. 
<!-- Tools like [REANA]() and [Snakemake]() can significantly aid in reproducibility by providing platforms for creating, managing, and sharing reproducible computational workflows. 
These tools help researchers document their experimental steps, track dependencies, and ensure that results can be replicated consistently, fostering trust in scientific discoveries. -->

## Towards Reusable Analyses: A Three-Step Approach

Let's explore a three-step approach:

1. **Capture Software and Code:** 
    * **Objective:** Package analysis code and its dependencies for reliable and reproducible execution.
    * **Current Practices:** Many analyses utilize Git (GitHub/GitLab) for code version control. Some leverage containerization technologies (e.g., Docker, Singularity) to encapsulate the entire software environment.
2. **Capture Commands:** 
    * **Objective:** Define the precise commands required to execute the analysis code.
    * **Current Challenges:** Analysis scripts often consist of complex and difficult-to-understand sequences of bash or Python commands.
3. **Capture Workflow:** 
    * **Objective:** Define the ordered execution of commands within the analysis, independent of the specific computing environment.
    * **Focus:** This tutorial will delve into workflow management using modern tools like Snakemake and demonstrate how to execute these workflows on CERN's REANA platform.

By implementing these steps, we can significantly improve the reproducibility, maintainability, and shareability of our physics analyses.

:::::::::::::::::::::::::::::::::::::::::::::::::::::: callout

## A quick remark on LAW

While some analysts utilize the Luigi Analysis Workflow (LAW)[https://github.com/riga/law], a workflow orchestration tool built on top of Spotify's (Luigi)[https://github.com/spotify/luigi] and developed by a CMS physicist, this tutorial focuses on Snakemake. Both LAW and Snakemake are excellent tools, and if you're already using LAW for your workflow management, that's great!

::::::::::::::::::::::::::::::::::::::::::::::::::::::

## What does reproducibility has to do with me?

:::::::::::::::::::::::::::::::::::::::::::::::::::::: challenge

## Who are the first users of a reproducible workflow?

:::::::::::::::::::::: solution
Me, myself and my group. :grin:
::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::


While we've discussed capturing entire CMS analyses, the benefits of reproducible workflows extend beyond best practices.

* Streamlining Repetitive Tasks: Many analyses involve numerous small, repetitive steps. Workflow orchestration tools automate these tasks, saving significant time and resources.
 * Facilitating Future Revisions: With increasingly complex analyses and rigorous CMS review processes, the need to revisit and modify earlier steps in the analysis is inevitable. A well-defined workflow ensures that these revisions can be implemented efficiently and accurately, preventing the need to "reinvent the wheel."

Ultimately, remember that the most frequent user of your analysis code will be your future self.

:::::::::::::::::::: keypoints

 - Workflow orchestration tools automate repetitive tasks, saving time and resources.
 - Well-defined workflows facilitate efficient and accurate revisions in complex analyses.

::::::::::::::::::::::::::::::::