---
layout: splash
permalink: /workshops/virtual_workshop2/summary/
---

# Summary of Virtual Workshop 2

Key findings of the workshop were:

- Dependency management remains a tricky problem that is coupled to CI/CD.
- Open problem to ensure that external components remain correct.

## Speaker Ryan M. Richard (AMES)

- [Video](https://youtu.be/v4iXgqOoa3c)
- Ryan discussed the best practices for multi-project CI/CD which have emerged
  so far.
- Best practices can be found [here](/best_practices/)

## BOF Session
Disclaimer: many of the attendees attended for the presentation and did not 
attend the BOF session that followed. Thus discussion was more limited.
{: .notice}

- Attendees shared experiences maintaining multi-project CI/CD ecosystems.
  - Python-based projects easier to maintain because they do not require
    compilation, i.e., significantly simpler CI workflows. 
  - In Python-based plugin ecosystems, dropping support for an old version of 
    Python requires modifying the CI workflows of each plugin.
- Attendees discussed open challenges in maintaining multi-project CI/CD
  ecosystems.
  - CI/CD workflows which determine if changes break downstream projects,
    especially when plugins are externally developed.
    - Attendees knew of other projects where the core project agreed to host
      CI/CD for all projects in order to ensure correctness.       