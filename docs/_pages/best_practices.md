---
layout: single
permalink: /best_practices/
title: Best Practices for Multi-Project CI/CD
toc: true
toc_sticky: true
---

This page collects the best practices for multi-project CI/CD and explains the
justification behind them. We have grouped the best practices according to the
CI/CD systems they apply to.

# 1. General Best Practices

Best practices in this subsection are applicable to any CI/CD system.

## 1.1 Treat Infrastructure as Code (IaC)

**Practice:** When possible, setting up of the CI/CD environment should be done
through "code" (for example configuration files and scripts) versus say
graphical user interfaces. Moreover the code should be subjected to the same
best practices as when one develops software. In particular, consider
versioning, documenting, and testing all IaC files.

**Explanation:** The idea that infrastructure should be treated as code is not
new (see for [example](https://en.wikipedia.org/wiki/Infrastructure_as_code)).
IaC brings a number of advantages including:

- *Scaling*. Easier to programmatically scale up or down by modifying the files.
- *Reliability*. You get the same environment each time.
- *Security*. You know that deployment happens within your constraints., and
- *Reproducibility*. Others can use the code to get the same environment.

## 1.2 Treat Code as Code (CaC)

**Practice:** All code --- be it the main software product, or the scripts
written to maintain that product --- should be developed following best
practices. In particular, we call out the scripts and pipelines powering CI/CD.

**Explanation:** Particularly when developing scientific software, there is a
propensity to treat supporting software (such as the scripts used for CI/CD)
as less important than the main software product. While it is tempting to
rebrand the supporting software as "infrastructure" and consolidate this best
practice with the previous IaC best practice, the term IaC is usually reserved
for the environment the software will run in and the hardware it will run on.
Hence the need for a new term. CaC is a reminder that best practices benefit all
software, not just the primary software product.

## 1.3 Don't Repeat Yourself (DRY)

**Practice:** Each piece of information should stem from a single source of
truth. With respect to multi-project CI/CD obeying DRY requires us to factor out
common CI/CD components and maintain them as modular, reusable infrastructure.

**Explanation:** DRY is again not a new idea (see for
[example](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)). Adhering to
DRY prevents the need to update information in multiple places. Admittedly when
developing CI/CD it is often tempting to copy/paste a CI/CD pipeline from one
project to another. In practice, the problem with this is that when either
pipeline is modified or updated, the changes must manually be applied to all
copies. This is often an error-prone task as it is easy to forget to propagate
changes or to miss a copy. Furthermore, find-and-replace may not work if the
lines in question have been modified with project-specific information.

# 2. GitHub Actions

These are best practices for developing and maintaining multi-project CI/CD
pipelines on GitHub using GitHub Actions.

## 2.1 Maintain One Workflow Per Trigger Condition

**Practice:** Each unique triggering event should have its own workflow. It
should be noted that "push to dev" and "push to release" are different events,
even though they both trigger on pushes.

**Explanation:** Enabling GitHub Actions on a repository requires defining one
or more workflows in the `.github/workflows` directory. While one could in
theory have multiple workflow files that trigger on the same event, since these
workflows will always run together, it eases the maintenance burden to store
them in the same file (e.g., only need to update a single file). This can be
thought of as an application of
[DRY](/best_practices/#13-dont-repeat-yourself-dry) in the sense that we are
maintaining a single source of truth for what to do in response to the trigger.

## 2.2 Name (Entry) Workflows After The Trigger Condition

**Practice:**  For entry workflows, i.e., workflow which are not reusable
workflows, name the file after the event which triggers it.

**Explanation:** If a repository does not contain reusable workflows, then
following from the
[previous](/best_practices/#21-maintain-one-workflow-per-trigger-condition)
best practice, if there are `n` workflows in the repository it is because the
repository defines automations for `n` trigger condition. In turn, the `n`
workflows differ from one another on what condition triggers them, and a naming
scheme based on that trigger condition provides an unambigous means of
differentiating them.

GitHub mandates that all workflows, entry or reusable, must be stored
in the `.github/workflows` directory. Following from
[CaC](/best_practices/#12-treat-code-as-code-cac), if a repository contains one
or more reusable workflows it should also contain entry workflows to
provide CI/CD to the reusable workflow(s). To be consistent with the "no
reusable workflow" use case, we recommend naming the entry workflow as if
the reusable workflows were not present. Naming the reusable workflow is the
subject of best practice: (TODO: link when written).

## 2.3 Use Dedicated Repositories for Actions

**Practice:** Actions (composite or otherwise) should be developed in a
repository dedicated to the action.

**Explanation:** This is a best practice suggested by
[GitHub](https://tinyurl.com/28bct65m). Reasons suggested by GitHub include:

- versioning, tracking, and releasing the action like other software
- easier for the community to discover the action
- narrows the scope of the code base
- decouples the action's version from other application code

In addition, following this best practice conforms to GitHub's best practices
and thus means your code is less likely to break when/if GitHub changes things
and will be more self-explanatory to others familiar with GitHub's best
practices.

## 2.4 Store Actions in .github/actions

**Practice:** This only applies if you are ignoring
[Best Practice 2.3](/best_practices/#23-use-dedicated-repositories-for-actions).
If an action is housed in a repository with other content (e.g.,
other actions, reusable workflows, or the application code) then the
action should live in the `.github/actions/<name_of_action>` directory (where
`<name_of_action>` should be replaced with the actual name of the action).

**Explanation:** This is a best practice suggested by
[GitHub](https://tinyurl.com/28bct65m). Following it is recommended in order to
conform to expectations.


## 2.5 Store Reusable Workflows Separate From Application Code.

**Practice:** Reusable workflows should be stored in a repository which does
not include application code. Ideally reusable workflows should be stored in a
centralized repository (e.g., the `.github` repository or a dedicated CI/CD
repository), or each reusable workflow should be stored in a dedicated
repository.

**Explanation:** By their nature reusable workflows are intended for use from
multiple repositories. They also tend to not be tied to a single application.
The latter point means that, unlike actions, it is usually difficult to
unambiguously associate a reusable workflow with an application repository and
doing so would introduce unnecessary coupling. This suggests that it is more
natural to either store an organization's reusable actions together (the special
`.github` repository being a natural choice) or to create dedicated repositories
for each reusable workflow akin to the similar
[best practice](/best_practices/#23-use-dedicated-repositories-for-actions)
for actions.

## 2.6 Version CI/CD Components

**Practice:** All CI/CD components including: workflows, reusable workflows,
actions, and composite actions should be managed by version control. There are
a number of different options for how to version the components including:

- CI/CD components are stored in the same repository as a project. The
  components and the project are necessarily versioned the same.

  - This is a good choice for single-project CI/CD since the components are
    stored with the project they are coupled to.
  - For multi-project CI/CD this choice can make sense for composite actions
    which build and install the accompanying project.
    - While the composite action's version will be tied to the version of the
      repository,

- Workflows and actions maintained in a centralized repository are versioned
  together, but separately from projects in other repositories.
- Maintaining separate repositories for each workflow and action allows the
  workflows and actions to be versioned separately from one another and from
  projects in other repositories

**Explanation:** This is really an application of
[Code as Code (CaC)](/best_practices/#12-treat-code-as-code-cac); however, the
fact that it is not possible to separately version control pieces of the same
repository leads to a series of trade-offs and best practices for navigating
those trade-offs. Essentially developers must weigh having a plethora of repositories against the need for fine-grained versioning.
