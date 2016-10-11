- Feature Name: Converge Foreman and Katello Adminstrative Scripts
- Start Date: 2016-10-11
- RFC PR: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

Between Foreman and Katello there are a variety of scripts that currently exist or emerging for adminstering the server and foreman proxy. For example, scripts that handle backup and restore, generating debug output, or managing services. As these scripts evolve, they can benefit from true code review, and centralization to increase innovation.

# Motivation
[motivation]: #motivation

As more users setup Foreman, increase the size of the installations, long lived installations and upgrade being able to maintain the health of the servers themselves becomes increasingly important. With all of the administration scripts scattered around, user contributions and innovations can be harder to come by. As well, discovery of what scripts and features are available becomes harder to find with a large number of scripts. The expectation is to turn our adminstrative code into centralally maintained entity and interface for users.

# Detailed design
[design]: #detailed-design

The first step is to get an idea of what currently exists to provide adminstration of a Foreman server:

 * foreman-debug
 * katello-debug
 * katello-backup
 * katello-restore
 * katello-service
 * katello-remove
 * capsule-remove
 * foreman-tail
 * foreman-rake
 * foreman-config
 * hammer_cli_foreman_admin

Based on the current design of the scripts and where they live, the following design has been put forth:

 * centralize adminstration into hammer_cli_foreman_admin
  - this would allow a single repository to manage scripts
  - this would allow users to have discoverability (e.g hammer admin --help to see list of optional commands)
 * ensure scripts can only be run when server or proxy are present
 * ensure a deprecation cycle for the existing commands 

```
$ hammer admin --help
Usage:
    hammer [OPTIONS] SUBCOMMAND [ARG] ...

Parameters:
 SUBCOMMAND                    subcommand
 [ARG] ...                     subcommand arguments

Subcommands:
 debug                         Generate debug output for upload
 services                      Manage status of services
 backup                        Generate server backup
 restore                       Use previously generated backup to restore server
 remove                        Remove all RPMs and configuration from the server
 tail                          Tail all relevant logs

```
 
 * move existing scripts to hammer_cli_foreman_admin
 * add maintainers of functionality to maintainers of hammer_cli_foreman_admin


# Drawbacks
[drawbacks]: #drawbacks

# Alternatives
[alternatives]: #alternatives

 1) Use something other than hammer_cli_foreman_admin for CLI-ing scripts together like regular opt-parse
 2) Use a centralized foreman-admin or foreman-tools repository to house all scripts and create a wrapper RPM to deliver them all as stand-alone scripts

# Unresolved questions
[unresolved]: #unresolved-questions
