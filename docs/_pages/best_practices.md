---
layout: splash
permalink: /best_practices/
title: Best Practices for Multi-Project CI/CD
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

## 2.1 Version CI/CD Components

**Practice:** All CI/CD components including: workflows, reusable workflows,
actions, and composite actions should be managed by version control. There are
a number of different options for how to version the components including:

- CI/CD components are stored in the same repository as a project. The
  components and the project are necessarily versioned the same.

  - This is a good choice for single-project CI/CD since the components are
    stored with the project they are coupled to.
  - For multi-project CI/CD this choice can make sense for composite actions
    which build and install the accompanying project.

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
