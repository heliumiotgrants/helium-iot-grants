# Milestone Update

## Project Title
Nova Labs IoT Classifiers and Knowledge Transfer

## Milestone Number
Milestone 3

## Milestone Summary
The goal of Milestone 3 is to create a new classifier to help secure the IoT network against spoofing attacks.

## Progress Update
- **Completed Objectives**: Extended the existing classifiers to address emerging threats  
- **In-progress Objectives**: None

A new classification service, based on the ITM model for radio propagation, was created. The service identifies beaconer–witness pairs exhibiting behavior consistent with spoofing. PDF report cards explaining model results for each hotspot are also generated for distribution to the community.

## Challenges and Adjustments

### Challenges

The primary challenge addressed during Milestone 3 was the computational cost of the ITM model. To support large-scale evaluation, a parallelized, memory-safe batched implementation was developed. Since the model operations are not vectorized by default, the pipeline was built to avoid resource exhaustion on commodity hardware.

## Budget Update (if applicable)

Milestone 3 was completed within the allocated budget.

## Next Steps

Milestone 3 concludes the IoT denylist knowledge transfer grant. A proposal for ongoing maintenance and support will be presented to the IoT working group by the end of June 2025.
