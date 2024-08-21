---
title: Introduction to Multi-Project CI/CD Using GitHub Actions
---

The point of this tutorial is to help you understand GitHub Actions in the
context of multi-project CI/CD. This tutorial assumes you have some familiarity
with GitHub Actions already, if not we recommend consulting the tutorials in
the [resources](https://multiprojectdevops.github.io/resources/#github-actions) section of this site.

The name GitHub Actions for GitHub's CI/CD system is somewhat unfortunate
because it makes it hard to distinguish between the CI/CD system and the
fundamental pieces of the system (actions). When we refer to the system we
will always use "GitHub Actions" (note the capital "A"), whereas the pieces are
just "actions" (note the lower case "a").
{: .notice}

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
and then writing the workflow in terms of the inputs. To distinguish resuable
workflows from non-reusable workflows we coin the term "entry workflows" for
the latter because non-reusable workflows are always the "entry" point for
GitHub actions, whereas resuable workflows must be called from an entry workflow
(or another reusable action). The term "workflow" will be used to refer to both
entry and reusable workflows.

"Composite actions" are
reusable steps, which bundle one or more steps together so they can be used as
a single action. We will explore reusable workflows and composite actions in
more detail in the next tutorial (TODO: link). For now we conclude this section
with a table summarizing the terminology.

| Term              | Definition                                             |
| ----------------- | ------------------------------------------------------ |
| GitHub Actions    | The name for the CI/CD system built into GitHub.       |
| Event             | An interaction which triggers a workflow.              |
| Entry Workflow    | The workflow called in response to a triggering event. |
| Job               | The set of steps to provide to a runner.               |
| Step              | The fundamental units of work in a job.                |
| Runner            | The cloud resources used to run the steps of a job.    |
| Action            | "GitHub Actions"-specific programs.                    |
| Reusable workflow | Workflows designed to be called from other workflows.  |
| Workflow          | Either an entry workflow or a reusable workflow.       |
| Composite action  | A series of actions bundled together as a single step. |
| Workflow template | Facilitates writing workflows in new repositories.     |


# CI/CD File Architecture

GitHub Actions are added to a repository by adding entry workflows to the
`.github/workflows` directory (path is relative to the root of the repository
and mandated by GitHub). Each workflow is a YAML file and GitHub requires that
these files either have a `.yml` or a `.yaml` extension. We
[recommend](/best_practices/#22-name-wokflows-after-the-trigger-condition) that
workflows be named for the event that triggers them (e.g.,
`push.yaml` if the workflow is called whenever pushing to a branch). Thus to
use GitHub Actions, the minimum file architecture of the repository is:

![Repository Architecture](/tutorials/assets/repo_minimum.png).

Generally speaking, most repositories will NOT contain reusable workflows.
However, when a repository does contain a reusable workflow it must also reside
in the `.github/workflows` directory. For example, say the above repository is
part of an organization which relies on a reusable workflow `push_common.yaml`.
The file architecture of the repository housing the `push_common.yaml` reusable
workflow would look something like:

![Repository with Reusable Workflow](/tutorials/assets/repo_with_reusable.png)

Actions (including composite actions) can live anywhere in the repository.
GitHub offers two suggestions depending on where you want the action to live.
If you want to house the action in a repository with other CI/CD components
and/or source code GitHub suggests what we term the "Shared Repository"
architecture. In the Shared Repository architecture,
[best practice](/best_practices/#24-store-actions-in-githubactions) is for
all actions to live in the `.github/actions` directory (path is again relative
to the root). Each action
in the `.github/actions` directory must live in its own subdirectory. The name
of the subdirectory should stem from the name of the action.

The other architecture assumes that the repository has been created exclusively
to house the action and we refer to this as the "Dedicated Repository"
architecture. In the Dedicated Repository architecture
[best practice](/best_practices/#23-use-dedicated-repositories-for-actions) is
for the action should live in a subdirectory
off the root (again the name of the subdirectory should stem from the name of
the action). Regardless of the chosen architecture, the contents of the
subdirectory is the source for the action including a YAML file named
`action.yaml` (or `action.yml`) which is the entry-point into the action. The
Shared Repository and Dedicated Repository architectures thus look something
like:

![Architectures Involving Actions](/tutorials/assets/repo_with_action.png)

# CI/CD Repository Architecture

The file architectures just described apply regardless of whether we are
interested in single-project or multi-project CI/CD. Where single-project and
multi-project CI/CD tend to differ is in how the CI/CD components are
distributed across GitHub, i.e., in their repository architecture.

Unlike multi-project CI/CD workflows, single-project CI/CD workflows are
typically not broken down into reusable workflows and/or custom actions and
therefore is a tendency for most single-project CI/CD workflows to live in the
same repository as the application code. We term this the "all-in-one"
architecture. The "all-in-one" architecture is also valid for multi-project
CI/CD workflows as long as the multi-project CI/CD workflows do NOT use reusable
workflows. If a multi-project CI/CD workflow relies on a reusable workflow,
[best practice](/best_practices/#25-store-reusable-workflows-separate-from-application-code)
is for the reusable workflow to either reside in a centralized repository,
or in a dedicated repository, respectively giving rise to the
"centralized repository" and "dedicated repository" architectures. The final
architecture considered here is the "separate organization" architecture, which
is an extreme variation on the "dedicated repository" architecture.

![CI/CD Repository Architectures](/tutorials/assets/multi_ci_repo.png)

Here we have defined CI/CD repository architectures based on where each CI/CD
component resides relative to the application code(s). In an actual GitHub
organization it is possible for different CI/CD components to rely on different
repository architectures. For example, it is entirely possible that an
organization has adopted the "centralized repository" architecture for most of
their CI/CD components, except for one or two components which rely on the "dedicated repository" architecture. The decision of which architecture to use
is made on a component-by-component basis. We suggest the following guidelines:

- **All-in-One**. Use if your CI/CD component is heavily tied to the application
  code and is unlikely to be useful to other applications. A quintessential
  example is a workflow that generates source files or parameters from a custom
  script.
- **Centralized Repository**. Use for reusable CI/CD components heavily tied to
  your organization's best practices and unlikely to conform to other
  organization's best practices. An example is a reusable workflow that is run
  on a pull request every time it is modified to ensure it is formatted
  correctly,includes the correct license header, passes static analysis etc.
  While each of these checks is likely being used by another organization, it is
  unlikely that another organization will have adopted the same set of tools,
  with the same options, and the same requirements.
- **Dedicated Repository**. Use when you write an action (or a reusable
  workflow) that is generic enough that it can be useful to other
  organizations which have adopted different standards. An example is an action
  which retrieves a deployed version of your organization's application and
  runs it as part of a downstream CI/CD workflow.
- **Separate Organization**. Use when your organization relies on the dedicated
  repository architecture so much that it becomes hard for users to browse your
  organization's repositories efficiently.
