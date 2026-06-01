# Clarvia Graph

**Open consequence graph for source-backed administrative workflows**

Clarvia Graph is the technical engine behind [Clarvia](https://clarvia.org). It stores all the official rules and steps for bereavement paperwork across Europe — structured so that apps, websites, and public services can use them automatically. For the simple, family-friendly version, see [clarvia.org](https://clarvia.org).

---

Technically, Clarvia Graph is a structured, versioned, source-backed knowledge graph for cross-border administrative consequences. It models what happens after a life event (starting with bereavement), what steps may be required, which authorities are involved, what documents are needed, and where the official source says so.

This repository contains:
- **Schemas** — JSON Schema definitions for all canonical record types
- **Vocabularies** — Controlled vocabularies for jurisdictions, domains, claim types, etc.
- **Graph data** — Source-backed consequences, task templates, conditions, and deadlines
- **Sources** — Official source registry, captured snapshots, and extracted assertions
- **Validation** — CLI tooling to validate, build, and test the graph
- **Exports** — Generated JSON, JSON-LD, CPSV-AP, and web runtime bundles

## Status

🔒 **Foundation specification locked** — The [foundation spec](https://github.com/clarvia-org/clarvia-graph/wiki) defines the complete data architecture, standards alignment, editorial governance, and extensibility model.

🚧 **Implementation planned** — Schema drafting and tooling development will begin once funding is confirmed.

## Architecture

```
source → snapshot → assertion → consequence → task_template → checklist_item
```

Every checklist item traces back to an official source. No legal consequence publishes without an approved source assertion.

## Key design decisions

- **Source-backed**: Every published claim traces to a captured official source
- **Three-valued logic**: Conditions evaluate to `true`, `false`, or `unknown` — never hides uncertainty
- **Cross-border**: Jurisdiction roles (death_place, habitual_residence, work_state, asset_situs) compose layered checklists
- **Static exports**: Consumer apps load generated JSON at build time — no runtime API dependency
- **Privacy-first**: Client-side condition evaluation, no user data sent to servers

## Standards

Clarvia maintains internal Clarvia-native schemas and generates compatibility views for:
- **CPSV-AP** — Core Public Service Vocabulary Application Profile
- **CCCEV** — Core Criterion and Core Evidence Vocabulary
- **ELI** — European Legislation Identifier
- **PROV-O** — W3C Provenance Ontology

## Scope

**v0.1 technical validation scope:** Bereavement workflows, Luxembourg proof dataset, and minimal cross-border fixtures for France/Germany/EU concepts where needed to test jurisdiction composition.

**Designed for extension to:** Additional jurisdictions (Belgium, Netherlands, ...) and life events (birth, relocation, ...) without schema changes.

## License

- **Code & tooling:** [EUPL-1.2](LICENSE)
- **Graph data:** [CC-BY-4.0](LICENSE-DATA)
- **Schemas & vocabularies:** CC0 or Apache-2.0
- **Source snapshots:** Not relicensed (follow original source terms)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to get involved.

## Related repositories

- [workflow-web](https://github.com/clarvia-org/workflow-web) — Consumer web application
- [workflow-data](https://github.com/clarvia-org/workflow-data) — Legacy checklist data (migration source)
