# Cyber Threat Intelligence Platform (CTIP)

[Repository home](../../README.md) · [Tool catalogue](../../docs/TOOL_CATALOGUE.md) · [Access and use](../../docs/ACCESS_AND_USE.md)

## 1. Overview

**Provider:** ENDOSCOPE (ENDO)

**Service family:** Threat & Risk Assessment Services

An AI-assisted Cyber Threat Intelligence platform for structured CTI ingestion, enrichment, correlation and intelligence sharing. It produces contextual threat information and indicators for downstream AENAON risk assessment workflows.

Technical reference: *AENAON Open Call #1 Technical Annex*, v1.0 (7 August 2026).

## 2. Potential Open Call use

Obtain enriched indicators and contextual intelligence for an approved pilot or risk assessment activity, subject to agreed organisation-workspace scope.

## 3. Access and deployment

ENDOSCOPE hosts and operates the service. Authorised beneficiaries receive controlled REST/JSON API access to an organisation workspace. Access credentials and endpoint details are supplied through authorised onboarding, separately from this repository.

## 4. Getting started

Agree the organisation workspace, intelligence needs and permitted inputs with ENDOSCOPE. Obtain scoped API credentials, endpoint details and applicable request/response documentation through onboarding. Confirm the relevant feed coverage and refresh arrangements before designing a consuming workflow; no API calls or schemas are invented here.

## 5. Inputs and outputs

- **Inputs:** Structured threat intelligence and indicators of compromise.
- **Outputs:** Enriched indicators, contextual metadata, structured intelligence responses and applicable pilot-specific information.

## 6. Interfaces and dependencies

Controlled REST/JSON API access. Relevant intelligence may inform the [Risk Assessment Platform](../risk-assessment-platform/README.md) where agreed; no automatic data transfer is asserted. CTI feeds are curated and refreshed periodically rather than continuously.

## 7. Documentation and resources

The responsible provider (ENDOSCOPE (ENDO)) supplies approved manuals, access instructions and technical resources where applicable to the authorised activity. Request current resources through the provider/project contact route below.

## 8. Limitations and authorised use

Self-managed deployment, source code, deployment artefacts, internal APIs and direct database access are excluded. Periodic feed refresh does not provide continuous intelligence updates. Observe agreed sharing restrictions and do not publish credentials or restricted indicators in this repository.

Follow the applicable project, Open Call and provider terms. Repository documentation, hosted service access, API access and downloadable software are separate arrangements. Never commit credentials or sensitive operational information. See [access and use](../../docs/ACCESS_AND_USE.md).

## 9. Support and updates

ENDOSCOPE (ENDO) is responsible for this tool's technical information and approved resources. Use your assigned project onboarding contact or [info@aenaon-project.eu](mailto:info@aenaon-project.eu) for routing to the provider. Provider updates are proposed through [pull requests for CILIX review and approval](../../CONTRIBUTING.md); only authorised CILIX staff merge approved changes.
