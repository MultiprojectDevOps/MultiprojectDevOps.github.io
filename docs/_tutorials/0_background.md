---
title: Background on Multi-Project CI/CD
---

As far as we know multi-project CI/CD is a relatively new concept, albeit an
obvious extension of traditional single-project CI/CD. To that end this tutorial
provides some high-level background information on DevOps, CI/CD, and
multi-project CI/CD.

# What is DevOps?

[Wikipedia](https://en.wikipedia.org/wiki/DevOps) defines DevOps as:

>a set of practices and tools. DevOps integrates and automates the work of
>software development and information technology as a means of decreasing the
>time needed to deploy high-quality software.

The key idea is that the software developers (people actually writing the
software) and the people on the operations side of things (those maintaining the
software) should not be siloed, but integrated. For the purposes of developing scientific software, the key aspects of DevOps are:

1. A desire to shorten development by automating as much as possible.
2. A philosophy that infrastructure should be treated as code.
3.

The first points places a large emphasis on developing automated solutions.
These solutions are usually in the form of scripts or workflows. The second
point means that the environment should be maintained through scripts,
configuration files, etc. instead of through say graphical user interfaces.
(the compute) following best
practices for software development, i.e., do not treat the automated solutions
as one-off, throw-away code.

# What is CI/CD?

Short for continuous integration and continuous deployment (depending on the
source the "D" may instead stand for delivery), CI/CD is the process of
automatically building, testing, and deploying software. The process is
summarized in the following figure.

![Typical Single-Project CI/CD Pipeline](/tutorials/assets/single_ci.png)

Explaining the steps in more detail is beyond our current scope, and would
"reinvent the wheel" since there are a number of really good tutorials already
out there. For more information on single project CI/CD see
[CI/CD Resources](/resources/#cicd-resources).

# What is the difference between DevOps and CI/CD?

CI/CD, and the practices underlying it, are a subfield of DevOps. Admittedly,
when it comes to developing scientific software DevOps and CI/CD are nearly
synonymous; this is because most scientific software teams:

- do not rigorously follow agile,
- are not developing cloud applications (which is almost required for the truly
  automated end-to-end realization of DevOps), and
- have no distinction between developers and operations staff (operations is
  something developers do in their "spare time.").

Point being, in the tech sector distinguishing between CI/CD and DevOps is much
easier.

# Shouldn't the organization be called Multi-Project CI/CD?

While it is true that the initial focus of the organization is primarily on
CI/CD, the name Multi-Project DevOps was chosen to be aspirational, i.e., we're
hoping to eventually branch out to other aspects of DevOps beyond CI/CD. We
would also be remiss if we didn't admit that other key factors in choosing the
organization name were that:

- MultiProjectContinuousIntegrationContinuousDeployment is really long,
- MultiProjectCI/CD is not allowed because of the "/", and
- MultiProjectCICD is hard to visually parse.

# What is multi-project CI/CD?

In the present context the adjective "multi-project" is used to denote
that we are considering not just a single project's CI/CD pipeline, but the
CI/CD pipelines for multiple projects simultaneously. From this definition it
should hopefully be clear that single-project CI/CD is a subset of multi-project
CI/CD and that the considerations for multi-project CI/CD are thus a superset
of the considerations for single-project CI/CD.

# How is multi-project CI/CD different from single project CI/CD?

When maintaining CI/CD for *N* projects it is important to note that the
projects are often not *N* random projects. Rather the projects often share
some development aspects, for example because they are developed by a common
group of developers, or because they are part of a shared ecosystem. The result
is that the the *N* pipelines tend to have common needs, e.g.:

- common software dependencies,
- use of the same build system, and/or
- reuse of automated solutions.

The need to maintain functionalities used by multiple CI/CD pipelines is the
primary difference between multi- project and single project CI/CD.

![Single project vs. multi-project CI/CD](/tutorials/assets/single_vs_multi.png)

# What are the best practices for maintaining multi-project CI/CD pipelines?

Other tutorials in this series are designed to delve into this topic in more
detail, but briefly it boils down to "treat CI/CD pipelines as code." More
specifically, since the CI/CD pieces are being used by multiple projects it
becomes even more important to:

- modularize and reuse CI/CD components
- document CI/CD components
- test changes/updates to the CI/CD components
- consider security ramifications of sharing components
