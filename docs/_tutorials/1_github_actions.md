---
title: Introduction to Multi-Project CI/CD Using GitHub Actions
---

This tutorial is still a work in progress.
{: .notice--warning}

The name GitHub Actions for GitHub's CI/CD system is somewhat unfortunate
because it makes it hard to distinguish between the CI/CD system and the
fundamental pieces of the system (actions). When we refer to the system we
will always use "GitHub Actions" (note the capital "A"), whereas the pieces are
just "actions" (note the lower case "a").
{: .notice}

The point of this tutorial is to help you understand GitHub Actions in the
context of multi-project CI/CD. This tutorial assumes you have some familiarity
with GitHub Actions already, if not we recommend consulting the tutorials in
the [resources](https://multiprojectdevops.github.io/resources/#github-actions) section of this site.

# Terminology

"GitHub Actions" is the name given to the CI/CD system built into GitHub. Here
the namesake of the CI/CD system, i.e., "actions," are programs specifically
designed to work on the GitHub Actions platform. Actions are reusable solutions
to common CI/CD needs, e.g., cloning the repository, obtaining a particular
dependency, or linting the source code. Using GitHub Actions, developers
can automatically perform software development and maintenance tasks when
one or more well defined conditions are met. These conditions are termed
"events." Examples of events include opening a pull request, or pushing to a
branch. A full list of events can be found [here](https://tinyurl.com/4drrbxwj).

When an event occurs it triggers what is called a "workflow." The workflow
contains the full description of the work to be done in response to the event.
Work in a workflow is grouped into one or more "jobs". When GitHub Actions runs
the workflow it divides the jobs up among available cloud resources. The cloud
resources are referred to as "runners." Finally, each job is made up of one or
more "steps", where a step is typically a shell command, a script of shell commands, or a call to an action. To
facilitate writing CI/CD workflows
[GitHub Marketplace](https://github.com/marketplace) provides a number of
reusable actions.

These pieces are summarized in the following figure:

![Overview of GitHub Actions](/tutorials/assets/github_actions.png)

The pieces introduced so far occur in both single-project and multi-project
CI/CD. In the context of multi-project CI/CD we also have "reusable workflows"
and "composite actions." "Reusable workflows" are workflows that have been
designed for use by multiple organizations, repositories, or use cases.
Typically, this is done by defining a set of inputs that the caller must provide
and then writing the workflow in terms of the inputs. "Composite actions" are
reusable steps, which bundle one or more steps together so they can be used as
a single action. We will explore reusable workflows and composite actions in
more detail in the next tutorial (TODO: link). For now we conclude this section
with a table summarizing the terminology.

| Term              | Definition                                             |
| ----------------- | ------------------------------------------------------ |
| GitHub Actions    | The name for the CI/CD system built into GitHub.       |
| Event             | An interaction which triggers a workflow.              |
| Workflow          | The series of jobs to run in response to an event.     |
| Job               | The set of steps to provide to a runner.               |
| Step              | The fundamental units of work in a job.                |
| Runner            | The cloud resources used to run the steps of a job.    |
| Action            | "GitHub Actions"-specific programs.                    |
| Reusable workflow | Workflows designed to be called from other workflows.  |
| Composite action  | A series of actions bundled together as a single step. |
| Workflow template | Facilitate writing workflows in new repositories.      |


# Multi-Project CI/CD File Architecture

Developing GitHub Actions requires some familiarity with YAML (a recursive
acronym that stands for "YAML Ain't Markup Language"). Since YAML is a
common data serialization language, we assume the reader has familiarity
with it; if not, see [GitHub Actions Resources](/resources/#github-actions) for
resources which will quickly acquaint you with YAML.
{: .notice}

To enable GitHub actions for a repository a developer must define one or more
top-level workflows, i.e., the workflows that GitHub will call when an event
occurs. All workflows, top-level or otherwise, must live in the `.github/
workflows` directory (path is relative to the root of the repository) of the
repository. Each workflow is a YAML file and GitHub requires that these files
either have a `.yml` or a `.yaml` extension. We recommend that workflows
defining entry-points be named for the event that triggers them (e.g.,
`push.yaml` if the workflow is called whenever pushing to a branch). Thus to use
GitHub actions the minimum file architecture of a repository is:

![Repository Architecture](/tutorials/assets/repository_architecture.png).



Reusable workflows must also reside in the `.github/workflows` directory of the
repository they belong to.

Unlike workflows, actions (including composite actions) can live anywhere in the
repository; however, GitHub strongly suggests that actions which do not live in
their own repository live in the  `.github/actions/name_of_action` directory
(path is again relative to the root and "`name_of_action`" should be replaced
with the actual name of the action). For this tutorial we are only concerned
with composite actions. Composite actions must be defined in a file named either
`action.yml` or `action.yaml`, i.e., you can not customize the name of the file beyond the extension. This is summarized in the following figure:



In the above figure our repository contains two composite actions --- "action 1"
and "action 2" --- and two workflows --- "push" and "workflow_1". `push.yaml` is
intended to be the entry-point workflow (called when there is a push to a
branch), whereas the workflow type of `workflow_1.yaml` is unspecified and may
be either a reusable workflow or an entry-point. Single-repository file
architectures like this are commonly used for single-project CI/CD.


While not strictly required, source files for single-project CI/CD usually live
alongside the software for which they provide CI/CD. This is the scenario
depicted in the previous figure. In theory, Since multi-project CI/CD will in general be
leveraged by many repositories (potentially across multiple organizations)
there are a number of repositories where CI/CD source files could potentially reside.

The remainder of this tutorial explores the three main multi-project CI/CD file
architectures and best practices for when to use each. To that end we
differentiate the architectures based on the degree of coupling among the
isolatable CI/CD components and the source code the CI/CD is maintaining. Of
note, each repository will include

The latter two are always

## Loose Coupling Architecture

One way to differentiate the three architectures is based on the degree of
coupling among the CI/CD components and the consuming repositories. If we try
to minimized coupling we obtain the following architecture:

![Loose Coupling Architecture](/tutorials/assets/multi_ci_multi.png)

This figure uses the `.github/` directory as an opaque representation of the

## In-Repo Multi-Project CI/CD
