
# Grant Application

## Project Title
**MeteoScientific Demo Dashboard**

## Applicant Information
- **Name**: Nik Hawks
- **Organization (if applicable)**: Gristle King Inc
- **Email**: nik@gk
- **GitHub Username**: @gristlekinginc

## Project Overview
Demonstrate how to move sensor data from the sensor device through an LNS, then onto a self-hosted dashboard using various tools such as Node-RED, Grafana, and Home Assistant.

## Problem Statement
The Helium LoRaWAN is still difficult for a "normal" person to actually use, meaning they get to not only onboard a sensor, but actually see and act on the data that sensor collects. While it's relatively easy to buy a sensor and provision it (videos & instruction for this already exist), getting to see or actually use the data is still non-entry-level work.

## Project Goals and Objectives
This project will walk the user step by step through how to use the Helium network in its current form. It will rely on the MeteoScientific LNS (though any can be substituted), then show how to use a Raspberry Pi or other SBC to set up a database, processor, and visualization for the data, along with potential uses for hooks, i.e. "sensors sense XY, triggers action Z".

## Proposed Solution
A video and blog post with a step-by-step guide for how to onboard a sensor, route it through an LNS to an end data storage and visualization piece, and potential actionable triggers for the device. This will be done for a rainwater tank (showing the fill level and triggering a Busylight to flash when it's at capacity).

### Workflow
1. **Sensor Data Transmission**:
   - Sensors send data via LoRa to a LoRa gateway.
   - The LoRa gateway forwards the data to the **LoRaWAN LNS**.

2. **Data Management by LoRaWAN LNS**:
   - The LNS (MeteoScientific) receives, decodes, and forwards the data to a service, typically using MQTT or HTTP.

3. **Data Processing with Node-RED**:
   - Node-RED subscribes to the data stream from the LoRaWAN LNS.
   - It processes, formats, or filters the data as needed.
   - Node-RED can then:
     - **Send the data to Home Assistant** for home automation purposes.
     - **Store the data in a database** (like InfluxDB) for historical analysis and visualization.

4. **Home Automation with Home Assistant**:
   - Home Assistant receives data from Node-RED and uses it for automations or displays it on simple dashboards.
   - Automations can be triggered based on sensor data, like turning lights on/off or sending alerts.

5. **Data Visualization with Grafana**:
   - Grafana queries the database (e.g., InfluxDB) where Node-RED has stored the sensor data.
   - Users can view advanced visualizations, trends, and dashboards in Grafana.
   - Alerts and insights can be configured based on sensor data trends.

This setup provides a flexible, automated, and visually rich solution for monitoring sensor data effectively.

## Project Impact
By enabling non-technical people to actually use the Helium network, this improves network-wide understanding and opens up the People's Network for use by anyone willing to pay attention, figure out their needs, and then copy/paste in a solution with the only customization being for the specific use case (their device, their need, etc.)

## Timeline and Milestones
Provide a detailed timeline, with milestones and expected completion dates.

| Milestone | Description | Expected Completion Date |
| --------- | ----------- | ------------------------ |
| Milestone 1 | Set up of full data flow | Date |
| Milestone 2 | Blog post written and published to MetSci website | Date |
| Milestone 3 | Video Production & Posting on MetSci YouTube | Date |
| Milestone 4 | All media (video, images, written content) delivered in a zip file for record keeping or republishing by Helium Foundation if MetSci blog disappears | … |

## Budget

| Item | Cost |
| ---- | ---- |
| Devices | $100 |
| Blog | $1,000 |
| Video Production | $3,000 |
| … | … |

## Additional Information
I've written and produced multiple tutorials for the Helium network prior to this, both on the original L1 as well as for the new Solana-based network. The most applicable work can be reviewed on the MeteoScientific site, found at: https://www.meteoscientific.com/
