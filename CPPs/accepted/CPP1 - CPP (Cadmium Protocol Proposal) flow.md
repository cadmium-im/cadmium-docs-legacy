# CPP#1 - Cadmium Protocol Proposals flow

## Introduction

This proposal about regulating whole flow of Cadmium Protocol Proposals. Also this proposal is an example of proposal composition.

## Message type identifiers

This proposal adds no identifiers to message types.

## Glossary

There is no terms to define that specific to this proposal.

## Use Cases

### Initial proposal discussion

Initial proposal discussion should take it's place at [our YouTrack instance](https://track.cadmium.im/issues/SPEC) by creating new issue. So even before proposal will be ready you MUST create an issue in "Specification" project about our proposal.

Issue body SHOULD contain complete body for your proposal, not just something like "let's use SMTP as connectivity channel" but rather complete description of your proposal (even in free form) with technical details which can be used for discussion.

This step is required to proceed to next stage.

This stage SHOULD take two (2) weeks maximum counting from issue creation date. It also might take even 1 minute before proceeding to next stage if proposal was discussed before using other channels (email, chats, etc.).

#### Voting

Voting about giving green or red light for proposal SHOULD be taken using:

* "Thumbs up" icon in proposal issue in YouTrack (it's located near issue title in the very right corner). This will show community interest in this proposal and might speed up core team and developers acceptance.

* Using comments in proposal issue. This is acceptable only for core team and developers to distinguish them from community votes. Also this is a "counter" which shows who voted from these groups. All other "thumbs up/down" posts will be removed from YouTrack and author of such posts may be banned.

After that you will receive "green light" to create pull request to [documentational project in git](https://git.cadmium.im/cadmium/documentation) with numeric ID to use for your proposal or "red light" if community and/or core team and developers won't accept your proposal. In case of denial complete reason SHOULD be specified in comments to this issue.

When issue is closed - there is no possibility to reopen it.

### Proposing your proposal

When preliminary stage is over and you've received "green light" for creating pull request to [documentational project in git](https://git.cadmium.im/cadmium/documentation) you SHOULD:

1. Ask for proposal ID to use in issue's comments or in one of our chats from core developers. Core developer should also specify given ID in comments in issue on our YouTrack instance.
2. Clone documentational repository to your local machine.
3. Create new branch named same as YouTrack issue (e.g. ``SPEC-111``). You should create new branch using ``master`` branch as starting point.
4. Create file in ``CPPs/proposed`` directory named like ``CPP123 - Your super enchancement proposal.md``. See examples in directories placed under ``CPPs``.
5. Use template from ``cadmium-proposal-document-format.md`` placed in ``CPPs`` directory to describe your proposal in file created at step #3.
6. Push branch to git and create pull request to ``master`` branch.

To attach your commits into YouTrack issue please add full YouTrack issue ID (e.g. ``SPEC-123``) into commit message, otherwise commit might be lost and proposal might be eventually denied.

**Please note that CPP ID you'll receive from core developer might not be the very same as issue ID in YouTrack.**

This stage could take up to 2 (two) months. You should take this time and polish your proposal as much as possible and to receive as much votes as possible.

This two-months-period is a maximum time range that can be used for discussing and polishing your proposal. This means that:

* If you're done in 2 weeks - your CPP may be accepted.
* If you're done in 1.5 months - your CPP may be accepted.
* If you're done in ``$(two months - 1 day)`` - your CPP may be accepted.
* If you haven't done in two months - your CPP WILL BE rejected.

### Actions for accepted proposal

When proposal is accepted it should be moved to ``accepted`` directory from ``proposed`` keeping it's CPP ID. This SHOULD be done by proposer himself. Failing to do so might result in banning from future proposals discussions.

After moving proposal's pull request SHOULD be merged in master. Right after that proposal considered as part of protocol and core developers SHOULD update protocol documentation to reflect changes.

### Rejected proposals

If proposal was rejected - YouTrack issue should be closed as well as pull request on Gitea without merging into master branch.

## Error Codes

This proposal adds no error codes to protocol.

## Business Rules

This proposal describes no business rules for protocol or applications but rather for community and developers.

## Security Considerations

This proposal have no security considerations.

## Acknowledgements

This document heavily inspired by Medium RFC#1 (which was also written by me) which was inspired by IETF RFCs.
