---
layout: splash
permalink: /resources/
---

This page collects various resources which were used to build the content of 
this website. This is NOT meant to be an exhaustive list of resources.

# GitHub Actions

## [Official Documentation](https://tinyurl.com/3cyxmwpw)

We have primarily focused our efforts on GitHub actions. As such, we have 
made copious use of the official documentation. The official 
documentation is dense, but thorough. We recommend using it as a resource for
looking up APIs.

# CI/CD Resources

## [BSSW: What is Continuous Integration?](https://tinyurl.com/ysnkyeyv)

Excellent introduction to what is CI from the perspective of scientific
software. Does not however cover the CD aspect.

# Multiproject CI/CD Resources

## [Damien Aicheh Blog](https://tinyurl.com/474x99k9)

This blog post focuses on how to use GitHub composite actions to reduce
duplication. Of note it advocates for factoring composite actions out into a
new repository and using version tags to refer to a specific implementation of
the composite action. 

## [Best Practices for Reusable Workflows in GitHub Actions](https://earthly.dev/blog/github-actions-reusable-workflows/)

Provides a short tutorial on reusable workflows and points out some of the 
"gotchas" associated with them including:

1. Dependency management can be tricky because the workflow is coupled to its
   contents.
2. Workflows do not inherit environment variables, they must be passed in.
3. Can only be nested four times.
4. Can only call 20 reusable workflows from a file.

It also suggests a number of best practices:

1. Parameterize workflows by defining inputs/outputs. Use default arguments
   when it makes sense.
2. Document the workflow.
3. Leverage composite actions to make code more manageable.
4. Use a common workflow repository.
5. Version your workflow (use the version of the repo). 
6. Test workflows. Create test workflows for the workflows which test the
   inputs produce the correct outputs. Use matrix strategies to trigger
   multiple invocations.
7. Follow naming conventions.
8. Try to make reusable workflows cross-platform or clearly define the platform
   it targets.
9. Iterate and refine based on feedback.

Admittedly the common repository and versioning advice is somewhat at odds with 
the versioning advice in their composite action tutorial (see the next 
subsection) which suggests one action per repo in order to associate the version
with that action and not multiple actions. 

 
## [Understanding and Using Composite Actions in GitHub](https://earthly.dev/blog/composite-actions-github/)

Provides a short tutorial on composite actions and provides some best practices.
Specific recommendations:

1. Composite actions should be in individual repositories.
   - Allows the action to be versioned by git tags
   - GitHub won't let you publish it to their action store otherwise.
2. Version the composite actions.
   - Facilitates use without changes breaking them.
3. If you do put multiple composite actions into a single repo, put each in its
   own directory.
