# Grant Application

## Project Title
**Chirpstack-HPR - Improvements/Updates**

## Applicant Information
- **Name**: Cory Callcott
- **Organization (if applicable)**: wdriot.com
- **Email**: cory@callref.com.au
- **GitHub Username**: @ccall48

## Project Overview
<!--Provide a brief summary of the project, its purpose, and its alignment with Helium IoT and grant program goals.-->
To update and maintain a light weight python connector to handle chirpstack LNS operators interactions with the helium packet router
via the iot-config server.

## Problem Statement
<!--What problem does this project address? Why is it important?-->
Continue to work on extracting the complexity and time involved of setting up and connecting a chirpstack instance and get operational on the helium IOT netork.

## Project Goals and Objectives
<!--Outline the primary goals and objectives. What will the project achieve?-->
- Lower the barrier of entry for prospective users or start ups.
- Provide documentation on using/getting started with connector.
- Dedicate a few hours every few weeks to allow time with and help people get started.
  - Propose to start with an hour or two every few weeks.
  - If this time is insufficient in the future, we may need to increase and revisit grant for more time allowance.

## Proposed Solution
<!--Describe the solution your project will deliver, including technical approaches, tools, and methodologies.-->
The solution will aim to deliver a normal chirpstack operating experience to users. With the added
benefit of synchronising the operators or users device statuses with the helium packet router by using the helium iot-config for:
- Synchronising device & join eui's on adding a new device.
- Remove device & join eui's on disabling or removing a device.
- Synchronising device session keys (SKFS) on device activation.
- Update device session keys (SKFS) on re-joins or re-activation of a device.
- Remove stale device session keys from route id.
- Add a max copies to device variables to allow a per device above packet deduplication limit if set above or below the default route id limit.

The underlying code is to be written in python and run from within a docker container from the same stack as an existing
chirpstack deplyment. Or as its own container connected to existing chipstack deployment via internal docker network.

## Project Impact
<!--Explain how this project will benefit the Helium IoT ecosystem or community.-->
With the recent passing of HIP 116 the network is now within reach of regular users for their own deployments. One of the last pieces
in the puzzle is maintaining and updating a synchronisation tool that requires minimal end user effort to operate.


## Timeline and Milestones
Provide a detailed timeline, with milestones and expected completion dates.

| Milestone | Description | Expected Completion Date |
| --------- | ----------- | ------------------------ |
| Milestone 1 | Description | Date |
| Milestone 2 | Description | Date |
| … | … | … |

## Budget
Provide a budget breakdown. Include estimated costs for key components or resources.

| Item | Cost |
| ---- | ---- |
| Component 1 | $ |
| Component 2 | $ |
| … | … |

## Additional Information
<!--Any other relevant information (e.g., partnerships, prior work, resources needed).-->

---
