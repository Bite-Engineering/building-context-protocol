# Building Context Protocol — Specification v0.1 (draft)

Maintained by Bite Engineering Ltd. Licensed CC BY 4.0.

## 1. Purpose

BCP is an open protocol for how AI agents and software tools discover, request and act on building data across the asset lifecycle, under explicit permission and with full provenance. It is the interface layer between a building's context (design, programme, cost, risk, carbon, CDE record) and any tool that consumes or produces it.

BCP is **not** another data schema. IFC, COBie, ISO 19650 and EN 15804 remain the content standards; BCP standardises how that content is exposed, scoped and audited, whatever format it is stored in. The analogy is MCP: MCP standardised the interface to tools, not the tools themselves.

## 2. Design principles

1. **Interface, not schema.** BCP wraps existing content standards; it never redefines what a wall or a cost line is.
2. **Permission is first-class.** No context moves without a scoped, auditable grant. This is what construction's NDA, liability and Building Safety Act reality demands, and what MCP lacks.
3. **Provenance on everything.** Every resource carries who produced it, when, from which source revision, and at which lifecycle stage.
4. **Built on MCP.** A BCP server is an MCP server plus a mandated envelope and conformance tests. No new transport; any MCP client can call it.
5. **Open spec, competitive implementations.** The protocol is governed openly; servers and capabilities compete on quality.

## 3. Core primitives

Four primitives, mirroring MCP's tools, resources and prompts, with two additions construction cannot do without.

| Primitive | What it is | Examples | MCP analogue |
| --- | --- | --- | --- |
| Context resource | A typed, versioned, read-only slice of building state | IFC model revision, cost plan, risk register, CDE submission log, programme, LCI table | Resource |
| Capability | A function an agent can invoke against context | Detect design change, price tender risk, assemble Gateway 2 narrative, run LCIA | Tool |
| Permission | A scoped grant to read a resource or invoke a capability, with audit | Contractor A may read SE B's model rev 12 for project X until practical completion | None (new) |
| Event | A notification that context changed | New CDE revision, change detected, permission revoked | None (new) |

Resource types are namespaced (`bcp.design.ifc`, `bcp.cost.plan`, `bcp.risk.register`, `bcp.carbon.lci`) and each declares its underlying content standard and version.

## 4. The BCP envelope

Every resource and every capability response is wrapped in a mandatory envelope. A payload without one is not BCP-conformant. The normative definition is `schema/envelope.schema.json`.

| Field | Content | Why |
| --- | --- | --- |
| `provenance` | Producing party, timestamp, source system, source revision ID, content hash | Golden thread; traceability of every number to its origin |
| `stage` | Lifecycle stage: RIBA 0–7 and, for carbon, EN 15804 module (A1–D) | The same wall has different meaning at Stage 3 and Stage 5 |
| `scope` | Project ID, asset ID, discipline, permission grant ID | Enforces who may see what, per project |
| `standard` | Underlying content standard and version (IFC 4.3, COBie 3, ISO 19650-2) | Consumers know how to parse without guessing |
| `audit` | Append-only log entries for each access and each invocation | Satisfies BSA record-keeping and NDA obligations |

The envelope is JSON; the payload inside it may be any format the declared standard specifies.

## 5. Transport and conformance

Transport is MCP as-is: JSON-RPC over stdio or streamable HTTP. A BCP server registers its resources and capabilities through standard MCP discovery; the envelope travels in the resource contents and tool results. Events use MCP's notification mechanism.

Three conformance levels let adoption start small.

| Level | Requirement | Who |
| --- | --- | --- |
| L1 Expose | Serves at least one context resource with a valid envelope | A CDE, a cost tool, a carbon database |
| L2 Permit | Enforces scoped permissions and writes the audit log | A contractor's project environment |
| L3 Act | Exposes capabilities and emits events | Risk pricing, change detection, LCIA engines |

A public conformance test suite validates each level. The reference implementation is an open-source L1 server for a CDE export, plus the envelope JSON schema.

## 6. Governance and roadmap

The spec is published under an open licence (Apache 2.0 for code, CC-BY for the specification) and governed by a steering group of founding partners: Tier 1 contractors who co-sign the spec and expose data through it. Bite Engineering maintains the reference implementation and the first L3 capability, tender risk pricing. Stewardship of the specification transfers to an independent steering group or foundation once three independent implementations exist.

1. **v0.1 (now):** this document; envelope JSON schema; resource namespaces for design, cost, risk and carbon.
2. **v0.2:** L1 reference server against one CDE export; conformance tests; first external implementation.
3. **v0.3:** permission model and audit log finalised.
4. **v1.0:** capabilities and events stable; three or more independent implementations; steering group chartered.

The spec grows only from calls real tools make. Nothing enters v1.0 that no implementation has used.
