---
layout: splash
permalink: /workshops/virtual_workshop1/summary/
---

# Summary of Virtual Workshop 1

## Speaker Adrien Bernede (LLNL)
- Use GitLab to host CI, but not for hosting (hosted on GitHub)
- Adrien is working on radius project (TODO: check name in transcript is right)
  which is an umbrella project supporting more than 30 open source projects at
  LLNL.
- Have several projects and want to build them together.
- Use Spack to build the software stack.
- Use parent-child GitLab CI pipelines.
- Each project defines its child pipeline and inputs it takes. The child 
  pipeline is then run by the parent pipeline 
  (TODO: Verify)
- Users can see what has been validated and use it in their pipelines
- Relies on Cmake cache for synchronizing dependencies (TODO: verify)
- Setup for a project looks like a list of specs.
- Common CI infrastructure sets project up for using GitLab's multiproject CI
- GitLab's multiproject CI requires one to be careful with how status are 
  reported (don't want to overwrite downstream dependency's status).
- Question: Do you have to keep track of the downstream branches to test?
  - Answer: Would test against master downstream branches because the idea is to
    make sure the change to the upstream repo doesn't break the downstream
    repos.

## Speaker Jonathan Waldrop (AMES)

Notes from chat:
On GitHub vs GitLab
- GitLab used internally for rapid development, GitHub for public release.
- SNL used to use GitHub, but has now moved fully to GitLab.
- LLNL is using GitHub for open-source projects, and GitLab for internal projects as a successor to Bitbucket/Bamboo.

On testing CI
- Don't know what "testing CI" really means
- I've had a lot of trouble "debugging CI" -- it seems the only way to really get over the humps to making your CI work is to run it, wait for a runner to pick up, (on an hpc system) deal with queues, ...

On best practices:
- Fun fact is that I actually encourage folks to consider CI configuration files as code, in the sense of avoiding duplication, keeping the code clean and documented. But I am not really "testing" the CI.
-  I'll admit that we never really thought about consolidating our CI to avoid repetition. We just have very similar GH actions workflows in multiple repos, but no formal mechanism to keep them in sync.
- we have a lot of ~10 LOC steps in GHA yamls that _could_ be wrapped up into standalone actions but it's a headache when 20% of the time one of those lines needs something different ... so then you either need to inject an option or just use the code itself anyway
- GHA are meant to describe steps of your CI workflow, aren’t there. Is there a way in github to write a workflow that would be a "template" for other project to use ?

Takeaways

- Need to better define multiproject CI/CD ahead of time.
  - Is it writing a single CI/CD for multiple projects?
  - Is it reusing CI/CD components in multiple projects?
  - Is it what GitLab calls mutiproject pipelines (when an upstream project 
    calls the pipeline of a downstream project)?