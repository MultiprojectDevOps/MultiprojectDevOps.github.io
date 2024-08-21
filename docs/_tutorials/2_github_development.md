---
title: Developing Composite Actions
---

Developing GitHub Actions requires some familiarity with YAML (a recursive
acronym that stands for "YAML Ain't Markup Language"). Since YAML is a
common data serialization language, we assume the reader has familiarity
with it; if not, see [GitHub Actions Resources](/resources/#github-actions) for
resources which will quickly acquaint you with YAML.
{: .notice}

# Example Use Case: On-Demand Install

The source code for `hello_world_library` can be found
[here](https://github.com/MultiprojectDevOps/hello_world_library) and the
source code for `hello_world_executable` can be found
[here](https://github.com/MultiprojectDevOps/hello_world_executable).
{: .notice}

One of the most important uses of any programming language is printing the
literal string `Hello World` to the screen :wink:. To that end, we have
decided to develop a reusable library which downstream users can call to --- you
guessed it --- print `Hello World` to the screen. While such a library could be
written most easily in a scripting language like Python, we have opted for C++
on account of the additional complexity which emerges from needing to build the
software before using it (readers should be able to follow this tutorial with
no prior C++ experience).

To this end we create the `hello_world_library` repository which will house the
source code for the library, a workflow for testing pull requests, and a
composite action for building and installing the library. We also create the
`hello_world_executable` repository to serve as an example downstream user. The
`hello_world_executable` repository contains the source for the executable, and
a workflow for testing the executable when a pull request is opened. This is summarized below:

![Architecture for composite action tutorial](/tutorials/assets/tutorial_architecture.png)

The key CI/CD design consideration here is that building and installing the
library will need to happen in two places. First, the CI/CD for the
`hello_world_library` repository will need to be able to build and install the
library in order to test pull requests aimed at modifying it. Second, downstream
applications, like `hello_world_executable`, will need to build the library
during their CI/CD in order to link to it. When the `hello_world_library` and
one of its consumers are managed by the same organization, this creates the
potential for a large amount of duplicate CI/CD in violation of
[DRY](/best_practices/#13-dont-repeat-yourself-dry). The solution is to rely
on a composite action.

## The Solution

The full source of the composite action is available
[here](https://github.com/MultiprojectDevOps/hello_world_library/blob/main/.github/actions/install_hello_world_library/action.yaml).
{: .notice}

A simplified version of the composite action is shown below.

```yaml
name: "Install Hello World Library"
inputs:
  install_location:
    description: "Full path to where the library should be installed"
    required: false
    default: ${GITHUB_WORKSPACE}/install
  do_checkout:
    description: "Do we need to checkout Hello World Library?"
    required: false
    default: true
runs:
  using: "composite"
  steps:
    - name: Checkout Repository
      run: |
         if ${{ inputs.do_checkout }} ; then \
             git clone <removed for clarity>
         fi
      shell: bash
    - name: Get Compilers and CMake
      run: sudo apt install gcc-11 g++-11 cmake
      shell: bash
    - name: Configure Hello World Library
      run:  |
        cmake -Bbuild <removed for clarity>
      shell: bash
    - name: Build Hello World Library
      run: cmake --build ${{ inputs.work_directory }}/build
      shell: bash
    - name: Install Hello World Library
      run: cmake --build ${{ inputs.work_directory }}/build --target install
      shell: bash
```

The action takes two inputs, `install_location` and `do_checkout`, which
respectively tell the action where to install the library and whether it needs
to checkout the source code (when used to test pull requests to the ;`hello_world_library` repository the source code to build will already be
present). The remainder of the action: checks out the repository if required,
obtains the toolchain necessary to build the library, and installs the library
to the desired location. In the `hello_world_library` repository the composite
action is used like:

```yaml
name: Pull Request Workflow

on:
  pull_request:
    branches:
      - main

jobs:
  test_build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout Repository
      uses: actions/checkout@v4
    - name: Install Hello World Library
      uses: ./.github/actions/install_hello_world_library
      with:
        do_checkout: false
```

whereas in the `hello_world_executable` repository the composite action's usage
is:

```yaml
<removed for clarity>
steps:
  <removed for clarity>
  - name: Install Hello World Library
    uses: MultiprojectDevOps/hello_world_library/.github/actions/install_hello_world_library@v3.0.0
    with:
      work_directory: ${{ inputs.work_directory }}/../hello_world_library
      install_location: ${{ inputs.install_location }}
  - name: Configure Hello World Executable
      run:  |
        cmake -DCMAKE_PREFIX_PATH=${{ inputs.install_location }} \
        <removed for clarity>
```

The key take away from the code examples is that by relying on a composite
action we have avoided duplicating the configure, build, install logic for the
library.

Now you may be wondering "why a composite action, versus say a reusable
workflow?" The key reason is that if we had used a reusable workflow it would
NOT be possible for steps to occur after the library was installed. This means
that within the `hello_world_library` repository we could not test the
resulting library and within the `hello_world_executable` repository we could
not go on to build the application. Another motivation for using a composite
action is to avoid cluttering the downstream logs; put another way, most
downstream consumers are interested in analyzing their workflow's outputs and
do not want to sift through the logs for installing `hello_world_library` to
do so.
