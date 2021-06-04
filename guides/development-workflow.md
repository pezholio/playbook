---
title: Development workflow
---

- [Development principles](#development-principles)
- [Introduction](#introduction)
- [The lifecycle of a story](#the-lifecycle-of-a-story)
- [Working on a support ticket](#working-on-a-support-ticket)
- [Deploying](#deploying)
- [Branching](#branching)
- [Committing](#committing)
- [Code review](#code-review)
- [Code style](#code-style)
- [Pull requests](#pull-requests)
- [Versioning releases](#versioning-releases)
- [Tracking changes](#tracking-changes)

## Introduction

### Development principles

#### Communicate frequently

- learn from each other
- make shared decisions
- prevent duplication
- solve problems quicker

#### Two pairs of eyes

All code we write should be reviewed by at least one peer

- build shared understanding
- build shared knowledge of code bases
- ship safer and higher quality code
- learn new skills and techniques

#### Share our progress

All code we write is represented in Trello (or similar) so that we can:

- visualise progress
- be confident what we should be working on
- react to blockers and bottlenecks
- see what each other is responsible for and opportunities to help or avoid
  duplicating effort
- document our progress to the wider team
- reference commits back to their original user needs

#### Own your code

We presume that the author of a piece of work is responsible for it from start
to finish. From writing the code, soliciting feedback, committing follow-up
changes to merging it in.

We do not expect other contributors to make changes to work that has already
been started by someone else, for example adding commits to an open pull
request. Though, you can ask permission to do so if you believe that would be
mutually beneficial.

The author should still be free to hand over full or partial responsibility of a
piece of work.

- creates a more stable working environment
- releasing a change to live is an achievement that should be felt
- gives us opportunity to learn from others so that we make the changes
  ourselves

#### Do the smallest amount of good work

We aspire to regularly ship
[good code](https://robots.thoughtbot.com/what-is-good-code) and deliver it to
the users as often as possible. Doing so efficiently requires a balanced
approach to development.

We believe that breaking down our work into small slices which are focused on
solving one problem well, is better than trying to propose multiple changes at
once. Small, focused slices are easier to understand, review and are
encapsulated if you'd like to revert or cherry-pick them.

#### The twenty-minute rule

1. being helpful is one of our values and this extends to development. If after
   spending 20 minutes on a problem you haven't made progress, invoke the
   twenty-minute rule with another developer and get their advice
1. discussions that go on for twenty minutes or more can be a time sink. If an
   agreement on how to progress cannot be found after 20 minutes, mentioning
   this rule provides a pre-agreed way to acknowledge the deadlock. Use this as
   an opportunity to rate how concerned we each are about the issue or escalate
   to another peer who could help break the deadlock

---

## The lifecycle of a story

Different projects have small variations to this process, but here are some
general guidelines.

### Start work on a new story

1. assign yourself to the Trello card
1. move the card to indicate you've started work
1. [make a new branch from develop](#how-to-make-a-branch)
1. write testable code that meets the acceptance criteria
1. update the [changelog](#tracking-changes)
1. [commit](#committing) your code

### Submit your story for code review

1. push your branch to the remote repository

   ```
   git push origin <branch-name>
   ```

1. create a [pull request](#pull-requests) into the `develop` branch
1. link the Trello card to the new pull request, linking directly or by using
   the Trello
   [power up](https://help.trello.com/article/1021-integrating-trello-with-zendesk)
1. move the card to indicate it's now ready for review by a peer

### Reviewing another developer's pull request

- move the Trello card to indicate you're now looking at it
- find and read the Trello card's acceptance criteria
- find and read through the pull request, carrying out a
  [code review](#code-review)
  - if you think it's ready:
    - leave your feedback (positive comments are great to receive)
    - approve the pull request
  - if you think it's ready:
    - leave your feedback
    - consider letting the author know by Slack that it's back with them

---

## Working on a support ticket

Support tickets can fall into two types of work flow depending on their
severity.

Most commonly tickets will simply fall into the same
[story process](#the-lifecycle-of-a-story), where fixes will go through
`develop` and Staging for review and acceptance.

They can however be sometimes when there's an emergency which requires a change
to production ASAP. This is when we reach for a [hotfix](#hotfix).

### Hotfix

We think of hotfixes in the same way as
[Wikipedia](https://en.wikipedia.org/wiki/Hotfix) describes them:

> a hotfix implies that the change may have been made quickly and outside normal
> development and testing processes

and also

> A hotfix is a change deemed critical enough that it cannot be held off until a
> regular content patch.

An emergency for instance would be when:

1. production is unusable - users are directly affected
1. a serious vulnerability has been discovered and is currently exploitable

#### Issue a hotfix

When the decision to make a hotfix is made it's important to communicate well.
Involve the rest of the team at this point and discuss the plan of action,
allowing delivery managers to communicate back to the client.

This is the only time a branch should be based off of `master` instead of
`develop`.

1. create a new card in Trello and move it to 'In progress'
1. create a new branch off of `master`

   ```
   git checkout -b <branch-name> origin/master
   ```

1. [commit](#committing) the fix
1. make a [pull request](#pull-requests) into `master`
1. move the Trello card into 'Awaiting review'

#### Review and deploy a hotfix

1. review the code
1. check the fix has been tested locally if possible
1. merge the pull request
1. manually merge the pull request back into `develop`

   ```
   git checkout develop
   git pull
   git merge origin/master
   git push
   ```

1. move the card on Trello into 'Deployed to production'
1. update the [changelog](#tracking-changes)

---

## Deploying

Our approach is one of
[continuous delivery](https://en.wikipedia.org/wiki/Continuous_delivery) meaning
we build, test and deploy as frequently as possible.

We do this by using services such as Chef, TravisCI or GitHub Actions which are
configured to listen for Git pushes and then initiate an automatic deployment.

Whilst these deployments should be automatic it should be noted that they can
take a short time to run.

### Staging

Staging deployments are automatic and should be initiated when each pull request
is merged to the working branch, usually `develop`.

### Production

Production deployments are done by manually by merging into `master`.

We do not have a standard release process though recent services follow a
process such as
[this example](https://github.com/DFE-Digital/buy-for-your-school/blob/develop/doc/release-process.md).

A basic process:

1. review what work is currently awaiting deployment by looking at Trello, if
   there are any unaccepted stories then you should hold off
1. update the [changelog](#tracking-changes)
1. let the team know you're deploying to Production
1. tag the merge commit with the version matching the
   [changelog](#tracking-changes)
1. verify the deployment has been successful with smoke tests
1. move Trello cards to indicate that they have now been deployed

---

## Branching

### How to make a branch

Create the branch off of `develop`:

```
git checkout -b <branch-name> origin/develop
```

### Naming the branch

Having a convention allows for:

- all branches connected to a story or ticket are easily connected now and in
  the future
- others can easily identify the intent of the contents without having to
  interpret a commit's title
- encourages small and concise commits which keeps the focus on the type of
  change being made. If you are adding a new feature and fix something along the
  way, that _should_ be 2 commits instead of 1

#### Prefixes

- Feature

  A branch that adds a new feature, as defined by the specified story

  Example: `feature/523797477-add-logging-to-registration`

- Fix

  A branch that corrects a problem in a feature already merged into `develop`

  Example: `fix/423797477-logging-happens-in-both-environments`

- Chore

  Chore branches are used for routine tasks for maintaining the application like
  package upgrades, they don't provide a new feature or fix an existing one

  Example: `chore/1934729239-reduce-caching-for-contact-details`

- Hotfix

  A branch that adds an urgent fix to a problem that affects production. These
  branches are based on `master` and do not go via `develop`, so must be also be
  merged into `develop` when deployed

  Example: `hotfix/253947623-remove-breaking-change-to-repair-creation`

### Update your branch with the latest changes (rebasing)

When working in a team, other pull requests will often get merged in before
yours is finished. It's important to make sure you're developing against the
latest version of the code base as much as possible.

You can do this by rebasing your work on top of the `develop` branch.

```
git checkout <branch-name>
git rebase origin/develop
git push --force-with-lease
```

During the rebase there may be conflicts which should be resolved at that point.
If in doubt talk to the other developer who you're conflicting with and reason
out what the correct state should be.

Force pushing in this situation is the only time it should be necessary, adding
`--force-with-lease` ensures you don't accidentally overwrite someone else's
additions.

---

## Committing

Taking the time to write good commits can be as valuable as writing good code.
They record the story of each code base, line by line. Being able to understand
why past decisions were made enables future us to make more informed decisions.

Helpful questions to ask when writing a commit:

- what is the **new** behaviour of the application?
- have I written down **why** I chose this approach over others I thought about?
- have I documented any risks, foreseen pitfalls or potential consequences?
- are there relevant links I could include to help the reader get the same
  information I had when making my decision?
- is the contents of the commit addressing more than one problem? Should it be
  split into a new commit?

### Examples

```
# Before
Added repairs

# After
Job postings are now displayed on the landing page

- Getting the Job postings out of the external API proved difficult but I think
  the solution we have will allow us to easily upgrade to the new release they
  have planned: <link>
```

```
# Before
Fixed the form

# After
Food order form works with special characters

- Previously the form failed to send if the starter value included any of the
  following characters: `!@£$%^&`
- Users should be able to use these characters and after a short investigation
  I found this new bug in PHP: <link>
- As a short term workaround until the bug is fixed I am stripping out any of
  these characters if they are present.
```

```
# Before
Gem upgrade

# After
Upgrade widget to v1.2.1

- This widget now includes a patch that improves resilience: <link>
- We are still unable to upgrade to v2.x as we need to do some migration work
  first
```

---

## Code review

Code reviews help us ship better quality code and provide an opportunity to
learn from each other.

, maintain design consistency, preempt future bugs, check security
considerations and to generally level up the proposal. When performing a code
review we:

- check the pull request is descriptive, explains the story and doesn't assume
  knowledge. Some templates ask for screenshots to help illustrate the change
  before and after
- check the code is safe (Brakeman should be checking this automatically)
- check the code follows a consistent style (Standard should be checking this
  automatically)
- check the code is readable (did you find it easy to build an understanding?)
- check the code for maintainability
  ([SOLID principles](https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design))
- check the code is tested (Test suites and Coverage tools should checked
  automatically)
- check the code is unlikely to introduce future bugs (this is tricky to do and
  it's okay if you don't find any as we fail fast and fix)
- check the commits are reasonably small and focused towards making one change
- check the commits messages are descriptive
- check the [changelog](#tracking-changes) has been updated

It is not important that a story be implemented exactly how you would have done
it. Only that it meets the criteria above. When we find problems, we give
feedback to the developer who implemented the story, not fix things ourselves
inline with our principle of ['Own your code'](#own-your-code).

It’s also important that code reviews have a constructive, amicable tone. To
this end, we bear in mind the
[Thoughtbot](https://github.com/thoughtbot/guides/tree/master/code-review) code
review guide, which contains good rules for keeping things positive and useful.

---

## Code style

We write our code in a consistent way to ensure it is well-structured and easy
to follow for the rest of the team. Similar to the guidelines laid out in
[Thoughtbot’s style guide](https://playbook.thoughtbot.com/#style-guide),
approach this guidance as a way to code on present and future projects, not
something to retroactively add to existing projects.

- PHP -
  [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)
- Ruby -
  [Ruby community style guide](https://github.com/bbatsov/ruby-style-guide)
- Sass -
  [Sass Guidelines - Syntax and formatting](https://sass-guidelin.es/#syntax--formatting)
- JavaScript - [Standard](https://standardjs.com/)

When joining an existing code base we suggest following any existing code style
rather than switch over to ours.

---

## Pull requests

When we have finished a piece of work on a branch, we
[make a pull request](https://help.github.com/articles/using-pull-requests/)
using the project's GitHub/GitLab page.

We prefix pull request titles with the Story ID to make it easy to find the
story and the acceptance criteria that should be met.

A good pull request should:

- describe the problem without assuming the reader has existing context
- highlight the changes being made
- describe and bring attention to particular difficulties or areas that you
  particularly want the opinion of the reviewer
- for user facing changes include screenshots of the before and after
- the changes should be focused on meeting the acceptance criteria
- focused on a single problem and preferring to split additional problems into
  other pull requests, the smaller the pull request, the easier it is to be
  reviewed and the quicker it is likely to be merged
- updates the [changelog](#tracking-changes) if it's a significant change

Real examples:

- [Cristina](https://github.com/UKGovernmentBEIS/beis-report-official-development-assistance/pull/1094)
- [Dom](https://github.com/dxw/judiciary-middleware/pull/497)
- [Ed](https://github.com/dxw/judiciary-middleware/pull/631)
- [George](https://github.com/dxw/mind-side-by-side/pull/307)
- [James C](https://github.com/UKGovernmentBEIS/beis-report-official-development-assistance/pull/1155)
- [Laura](https://github.com/UKGovernmentBEIS/beis-report-official-development-assistance/pull/665)
- [Lawrence](https://github.com/dxw/mind-side-by-side/pull/229)
- [Leeky](https://github.com/UKGovernmentBEIS/beis-report-official-development-assistance/pull/1130)
- [Lorna](https://github.com/DFE-Digital/buy-for-your-school/pull/318)
- [Meyric](https://github.com/UKGovernmentBEIS/beis-report-official-development-assistance/pull/1149)
- [Nick](https://github.com/DFE-Digital/buy-for-your-school/pull/188)
- [Robbie](https://github.com/UKGovernmentBEIS/beis-report-official-development-assistance/pull/1087)
- [Stuart](https://github.com/UKGovernmentBEIS/beis-report-official-development-assistance/pull/1121)

---

## Versioning releases

We don't use explicit versioning for application code; rather we operate a
continuous deployment approach as described in the [deploying](#deploying)
section.

However, when building libraries, APIs, or other software components designed
for reuse, versioning is important. All reusable components should have properly
versioned releases. This includes Wordpress plugins and themes, Ruby gems, and
so on, whether open source or otherwise.

Versioning should follow the [Semantic Versioning](https://semver.org) standard,
and projects that use them should specify their requirements so that the
appropriate versions are used.

Releases should be created by:

- updating the version appropriately in package metadata and committing to git
- updating the [changelog](#tracking-changes) to draw a line under changes and
  attribute them to a version
- tagging that commit using a tag of the form `vX.Y.Z`
- building the appropriate release package from that tag

---

## Tracking changes

If a project is not continuously released, and has staged releases, we want to
track what goes into those releases, so we can be confident about what we're
shipping, know when we shipped it, and share what we've done with our clients
(and maybe beyond).

Including a `CHANGELOG.md` file in the root of a repository is a standard for
tracking these changes against versions, but the only work if they are updated.
When introducing a significant change to a project, we should be recording that
change at the time we make it, while the issue is fresh in our minds.

We follow the [Keep a Changelog 1.0.0](https://keepachangelog.com/en/1.0.0/)
format for the changelogs we keep. This means integrating updating the changelog
into the release process for the project. This also means
[versioning](#versioning-releases) the project.

If the team decides that a changelog is not appropriate for a project, you
should document that decision as an
[Architectural Decision Record (ADR)](https://adr.github.io/), so future
maintainers and contributors understand the reasoning for not doing so.

See
[Tech Team RFC-019](https://github.com/dxw/tech-team-rfcs/blob/master/rfc-019-use-changelogs-to-track-changes.md)
for more context around this decision.
