---
title: Background on Multiproject CI/CD
---

This tutorial is still a work in progress.
{: .notice--warning}

# What is multiproject CI/CD and multiproject DevOps?

We assume that most readers have some familiarity with CI/CD (short for 
continuous integration/continuous deployment) in the context of a single 
project. Such efforts usually involve building a project with a set of changes,
testing that the changes do not break the project, merging those changes into 
the project,and then deploying the updated project. DevOps is a set of 
practices and processes used to decrease the time needed to deploy high-quality
software. For more information on CI/CD see [CI/CD Resources](/resources/#cicd-resources). The process is summarized in the following figure.

![Typical Single-Project CI/CD Pipeline](/tutorials/assets/single_ci.png)

In the present context the adjective "multiproject" is used to denote
that we are considering not just a single project's CI/CD pipeline, but the 
CI/CD pipelines for multiple projects simultaneously. 

# How is multiproject CI/CD different from single project CI/CD?

When maintaining CI/CD for *N* projects it is important to note that the 
projects are often not *N* random projects. Rather the projects often share
some development aspects, for example because they are developed by a common 
group of developers, or because they are part of a shared ecosystem. The result
is that the the *N* pipelines tend to have common needs, e.g.:
- common software dependencies, 
- use of the same build system, and/or
- reuse of automations.
The need to maintain functionalities used by multiple CI/CD pipelines is the 
primary difference between multiproject and single project CI/CD.

# What are the best practices for maintaining multiproject CI/CD pipelines?

Other tutorials in this series are designed to delve into this topic in more
detail, but briefly it boils down to "treat CI/CD pipelines as code." More
specifically, since the CI/CD pieces are being used by multiple projects it
becomes even more important to:

- modularize and reuse CI/CD components
- document CI/CD components
- test changes/updates to the CI/CD components
- consider security ramifications of sharing infrastructure