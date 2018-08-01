---
title: Authorization
parent: Concepts
weight: 52
---

Authorization is handled separately for _Direct Device Integration (DDI) API_ and _Device Management Federation (DMF) API_ (where successful authentication includes full authorization) and _Management API_ and _UI_ which is based on Spring security [authorities](https://github.com/eclipse/hawkbit/blob/master/hawkbit-security-core/src/main/java/org/eclipse/hawkbit/im/authentication/SpPermission.java).
<!--more-->

However, keep in mind that hawkBit does not offer an off the shelf authentication provider to leverage these permissions and the underlying multi user/tenant capabilities of hawkBit. Check out [Spring security documentation](http://projects.spring.io/spring-security/) for further information. In hawkBit [SecurityAutoConfiguration](https://github.com/eclipse/hawkbit/blob/master/hawkbit-autoconfigure/src/main/java/org/eclipse/hawkbit/autoconfigure/security/SecurityAutoConfiguration.java) is a good starting point for integration.

The default implementation is single user/tenant with basic auth and the logged in user is provided with all permissions.

## DDI API
An authenticated target is permitted to:
- retrieve commands from the server
- provide feedback to the the server
- download artifacts that are assigned to it

A target might be permitted to download artifacts without authentication (if enabled, see above). Only the download can be permitted to disable the authentication. This can be used in scenarios where the artifacts itself are e.g. signed and secured.  

## Management API and UI

### Delivered Permissions
- READ_/UPDATE_/CREATE_/DELETE_TARGETS for:
  - Target entities including metadata (that includes also the installed and assigned distribution sets)
  - Target tags
  - Target actions
  - Target registration rules
  - Bulk operations
  - Target filters

- READ_/UPDATE_/CREATE_/DELETE_REPOSITORY for:
  - Distribution sets
  - Software Modules
  - Artifacts
  - DS tags

- READ_TARGET_SECURITY_TOKEN
  - Permission to read the target security token. The security token is security concerned and should be protected.

- DOWNLOAD_REPOSITORY_ARTIFACT
  - Permission to download artifacts of an software module (Note: READ_REPOSITORY allows only to read the metadata).

- TENANT_CONFIGURATION
  - Permission to administrate the tenant settings.

- ROLLOUT_MANAGEMENT
  - Permission to provision targets through rollouts.

### Permission Matrix for example uses cases that need more than one permission

Use Case                                                                   | Needed permissions
-------------------------------------------------------------------------- | --------------------------------------------------
Search _targets_ by installed or assigned _distribution set_               | READ_TARGET, READ_REPOSITORY
Assign _DS_ to a _target_                                                  | READ_REPOSITORY, UPDATE_TARGET
Assign DS to target through a _Rollout_, i.e. _Rollout_ creation and start | READ_REPOSITORY, UPDATE_TARGET, ROLLOUT_MANAGEMENT
Read _Rollout_ status including its _deployment groups_                    | ROLLOUT_MANAGEMENT
Checks _targets_ inside _Rollout deployment group_                         | READ_TARGET, ROLLOUT_MANAGEMENT

## Device Management Federation API
The provided _RabbitMQ_ [vhost and user](https://www.rabbitmq.com/access-control.html) should be provided with the necessary permissions to send messages to hawkBit through the exchange and receive messages from it through the specified queue.