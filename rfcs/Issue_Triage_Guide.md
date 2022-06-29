# O3DE Simulation SIG - Preliminary Issue Triage Guide

While the [common issue triage guide for O3DE](https://github.com/o3de/community/issues/130) has not yet been established, this proposal establishes a preliminary issue triage guide for SIG-Simulation. It is intended to be replaced by the official triage guide, once approved.

## Overview

This guide covers how to triage GitHub issues for SIG-Simulation. Maintainers are encouraged to use and update this guide to ensure all contributors to SIG-Simulation understands how issues are handled and accepted by the SIG.

##  Issue Triaging

Triaging is the process used to handle intake of issues into the SIG-Simulation backlog. The process aims to ensure issues are both relevant to SIG-Simulation and contain sufficient information so that the community can take action.

Process aims to ensure that:
* Issues are appropriate for SIG-Simulation. Confirms that issues are actual issues, rather than requests for help or issues for another SIG.
* Issues have clear information to enable SIG-Simulation to address the problem or request.
* Issues are regularly maintained and updated until they are resolved.  
* Issue load is balanced across SIG maintainers when action is required.
* All the SIG-Simulation community can participate.

# Process

SIG-Simulation triages issues once a week on [Thursday](https://lists.o3de.org/g/o3de-calendar/calendar). Anyone is welcome to attend. Triage will be led by SIG chair, co-chair or maintainer (referred to below as *Triage Leader*)

## Triage Leader Guide

1. Join the SIG-Simulation discord voice channel
2. Announce yourself as the Triage Leader and wait a few minutes for others to join the call.
3. Use the *Individual Issue Triage* guide below to process all new issues for SIG:
   1. Review any open issues with [needs-sig](https://github.com/o3de/o3de/issues?q=is%3Aissue+is%3Aopen+label%3Aneeds-sig) that may be for SIG Simulation
        1. Remove `needs-sig` and add `sig/simulation`. These items will now show up when reviewing issues below.    
   2. Process the main repository issues
        1. [Critical issues](https://github.com/o3de/o3de/issues?q=is%3Aissue+is%3Aopen+label%3Aneeds-triage+label%3Asig%2Fsimulation++label%3Apriority%2Fcritical)
        2. [Major issues](https://github.com/o3de/o3de/issues?q=is%3Aissue+is%3Aopen+label%3Aneeds-triage+label%3Asig%2Fsimulation++label%3Apriority%2Fmajor)
        3. [Other issues sorted by age](https://github.com/o3de/o3de/issues?q=is%3Aissue+is%3Aopen+label%3Aneeds-triage+label%3Asig%2Fsimulation+sort%3Acreated-desc)
   4. Process all the new [SIG-Simulation](https://github.com/o3de/sig-simulation/issues) issues in a similar way.

If there are questions about what to do with an issue please raise questions with SIG Chair(s) or start a conversation in SIG-Simulation.

### Individual Issue Triage

1. (Recommendation) Announce issue number and title to those in Discord voice channel, so others can follow along. 
4. (Main issues only) Ensure issue is for SIG-Simulation. 
   1. If the issue is not for SIG-Simulation, remove the `sig/simulation` label and comment on the issue as to reason why issue is not for SIG-Simulation. 
   2. If the correct SIG is known assign issue to that SIG. Otherwise, add the `needs-sig` label so the general O3DE issue triage meeting can find the appropriate owners. 
5. Review the issue and any comments to see if it can be accepted by SIG
   1. **If issue is a bug**: Check that report has enough information for someone to reproduce or understand the issue?
   2. **If issue is a feature request**: Review the technical implications of the request. 
      1. If it's a large change then the issue should become an RFC or be brought to the SIG-Simulation meeting for discussion. 
         1. Ask requestor to bring the issue back as an RFC or start a discussion topic.
         2. Add the issue to the next SIG-Simulation meeting agenda, if that would be more appropriate. 
6. If issue can be **accepted** then:
   1. If issue is a bug, add the `kind/bug` label.
   2. If issue is for a feature request, add either `kind/feature`, `kind/enhancement`, or `kind/task`. Add the relevant feature labels as appropriate, e.g., `feature/physics` or `feature/animation`. 
   5. Set a **priority** using a `priority/X` tag  for issue based on impact (ask other SIG members on call for guidance).  For feature requests, also set an appropriate `feature-need/X` tag.
   7. Mark the issue as `triage/accepted`. 
8. If the issue **requires more information** or is **rejected**, then:
   1. Assign a reviewer, if required, to handle follow-up comments, to reproduce the issue or ask for further clarifying information.
   4. **If issue is rejected**: Reviewer/triage leader should reject issue and provide reason for rejection. 
      1. Mark the issue as `triage/declined`.
   5. **If issue needs more information**: Reviewer/triage leader should add clear comments requesting the additional information. 
      1. Mark the issue with `triage/needs-information`. It's recommended that all issues in this state have an assigned reviewer who will track updates until all required information is received. Issue can then be reconsidered for acceptance.
9. If we need feedback on a particular UX workflow, add labels `needs-ux-info` or `needs-ux-action`.
10. Remove the `needs-triage` label from issue.
11. Add comments to issue, when appropriate, to capture issue triage decisions.

### Additional Labels to Consider for Contributors

* Consider adding the `good-first-issue` label to identify issues that have straightforward/simple fixes for new contributors to fix. Examples could include config, docs, comments and testing changes.
* Consider adding the `help-wanted` label for issues that do not have immediate resourcing and contributions by others would be welcome.

## Additional Triage Tasks

If time permits (select some of these):

* Review all open [bugs without acceptance](https://github.com/o3de/o3de/issues?q=is%3Aissue+is%3Aopen+label%3Asig%2Fsimulation+-label%3Atriage%2Faccepted) and ensure they have `triage/accepted`.
  * Ensures issue that have been assigned to SIG are captured.
* Review any open [blocker](https://github.com/o3de/o3de/issues?q=is%3Aissue+is%3Aopen+label%3Asig%2Fsimulation+label%3Apriority%2Fblocker) and [critical](https://github.com/o3de/o3de/issues?q=is%3Aissue+is%3Aopen+label%3Asig%2Fsimulation+label%3Apriority%2Fcritical) issues in the main repository:
  * Ensures priority is still valid. Ensures issues are still valid.
  * Assign any required commentators or ask for updates.
* Review PRs for the SIG that are more than [30 days old](https://github.com/o3de/o3de/pulls?q=is%3Apr+is%3Aopen+label%3Asig%2Fsimulation+sort%3Acreated-asc) old, ensure PR appears to be active or ask contributor for to update PR or close out. 
  * Ensures PRs are still active and are not blocked on contributor or maintainer action.  
* Review issues that are more than [90 days old](https://github.com/o3de/o3de/issues?q=is%3Aissue+is%3Aopen+label%3Asig%2Fsimulation+sort%3Acreated-asc)
  * Ensures issues are still relevant to SIG. 

## Issue Workflow

If you are assigned an issue to validate, work with requestor to get enough information to validate the issue.

If it can be reproduced then:

* Add a comment to confirm reproduction and add `triage/accepted` labels.
* Define priority with SIG Chair(s).
* Ensure issue is not a duplicate.

If issue cannot be reproduced then:

* Comment on the issue and ask the requester for more information to aid reproduction, add the `triage/needs-information` label.
* Or close the issue if both parties agree that this is not an issue/not reproducible.

If the issue is not clear or needs more information:

* Comment on the issue and add the `triage/needs-information` label to show that the requestor needs to provide more information.

## Stale or Abandoned issues

SIG-Simulation will periodically audit for stale items. If during triage, you encounter stale issues, use the guidance below to see if issue should be closed.

## No Activity for 30 days

An issue without `triage/accepted` label is considered abandoned if there has been no activity for *30* days, especially if has had `triage/needs-information` label applied and there has been no follow-up from issue reporter.  Abandoned issues **can** be closed. Closed issues may be reopened if additional information is provided.

## FAQ

1. What should I do if triage rejects my issue?
   1. Issues should be rejected with clear comments that provide reason for rejection. If you disagree or want to discuss the reason please start a chat in SIG-Simulation or add as an agenda item for SIG-Simulation's public meeting.
   2. If you still do not support SIG-Simulation's decision, then please raise with the [O3DE TSC](https://github.com/o3de/tsc).
2. What should I do if I have an urgent issue that cannot not wait for public issue triage?
   1. Please raise the issue in [SIG-Simulation Discord](https://discord.gg/6nSYcUBaR3) chat and ask for triage, this is so all SIG has visibility.
   2. SIG-Chair(s) can appoint a reviewer to ensure issue is triaged as soon as possible.
   3. If the intent is for you to work on the issue immediately, then please self-assign or work with a maintainer to assign.
3. What should I do if I reported an issue but no action has been taken?
   1. If an issue with the SIG-Simulation label has had no updates for a while (14 days), follow-up with the SIG, either through [Discord chat channel](https://discord.gg/6nSYcUBaR3), [triage or standard meeting](https://lists.o3de.org/g/o3de-calendar/calendar). Consider attending a SIG-Simulation meeting to raise the issue for discussion.
4. What can I do if I have a question for SIG-Simulation?
    1. The preferred way to raise questions is through the [SIG-Simulation Discord chat channel](https://discord.gg/6nSYcUBaR3) or the [SIG-Simulation discussion panel](https://github.com/o3de/sig-simulation/discussions).


## Acknowledgments

This guide was informed by the [SIG-Network Triage Guide](https://raw.githubusercontent.com/o3de/sig-network/main/TRIAGE_GUIDE.md)
