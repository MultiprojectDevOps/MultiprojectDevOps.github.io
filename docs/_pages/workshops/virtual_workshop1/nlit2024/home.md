---
layout: splash
permalink: /workshop_reports/nlit2024/
title: BoF at NLIT 2024
---
# BoF at NLIT 2024

Ryan Richard is thankful for having had the opportunity to hold a Birds of a Feather (BoF) workshop at the 2024 National Laboratory Information Technology Summit in Seattle, Washington ([Conference Website](https://www.fbcinc.com/e/nlit/default.aspx)) on 4/11/2024 from 8:00 AM to 8:30 AM PDT. Ryan is also thankful to the many people who participated in the workshop. The results of this workshop are summarized on this page.

## What pieces of CI/CD do you currently reuse among projects?

This was the icebreaker question. The participants identified:

1. Secrets
2. Artifacts
3. CI/CD pipelines

## How do you currently reuse CI/CD pieces among projects?

In responding to the first question, several participants also identified how they achieved reuse. Naturally, prompting this question. Participant answers included:

1. Artifacts reuse is facilitated by jfrog and sonatype
2. CI/CD pipelines reused through:
   - GitLab components
   - Sharing of templates (presumably through version control)
   - Raw YAML files (copy/paste)

## How are people doing dependency management?

At this point the conversation became more organic and participants noted that one of the hardest parts of CI/CD maintenance is managing dependencies. To which a participant posed this question. Of note, in the context of this question dependency management seemed to be understood by the participants as simply making sure the target software builds. To that end the participants noted:

- Some repos intentionally do not preserve backwards compatibility
- Hard to have all integration tests needed
- Can't test every combination of versions.
- Many developers struggle with versioning and git

## What tests do your CI/CD pipelines perform?

While the participants were passionate about discussing dependency management, for the sake of time, the topic was changed to types of tests via this question. With respect to this question the participants noted:

- CI/CD not enforced.
- Rely primarily on acceptance testing (little unit testing)
- Have considered a tiered approval solution.
- Some developers separate their components to avoid CI/CD

## Summary

NLIT is a conference which brings together members of the US lab system's IT community. Generally speaking, many of the members in participation showed interest in multi-project CI/CD, but were still a ways out from implementing multi-project solutions on account of still dealing with more fundamental software engineering problems. With respect to multi-project CI/CD the key takeaways were:

- Interest in sharing more than just workflows, e.g., secrets and artifacts.
- Reuse relies on a mix of features provided by the host of the source code as well as features from third party applications
- There is still a need for permeating better practices throughout the developer community.
