- Feature Name: Converge Katello and Foreman Certificate Usage
- Start Date: 2016-10-12
- RFC PR: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

Foreman defaults to using Puppet certificates for the web UI. Katello requires a certificate authority to hand to Candlepin for cutting client certificates and entitlement certificates. The same certificate authority used to cut client certificates is needed to have signed the server certificates so that clients can talk back to the server and access HTTPs based repositories. Further, certificates are needed to configure communication with Pulp, Qpid, Capsules (Smart Proxies) and creating entities like trustores for Candlepin.

Another aspect of certificates is the desire by users to be able to supply their own certificates for the web UI. Users should be able to change those fluidly without having to propagate new certificates across their entire client infrastructure. 

The goal of this feature is to converge certificate management between Foreman and Katello and provide a scenario to upgrade from base Foreman to Katello's more complex certificate requirements.

# Motivation
[motivation]: #motivation

The complex nature of Katello's certificate requirements means there needs to be a path from a base Foreman install to add Katello and keep the current install and infrastructure able to communicate. There is also the need to support custom web certificates for users. The expectation is that a user will be able to install Foreman and add Katello to it and have certificates continue to work.

# Detailed design
[design]: #detailed-design

The certificates design has many facets to it for consideration. The first is to look at what could be contributed to base Foreman.

### Base Foreman Install

Provide puppet-certs that can be used to install Foreman using custom certificate setup without being tied to Puppet certificates within the infrastructure. This would include the creatoin of a self-signed CA that provides web certificates (or signs users custom web certificates) and the generation of client and server certificates for all Foreman Proxies.

### Katello

When Katello is added to an existing Foreman install, there are two potential design options:

 1) Katello generates a CA that is signed by the Foreman root CA and hands that to Candlepin and Pulp
 2) Katello hands the Foreman root CA to Candlepin and Pulp

### Reduction in Overall Certificates

The current Katello design for certificates generates client and server certificates for each service that requires them and places them in a singular location. The certificate overhead could be reduced by generating a single server and client certificate per host that needs them. For example, the main server would have a single server and client certificate set, and each Foreman Proxy would also have a single set of certificates shared among services that need them.

### Removal of katello-certs-tools

The current tooling behind certificates relies on a combination of Puppet manifests and types and a command line utility katello-certs-tools. The katello-certs-tools tool is a Python based utility that generates certificates and wrapper RPMs in a single location. These certificates are then installed via RPM to a new location and then copied by Puppet to a third location which serves as the final location that services are pointed at. While this tool has served well, the code is older, and harder to maintain as an SSL utility. Recommendations:

 1) Replace katello-certs-tools with pure Puppet types and code for maintaing certificates
 2) Build a new Ruby based utility for generating and managing certificates as well as encapsulating functionality such as the katello-certs-check and custom certificates support
 3) Use FreeIPA PKI management capabilities

# Drawbacks
[drawbacks]: #drawbacks

N/A

# Alternatives
[alternatives]: #alternatives

This is sort of covered within the design section itself.

# Unresolved questions
[unresolved]: #unresolved-questions

How to handle upgrades from existing Foreman and Katello installations?
