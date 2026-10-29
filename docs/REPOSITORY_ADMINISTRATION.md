# Repository administration

[Repository home](../README.md) · [Contribution process and directory responsibilities](../CONTRIBUTING.md)

This guide describes the intended repository access and approval policy. GitHub permissions and branch protection must be configured and verified by an authorised administrator; documentation alone does not enforce these controls. Keep the repository private and do not enable GitHub Pages or other public publishing without separate authorisation.

## Access model

| Audience | Recommended access and responsibility |
| --- | --- |
| Authorised CILIX maintainers | Repository administration limited to staff who need it; merging restricted to a verified authorised CILIX team. Final documentation approval and common/governance maintenance belong to CILIX. |
| Technical partners | Write access only where needed to create branches and submit PRs. Organisational responsibility is limited to their own directories listed in CONTRIBUTING. No direct updates or merging to the approved branch. Common or cross-provider changes require CILIX coordination and approval. |
| Open Call beneficiaries | Read role only, with no additional inherited or direct grant that enables pushing or merging. Repository access gives no entitlement to software, workspaces, APIs, credentials or restricted artefacts. |

Review effective access, including organisation base permissions, team memberships, direct collaborators, apps and deploy keys. A nominal Read assignment does not remove stronger inherited permissions. Do not use a public fork or change visibility as a workaround for private repository access. Any future access changes require a separate authorised administrator action.

## Protect the default branch (`master`)

Check the organisation's GitHub plan and verify the CILIX team or individual accounts that will be permitted to merge. The repository's default branch is `master`. In **Settings → Branches**, create a protection rule for `master` (or an equivalent repository ruleset in **Settings → Rules → Rulesets**). If the default branch is subsequently renamed, update the rule and the documented references. Private-repository protection and push restrictions depend on the GitHub plan.

Apply the following settings to `master`:

- Require a pull request before updates and at least one approving review from CILIX. Dismiss stale approvals after new commits and require approval of the latest reviewable push by someone other than its author. Require resolution of review conversations.
- Restrict who can push/update the branch to the verified authorised CILIX team. Require PRs for that team as well. With PR enforcement, this update restriction also limits who can merge; partner Write access must not permit merging into the protected branch.
- Prevent force pushes and branch deletion. Apply protections to administrators and remove bypass allowances for partners, apps and other identities. Keep any exceptional administration narrowly controlled and audited by CILIX.
- Ensure required approval is actually CILIX approval. A generic “one approval” setting alone does not establish reviewer affiliation. Once identities are verified, use required code-owner review with verified CILIX ownership covering all paths, including governance/ownership files, or another supported equivalent that guarantees CILIX approval. Partner technical review remains part of the contribution process and does not replace CILIX approval.
- Require automated checks only after they exist, are trusted and have been validated. This contribution adds no required status checks or workflow enforcement.

Use a test PR to verify effective behaviour: a partner can propose changes but cannot directly update or merge to the protected branch; an authorised CILIX maintainer can merge only after the required review; a beneficiary cannot push or merge. Check the repository's actual settings rather than treating this guide as proof of enforcement.

## Directory scope and verified identities

GitHub's ordinary Write role does not restrict editing to particular paths. The provider-directory table is a contribution policy enforced through CILIX review unless a separately verified mechanism is deployed. CODEOWNERS requests reviews; it does not itself prevent out-of-directory edits. No CODEOWNERS file or partner-identity validation workflow is created here because GitHub accounts/team memberships have not been verified.

If CILIX later adds lightweight PR validation, first verify and maintain an explicit username/provider or team/provider mapping through authorised organisation data. Compare every changed path against that mapping, including deletions and both old and new paths of renames. Treat unknown identities and common/governance changes as requiring CILIX review; use an explicit verified CILIX exception rather than a self-declared provider field or branch name. Protect the mapping and validator from partner modification, validate against trusted base-branch policy, and require the resulting check only after testing it. Do not run untrusted PR code with privileged tokens. The PR template's provider field is review context, not authentication.

Before providing partners with Write access, verify that the protection rule is **active** on `master` and that only authorised CILIX staff can merge. Configure GitHub identities, role assignments, required reviews and any validation automation separately. The repository's contribution policy must be tested against actual permissions, not inferred from these instructions.
