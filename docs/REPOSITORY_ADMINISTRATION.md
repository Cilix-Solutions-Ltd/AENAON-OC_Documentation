# Repository administration

[Repository home](../README.md) · [Contribution process and directory responsibilities](../CONTRIBUTING.md)

This is a recommendation for an authorised CILIX administrator, not an applied configuration or evidence of current enforcement. This documentation change does not alter permissions, visibility, invitations, branch protection, rulesets, organisation settings or publishing. Do not enable GitHub Pages or another public publishing mechanism as part of this documentation rollout.

## Access model

| Audience | Recommended access and responsibility |
| --- | --- |
| Authorised CILIX maintainers | Repository administration limited to staff who need it; merging restricted to a verified authorised CILIX team. Final documentation approval and common/governance maintenance belong to CILIX. |
| Technical partners | Write access only where needed to create branches and submit PRs. Organisational responsibility is limited to their own directories listed in CONTRIBUTING. No direct updates or merging to the approved branch. Common or cross-provider changes require CILIX coordination and approval. |
| Open Call beneficiaries | Read role only, with no additional inherited or direct grant that enables pushing or merging. Repository access gives no entitlement to software, workspaces, APIs, credentials or restricted artefacts. |

Review effective access, including organisation base permissions, team memberships, direct collaborators, apps and deploy keys. A nominal Read assignment does not remove stronger inherited permissions. Do not use a public fork or change visibility as a workaround for private repository access. Any future access changes require a separate authorised administrator action.

## Recommended protection for main

Before enabling controls, verify the organisation's GitHub plan and supported protection/ruleset features. Confirm the real authorised CILIX team and its membership. Do not invent team slugs or usernames. The current default branch at implementation is `master`; no `main` branch exists. Apply equivalent protections to the active default branch through a separately authorised administration task, and retain them if CILIX later establishes `main`. Do not rename or create the approved branch as part of this contribution.

Recommended settings for `main` (and the active default branch until then):

- Require a pull request before updates and at least one approving review from CILIX. Dismiss stale approvals after new commits and require approval of the latest reviewable push by someone other than its author. Require resolution of review conversations.
- Restrict who can push/update the branch to the verified authorised CILIX team. Require PRs for that team as well. With PR enforcement, this update restriction also limits who can merge; partner Write access must not permit merging into the protected branch.
- Prevent force pushes and branch deletion. Apply protections to administrators and remove bypass allowances for partners, apps and other identities. Keep any exceptional administration narrowly controlled and audited by CILIX.
- Ensure required approval is actually CILIX approval. A generic “one approval” setting alone does not establish reviewer affiliation. Once identities are verified, use required code-owner review with verified CILIX ownership covering all paths, including governance/ownership files, or another supported equivalent that guarantees CILIX approval. Partner technical review remains part of the contribution process and does not replace CILIX approval.
- Require automated checks only after they exist, are trusted and have been validated. This contribution adds no required status checks or workflow enforcement.

Use a test PR to verify effective behaviour: a partner can propose changes but cannot directly update or merge to the protected branch; an authorised CILIX maintainer can merge only after the required review; a beneficiary cannot push or merge. Check the repository's actual settings rather than treating this guide as proof of enforcement.

## Directory scope and verified identities

GitHub's ordinary Write role does not restrict editing to particular paths. The provider-directory table is a contribution policy enforced through CILIX review unless a separately verified mechanism is deployed. CODEOWNERS requests reviews; it does not itself prevent out-of-directory edits. No CODEOWNERS file or partner-identity validation workflow is created here because GitHub accounts/team memberships have not been verified.

If CILIX later adds lightweight PR validation, first verify and maintain an explicit username/provider or team/provider mapping through authorised organisation data. Compare every changed path against that mapping, including deletions and both old and new paths of renames. Treat unknown identities and common/governance changes as requiring CILIX review; use an explicit verified CILIX exception rather than a self-declared provider field or branch name. Protect the mapping and validator from partner modification, validate against trusted base-branch policy, and require the resulting check only after testing it. Do not run untrusted PR code with privileged tokens. The PR template's provider field is review context, not authentication.

CILIX must separately decide and verify the actual team identities, effective access grants, branch protections, required review configuration and any future validation automation. No accounts are assigned by this guide.
