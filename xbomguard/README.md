# xBOMGuard — Getting Started

xBOMGuard helps teams upload and analyse CycloneDX Bills of Materials (BOMs), identify vulnerabilities and findings, prioritise risk, and integrate the results with other systems through its public API and webhooks.

This guide explains the typical flow for a new user, from requesting an account to uploading a BOM and consuming its results.

## 1. Request an account

xBOMGuard accounts are created on request. Email either of the following contacts:

- [v.papasavva@cilix.com.cy](mailto:v.papasavva@cilix.com.cy)
- [y.polykarpou@cilix.com.cy](mailto:y.polykarpou@cilix.com.cy)

In your request, include the email address or addresses that should be registered.

After the accounts have been created, each relevant email address will receive the xBOMGuard link and the credentials needed to sign in. Credentials should be stored securely and must not be shared through public or unencrypted channels.

Sign in at [xbomguard.cilix.com.cy](https://xbomguard.cilix.com.cy).

## 2. Create a project

After signing in:

1. Open **Projects**.
2. Select the option to create a new project.
3. Enter the project's details and save it.
4. Keep the project's key available; it identifies the project in public API requests.

A project groups the releases, BOMs, components, vulnerabilities, and findings for one product or system.

## 3. Upload a BOM

Open the project and upload a BOM for the relevant release. xBOMGuard currently supports **CycloneDX JSON through specification version 1.6**.

For the most useful analysis, include as much of the following CycloneDX data as is available:

- Component names and versions
- Package URLs (PURLs) and CPEs
- Cryptographic hashes
- Suppliers
- Services
- Dependency relationships

When a BOM is uploaded, xBOMGuard creates or updates the corresponding release and processes its component inventory. Once processing finishes, the project and release views expose the detected components, vulnerabilities, findings, and prioritised findings.

For automated uploads, first validate the BOM and then import it through the public API:

```bash
# Validate the file
curl -X POST "https://xbomguard.cilix.com.cy/api/v1/boms/validate" \
  -H "Authorization: Bearer <api_token>" \
  -F "bom=@./bom.cdx.json;type=application/json"

# Import the file and create or update a release
curl -X POST "https://xbomguard.cilix.com.cy/api/v1/boms/import" \
  -H "Authorization: Bearer <api_token>" \
  -F "projectKey=<project_key>" \
  -F "releaseVersion=<release_version>" \
  -F "releaseDate=2026-06-29T12:00:00Z" \
  -F "environment=staging" \
  -F "isLatest=true" \
  -F "bom=@./bom.cdx.json;type=application/json"
```

The import request uses `multipart/form-data` and accepts these fields:

| Field | Required | Description |
| --- | --- | --- |
| `projectKey` | Yes | Key of the existing xBOMGuard project. |
| `releaseVersion` | Yes | Release, build, image, scenario, or delivery version; maximum 100 characters. An existing release is resolved using this value. |
| `bom` | Yes | CycloneDX JSON file. |
| `releaseDate` | No | Release timestamp in ISO 8601 format. |
| `environment` | No | `dev`, `staging`, `prod`, or `other`. |
| `customEnvironment` | No | Custom label, up to 100 characters, normally used with `environment=other`. |
| `isLatest` | No | Set to `true` only if this should become the project's latest release. |

Validation returns HTTP `200` with metadata such as the detected BOM type, format, specification version, and component, service, and dependency counts. Import returns HTTP `202` with `release`, `releaseStatus`, and `bom` data because processing continues asynchronously.

The import endpoint creates or resolves a release using `projectKey` and `releaseVersion`; it does not accept a raw release ID. Keep the returned `release.releaseId` exactly as provided for follow-up requests. xBOMGuard IDs exposed by the API are encrypted public tokens and should not be parsed or replaced with internal database IDs.

## 4. Review the results

After the BOM has been processed, use the dashboard or public API to review the release. Common API operations include:

| Purpose | Endpoint |
| --- | --- |
| List a project's releases | `GET /api/v1/projects/{projectKey}/releases` |
| Get release details and processing status | `GET /api/v1/releases/{releaseId}` |
| Get BOM metadata | `GET /api/v1/releases/{releaseId}/bom` |
| Download the current BOM | `GET /api/v1/releases/{releaseId}/bom/download` |
| List normalized components | `GET /api/v1/releases/{releaseId}/components` |
| List vulnerabilities | `GET /api/v1/releases/{releaseId}/vulnerabilities` |
| List findings | `GET /api/v1/releases/{releaseId}/findings` |
| Get a finding | `GET /api/v1/findings/{findingId}` |
| List prioritised findings for a release | `GET /api/v1/releases/{releaseId}/prioritised-findings` |
| List prioritised findings for a project | `GET /api/v1/projects/{projectKey}/prioritised-findings` |
| Get the release prioritisation summary | `GET /api/v1/releases/{releaseId}/prioritisation` |

Example:

```bash
curl -X GET \
  "https://xbomguard.cilix.com.cy/api/v1/releases/<release_id>/vulnerabilities" \
  -H "Authorization: Bearer <api_token>"
```

### Recommended API flow

For an integration that consumes xBOMGuard results:

1. Call `GET /api/v1/projects/{projectKey}/releases?page=1` to discover releases.
2. Store each returned encrypted `releaseId` exactly as received.
3. Call `GET /api/v1/releases/{releaseId}` to confirm processing status and review summary counts.
4. Retrieve release-scoped prioritised findings for a single build or project-scoped prioritised findings for a cross-release worklist.
5. Fetch components, vulnerabilities, findings, BOM data, or graph context when additional detail is required.

Release-scoped prioritised findings support filters such as `status`, `priorityBand`, `minPriorityScore`, `vulnerabilityId`, `componentVersionId`, `supplierId`, `kev`, `vexStatus`, `includeAcceptedRisk`, `includeSuppressed`, `sort`, and `page`. Project-scoped requests also support `releaseLifecycle` and `isLatest`.

```bash
# Critical prioritised findings for one release
curl -X GET \
  "https://xbomguard.cilix.com.cy/api/v1/releases/<release_id>/prioritised-findings?priorityBand=critical&page=1" \
  -H "Authorization: Bearer <api_token>"

# Prioritised findings from the latest project release
curl -X GET \
  "https://xbomguard.cilix.com.cy/api/v1/projects/<project_key>/prioritised-findings?isLatest=true&page=1" \
  -H "Authorization: Bearer <api_token>"
```

Prioritised findings can provide affected component references, vulnerability identifiers such as CVEs or advisories, priority score and band, reason codes, Known Exploited Vulnerability (KEV) context, VEX/applicability state, lifecycle data, and accepted-risk or suppression state where available.

### Dependency and blast-radius context

Optional graph endpoints add dependency context to findings and can help risk assessment or pentesting teams understand the potential impact of a vulnerable component:

| Purpose | Endpoint |
| --- | --- |
| Release graph summary | `GET /api/v1/graph/releases/{releaseId}/summary` |
| Component dependencies | `GET /api/v1/graph/components/{componentVersionId}/dependencies` |
| Component dependents | `GET /api/v1/graph/components/{componentVersionId}/dependents` |
| Vulnerability blast radius | `GET /api/v1/graph/vulnerabilities/{vulnerabilityId}/blast-radius` |

Use only encrypted identifiers returned by public API responses, and pass them back unchanged. For BOM downloads, omit the optional `bomId` unless xBOMGuard has explicitly supplied an encrypted BOM token.

### Producer and consumer integrations

The supplied integration guides distinguish between two common account workflows:

- **BOM producers** validate and import CycloneDX files, then monitor the created release and consume its results.
- **Read-only consumers**, such as risk-assessment or pentesting integrations, discover existing releases and read prioritised findings, components, vulnerabilities, evidence, and graph context. They should not upload BOMs unless their assigned integration role explicitly permits it.

## Public API access

The public API uses bearer-token authentication.

1. Sign in to xBOMGuard.
2. Open [Settings → Security](https://xbomguard.cilix.com.cy/settings/security).
3. Create a new API token and select the required duration.
4. Copy the token immediately and store it in a secure secret manager.

> **Important:** The API token is shown only when it is created. It cannot be retrieved later. If it is lost, create a replacement token.

Send the token with every API request:

```http
Authorization: Bearer <api_token>
```

Do not place passwords or API tokens in URLs, query strings, source control, file names, or logs. Rotate or revoke tokens that are no longer required.

API resources:

- Base URL: `https://xbomguard.cilix.com.cy/api/v1`
- Interactive OpenAPI/Swagger documentation: [xbomguard.cilix.com.cy/api/v1/docs](https://xbomguard.cilix.com.cy/api/v1/docs)

The Swagger documentation is the authoritative reference for request fields, optional filters, response schemas, and the complete set of currently available endpoints.

### Common API errors

| Status | Meaning | Recommended action |
| --- | --- | --- |
| `400` | Invalid request, unsupported BOM, invalid encrypted ID, or unsupported filter. | Correct the request fields, file, identifier, or query parameter and retry. |
| `401` | Missing, expired, or invalid bearer token. | Check the `Authorization` header or generate a replacement token. |
| `404` | The requested project, release, BOM, finding, component, or graph object does not exist or is not visible to the token. | Confirm the project key, encrypted identifier, release status, and account permissions. |

## Webhooks

Webhooks provide instant notifications without repeatedly polling the API. Create a webhook in xBOMGuard, enter the HTTPS endpoint that should receive notifications, and subscribe it to the events your integration needs.

Available event types include:

- New vulnerability
- New finding
- BOM processed
- BOM failed

The receiving endpoint should acknowledge deliveries promptly, validate requests using the security controls exposed in the webhook configuration, handle duplicate deliveries safely, and perform longer-running work asynchronously. Avoid including credentials or API tokens in the webhook URL.

## End-to-end flow

```text
Request account
      ↓
Receive login details and sign in
      ↓
Create a project
      ↓
Upload a CycloneDX JSON BOM (up to version 1.6)
      ↓
xBOMGuard processes the BOM and creates/updates the release
      ↓
Review components, vulnerabilities, findings, and prioritised findings
      ↓
Use the public API and/or webhooks for automation
```

## Support

For account creation or onboarding assistance, contact:

- [v.papasavva@cilix.com.cy](mailto:v.papasavva@cilix.com.cy)
- [y.polykarpou@cilix.com.cy](mailto:y.polykarpou@cilix.com.cy)
