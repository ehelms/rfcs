- Feature Name: Remove Capsule in favor of Smart Proxy
- Start Date: 2016-11-10
- RFC PR: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

A Capsule is essentially a Smart Proxy that deploys Pulp, and a reverse proxy to achieve content federation and an isolated communication point fo clients. Foreman deploys the Smart Proxy on port 8443 by default, while Katello deploys it on 9090 by default. Katello deployed Smart Proxies on 9090 because Candlepin was already running on 8443 on the server.

# Motivation
[motivation]: #motivation

The "Capsule" terminology and code creates a perceived difference between a Foreman Proxy and the Capsule even though a Capsule is a Foreman Proxy with a few added features. Centralizing development of the Foreman Proxy will allow better innovation and less confusion for community users. The expected outcome is that we've removed the notion of a 'Capsule' and are centralized on the Foreman Proxy and adding features to it.

# Detailed design
[design]: #detailed-design

The design is broken down into two pieces, a short and long term solution. First, the short term solution is described which has the goal of getting rid of the Capsule nomenclature.

The following is a list of changes that would be made:

 * Rename puppet-capsule to puppet-foreman_proxy_content
 * Move the puppet module to the top level of the Katello installer scenarios
 * Move configuration of Pulp solely to puppet-foreman_proxy_content
 * Rename capsule scenario to foreman_proxy_content scenario
 * Eliminate capsule-remove in favor of katello-remove
  - Smart Proxy with Pulp
  - Content Smart Proxy
  - Isolated Smart Proxy
 * Add Smart Proxy features and plugins for:
  - reverse proxy
  - qpid-dispatch router
 * Drop Pulp Node nomenclature for:
  - Content
  - Content Master
 * Add functionality in puppet-capsule to puppet-foreman_proxy
 * Rename capsule-remove to foreman-proxy-remove (or generic foreman-remove)
 * Rename capsule-certs-generate to foreman-proxy-certs-generate
 * Update server APIs to drop Capsule named APIs in favor of Smart Proxy APIs
 * Add smart-proxy scenario to foreman-installer and migrate capsule scenario to it

# Drawbacks
[drawbacks]: #drawbacks

None.

# Alternatives
[alternatives]: #alternatives

An alternative would be to keep things separated but drop the Capsule name in favor of the naming concepts mentioned in the design such as using content.

# Unresolved questions
[unresolved]: #unresolved-questions
