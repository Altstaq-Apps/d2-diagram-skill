# D2 Diagram Skill — Bluestaq Navy Edition

A Claude Code skill that generates production-grade [D2](https://d2lang.com/) diagrams with hand-drawn sketch aesthetics, vendor icons, animated data flows, and the Bluestaq Navy dark palette. Built for system architecture, cloud infrastructure, security topologies, data flows, and more.

## What It Does

When a user asks Claude Code to *diagram*, *visualize*, *sketch*, or *map out* any technical system, this skill activates and produces a single, self-contained `.d2` file that renders into a polished SVG. Every diagram follows the Bluestaq design system:

- **Sketch mode** — hand-drawn aesthetic via D2's sketch renderer
- **Bluestaq Navy palette** — dark theme with navy, blue, and orange accents
- **17 typed style classes** — consistent styling without inline repetition
- **7 semantic connectors** — sync, async, data-flow, monitoring, replication, critical-path, boundary-crossing
- **L1–L4 abstraction hierarchy** — every diagram declares its audience and scope
- **Vendor icons** — 40+ verified icons from `icons.terrastruct.com`

---

## Quick Start

### Prerequisites

- [D2](https://d2lang.com/tour/install) v0.7+ installed
- [Claude Code](https://claude.ai/claude-code) with skills support

### Install the Skill

Copy the `skill/` directory into your Claude Code skills path:

```bash
cp -r skill/ ~/.claude/skills/d2-diagram/
```

### Render a Diagram

```bash
d2 --sketch --theme 200 --layout elk input.d2 output.svg
```

| Flag | Purpose |
|------|---------|
| `--sketch` | Hand-drawn aesthetic |
| `--theme 200` | Terminal dark theme |
| `--layout elk` | ELK layout engine (best for nested containers) |

---

## Bluestaq Navy Palette

Every diagram uses this single palette. Legacy palettes (Ocean Depths, Forest Canopy, etc.) are retired.

| Token | Hex | Use |
|-------|-----|-----|
| `${primary}` | `#162646` | Outermost containers, account boundaries |
| `${secondary}` | `#385FAF` | Internal service borders, sync connectors |
| `${accent}` | `#9CADD7` | Data flow connectors, metadata labels |
| `${surface}` | `#1C2F4A` | Node fills (dark background) |
| `${text}` | `#D6D6DA` | All node labels |
| `${text-light}` | `#FFFFFF` | Labels on dark fills (primary, secondary) |
| `${muted}` | `#739BCF` | Sublabels, group borders |
| `${orange}` | `#F5871F` | External borders, critical paths, security boundaries |
| `${success}` | `#4CAF80` | Success states |
| `${danger}` | `#E24B4A` | Error states, rejection paths |

> **Rule**: Orange is stroke/border only. Never use `style.fill: ${orange}`.

---

## Style Classes (17)

### Connection Classes

| Class | Visual | Use |
|-------|--------|-----|
| `sync-connection` | Blue solid | REST, gRPC, request/response |
| `async-connection` | Blue dashed, animated | Events, pub/sub, SQS |
| `critical-path` | Orange thick | Errors, alerts — use sparingly |
| `data-flow` | Blue animated | Active data movement |
| `monitoring-link` | Grey dashed | Metrics, logs, passive |
| `replication` | Blue dashed, animated | DB replication, S3 sync |
| `boundary-crossing` | Orange dashed | Trust/security zone crossing |

### Node Classes

| Class | Shape | Use |
|-------|-------|-----|
| `internal-service` | rectangle | Owned services |
| `external-node` | rectangle (orange border) | External systems, entry points |
| `microservice` | hexagon | Independent services |
| `datastore` | cylinder | Databases, object stores |
| `queue` | queue | Message queues, event buses |
| `actor` | person | Human users |
| `aws-service` | cloud | AWS managed services |

### Container Classes

| Class | Visual | Use |
|-------|--------|-----|
| `account-boundary` | Navy fill, bold | AWS account, GovCloud |
| `vpc-container` | Deep navy, dashed | VPC, network zone |
| `service-group` | Surface fill, dashed | Logical service cluster |
| `security-boundary` | Transparent, orange dashed | FedRAMP, IL, ITAR |

---

## Abstraction Hierarchy (L1–L4)

Every diagram declares its level in a header comment. Default to **L2**.

| Level | Name | Audience | Scope | Connector Labels |
|-------|------|----------|-------|-----------------|
| **L1** | Landscape | Exec, customer | All products + external deps | Short noun phrases |
| **L2** | Architecture | Engineering leads | One system + integrations | Verb phrases |
| **L3** | Deployment | DevOps, cloud architects | VPCs, AZs, GovCloud, IAM | Protocol + port |
| **L4** | Component | Developers | Internals of one container | Method/event names |

**Rules**:
- Never combine levels in one diagram — drill down across separate diagrams
- L1 for executive briefings, L3 for infrastructure, L4 rarely

### Required Header Block

```d2
# LEVEL: L2
# AUDIENCE: Engineering leads
# COMPLIANCE: none
# Shows: System X architecture and integration points
```

---

## Examples

The `examples/` directory contains 8 complete, renderable Bluestaq diagrams:

| # | File | Type | What It Shows |
|---|------|------|---------------|
| 1 | `1-landscape.d2` | L1 Landscape | Product ecosystem with actors and external systems |
| 2 | `2-web-app-architecture.d2` | L2 Architecture | CloudFront + Node.js + RDS/ElastiCache in VPC |
| 3 | `3-cicd-pipeline.d2` | L2 Pipeline | GitHub Actions → parallel tests → Docker → canary deploy |
| 4 | `4-k8s-cluster.d2` | L2 K8s Topology | 3 namespaces, ingress, deployments, statefulsets, PVs |
| 5 | `5-sequence.d2` | L2 Sequence | OAuth2 login flow with Auth0 and error handling |
| 6 | `6-network-security.d2` | L3 Security | FedRAMP High boundary, trust zones, firewalls |
| 7 | `7-data-flow.d2` | L2 Data Flow | ETL pipeline: PostgreSQL → Spark → Redshift/S3 |
| 8 | `ai-agent-firewall.d2` | L2 Architecture | AI firewall with inbound/outbound inspection layers |

Render any example:

```bash
d2 --sketch --theme 200 --layout elk examples/ai-agent-firewall.d2 output.svg
```

---

## Repo Structure

```
d2-diagram-skill/
├── README.md              # This file
├── .gitignore             # Excludes .svg, .DS_Store
├── skill/                 # Claude Code skill (install to ~/.claude/skills/d2-diagram/)
│   ├── SKILL.md           # Skill definition with frontmatter
│   ├── references/
│   │   ├── d2-style-guide.md   # Complete style reference (~1,380 lines)
│   │   └── d2-examples.md      # 8 worked examples (~1,095 lines)
│   └── evals/
│       ├── evals.json          # 6 quality evaluation test cases
│       └── trigger_queries.json # 26 trigger queries (14 positive, 12 negative)
├── spec/
│   └── bluestaq_d2_spec.md     # Bluestaq D2 standards specification
├── examples/              # 8 renderable D2 diagrams
│   ├── 1-landscape.d2
│   ├── ...
│   └── ai-agent-firewall.d2
└── legacy/                # Pre-Bluestaq diagrams (reference only)
    └── *.d2
```

---

## Evaluation Results

The skill's trigger description was tested against 26 queries using the skill-creator evaluation framework:

| Metric | Score |
|--------|-------|
| **Total queries** | 26 |
| **Positive triggers** (should activate) | 14/14 (100%) |
| **Negative triggers** (should not activate) | 12/12 (100%) |
| **Overall accuracy** | 26/26 (100%) |

Positive triggers include: microservices diagrams, network topology, data flows, ER diagrams, CI/CD pipelines, Kubernetes clusters, sequence diagrams, Bluestaq landscape, GovCloud deployment, FedRAMP security topology.

Negative triggers include: D3.js charts, Grafana dashboards, Terraform modules, matplotlib, PlantUML, Datadog APM, ASCII art, Tailwind CSS config, FedRAMP SSP prose docs.

---

## Key Anti-Patterns

- **Never use orange as a fill** — orange is stroke/border only
- **Never combine L2 and L3** on the same canvas — split into separate diagrams
- **Never use legacy palettes** — always Bluestaq Navy
- **Never skip the header comment block** — level and audience determine everything
- **Never use `stroke-width: 1.5`** — D2 v0.7.x requires integer values (1, 2, 3)
- **Avoid unlabeled boundary crossings** — every edge crossing a trust zone needs a protocol label

---

## D2 Compatibility Notes

Tested with D2 v0.7.1. Known issues:

- `stroke-width` must be integer (no decimals)
- `dev%2Fterraform.svg` icon returns 403
- `aws%2F_General%2FAWS-General_light-bg.svg` icon returns 403
- Sequence diagrams use dagre internally regardless of `layout-engine` setting

---

## License

Internal use — Altstaq / Bluestaq.
