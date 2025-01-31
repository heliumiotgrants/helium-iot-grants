# Milestone Update

## Project Title
**Nova Labs IoT Classifiers and Knowledge Transfer**

## Milestone Number
**Milestone 1**

## Milestone Summary
The goals of milestone 1 are to allocate the required infrastructure and independently run the Nova Labs IoT deny-list classification system. 

## Progress Update
- **Completed Objectives**: Transfer the existing classification system for the IoT deny-list to new infrastructure.
- **In-progress Objectives**: None.

The full classification pipeline is now running successfully on cloud infrastructure. This includes the ArangoDB ETL (extract, transform, load) and the classification software itself. A subset of the classification results for data from January 1 to January 2, 2025, is included, demonstrating the pipeline's functionality and output.

## Challenges and Adjustments

### Challenges

Three challenges were addressed during the execution of milestone 1:

1) Access to the Nova Labs ETL needed to be obtained by a written request sent to their engineering team. We have obtained read-only access to the ETL and classification repository for the purpose of executing the objectives of this grant. Nova Labs does not currently permit the redistribution of code from this repository. 

2) The classifier depends on an ArangoDB ETL, which ingests oracle data from an S3 bucket. The Nova Labs S3 bucket used by the ETL is synchronized to the upstream source: https://docs.helium.com/oracles/oracle-data/. References in the oracle code (https://github.com/helium/oracles) were changed to reflect the different bucket prefix, since this is not a configuration option. The changes are available on the following GitHub branch: https://github.com/mrpatrick1991/oracles/tree/requestor-pays-matching-upstream. These changes will be submitted for review by the Oracles team as a configuration option.

3) The upstream oracle data bucket is "requestor-pays", meaning that data egress costs from Amazon S3 to the internet are paid by the user downloading the data. A software patch was written to make the ingest stage of the classification pipeline compatible with this type of bucket.

## Budget Update (if applicable)

Milestone 1 was completed within the allocated budget. 

## Next Steps

Milestone 2 is scheduled to be completed by the end of March, 2025. The objective of this milestone is to comprehensively document the existing classification pipeline. The documentation will include:

1) Steps to reproduce the work done in Milestone 1, including the required changes to the configuration and oracle code.

2) The operation, properties, and adjustable parameters of the existing classifiers.

3) Steps to create "report cards" detailing the classification results for each hotspot. 

The purpose of Milestone 2 is to ensure that the Helium Foundation and IoT Working Group have the information required to run the full deny-list classification pipeline independently. 

---
