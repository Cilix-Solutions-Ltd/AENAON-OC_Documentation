# Threat Hunting & Monitoring — CILIX Workbench

[Repository home](../../README.md) · [Tool catalogue](../../docs/TOOL_CATALOGUE.md) · [Access and use](../../docs/ACCESS_AND_USE.md)

## 1. Overview

**Provider:** CILIX

**Service family:** Risk/Threat Monitoring & Cyberattack Management Services

A proprietary hosted prototype for preparing and documenting hypothesis-driven security hunts. It transforms asset, exposure, vulnerability, risk and CTI/TTP information into structured investigation objectives, Kestrel-compatible huntbooks and reusable hunt packs.

Technical baseline: AENAON Open Call #1 Technical Annex, version 1.0, released 7 August 2026, as supplied for this catalogue.

## 2. Potential Open Call use

Prepare investigation objectives and steps, document findings and export reusable hunt packs for an approved representative offshore-wind security activity. Execution against telemetry is a separate arrangement.

## 3. Access and deployment

CILIX operates the workbench as a hosted service. Selected beneficiaries receive managed browser access. Source code and self-managed deployment of the workbench are not included. Access to this catalogue does not create a workbench account or provide a Kestrel execution environment.

## 4. Getting started

Agree the hunt preparation use case, browser access and permitted contextual inputs with CILIX. Prepare manual or structured CSV/JSON information. For exported hunt-pack execution, separately arrange a compatible Kestrel runtime, suitable connectors and available telemetry, and obtain explicit authorisation before using live operational telemetry.

## 5. Inputs and outputs

- **Inputs:** Primarily manual or structured CSV/JSON asset, exposure, vulnerability, risk and CTI/TTP information.
- **Outputs:** Investigation steps, Kestrel-compatible `.hf` hunt packs, findings and reports.

## 6. Interfaces and dependencies

Managed browser access is described. Exported hunt packs may be executed using a separately configured compatible Kestrel runtime where appropriate connectors and telemetry are available. No workbench API or built-in telemetry ingestion is specified. See the [integration overview](../../docs/INTEGRATION_OVERVIEW.md) for conditional information flows.

## 7. Documentation and resources

CILIX supplies applicable manuals, access details and any authorised technical resources through onboarding. No detailed manual, download URL or provider endpoint has been supplied for this profile. Request the relevant instructions through the provider/project route below; do not substitute unverified installation commands or links.

## 8. Limitations and authorised use

The workbench does not independently collect telemetry and is not a production SIEM/SOAR, EDR, SOC/MDR, 24/7 monitoring or automated incident-response service. Live operational telemetry requires explicit authorisation. Source code and self-managed deployment are excluded. Generating a compatible hunt pack does not establish that it has run, that connectors are available or that an incident has been detected.

Follow the applicable project, Open Call and provider terms. Repository documentation, hosted service access, API access and downloadable software are separate arrangements. Never commit credentials or sensitive operational information. See [access and use](../../docs/ACCESS_AND_USE.md).

## 9. Support and updates

CILIX is responsible for this tool's technical information and approved resources. Use your assigned project onboarding contact or [info@aenaon-project.eu](mailto:info@aenaon-project.eu) for routing to the provider. Provider updates are proposed through [pull requests for CILIX review and approval](../../CONTRIBUTING.md); only authorised CILIX staff merge approved changes.
