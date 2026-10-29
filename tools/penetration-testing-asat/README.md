# Penetration Testing Solution (ASAT)

[Repository home](../../README.md) · [Tool catalogue](../../docs/TOOL_CATALOGUE.md) · [Access and use](../../docs/ACCESS_AND_USE.md)

## 1. Overview

**Provider:** ZELUS

**Service family:** Vulnerability Discovery & Penetration Testing Services

The Automated Security Assessment Toolkit (ASAT) orchestrates network and web security assessments through a common REST API. It integrates Nmap, Nikto, ADEnum, Kerbrute and FFuF, executes assessments in isolated Docker containers and correlates results using Neo4j.

Technical reference: *AENAON Open Call #1 Technical Annex*, v1.0 (7 August 2026).

## 2. Potential Open Call use

Perform explicitly authorised vulnerability assessments, asset and service discovery, attack-surface mapping and reporting for the agreed target environment.

## 3. Access and deployment

The described deployment is a Docker application stack with a web dashboard and REST API. Agree the permitted environment and access arrangements with ZELUS. Container images, installation packages and deployment instructions are available only where authorised and provided by ZELUS.

## 4. Getting started

Obtain explicit authorisation for the target environment before any assessment. Agree permitted targets, scope, timing and scan configuration with the relevant environment owner and ZELUS. Prepare project information, then obtain the applicable stack prerequisites, provisioning resources, account details and API documentation from ZELUS through authorised onboarding.

## 5. Inputs and outputs

- **Inputs:** Authorised targets, project information and scan configurations.
- **Outputs:** Discovered assets and services, assessment findings, graph-correlated information and PDF reports.

## 6. Interfaces and dependencies

A common REST API and web dashboard coordinate the Docker-based assessment stack; Neo4j supports result correlation. Use only the interfaces and assessment configuration supplied by ZELUS. A graph-correlated output does not imply direct database access.

## 7. Documentation and resources

The responsible provider (ZELUS) supplies approved manuals, access instructions and technical resources where applicable to the authorised activity. Request current resources through the provider/project contact route below.

## 8. Limitations and authorised use

Assessments require explicit authorisation of the relevant target environment. Container isolation does not remove the need for target permission and operational safeguards. Do not publish live targets, credentials or confidential findings in this repository, or assume unrestricted access to deployment artefacts.

Follow the applicable project, Open Call and provider terms. Repository documentation, hosted service access, API access and downloadable software are separate arrangements. Never commit credentials or sensitive operational information. See [access and use](../../docs/ACCESS_AND_USE.md).

## 9. Support and updates

ZELUS is responsible for this tool's technical information and approved resources. Use your assigned project onboarding contact or [info@aenaon-project.eu](mailto:info@aenaon-project.eu) for routing to the provider. Provider updates are proposed through [pull requests for CILIX review and approval](../../CONTRIBUTING.md); only authorised CILIX staff merge approved changes.
