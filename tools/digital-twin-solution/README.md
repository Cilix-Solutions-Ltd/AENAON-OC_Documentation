# Digital Twin Solution

[Repository home](../../README.md) · [Tool catalogue](../../docs/TOOL_CATALOGUE.md) · [Access and use](../../docs/ACCESS_AND_USE.md)

## 1. Overview

**Provider:** ZELUS

**Service family:** Digital Twins & Virtualisation Services

A distributed simulation and digital twin environment for offshore wind farms. It uses OPC UA and TCP fieldbus communication to represent turbine operations, electrical infrastructure and environmental conditions.

Technical reference: *AENAON Open Call #1 Technical Annex*, v1.0 (7 August 2026).

## 2. Potential Open Call use

Experiment with simulated operational scenarios, monitoring and anomaly detection; examine predictive-maintenance approaches and industrial IoT integration within an agreed representative environment.

## 3. Access and deployment

The service is described as Docker-based and may be hosted on-premises or in a private cloud. ZELUS provides access details during onboarding and confirms the authorised deployment resources. Container images and deployment packages must not be assumed to be publicly downloadable.

## 4. Getting started

Agree the simulation objective and permitted infrastructure representation with ZELUS. Prepare relevant turbine parameters, ICS configurations and approved measurements. Confirm host prerequisites, resource requirements, approved deployment resources or hosted environment, dashboard access and applicable interface instructions with ZELUS before deployment.

## 5. Inputs and outputs

- **Inputs:** Turbine parameters, ICS configurations, sensor and weather measurements, and historical operational data, where available and authorised.
- **Outputs:** Simulated system states, performance metrics, anomaly results and reports.

## 6. Interfaces and dependencies

Known interfaces include web dashboards, OPC UA and REST APIs, alongside TCP fieldbus communication in the simulated environment. The Digital Twin supports the [Cyber Range Solution](../cyber-range-solution/README.md). ZELUS agrees environment configuration and actual interface access; no beneficiary endpoints or deployment specification are supplied here.

## 7. Documentation and resources

The responsible provider (ZELUS) supplies approved manuals, access instructions and technical resources where applicable to the authorised activity. Request current resources through the provider/project contact route below.

## 8. Limitations and authorised use

Use approved simulated scenarios and permitted data. The architecture description does not grant access to images, packages or unrestricted deployment rights. Authorise any connection to operational systems separately; simulation outputs should be interpreted within the agreed scenario and available input data.

Follow the applicable project, Open Call and provider terms. Repository documentation, hosted service access, API access and downloadable software are separate arrangements. Never commit credentials or sensitive operational information. See [access and use](../../docs/ACCESS_AND_USE.md).

## 9. Support and updates

ZELUS is responsible for this tool's technical information and approved resources. Use your assigned project onboarding contact or [info@aenaon-project.eu](mailto:info@aenaon-project.eu) for routing to the provider. Provider updates are proposed through [pull requests for CILIX review and approval](../../CONTRIBUTING.md); only authorised CILIX staff merge approved changes.
