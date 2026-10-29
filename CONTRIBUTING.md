# Contributing documentation

[Repository home](README.md) · [Tool profile template](templates/TOOL_PROFILE_TEMPLATE.md) · [Administrator guide](docs/REPOSITORY_ADMINISTRATION.md)

**Partner edits own documentation → Partner submits PR → CILIX reviews → CILIX merges → OC beneficiaries read the updated documentation.**

## Responsibilities and scope

Technical partners may create branches and submit pull requests for tools belonging to their organisation. Each provider is responsible for technical accuracy, current manuals, approved artefacts, resource links and access instructions for its own tools.

| Responsible provider | Directories maintained |
| --- | --- |
| ZELUS | `tools/digital-twin-solution/`, `tools/penetration-testing-asat/`, `tools/cyber-range-solution/` |
| CILIX | `tools/xbomguard/`, `tools/threat-hunting-monitoring-workbench/` |
| PRIVACT | `tools/risk-assessment-platform/`, `tools/threat-detection-incident-response-platform/` |
| ENDOSCOPE (ENDO) | `tools/cyber-threat-intelligence-platform/`, `tools/strategic-cyber-threat-intelligence-platform/` |

CILIX is the repository maintainer and final approval authority. It maintains the common README, navigation, contribution instructions, templates and repository-wide documentation, including governance files. The legacy `xbomguard/README.md` navigation link is coordinated by CILIX. CILIX may request corrections or reject submissions that do not meet documentation requirements. Only authorised CILIX staff may merge pull requests into `main`.

Partners must not modify another provider's material, common documentation, contribution rules or governance files without CILIX coordination and approval. For a change spanning providers, agree responsibilities with CILIX before editing; identify that coordination in the PR. Partners cannot merge into `main`. Open Call beneficiaries have read-only access to approved contents and cannot push, merge or modify documentation. This access grants no proprietary software, service, credential or restricted-artefact access.

These are contribution rules, not automatic per-directory GitHub permissions. Repository Write access normally permits changes across directories. Enforcement depends on separately configured repository settings and review; this change does not configure them. See the [administrator guide](docs/REPOSITORY_ADMINISTRATION.md).

## Branch and pull-request process

1. **Confirm scope.** Identify the provider and affected tool. Obtain the provider's technical confirmation and coordinate any common or cross-provider change with CILIX.
2. **Start from the current base branch.** In GitHub, select the repository's default branch, then use the branch selector to create a descriptively named documentation branch, for example `docs/zelus-digital-twin-guide`. Do not edit the protected branch directly. At the time of this initial catalogue, the default branch is `master`, and `main` does not exist. Use `master` as the PR base until CILIX separately establishes `main`; the same review and CILIX-only merge policy is intended for the active default branch. This documentation does not rename branches.
3. **Edit the relevant tool directory.** Follow the [profile template](templates/TOOL_PROFILE_TEMPLATE.md). Keep the profile navigable and use British English. Additional Markdown guides, approved examples and links can live within the tool directory. For authorised PDFs, note that the current `.gitignore` ignores `*.pdf`; coordinate inclusion with CILIX rather than changing repository-wide ignore rules without agreement.
4. **Validate the update.** Check relative links, heading structure, factual accuracy, access claims and confidentiality. Confirm external links with the owner and intended audience. Do not publish credentials, sensitive infrastructure details, proprietary code or binaries, or material without distribution permission. Do not invent manuals, endpoints, installation commands, versions or download availability.
5. **Commit to your branch and open a pull request.** On GitHub, use **Compare & pull request**, set the current default branch as the base and your branch as the compare branch. Complete the [PR template](.github/PULL_REQUEST_TEMPLATE.md), naming the provider, tool, update, technical confirmation and any CILIX coordination. Review the entire changed-file list for scope.
6. **Request CILIX review.** Use GitHub's reviewer selector only for an actual CILIX reviewer confirmed through project coordination. If no verified reviewer is available in the selector, ask your CILIX project contact through the agreed channel to route the PR; do not invent or tag a team/account. Identify CILIX review as required in the PR description.
7. **Address review and wait for CILIX.** Update the same branch when corrections are requested. CILIX gives final approval and only authorised CILIX staff merge. A partner's technical confirmation does not replace CILIX approval. Do not merge your own submission.

## Reviews and identity configuration

CODEOWNERS and required reviews may be configured separately after real GitHub identities and teams have been agreed and verified. No unverified CODEOWNERS assignments or identity-based automation are included. CILIX coordination is required for changes to ownership or validation rules themselves. The [administrator guide](docs/REPOSITORY_ADMINISTRATION.md) explains recommended permissions and branch protection; those recommendations are not a claim that controls are enabled.
