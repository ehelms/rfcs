- Feature Name: Katello as a (Real) Plugin
- Start Date: 2016-09-09
- RFC PR: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

The goal of this effort is to allow Katello to be treated from a community and technical perspective as any other plugin.

# Motivation
[motivation]: #motivation

Katello was originally a stand alone Rails application that attempted to integrate with Foreman through a single-sign-on process, Rails engines that knew how to communicate with one another and switching menu structures. After a desire to centralize on a single community and bring integration tighter, Katello was moved to be a plugin but given the constraints on Katello for depoyment along with timeline the deployment of Foreman and Katello was made unique when compared to any other plugin. 

# Detailed design
[design]: #detailed-design

Given this is a large area of work with many facets, the design will list out each issue with proposed design solutions and any drawbacks. This may be able to be split out to their own RFCs over time.

## Certificates

Foreman defaults to using Puppet certificates for the web UI. Katello requires a certificate authority to hand to Candlepin for cutting client certificates and entitlement certificates. The same certificate authority used to cut client certificates is needed to have signed the server certificates so that clients can talk back to the server and access HTTPs based repositories. Further, certificates are needed to configure communication with Pulp, Qpid, Capsules (Smart Proxies) and creating entities like trustores for Candlepin.

Another aspect of certificates is the desire by users to be able to supply their own certificates for the web UI. Users should be able to change those fluidly without having to propagate new certificates across their entire client infrastructure.

#### Design

Introduce the concept of an infrastructure port that the application runs on and uses the self-signed certificates for. This port would exist in a known range (e.g. 9070 - 9079) so that all other backend services could be similarly configured within this range with well defined port values. For example, Pulp could be deployed on 9072 and Candlepin 9073. All Smart Proxies, Capsules and Clients would talk to port 9070 and leave the web UI to run on 443 as is today. This would allow users to switch out the web UI certificates simply and easily while requiring no updates to the clients. 

Action Items:
 1. Complete [PR](https://github.com/theforeman/puppet-foreman/pull/374)
 2. Add upgrade flag or steps to upgrade existing Smart Proxy to new port

#### Drawbacks

This could introduce potential performance issues with running the application on two ports or complex configuration to ensure proper redirects. This change would introduce a one-time infrastructure update for existing users to move servers and Smart Proxies to the new port and certificate setup. Clients would also need updating to ensure they are pointing and talking to the correct port with RHSM. 

## Smart Proxy vs. Capsule

A Capsule is essentially a Smart Proxy that deploys Pulp, and a reverse proxy to achieve content federation and an isolated communication point fo clients. Foreman deploys the Smart Proxy on port 8443 by default, while Katello deploys it on 9090 by default. Katello deployed Smart Proxies on 9090 because Candlepin was already running on 8443 on the server.

#### Design

Given the above discussion of infrastructure port, introduce a designated Smart Proxy port (e.g. 9071) that is predictable and not commonly used as 8443 is today. Further, merge the puppet-capsule and puppet-foreman_proxy modules to unify Smart Proxies and Capsules and give the content aspects of a Smart Proxy a new name (e.g. content node or content proxy). Content aspects are the deployment of Pulp for content federation and a reverse-proxy to create an isolation point for clients to communicate through and only through a Smart Proxy.

Action Items:
 1. Change nomenclature from Capsule to Content Smart Proxy
 2. Merge puppet-capsule into puppet-foreman_proxy
 3. Rename Capsule scenario
 4. Add installer migration to move old capsule module to smart proxy

#### Drawbacks

The port change would require infrastructure propogation for existing users. The current Capsule concept does not use a Smart Proxy in the traditional sense given it uses a reverse proxy for clients to talk back to the server in an isolated way and deploys Pulp for content federation. Given Pulp has it's own stable API, there is no need to go through the Smart Proxy. However, the Smart Proxy is used to provide statusof Pulp, storage information and indicators of enabled features.

## Puppet Modules

Currently Katello maintains a set of puppet modules that set it a part from other plugins. In order to install and configure Katello, it requires `puppet-katello` for configuring Katello as a plugin and backend services and `puppet-capsule` to configure content features on a Smart Proxy. Further, Katello maintains a set of puppet modules for each of it's backend services including the aforementioned certificate setup.

#### Design

This has been previously discussed and outlined by an existing [foreman-dev post](https://groups.google.com/forum/#!topic/foreman-dev/hebniL0yyqM). The larger proposals would be:

 * Add Katello plugin manifest to puppet-foreman
 * Add Katello plugin manifests for each backend service to puppet-foreman
 * Add puppet-capsule features to puppet-foreman_proxy

#### Drawbacks

Adds a lot more manifests to puppet-foreman and puppet-foreman_proxy and keeps things less separated and focused.

## Installer Repositories

The `katello-installer` uses the foreman-installer and scenarios in order to install providing it's own scenario for Katello and for the Capsule. These including the system checks, and hooks are all contained within the `katello-installer` repository.

### Design

Unify the installer by storing multiple scenarios and dependencies in the single `foreman-installer` repository. This would put everything in one place and encourage more design work to support a variety of scenarios for different deployment types.

Action Items:
  1. Move katello-installer checks to foreman-installer
  2. Move katello-installer hooks to foreman-installer
  3. Move katello scenario to foreman-installer
  4. Move Capsule (or merged scenario from above) to foreman-installer
  5. Move devel scenario to foreman-installer
  6. Update Puppetfile for Katello modules
  7. Move capsule-certs-generate to foreman-installer

### Drawbacks

Merging the two together could muddy the waters with respect to different sets of hooks, requirements and modules required to install a given scenario. 

## Release Repositories

Currently the Foreman repositories are housed at [http://yum,theforeman.org](http://yum.theforeman.org) and Katello's at [https://fedorapeople.org/groups/katello/releases/yum/](https://fedorapeople.org/groups/katello/releases/yum/).

#### Design

Move the Katello repositories to be served from the Foreman repository location. Further, would be to move all packages in katello-packaging to foreman-packaging and into the plugins repository.

#### Drawbacks

None.

## Github Organization

Currently Foreman and Katello are split into two separate github organizations

### Design

Move all active Katello projects into the Foreman github organization and update all related infrastructure:

 * Redmine repositories that are synced
 * prprocessor repository list
 * CI jobs

## Documentation

Today the Foreman website and plugins are kept in a different repository than Katello. Further, the websites and documentation are at different URLs [http://www.theforeman.org](http://theforeman.org) and [http://www.katello.org](http://www.katello.org).

#### Design

Action Items:
 * Move Katello's documentation inside the plugin section of the Foreman's website documentation
 * Get rid of the katello.org repository and website.
 * Add documentation to Katello around deployment strategies, diagrams

#### Drawbacks

The effort to re-do the website design, and include all of the current information displayed such as troubleshooting, user guides, and developer information.

# Drawbacks
[drawbacks]: #drawbacks

The grand drawback with this work would be that it would interrupt business as usual and no changes or work is needed that would disrupt the release flow of Katello.

# Unresolved questions
[unresolved]: #unresolved-questions

 * What to do about nested Organizations given Katello does not know how to support this given it's organization model.
