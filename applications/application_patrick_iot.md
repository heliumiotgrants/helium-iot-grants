# Grant Application

## Project Title
**Nova Labs IoT Classifiers and Knowledge Transfer**

## Applicant Information
- **Name**: Matthew Patrick (Starwatcher)
- **Organization (if applicable)**: None
- **Email**: matthew.ryan.patrick@gmail.com
- **GitHub Username**: @mrpatrick1991

## Project Overview

Transfer the IOT deny-list classifiers developed at Nova Labs, as well as their documentation, to the Helium Foundation and IOT Working Group (IOT WG).

## Problem Statement

The IoT deny-list is essential to protect the Helium IoT network from attacks that can occur faster than Proof of Coverage (PoC) can adapt. It blocks specific hotspot pubkeys from receiving PoC rewards, requiring maintenance to remain effective against new threats. Currently, the deny-list classifiers lack community documentation, maintenance, and performance tracking, reducing their effectiveness as a transparent and accountable tool.

## Project Goals and Objectives

The primary goals are:

1) Transfer knowledge of the IoT deny-list and classifiers from Nova Labs to the Helium Foundation and IoT WG.
2) Maintain and expand the existing classifiers to counter emerging threats, particularly new attenuator clusters in China.

This project aims to establish a path toward the eventual retirement of the IoT deny-list by incorporating the classifiers into a more robust PoC model. By the end of the project, the IoT WG will have full autonomy in managing the deny-list and implementing updates.

## Proposed Solution

The existing classifiers will be documented, and new infrastructure will be provisioned to run them. Maintenance and documentation will ensure that deny-list runs can be managed by the IoT WG or Helium Foundation with minimal time commitment. A request will be made to Nova Labs to coordinate this transition and build on their existing work where feasible.

## Project Impact

1) The Helium Foundation and the community will manage the IoT deny-list, maintaining it as a defense against network attacks that cannot be quickly mitigated through PoC changes.
2) Comprehensive documentation of the classification system from Nova Labs will become publicly available, enabling stakeholders to make informed governance decisions.
3) Discussion time taken by deny-list issues in IoT WG sessions will be reduced, allowing more focus on network growth opportunities.

## Timeline and Milestones


| Milestone                  | Description                                                    | Expected Completion Date |
| -------------------------- | -------------------------------------------------------------- | ------------------------ |
| **Infrastructure Provisioning** | Transfer the existing classification system to new infrastructure | January 2024            |
| **Documentation**               | Comprehensively document the existing classifiers               | March 2025             |
| **Maintenance and New Classifiers** | Update the existing classifiers to address emerging threats     | April 2025            |


## Budget

Costs are in USD. The hourly development rate is $81.25.

| Item                             | Development Time  | Cost    |
| -------------------------------- | ------| ------- |
| **Documentation**                | 2 weeks | $7,500 |
| **Transfer to New Infrastructure** | 2 weeks | $7,500 |
| **Creation of New IoT Classifiers** | 4 weeks | $15,000 |

### Infrastructure Costs

Running the classifiers and ArangoDB instance requires 64 GiB of RAM and at least 16 vCPUs, which is estimated to cost $500 per month to host. We are exploring the possibility of leveraging existing infrastructure from Nova Labs or the Helium Foundation to offset costs.

Alternatively, purchasing hardware with sufficient compute power may be more economical, as the project does not require the scalability or Service Level Agreement (SLA) provided by AWS. The one-time hardware cost would be $2,500 USD. Internet connection, power, and maintenance are estimated will cost $150 per month.

### Maintenance

After completing the initial project work, continued software maintenance will be required to ensure key services remain operational. This maintenance will cost $500 per month on an ongoing basis.

Maintenance work can be paused or transferred to another person by the IoT Working Group or Helium Foundation. Detailed documentation on the infrastructure will be kept public to facilitate a smooth transition when needed.


## Additional Information

As the original designer of the IoT deny-list and most classifiers used by Nova Labs, I bring firsthand knowledge and technical insight to this transition. 

---

## Addition - Wednesday, December 4, 2024

$1000 USD of the $2500 USD infrastructure budget is requested initially to provision cloud infrastructure. This will cover 2 months of hosting to deliver Milestone 1 - Infrastructure provisioning, to be delivered by the end of January 2024. 
