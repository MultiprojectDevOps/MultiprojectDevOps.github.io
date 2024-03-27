---
title: Background on Multiproject CI/CD
---

This tutorial is still a work in progress.
{: .notice--warning}

# What is multiproject CI/CD and Multiproject DevOps?

We assume that most readers have some familiarity with CI/CD (short for 
continuous integration/continuous deployment) in the context of a single 
project. Such efforts usually involve building a project with a set of changes,
testing that the changes do not break the project, and then merging those 
changes into the project. CI/CD workflows are used to automate as much of
the DevOps process as possible. 

We suspect that readers are less familiar with the complexities which arise when
organization find themselves developing and maintaining CI/CD workflows for 
multiple projects. The Multiproject DevOps organization was created to establish 
best practices and to provide resources for managing multiproject CI/CD 
workflows.

# What are the challenges associated with multiproject CI/CD?

Compared to developing/managing CI/CD for a single project, developing and
managing CI/CD for multiple projects comes with their own pitfalls:

## Avoiding duplication

Also known as the DRY principle, i.e., "don't
repeat yourself" it is important to avoid duplication so that:

- there exists a single source of truth
- bugs/errors are not propagated via copy/paste
- easier to reuse battle-tested functionality
