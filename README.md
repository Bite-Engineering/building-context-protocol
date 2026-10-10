# Building Context Protocol (BCP)

An open protocol for how AI agents and software tools discover, request and act on building data across the asset lifecycle, under explicit permission and with full provenance.

BCP is not a data schema. IFC, COBie, ISO 19650 and EN 15804 remain the content standards. BCP standardises how that content is exposed, scoped and audited, whatever format it is stored in. It is built on the [Model Context Protocol](https://modelcontextprotocol.io): a BCP server is an MCP server plus a mandated envelope and conformance tests.

**Specification:** [spec/bcp-v0.1.md](spec/bcp-v0.1.md) · **Rendered:** https://buildingcontextprotocol.com
**Envelope schema:** [schema/envelope.schema.json](schema/envelope.schema.json)

## Status

v0.1 draft. The envelope and resource namespaces are defined; the permission model, events and conformance test suite are in progress. Expect breaking changes before v1.0.

## Implementing Level 1 (Expose)

An L1 server serves at least one context resource with a valid envelope. Using the MCP Python SDK:

1. Create an MCP server and register a resource with a BCP namespace URI, for example `bcp://project-123/bcp.cost.plan`.
2. Return the resource contents as a JSON object that validates against `schema/envelope.schema.json`, with your payload in the `payload` field.
3. Declare the resource's content standard in `envelope.standard` (for example `IFC 4.3`, `COBie 3`).
4. Validate with `jsonschema` or any JSON Schema 2020-12 validator.

A worked reference server will ship with v0.2.

## Resource namespaces

| Namespace | Content |
| --- | --- |
| `bcp.design.ifc` | Design model revisions |
| `bcp.cost.plan` | Cost plans and take-offs |
| `bcp.risk.register` | Risk registers |
| `bcp.carbon.lci` | Life cycle inventory tables |
| `bcp.programme` | Programme and schedule |
| `bcp.cde.log` | CDE submission records |

## Governance

Maintained by [Bite Engineering Ltd](https://www.bite.engineering). Stewardship transfers to an independent steering group once three independent implementations exist. Proposals are made by pull request against `spec/`; see CONTRIBUTING.md (coming with v0.2).

## Licence

- Specification text (`spec/`, `docs/`): [CC BY 4.0](LICENSE-SPEC)
- Schema and code (`schema/`, any reference implementation): [Apache 2.0](LICENSE)
