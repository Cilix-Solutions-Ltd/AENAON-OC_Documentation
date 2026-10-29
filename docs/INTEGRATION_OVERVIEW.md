# Integration overview

[Repository home](../README.md) · [Tool catalogue](TOOL_CATALOGUE.md)

Tools may be used individually or together within an approved use case. The relationships below describe potential information flows or stated dependencies from the Technical Annex baseline. They do not assert deployed integrations, automated transfers, compatible schemas or API availability for a particular beneficiary.

| Relationship | Scope and conditions |
| --- | --- |
| [Digital Twin](../tools/digital-twin-solution/README.md) → [Cyber Range](../tools/cyber-range-solution/README.md) | The Digital Twin supports realistic simulated infrastructure for cyber range exercises. ZELUS agrees environment provisioning, scenario inputs and operator roles. |
| [xBOMGuard](../tools/xbomguard/README.md) → [Risk Assessment Platform](../tools/risk-assessment-platform/README.md) | Exposure information may inform asset and dependency risk assessment. CILIX and PRIVACT must agree permitted information, format, identifiers and access. No automatic synchronisation is promised. |
| [CTIP](../tools/cyber-threat-intelligence-platform/README.md) / [SCTIP](../tools/strategic-cyber-threat-intelligence-platform/README.md) → risk assessment | Relevant contextual or strategic intelligence may support risk scenarios and mitigation priorities. ENDOSCOPE and PRIVACT agree the applicable inputs and permitted sharing. CTIP feeds are periodically refreshed. |
| Asset, exposure, risk and CTI/TTP information → [CILIX Workbench](../tools/threat-hunting-monitoring-workbench/README.md) | Manual or structured CSV/JSON inputs can support hypothesis-driven hunt preparation. Input compatibility and permissions must be confirmed; no live ingestion pipeline is implied. |
| CILIX Workbench → external Kestrel runtime | Exported Kestrel-compatible `.hf` hunt packs may be executed in a separately configured compatible runtime with suitable connectors and authorised telemetry. Runtime deployment and telemetry collection are separate from the hosted workbench. |
| Pilot information and relevant AENAON services → SCTIP | Agreed information may support pilot-specific strategic intelligence reports. Results reflect intelligence available at generation time. |

Before combining tools, agree the purpose, responsible providers, permitted data, formats, scope of any API access and how results will be interpreted. Obtain actual interface documentation from the providers. No endpoint, protocol or availability commitment beyond a tool's stated baseline is established here. See [access and use](ACCESS_AND_USE.md).
