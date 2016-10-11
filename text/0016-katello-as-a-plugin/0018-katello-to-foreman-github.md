- Feature Name: Move Katello to Foreman Github organization
- Start Date: 2016-10-11
- RFC PR: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

Currently Foreman and Katello are split into two separate github organizations with all of Katello's gem's, puppet modules, installer and other code repositories.

# Motivation
[motivation]: #motivation

Nearly all Foreman code and plugins live centrally located at the Foreman github organization providing a single place to look for code repositories. The expectation is to move all Katello active code repositories to the Foreman github organization and deprecate using Katello's.

# Detailed design
[design]: #detailed-design

The following are changes the Katello team feel would be required in order to merge into the Foreman github organization. These are in part due to the size of the Katello developer community as well as the size of the code set being moved over.

 * someone(s) from the Katello organization should be owners on the Foreman organization
 * move only *active* code repositories over

Given Github adds in automatic redirects for code moves, the following are changes that should be made but are not required for the initial phase.

 * spec files in katello-packaging will require updates to point source tarballs from new locations
 * puppet-katello_devel github links will need updating
 * Puppetfile in katello-installer would need updating to point to new location
 * Updates to prprocessor for repository locations
 * prprocessor repository list updated
 * Updates to CI jobs and scripts

### Rename Katello

An idea has been put forth to rename Katello's gem to match that of other plugin patterns. That is to move from `katello` to `foreman_katello` (and the CLI as well) by changing:

 * git repository
 * gem name
 * RPM name

But not changing:

 * actual modules in code, i.e. no `Katello::` to `ForemanKatello::`

# Drawbacks
[drawbacks]: #drawbacks

This could cause some churn during the move of the repositories but in theory the Github redirects should lessen that. This presents a change to the current Foreman Github organization structure that may be controversial to some as adding owners is typically not a small thing.

# Alternatives
[alternatives]: #alternatives

The alternative is to do nothing.

# Unresolved questions
[unresolved]: #unresolved-questions
