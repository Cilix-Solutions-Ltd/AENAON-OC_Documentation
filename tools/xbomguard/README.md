# xBOMGuard — AENAON Open Call user and integration guide

[Repository home](../../README.md) · [Tool catalogue](../../docs/TOOL_CATALOGUE.md) · [Getting started](../../docs/GETTING_STARTED.md) · [Access and use](../../docs/ACCESS_AND_USE.md)

**Provider:** CILIX · **Service family:** Vulnerability Discovery & Penetration Testing Services

## 1. Purpose and scope

xBOMGuard is a CILIX-hosted platform for **xBOM-driven cyber asset intelligence**. It processes structured Bill of Materials (BOM) evidence into inventories of software components, versions, suppliers and dependencies; enriches component information with vulnerability and exploitability intelligence; and supports contextual exposure prioritisation, dependency/blast-radius analysis and reporting.

For an AENAON Open Call (OC) activity, a beneficiary may examine the software composition of a product, release, application, container-based workload or representative offshore-wind technology stack. The results can support decisions about further testing, remediation and, where agreed, risk-assessment workflows. Beneficiaries may use xBOMGuard on its own; use of the ZELUS Digital Twin or any other AENAON tool is **not a prerequisite**.

xBOMGuard is **BOM/evidence-driven**. It does not discover arbitrary physical or operational technology (OT) infrastructure, generate a complete BOM from an unknown environment, perform penetration testing, conduct live monitoring, or provide incident response. A vulnerability match represents **potential exposure**, not proof of exploitability or confirmed compromise. The usefulness of the results depends on the completeness and currency of the evidence submitted.

## 2. Choose the applicable usage mode

### Mode A — Hosted browser-based analysis (standard)

This is the default route for OC beneficiaries who want to examine their own approved software/component evidence.

1. Agree the approved activity, permitted inputs, users and expected outputs with CILIX.
2. CILIX arranges access to an **authorised, project-scoped xBOMGuard workspace** and communicates the applicable sign-in and project details through a secure channel.
3. Prepare and upload a supported BOM using the web interface.
4. Wait for the import and enrichment process to complete.
5. Review the project/release, component and dependency inventory, vulnerabilities, findings and prioritised results.
6. Generate or export the available summaries and reports for the approved activity.

An OC beneficiary must use **its own assigned workspace/project** and must not assume access to a pre-existing consortium project. Workspace provisioning, permissions, supported export options and any demonstration dataset are confirmed during onboarding. Do not upload sensitive data unless the relevant parties have authorised its processing.

### Mode B — Approved REST API integration (optional)

Where an approved OC use case requires automation, CILIX may issue **scoped API credentials** and the current interface documentation. Possible authorised roles include:

- **BOM producer:** validate and submit supported BOM evidence, then retrieve the resulting release status and findings.
- **Read-only consumer:** retrieve permitted releases, components, exposure findings and relevant prioritisation or dependency information, without modifying project evidence.
- **Integration with another AENAON tool:** exchange explicitly agreed results or reports with a separately authorised service. Data formats, access rights and integration responsibilities must be agreed between the relevant providers.

**API access, available endpoints, webhooks and permissions are role- and deployment-dependent.** A browser account or this repository's Read permission does not automatically provide API credentials, webhook configuration, access to another project, or a machine-to-machine integration.

## 3. Request access and prepare your input

Contact your assigned AENAON onboarding contact or [info@aenaon-project.eu](mailto:info@aenaon-project.eu) to request routing to CILIX. Explain the approved use case, preferred mode (browser or API), number of authorised users, whether sensitive operational or supplier information is involved, and whether a representative/synthetic BOM can be used.

