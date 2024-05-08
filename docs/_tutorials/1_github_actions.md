---
title: GitHub Actions and Multiproject CI/CD
---

<!--
  ~ Copyright 2024 Multiproject DevOps Team
  ~
  ~ Licensed under the Apache License, Version 2.0 (the "License");
  ~ you may not use this file except in compliance with the License.
  ~ You may obtain a copy of the License at
  ~
  ~ http://www.apache.org/licenses/LICENSE-2.0
  ~
  ~ Unless required by applicable law or agreed to in writing, software
  ~ distributed under the License is distributed on an "AS IS" BASIS,
  ~ WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  ~ See the License for the specific language governing permissions and
  ~ limitations under the License.
-->


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

- Reusable workflows. Templated workflows. Acts like a job. Can call other
  reusable workflows.
- Composite actions. Bundles many actions together into a single step. Must be 
  called from within another workflow's jobs.

