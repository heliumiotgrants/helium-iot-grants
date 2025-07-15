# Grant Application

## Project Title
**Chirpstack-HPR - Improvements/Updates**

## Applicant Information
- **Name**: Cory Callcott
- **Organization (if applicable)**: wdriot.com
- **Email**: ccall48@hotmail.com
- **GitHub Username**: @ccall48
- **Discord Username**: Bones

## Project Overview
<!--Provide a brief summary of the project, its purpose, and its alignment with Helium IoT and grant program goals.-->
For existing time put into updating and maintaining an existing light weight python connector/sidecar to handle chirpstack LNS operators interactions with the helium packet router via the iot-config server.

[Gitbub Respository Link](https://github.com/ccall48/chirpstack-hpr)

## Problem Statement
<!--What problem does this project address? Why is it important?-->
Continue to work on extracting the complexity and time involved in setting up and connecting a Chirpstack v4 instance and get operational on the Helium IOT Netork.

## Project Goals and Objectives
<!--Outline the primary goals and objectives. What will the project achieve?-->
- Lower the barrier of entry for prospective users or start ups to Helium ecosystem.
- Provide documentation on using/getting started with the connector.
  - Github Pages or to Helium Docs.
- Dedicate a few hours every few weeks to allow time with and help people get started.
  - Propose to start with an hour every week in AEST/AEDT Time zone initially for 3 months.
  - If this time is insufficient in the future, we may need to increase and revisit grant for more time allowance.

## Proposed Solution
<!--Describe the solution your project will deliver, including technical approaches, tools, and methodologies.-->
As the solution is already operational it will aim to continue delivering a normal Chirpstack v4 operating experience to users. With the added
benefit of synchronising the operators or users device statuses with the helium packet router by using the helium iot-config for:
- Synchronising device & join eui's on adding a new device.
- Remove device & join eui's on disabling or removing a device.
- Synchronising device session keys (SKFS) on device activation.
- Update device session keys (SKFS) on re-joins or re-activation of a device.
- Remove stale device session keys from route id.
- Add a max copies to device variables to allow a per device above packet deduplication limit if set above or below the default route id limit.

The underlying code is written in python3 and runs from within a docker container from the same stack as an existing
Chirpstack v4 deployment. Or as its own container connected to existing Chirpstack v4 deployment via internal docker network.

## Project Impact
<!--Explain how this project will benefit the Helium IoT ecosystem or community.-->
With the recent passing of HIP 116 the network is now within reach of regular users for their own deployments. One of the last pieces
in the puzzle is maintaining and updating a simple synchronisation tool that requires minimal end user effort to operate. This is a solution
for people who do not need or want to run a public console or need to handle and run stripe payments for tenants.

## Timeline and Milestones
<!--
Provide a detailed timeline, with milestones and expected completion dates.

| Milestone | Description | Expected Completion Date |
| --------- | ----------- | ------------------------ |
| Milestone 1 | Description | Date |
| Milestone 2 | Description | Date |
| … | … | … |
-->
Provide a detailed timeline, with milestones and expected completion dates.

| Milestone 1 | Description | Expected Completion Date |
| --------- | ----------- | ------------------------ |
| Add Device EUI's | Add and synchronise device eui's with HPR on creating/adding a new device | Date (Complete) |
| Remove Device EUI's | Remove and synchronise device eui's with HPR on deleting/disabling a device | Date (Complete) |
| Device Session Keys (SKFS) 1 | Synchronise SKFS on device join and rejoin | Date (Complete) |
| Device Session Keys (SKFS) 2 | Synchronise SKFS on device max copies set or update | Date (Complete) |
| Device Session Keys (SKFS) 3 | Synchronise SKFS on device removal or on stale device SKFS | Date (Complete) |
| Migrate to gRPC | Run and sign all helium syncronisation over gRPC | Date (Complete) |
| … | … | … |

| Milestone 2 | Description | Expected Completion Date |
| --------- | ----------- | ------------------------ |
| Documentation | Usage documentation to Github Pages or Helium Docs site | estimate 4 weeks |
| … | … | … |

| Milestone 3 | Description | Expected Completion Date |
| --------- | ----------- | ------------------------ |
| Setup Help | Time allocation to do with helping people setup and use the connector in #openlns discord channel | ~ 3 months |
| … | … | … |

## Budget
<!--
Provide a budget breakdown. Include estimated costs for key components or resources.

| Item | Cost |
| ---- | ---- |
| Component 1 | $ |
| Component 2 | $ |
| … | … |
-->
Budget breakdown done on estimated development time which was completed over 3 months (Initially Started in June 2023).
Coding rate off estimated intermediate median python rate which worked out to be ~$41usd per hour. More time has been
commited since inception due to release version changes in chirpstack especially between v4.7 and v4.8 which had breaking 
changes which needed to be fixed.

| Item | Cost |
| ---- | ---- |
| Coding and development time for initial connector 3~4 hours per work day over 3 months | $10,000 |
| Migrating connector to use gRPC and sign transactions 3~4 hours per work day over ~2 weeks | $1,080 |
| Initial documentation using Github Pages soluion or on Helium Docs site | $1,080 |
| Initial 3 months, allocation of 1 hour per week in voice/text/chat channel to help people get started | $960 |
| … | … |

## Additional Information
<!--Any other relevant information (e.g., partnerships, prior work, resources needed).-->

---
