- Feature Name: Merge Katello repositories and packaging with Foreman
- Start Date: 2016-10-11
- RFC PR: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

Currently the Foreman repositories are housed at [http://yum,theforeman.org](http://yum.theforeman.org) and Katello's at [https://fedorapeople.org/groups/katello/releases/yum/](https://fedorapeople.org/groups/katello/releases/yum/).

# Motivation
[motivation]: #motivation

The goal is to push Katello to be more like other plugins, this includes housing of the code in the same repositories as other plugins. Further, the Foreman repository location allows gathering statistics around use. The expected outcome is that Katello is served up like any other plugin, any dependencies required are served from the Foreman yum repositories and that packaging is managed from the same centralized foreman-packaging repository.

# Detailed design
[design]: #detailed-design

The general design goal is two fold:

 * host Katello code and secondary repositories such as Pulp, Candlepin and Client at yum.theforeman.org
 * merge katello-packaging to foreman-packaging to treat Katello like other plugins and centralize

The tasks and changes involved:

 * Add all packages in katello-packaging to foreman-packaging
 * Add katello-packaging maintainers to foreman-packaging as maintainers
 * Add comps for Pulp and Candlepin to foreman-packaging and host repositories for them on yum.theforeman.org
 * Leave old repositories at old location to avoid having to push updates to katello-repos RPM

# Drawbacks
[drawbacks]: #drawbacks

This will, like other changes, create churn in availability of repositories to users, and documentation. Another drawback is the number of packages and repositories Katello will add to the packaging repository and the yum reposiotries.

# Alternatives
[alternatives]: #alternatives

 * Keep katello-packaging but use yum.theforeman.org for repository distribution

# Unresolved questions
[unresolved]: #unresolved-questions
