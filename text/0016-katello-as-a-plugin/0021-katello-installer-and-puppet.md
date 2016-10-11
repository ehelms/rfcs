- Feature Name: Merge Katello Installer and Puppet Modules to Foreman
- Start Date: 2016-10-11
- RFC PR: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

The `katello-installer` uses the foreman-installer and scenarios in order to install providing it's own scenario for Katello and for the Capsule. These including the system checks, and hooks are all contained within the `katello-installer` repository. Katello maintains a set of puppet modules that set it a part from other plugins. In order to install and configure Katello, it requires `puppet-katello` for configuring Katello as a plugin and backend services and `puppet-capsule` to configure content features on a Smart Proxy. Further, Katello maintains a set of puppet modules for each of it's backend services including the aforementioned certificate setup.

# Motivation
[motivation]: #motivation

The divide between the Foreman and Katello installers creates a gap in innovation where changes to one repository must be replicated in the other or innovations live in one and not the other. Two good examples of this are the upgrade support in the katello-installer and the support of a Capsule (Foreman Proxy) scenario for easy installation and configuration. By bringing the installers and puppet modules together, we expect to centarlize innovation and community usage to reduce overhead, increase community involvement and increase innovation that makes it way to a broader community.

# Detailed design
[design]: #detailed-design

The merging of the installer and puppet modules has been combined into a single RFC given they are so closely related. The design details are going to be split into two areas: installer and puppet modules.

For puppet modules:

 * merge puppet-katello into puppet-foreman as a plugin manifest
  - add test coverage during merge
 * move puppet-capsule features to puppet-foreman_proxy
  - remove customized puppet from puppet-capsule module
  - move reverse proxy
  - move qpid setup
  - move pulp setup
 * Move Katello puppet modules to the Foreman organization

Installer:

 * Move katello scenario into foreman-installer config
 * Rename capsule scenario to foreman-proxy and move into foreman-installer
 * Create sub-direcotires in hooks and checks for scenarios to put customized hooks and checks in
  - this would prevent having to do a lot of "if" checks for modules being enabled
 * Add Katello modules to foreman-installer Puppetfile
 * Add upgrade functionality as first class to foreman-installer
 * Add puppet upgrade functionality to foreman-installer
 

# Drawbacks
[drawbacks]: #drawbacks

The addition of more puppet module code to the Foreman will increase testing overhead on an already overloaded Travis setup. This may require some consideration of other testing infrastructures to not incur slow downs.

This is large change bringing together the installers with multiple scenarios being hosted. This may cause some breakage or churn settling them. Further this may illuminate even more the need for composable scenarios.

# Alternatives
[alternatives]: #alternatives

1) Keep the installers separated but merge together puppet-katello and puppet-capsule.

# Unresolved questions
[unresolved]: #unresolved-questions

What parts of the design are still TBD?
