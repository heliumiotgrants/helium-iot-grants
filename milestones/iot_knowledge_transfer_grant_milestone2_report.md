# Milestone Update

## Project Title
**Nova Labs IoT Classifiers and Knowledge Transfer**

## Milestone Number
**Milestone 2**

## Milestone Summary
The goal of Milestone 2 is to document the existing IoT deny-list classification service operated by Nova Labs.

## Progress Update
- **Completed Objectives**: Document the classification service and present it to the Helium Foundation and IoT Working Group.  
- **In-progress Objectives**: None.

The classification service, including the ArangoDB ETL (Extract-Transform-Load) program and individual classifiers, is explained. The theory of operation is presented alongside a recipe and test environment for running the complete pipeline. Worked examples are included, and possible caveats and exceptions to the classifier assumptions are documented.

## Challenges and Adjustments

### Challenges

The primary challenge addressed during the delivery of Milestone 2 was that the ETL and classification service are closed-source and were only minimally documented. Specific knowledge from my time working as a data scientist for Nova Labs, as well as experimentation, was required.

## Budget Update (if applicable)

Milestone 2 was completed within the allocated budget.

## Next Steps

Milestone 3 is scheduled to be completed by the end of April 2025. The objective of this milestone is to write an additional classifier to help defend the IoT network against attacks that are not prevented by the existing system. The following steps will be required:

1. Community engagement and discussion in the IoT Working Group to determine the best source for upstream data (Nova ETL or a community alternative).  
2. Design the new classifier to respond to emerging threats.  
3. Deploy the new classifier and verify results.

The purpose of Milestone 3 is to ensure that the Helium Foundation and IoT Working Group have access to an open and auditable classification pipeline to protect the network from emerging threats.