**Service:** [xBOMGuard sign-in](https://xbomguard.cilix.com.cy) (access subject to provisioning).

**Preferred input:** CycloneDX JSON. The previously documented xBOMGuard import workflow supports **CycloneDX JSON through specification version 1.6**; CILIX must confirm the currently deployed version and any applicable format limits before use. The OC Technical Annex also identifies SPDX JSON, structured component inventories, SARIF findings, purl/CPE identifiers, and optional lifecycle, criticality or VEX/OpenVEX evidence as possible inputs **where supported by the authorised workflow**. This does not mean the CycloneDX upload endpoint accepts all those formats.

A useful CycloneDX BOM should include, where available:

- Component names and versions.
- Package URLs (purls), CPE identifiers and cryptographic hashes.
- Supplier information.
- Services and dependency relationships.
- Relevant build/release and lifecycle context.

You may use representative or synthetic BOMs when production information cannot be shared. **Do not commit BOMs, credentials, security findings, operational data or actual project identifiers to this GitHub repository if they are confidential.**

## 4. Browser workflow: upload, analyse and report

After CILIX has provisioned access:

1. Sign in and locate your **assigned project** under *Projects*.
2. Select the relevant project and upload a supported CycloneDX JSON BOM for an approved product, build or release.
3. Identify the intended release/version and any applicable environment information according to the platform prompts.
4. Wait for asynchronous processing and enrichment to finish; do not interpret partial results as final.
5. Examine the release's component inventory and dependency relationships.
6. Review vulnerabilities, findings, prioritisation scores/bands and explanatory context where available.
7. Use the available summary, report or export functions for the agreed activity.

Results may include component and dependency inventories, enriched vulnerability findings, prioritisation bands and reasons, project/release views, blast-radius/posture summaries, and supported JSON, PDF, CSV, CycloneDX or SARIF outputs **where enabled for the assigned workspace and workflow**.

## 5. Optional API workflow and examples

**Use this section only after CILIX has authorised Mode B and confirmed the API version, permissions and endpoints for your assigned environment.** The paths and request fields below are retained from the existing technical guide; they have **not been independently tested against the live deployment for this OC documentation update**. The current [OpenAPI/Swagger documentation](https://xbomguard.cilix.com.cy/api/v1/docs) is the operational reference for authorised API users.

The existing guide identifies the API base URL as `https://xbomguard.cilix.com.cy/api/v1`. In these examples, substitute **your assigned project key**, token and actual release details; never copy an identifier from another beneficiary's or consortium project's integration.

### 5.1 Validate and import a CycloneDX BOM

The documented upload operation uses `multipart/form-data`:

```bash
# Validate supported CycloneDX JSON (authorised API user only)
curl -X POST "https://xbomguard.cilix.com.cy/api/v1/boms/validate" \
  -H "Authorization: Bearer <api_token>" \
  -F "bom=@./bom.cdx.json;type=application/json"

# Import into YOUR assigned project (BOM-producer role only)
curl -X POST "https://xbomguard.cilix.com.cy/api/v1/boms/import" \
  -H "Authorization: Bearer <api_token>" \
  -F "projectKey=<assigned_project_key>" \
  -F "releaseVersion=<release_version>" \
  -F "environment=staging" \
  -F "bom=@./bom.cdx.json;type=application/json"
```

Documented import fields:

| Field | Requirement | Purpose |
| --- | --- | --- |
| `projectKey` | Required | Project key **provided by CILIX**. |
| `releaseVersion` | Required | Build, release, image or scenario version; documented maximum 100 characters. |
| `bom` | Required | Supported CycloneDX JSON file. |
| `releaseDate` | Optional | ISO 8601 release timestamp when available. |
| `environment` | Optional | Documented values: `dev`, `staging`, `prod`, `other`. |
| `customEnvironment` | Optional | Custom environment label where applicable. |
| `isLatest` | Optional | Mark the release as latest when appropriate. |

The existing technical guide reports HTTP `200` for validation and `202` for import, because subsequent processing is asynchronous. Import creates or resolves a release by project key and release version. Preserve the returned `release.releaseId` exactly: exposed IDs are opaque/encrypted public tokens, **not internal database identifiers**.

### 5.2 Monitor processing and retrieve findings

The following paths are documented in the earlier integration guide. Availability and filters depend on the assigned role and current API contract.

| Purpose | Documented endpoint |
| --- | --- |
| List project releases | `GET /api/v1/projects/{projectKey}/releases` |
| Release detail and processing status | `GET /api/v1/releases/{releaseId}` |
| BOM metadata | `GET /api/v1/releases/{releaseId}/bom` |
| Download permitted BOM | `GET /api/v1/releases/{releaseId}/bom/download` |
| Components | `GET /api/v1/releases/{releaseId}/components` |
| Vulnerabilities | `GET /api/v1/releases/{releaseId}/vulnerabilities` |
| Findings | `GET /api/v1/releases/{releaseId}/findings` |
| Single finding | `GET /api/v1/findings/{findingId}` |
| Release prioritised findings | `GET /api/v1/releases/{releaseId}/prioritised-findings` |
| Project prioritised findings | `GET /api/v1/projects/{projectKey}/prioritised-findings` |
| Release prioritisation summary | `GET /api/v1/releases/{releaseId}/prioritisation` |

```bash
# Discover releases for YOUR assigned project
curl -X GET \
  "https://xbomguard.cilix.com.cy/api/v1/projects/<assigned_project_key>/releases?page=1" \
  -H "Authorization: Bearer <api_token>"

# Check processing status using the returned opaque releaseId
curl -X GET \
  "https://xbomguard.cilix.com.cy/api/v1/releases/<release_id>" \
  -H "Authorization: Bearer <api_token>"

# Read vulnerability results after processing completes
curl -X GET \
  "https://xbomguard.cilix.com.cy/api/v1/releases/<release_id>/vulnerabilities" \
  -H "Authorization: Bearer <api_token>"
```

For a consuming integration, the usual flow is: discover permitted releases; retain the returned release ID unchanged; check completion status; retrieve the relevant prioritised findings; optionally query individual components, vulnerabilities or supporting evidence.

### 5.3 Prioritisation and release filters

Documented prioritisation results may include affected component references, CVE/advisory identifiers, priority score and band, reason codes, KEV context, VEX/applicability state, lifecycle context, and accepted-risk or suppression status, where evidence exists.

The previous API guide lists possible release-scoped filters including `status`, `priorityBand`, `minPriorityScore`, `vulnerabilityId`, `componentVersionId`, `supplierId`, `kev`, `vexStatus`, `includeAcceptedRisk`, `includeSuppressed`, `sort`, and `page`. Project-scoped requests may additionally support `releaseLifecycle` and `isLatest`. Confirm accepted values and filter availability in the current API documentation.

```bash
curl -X GET \
  "https://xbomguard.cilix.com.cy/api/v1/releases/<release_id>/prioritised-findings?priorityBand=critical&page=1" \
  -H "Authorization: Bearer <api_token>"
```

### 5.4 Optional dependency and blast-radius context

The previous guide documents these optional public graph routes:

| Purpose | Documented endpoint |
| --- | --- |
| Release graph summary | `GET /api/v1/graph/releases/{releaseId}/summary` |
| Component dependencies | `GET /api/v1/graph/components/{componentVersionId}/dependencies` |
| Component dependents | `GET /api/v1/graph/components/{componentVersionId}/dependents` |
| Vulnerability blast radius | `GET /api/v1/graph/vulnerabilities/{vulnerabilityId}/blast-radius` |

Such outputs describe **dependency context**. They are not proof that an attack has propagated or that a specific vulnerability is exploitable. Graph routes do not grant direct access to the database, internal graph service or raw graph storage. Use only opaque identifiers returned by authorised API responses.

### 5.5 Optional webhook notifications

The existing guide describes webhooks for events such as **new vulnerability**, **new finding**, **BOM processed**, and **BOM failed**. Webhooks are not a default OC entitlement; availability, subscribed events, destination registration and request-authentication mechanisms must be confirmed by CILIX.

If authorised, use HTTPS endpoints, validate notification authenticity using supported security mechanisms, acknowledge deliveries promptly, and handle duplicate deliveries safely. Never put tokens or secrets in webhook URLs. An integration can instead poll the permitted release-status endpoint if webhooks are not enabled.

### 5.6 Common API errors

| HTTP status | Interpretation and next step |
| --- | --- |
| `400` | Invalid or unsupported request/file, ID or filter: check the current API contract and supplied values. |
| `401` | Missing, expired or invalid bearer token: verify the secure token provisioning and authorisation. |
| `404` | Object unavailable, not found or outside the token's visibility: verify the assigned project and opaque identifier, without attempting to access another workspace. |

This table reflects the existing guide and is not an exhaustive error contract. Follow the current API specification and onboarding support instructions for other responses.

## 6. Sharing results with other AENAON tools

xBOMGuard results may support authorised vulnerability-assessment and risk-assessment activities. For example, relevant component exposure and dependency information may be provided to the ZELUS assessment tooling or PRIVACT Risk Assessment Platform **if the providers agree the data, formats, credentials and respective responsibilities**. There is no implication of a live integration or automatic synchronisation for every OC beneficiary. The existing consortium Digital Twin integration used a specific project-scoped producer/consumer workflow; its identifiers and permissions are **not reusable** for other OC activities.

## 7. Security, licence and technical boundaries

- **Proprietary Background:** xBOMGuard is CILIX proprietary Background. Access is limited to agreed AENAON OC activities. Additional integrations, dedicated deployments or continued use beyond the agreed scope require prior CILIX agreement.
- **Hosted provision:** CILIX operates the platform. Source code, self-managed deployment, deployment artefacts, internal APIs, direct database or raw-graph access are excluded.
- **Data authorisation:** Upload only evidence you are authorised to provide. Use representative or synthetic inputs when operational details cannot be disclosed. Follow applicable confidentiality, security and data-protection conditions.
- **Project isolation:** Use only the workspace, roles and project identifiers provided for the approved activity. Do not exchange tokens or publish another party's identifiers, BOMs or findings in this repository.
- **Interpretation:** BOM coverage can be incomplete or stale. A vulnerability match signals potential exposure; it does not establish exploitation, exploitability in context or an operational incident.
- **Testing/monitoring:** No active infrastructure scanning, penetration testing, ongoing monitoring, SOC operations or incident response is included merely by receiving xBOMGuard access.

## 8. Further documentation and support

- [xBOMGuard sign-in](https://xbomguard.cilix.com.cy)
- [Interactive OpenAPI/Swagger documentation](https://xbomguard.cilix.com.cy/api/v1/docs) — for authorised API users; verify against the deployed service.
- [xBOMGuard knowledge documentation](https://learn-xbomguard.cilix.com.cy) — follow the materials applicable to the assigned workflow.
- [AENAON getting-started guide](../../docs/GETTING_STARTED.md)
- [Access and use conditions](../../docs/ACCESS_AND_USE.md)

For OC onboarding, use the assigned project contact or [info@aenaon-project.eu](mailto:info@aenaon-project.eu) for routing to CILIX. Contact details, credentials and API permissions for a specific activity are communicated through the authorised onboarding channel.

**Maintainer:** CILIX. Updates to this page follow the [partner contribution and PR process](../../CONTRIBUTING.md); the approved repository branch is maintained by CILIX.
