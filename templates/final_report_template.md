# Final Report

## Project Title
Nova Labs IoT Classifiers and Knowledge Transfer

## Project Summary

This project aimed to transfer knowledge of the IoT deny-list classifiers developed at Nova Labs, along with their documentation, to the Helium Foundation and IoT Working Group (IoT WG).

The IoT deny-list is essential to protecting the Helium IoT network from attacks that can occur faster than Proof of Coverage (PoC) mechanisms can adapt. It blocks specific hotspot pubkeys from receiving PoC rewards and requires regular maintenance to remain effective against new and evolving threats.

By updating and documenting the deny-list classifiers, we preserve their value as a transparent and accountable tool to ensure network integrity.

## Accomplished Objectives

| Milestone                         | Description                                                    | Date Completed |
| --------------------------------- | -------------------------------------------------------------- | --------------- |
| Infrastructure Provisioning      | Transfer the existing classification system to new infrastructure | January 2024    |
| Documentation                    | Comprehensively document the existing classifiers               | March 2025      |
| Classifier Maintenance and Development | Update the existing classifiers to address emerging threats     | June 2025       |


## Accomplished Objectives

| Milestone                  | Description                                                    | Date Completed |
| -------------------------- | -------------------------------------------------------------- | ------------------------ |
| **Infrastructure Provisioning** | Transfer the existing classification system to new infrastructure | January 2024            |
| **Documentation**               | Comprehensively document the existing classifiers               | March 2025             |
| **Maintenance and New Classifiers** | Update the existing classifiers to address emerging threats     | June 2025            |


## Project Impact and Outcomes

1. The Helium Foundation and community now have the necessary knowledge to manage the IoT deny-list, maintaining it as a defense against attacks that cannot be quickly addressed through PoC adjustments.

2. Comprehensive documentation of the classification system originally developed by Nova Labs is now publicly available, enabling all stakeholders to make informed governance decisions.

3. Time spent on deny-list issues during IoT WG sessions has been significantly reduced, allowing the group to focus on network growth and ecosystem development.

4. The new ITM classifier helps ensure rewards remain in the hands of hotspot owners who are actively contributing to the network, incentivising legitimate gateway deployments.


## Challenges and Lessons Learned

1. Documenting the existing classification pipeline required access to institutional knowledge held by the Nova Labs engineering team. Their cooperation was critical to delivering Milestone 2 successfully.

2. Infrastructure costs for compute-heavy workloads continue to rise. A balance was struck between Amazon S3 egress fees and hosting a high-performance compute node to run classification workloads effectively.

3. The threat landscape targeting the IoT network evolves over time. Frequent communication with the IoT Working Group and active community members helped shape a classifier that delivers the greatest impact under current conditions.

## Budget Summary

This project was completed as per the proposed budget. 

| Item                             | Development Time  | Cost    |
| -------------------------------- | ------| ------- |
| **Documentation**                | 2 weeks | $7,500 |
| **Transfer to New Infrastructure** | 2 weeks | $7,500 |
| **Creation of New IoT Classifiers** | 4 weeks | $15,000 |

## Future Recommendations

The IoT Working Group plays a vital role in guiding the growth of the Helium network and identifying new industrial use cases. As deny-list issues are now largely handled through this project, discussion time devoted to them during IoT WG sessions should continue to be kept to a minimum.

## Additional Documentation

- Milestone 2 documentation:  
  https://github.com/heliumiotgrants/helium-iot-grants/tree/main/milestones/docs

- Source code for the new ITM classifier (Milestone 3):  
  https://github.com/mrpatrick1991/helium-itm-classifier
  
---
