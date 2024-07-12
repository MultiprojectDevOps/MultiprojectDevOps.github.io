---
layout: splash
permalink: /workshops/virtual_workshop1/summary/
---

# Summary of Virtual Workshop 1

At a high-level the key findings of this workshop were:

- Need to better define multiproject CI/CD ahead of time.
  - Is it writing a single CI/CD for multiple projects?
  - Is it reusing CI/CD components in multiple projects?
  - Is it what GitLab calls mutiproject pipelines (when an upstream project 
    calls the pipeline of a downstream project)?
- GitHub and GitLab are both used for scientific computing, with GitHub
  being preferred for open-source projects and GitLab for projects where 
  security is an issue.
- Approaches to multiproject CI/CD may need to vary depending on how tightly
  federated the projects are.
- Security remains a large consideration for some organizations, particularly
  when reusing CI/CD pipelines from other organizations.
  - Likely will need to separate out secure parts.
- When reuse is possible, GitHub/GitLab offer their own solutions; however,
  some devops engineers have also rolled their own solutionss such as relying on dedicated CI/CD repos or using git submodules.

The following subsections provide more detailed summaries of the two speakers
and the birds of a feather session that followed.

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
- Focus is on how CI/CD is currently being handled inside the NWChemEx stack.
- NWChemEx is a modular rewrite of NWChem using a combination of C++ and Python.
- Targets exascale machines, but intended for use on all platforms.
- Modules are interoperable so should be able to reuse CI infrastructure.
- Components are in separate repos, but want CI to be usable by those repos.
- Lots of potential CI/CD solutions on which to build multiproject CI/CD.
- Tried synching files in the past, but that was a mess.
- Using containerization to cache dependencies and ensure repeatable, 
  reproducible environment for each build.
- Use GitHub's reusable workflows to reference the CI stored in a central
  repository. Allows the CI to only be written once.
- Question: If a repo requires a change in the central CI do you have to make
  a separate version?
- Answer: Yes, but the NWChemEx CI is designed so one can ignore parts that are
  not relevant/sufficient while still using the parts that are.
- Question: Are you using containers in production?
- Answer: Not really. Containers are used to ensure reproducible builds.
- Question: How is the CI pulling the source code for each project being built?
- Answer: Uses the GitHub action for checking out repos. The build system takes
  care of pulling dependencies.
- Question: Does the CI/CD also ensure that external plugins work? Can you 
  merge if a change breaks an external plugin? 
- Answer: NWChemEx users can use as much or as little of the internal
  infrastructure as they choose. Beyond that it would be a case-by-case basis.


## BOF Session
- Question: With the CI/CD steps so tailored to the project, how does one write
  generic tutorials and best practices for multiproject CI/CD?
- Answer: Focusing more on how to structure the CI/CD, where to store the CI/CD
  pipelines, how to structure the directories, whether to have each repo provide
  its own build actions, etc.
- Question: Are you taking security into account? Using GitHub actions developed
  by other people could compromise secrets.
- Answers: 
  - Less of a concern for open-source projects because they only need
    secrets to deploy. Deployment actions are written by the organization and
    thus shouldn't be a security concern.
  - Can separate CI/CD so untrusted steps don't happen in the same workflow as
    trusted steps.
  - The Multiproject DevOps organization would welcome tutorials on 
    cybersecurity
- In the Julia ecosystem (specifically in optimization circles), packages will
  build downstream dependencies with their new changes to see if they break
  things. 
  - Spack package manager does similar testing to make sure changes still lead
    to consistent dependency trees.
  - Jenkins, GitHub, and GitLab make it easy to call other pipelines enabling
    this sort of downstream testing for other ecosystems. 
- Another strategy for multiproject CI/CD is to store the CI/CD in a repository
  that is included by other repositories via git submodules.
  - Strategy may not work on GitLab. 
- Approaches to multiproject CI/CD may vary for projects which form a loose
  federation versus those that are a more tight federation.
- Some projects can delegate CI/CD to a super project. Works best with a tight
  federation.
- Question: How do you test your CI/CD?
  - Not obvious what "testing CI" means.
  - Debugging CI is difficult, only tried and true solution is to run it and
    see what happens.
  - Code coverage can help. If code coverage suddenly drops CI/CD may not be
    running part of the pipeline.
  - CI errors caught quickly because they are run often.
  - Check for artifacts at end of build.
  - If CI is in separate repos can make changes on branches to avoid breaking
    downstream repos.
- Question: What sort of CI/CD pieces do people reuse?
  - Perhaps less of an issue for Python because the builds are easier.
  - Distinguish between reusing something someone else wrote vs. something the
    organization wrote. Again security concerns.
  - A lot of code duplication is small, and while it could be factored out, the
    code to call the factored code is just as repetitive.
- Question: Are the testing and build machinery the same?
  - Concerns about secrets and CI passing suggest people treat them the same.

