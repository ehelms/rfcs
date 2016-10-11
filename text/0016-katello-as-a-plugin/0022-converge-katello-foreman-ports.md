- Feature Name: Converge Katello and Foreman Ports
- Start Date: 2016-10-11
- RFC PR: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

The Foreman and Katello installations have a varying use of ports for common services (e.g. Capsules use 9090 and Foreman Proxy uses 8443) as well Katello adds services that require use of common ports. Based on user feedback the goals of port changes should be:

 * Least number of ports
 * Use of well known or well-defined ports for security contexts

# Motivation
[motivation]: #motivation

The difference in port usage causes differences in installations making it harder to add Katello to a Foreman, have common documentation and SELinux rules. The expectation is to converge on a common port usage paradigm that can be used for current services and as a guideline when adding new services in the future.

# Detailed design
[design]: #detailed-design

### Capsule vs Smart Proxy Ports

The Foreman Proxy uses 8443 by default on installation while the Capsule install for Katello uses 9090. This was originally done due to Candlepin's use of 8443; however, given Candlepin is a local installation the port it uses matters less.

 * Move to 8443: allows use of existing Smart Proxies and existing SELinux contexts
 * Move to 9090: existing Capsules don't need to change, SELinux contexts must expose this port
 * Move to 9070 (infrastructure port): SELinux contexts must be updated, centralized port range for all services to run on

### Reverse Proxy

The Katello reverse proxy deployed on Capsules to achieve isolation feature currently runs on 8443. This means that subscription-manager is configured to point at and go through 8443 and is yet another port that must be managed.

 * Move reverse proxy to 443
 * Maintain reverse proxy on 8443 and 443 for a release and then remove 8443

### Custom Port Ranges

One idea is to move all services to a set of well known ports and choose a port range that allows adding new services to a well defined location. For example:

 * 9070: Smart Proxy
 * 9071: Pulp
 * 9072: Candlepin
 * 9073: Qpid
 * 9074: Reverse Proxy
 * 9075-9079: New Services

### Infrastructure Port

Introduce a port that is well known, and defined for allowing infrasturcture to access the application. This port would use custom certificates that are broken apart from the certificates of the web UI given users typically want to customize them. This port would require updating of SELinux, Smart Proxies and Clients but looking ahead would provide a controlled entry port that is well known across all Foreman infrsturcture which allows better security planning.

# Drawbacks
[drawbacks]: #drawbacks

These options can introduce changes to current infrastructures as well as upgrade challenges.

# Alternatives
[alternatives]: #alternatives

# Unresolved questions
[unresolved]: #unresolved-questions
