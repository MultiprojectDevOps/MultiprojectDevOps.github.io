---
title: Developing Reusable Workflows
---

This tutorial continues our discussion of multi-project CI/CD using GitHub
Actions. This tutorial focuses on reusable workflows and assumes you have read
the composite actions
[tutorial](/tutorials/2_composit_actions) and/or are familiar with them.

# Example Use Case: Code Quality Standards

The source code for `hello_world_library` can be found
[here](https://github.com/MultiprojectDevOps/hello_world_library), the
source code for `hello_world_executable` can be found
[here](https://github.com/MultiprojectDevOps/hello_world_executable), and
the source code for `.github` can be found
[here](https://github.com/MultiprojectDevOps/.github).
{: .notice}

In the
[On-Demand Install Tutorial](https://multiprojectdevops.github.io/tutorials/2_composite_actions/#example-use-case-on-demand-install)
we created a composite action that installed our library as part of the CI/CD
workflow. This action was used in the CI/CD workflow of the
`hello_world_library` to verify that changes to the library did not break it
and it was used in the CI/CD workflow for the `hello_world_executable` to
build the library for the executable. Now that our organization has working
software, we want to enforce code-quality standards for the source code.

For the sake of this example we have settled on two quality checks. First, every
source file should include a license header that assigns the copyright to the
GitHub organization and licenses the file under the Apache 2.0 open-source
license. Second, we want to ensure a consistent format throughout the source
code. Thankfully GitHub actions already exist for both of these tasks (e.g.,
[licensing](https://github.com/apache/skywalking-eyes) and
[formatting](https://github.com/DoozyX/clang-format-lint-action)).

From the design standpoint it is important to note that
`hello_world_library` and `hello_world_executable` are both written in C++ and
managed by our organization. Hence enforcement of these standards will look
similar for each repository. Rather than duplicating the CI/CD jobs for
enforcing these standards we will create a reusable workflow. Following
[best practices](/best_practices/#25-store-reusable-workflows-separate-from-application-code)
we will store the reusable workflow in the centralized `.github` repository.
The relevant pieces of the architecture are summarized below.

![Architecture for reusable workflow tutorial](/tutorials/assets/reusable_workflow_architecture.png)

## The Solution

The full source of the reusable workflow is available
[here](https://github.com/MultiprojectDevOps/.github/.github/workflows/pull_request_common.yaml).
{: .notice}

A simplified version of the reusable workflow is shown below.

```yaml
name: Multi-Project DevOps Common C++ Pull Request

on:
  workflow_call:
    inputs:
      source_directories:
        description: "Space separated list of source directories"
        type: string
        required: true

jobs:

  check_license:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: apache/skywalking-eyes@v0.4.0

  format_code:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: DoozyX/clang-format-lint-action@v0.12
        with:
          source: ${{ inputs.source_directories }}
          extensions: "hpp,cpp"
```

The workflow depends on one input, `source_directories` which is the list of
directories containing C++ source files. When called it then launches two jobs.
The first job, `check_license`, checks out the pull request and then runs a
licensing action on it. The action will fail if any files are not properly
licensed. The second job, `format_code`, invokes clang-format (a commonly used
tool for formatting C/C++ source files) on the directories the user provided. It
will fail if any of the files are not properly formatted.

In `hello_world_library`, this reusable workflow is called from the
`pull_requst.yaml` workflow like:

{% highlight yaml linenos %}
name: Pull Request Workflow

on:
  pull_request:
    branches:
      - main

jobs:

 maintenance:
    uses: MultiprojectDevOps/.github/.github/workflows/pull_request_common.yaml@main
    with:
      source_directories: 'include  src'
  test_build:
    runs-on: ubuntu-latest
    steps:
        <removed for clarity>
{% endhighlight %}

The key lines are line numbers 10 through 13. Line 10 defines a new job called
"maintenance" which invokes the reusable workflow (this is explicitly done on
line 11). Lines 12 and 13 specify the directories which contain C++ source
files. Use of the reusable workflow from `hello_world_executable` is similar:

{% highlight yaml linenos %}
name: Pull Request Workflow

on:
  pull_request:
    branches:
      - main

jobs:
  maintenance:
    uses: MultiprojectDevOps/.github/.github/workflows/pull_request_common.yaml@main
    with:
      source_directories: 'src'

  test_build:
    runs-on: ubuntu-latest
    steps:
        <removed for clarity>
{% endhighlight %}

The key difference is line 12 where we only pass the `src` directory (the
executable has no `include` directory). The need to pass different directories
was the reason behind the `source_directories` input.

Similar to previous tutorials, you may be wondering "Why a reusable workflow
as opposed to a composite action?". The key reason is we are grouping together
jobs that are common to each workflow. With a composite action we can only group
together steps. Admittedly you may wonder "Why treat licensing and formatting
as two separate jobs versus say two steps in the same job?" Reason one is we
want the jobs to execute in parallel for efficiency. Reason two is that we
want more fine-grained logging of failures, i.e., we want the user to know
immediately if the CI/CD workflow failed because of licensing and/or
formatting. If the steps were combined the user would have to sift through the
combined log to distinguish this.
