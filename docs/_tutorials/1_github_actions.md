---
title: GitHub Actions and Multiproject CI/CD
---

This tutorial is still a work in progress.
{: .notice--warning}

The point of this tutorial is to help you understand GitHub Actions in the
context of multiproject CI/CD. This tutorial assumes you have some familiarity
with GitHub Actions already, if not we recommend consulting one of the
following tutorials:

TODO: tutorial list

# Terminology

GitHub Actions is the name given to the CI/CD system built into GitHub. For
completeness the basic pieces of a CI/CD pipeline leveraging GitHub Actions
are:

- Workflow. Defines the pipeline. Workflows are the highest level of the
  hierarchy.
- Job. Workflows are broken into jobs. Jobs may run on different machines, thus
  jobs provide you concurrency.
- Step. Each job is broken into a series of steps. These steps run on the
  same machine and each step has access to the previous step's outputs. Each 
  step is either a script or a call to an action. Steps are the most fundamental piece of a GitHub workflow.

In the context of multiproject CI/CD we also have:

- Reusable workflows. Templated workflows. Acts like a job. Can't call other
  reusable workflows.
- Composite actions. Bundles many actions together into a single step. Must be 
  called from within another workflow's jobs.

