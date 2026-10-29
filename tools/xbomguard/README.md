# xBOMGuard

[Repository home](../../README.md) · [Tool catalogue](../../docs/TOOL_CATALOGUE.md) · [Access and use](../../docs/ACCESS_AND_USE.md)

## 1. Overview

**Provider:** CILIX

**Service family:** Vulnerability Discovery & Penetration Testing Services

A proprietary platform for xBOM-driven cyber asset intelligence. It transforms BOM evidence into component, version, supplier and dependency views, with vulnerability and exploitability enrichment, contextual prioritisation, exposure analysis, blast-radius analysis and reporting.

Technical baseline: AENAON Open Call #1 Technical Annex, version 1.0, released 7 August 2026, as supplied for this catalogue.

## 2. Potential Open Call use

Assess software components and dependency chains within representative offshore-wind technology stacks. Review potential exposure and prioritised findings to support investigation and, where agreed, downstream risk assessment.

## 3. Access and deployment

CILIX hosts and operates xBOMGuard. Selected beneficiaries receive controlled browser access to project-scoped workspaces. Documented REST API access may be available where justified and authorised; repository access does not enable it. No self-managed deployment or software/source-code distribution is included.

## 4. Getting started

Agree the approved use case, project workspace, user roles and permitted BOM/inventory data with CILIX. Obtain browser credentials through the secure onboarding channel. If API access is needed, obtain explicit authorisation and current scoped documentation before integration. Confirm supported formats for your assigned workflow rather than assuming every import/export is enabled.

## 5. Inputs and outputs

- **Inputs:** CycloneDX JSON BOMs, supported SPDX JSON and structured inventories, and relevant vulnerability or lifecycle information. The existing ZELUS guide documents a CycloneDX JSON workflow through specification version 1.6; it does not establish SPDX support for that particular upload endpoint.
- **Outputs:** Dashboards, enriched findings, component and dependency inventories, prioritisation information, and supported JSON, CSV, PDF and BOM-related exports, subject to the authorised workflow.

## 6. Interfaces and dependencies

Controlled browser access and justified, authorised documented REST API access. Exposure information may inform the [Risk Assessment Platform](../risk-assessment-platform/README.md) where providers agree data sharing and formats. No live or automatic integration is implied. Internal APIs, direct database access and raw-graph access are excluded.

## 7. Documentation and resources

The [existing ZELUS integration guide](ZELUS_INTEGRATION_GUIDE.md) is preserved for its stated project and authorised roles; its pre-existing API examples use illustrative parameter markers, not supplied credentials. It is not a general entitlement to that workspace. The guide includes the owner-supplied [sign-in location](https://xbomguard.cilix.com.cy), [OpenAPI/Swagger documentation](https://xbomguard.cilix.com.cy/api/v1/docs) and [knowledge documentation](https://learn-xbomguard.cilix.com.cy). CILIX confirms current applicability and access during onboarding; these destinations are not a promise of anonymous or uninterrupted availability.

## 8. Limitations and authorised use

No self-managed deployment, source-code distribution, deployment artefacts, direct database/raw-graph access or internal APIs are included. xBOMGuard does not replace active discovery, penetration testing, continuous monitoring or incident response. A matched vulnerability indicates potential exposure, not confirmed exploitation. Only submit data you are authorised to share; keep BOMs and findings out of this repository when confidential.

Follow the applicable project, Open Call and provider terms. Repository documentation, hosted service access, API access and downloadable software are separate arrangements. Never commit credentials or sensitive operational information. See [access and use](../../docs/ACCESS_AND_USE.md).

## 9. Support and updates

CILIX is responsible for this tool's technical information and approved resources. Use your assigned project onboarding contact or [info@aenaon-project.eu](mailto:info@aenaon-project.eu) for routing to the provider. Provider updates are proposed through [pull requests for CILIX review and approval](../../CONTRIBUTING.md); only authorised CILIX staff merge approved changes.
