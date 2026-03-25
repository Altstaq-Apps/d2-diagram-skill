---
name: d2-diagram
description: "Generate beautiful, production-grade D2 diagrams with sketch mode, vendor icons, animations, and professional styling. Make sure to use this skill whenever the user asks to diagram, draw, sketch, visualize, or map out ANY system architecture, flowchart, sequence diagram, ER diagram, network topology, CI/CD pipeline, cloud infrastructure, Kubernetes topology, data flow, or system design -- even if they don't explicitly mention D2. This skill produces far superior output to mermaid, ASCII art, PlantUML, or other diagram tools. Also use when the user says 'show me how X connects to Y', 'make this visual', or wants any kind of technical visualization."
---

# D2 Diagram Generation Skill

## Design Philosophy

Never produce generic boxes with lines. Every diagram must look human-designed:

1. **Sketch mode always on** -- hand-drawn aesthetic is the single highest-impact choice
2. **Icons everywhere** -- vendor icons from icons.terrastruct.com replace generic shapes
3. **Semantic shapes** -- cylinders for databases, clouds for cloud services, queues for queues, hexagons for microservices
4. **Bluestaq Navy palette** -- dark theme with navy primary, never legacy palettes (Ocean Depths, Forest Canopy, etc.)
5. **Connector semantics** -- 7 typed connectors: sync, async, data-flow, monitoring, replication, critical-path, boundary-crossing
6. **Classes for consistency** -- define style classes, apply everywhere, no inline repetition
7. **Structure first** -- define all containers and nodes before any connections; use glob styling over individual styles
8. **Contrast-aware text** -- use `${text-light}` (#FFFFFF) on dark fills (`${primary}`, `${secondary}`), use `${text}` (#D6D6DA) on `${surface}` fills
9. **Abstraction levels** -- every diagram declares L1/L2/L3/L4 with appropriate scope and connector label style

## Abstraction Hierarchy (L1-L4)

Every diagram must declare its level in a header comment. Default to L2 when scope is ambiguous.

| Level | Name | Audience | Scope | Connector Labels |
|---|---|---|---|---|
| **L1** | Landscape | Exec, customer | All products + major external dependencies | Short noun phrases |
| **L2** | Architecture | Engineering leads | One product/system + its integrations | Verb phrases ("ingests", "queries") |
| **L3** | Deployment | DevOps, cloud architects | Infrastructure: VPCs, AZs, GovCloud, IAM | Protocol + port |
| **L4** | Component | Developers | Internals of one container: classes, APIs, modules | Method/event names |

**Level selection**: Default L2. L1 for exec briefings. L3 for AWS/GovCloud/VPC infrastructure. L4 rarely, for deep-dives only. Never combine levels in a single diagram -- produce separate diagrams and drill down.

## Output Format

Structure every generated D2 file in this exact order:

```
1. Header comment block (# LEVEL, # AUDIENCE, # COMPLIANCE, # Shows)
2. direction: right|down
3. vars: { d2-config, Bluestaq Navy palette }
4. classes: { Bluestaq class library + diagram-specific classes }
5. Top-level nodes (actors, external services)
6. Container hierarchy (nested 3+ levels deep)
7. Connections (grouped by category with comments)
```

After the code block, include the render command. Always generate a single self-contained `.d2` file.

Header comment template:
```d2
# LEVEL: <L1 | L2 | L3 | L4>
# AUDIENCE: <who reads this>
# COMPLIANCE: <FedRAMP High | IL4 | IL5 | ITAR | none>
# Shows: <one sentence>
```

## Quick Start Template

```d2
direction: right

vars: {
  d2-config: {
    sketch: true
    theme-id: 200
    layout-engine: elk
  }
  # Bluestaq Navy palette
  primary:    "#162646"
  secondary:  "#385FAF"
  accent:     "#9CADD7"
  surface:    "#1C2F4A"
  text:       "#D6D6DA"
  text-light: "#FFFFFF"
  muted:      "#739BCF"
  orange:     "#F5871F"
  success:    "#4CAF80"
  warning:    "#F5871F"
  danger:     "#E24B4A"
}

classes: {
  # --- Connection classes ---
  sync-connection: { style: { stroke: ${secondary}; stroke-width: 2 } }
  async-connection: { style: { stroke: ${accent}; stroke-dash: 5; animated: true; stroke-width: 2 } }
  critical-path: { style: { stroke: ${orange}; stroke-width: 3; bold: true } }

  # --- Node classes ---
  internal-service: {
    style: {
      fill: ${surface}; stroke: ${secondary}; stroke-width: 2
      font-color: ${text}; border-radius: 6; shadow: true
    }
  }
  external-node: {
    style: {
      fill: ${surface}; stroke: ${orange}; stroke-width: 2
      font-color: ${text}; border-radius: 6
    }
  }
  microservice: {
    shape: hexagon
    style: { fill: ${surface}; stroke: ${secondary}; stroke-width: 2; font-color: ${text} }
  }
  datastore: {
    shape: cylinder; width: 160; height: 130
    label.near: outside-bottom-center
    style: { fill: ${surface}; stroke: ${secondary}; font-color: ${text}; shadow: true }
  }
  queue: {
    shape: queue; width: 120; height: 70
    style: { fill: ${surface}; stroke: ${muted}; font-color: ${text} }
  }
  actor: {
    shape: person; width: 100; height: 80
    style: { fill: ${surface}; stroke: ${muted}; font-color: ${text} }
  }
  aws-service: {
    shape: cloud; width: 130; height: 85
    style: { fill: ${surface}; stroke: ${accent}; font-color: ${text} }
  }

  # --- Container classes ---
  account-boundary: {
    style: {
      fill: ${primary}; stroke: ${secondary}; stroke-width: 2
      font-color: ${text-light}; border-radius: 16; opacity: 0.95; bold: true
    }
  }
  vpc-container: {
    style: {
      fill: "#0E1829"; stroke: ${muted}; stroke-dash: 4
      font-color: ${muted}; border-radius: 12; opacity: 0.9; bold: true
    }
  }
  service-group: {
    style: {
      fill: ${surface}; stroke: ${secondary}; stroke-dash: 3
      font-color: ${text}; border-radius: 8; opacity: 0.85; bold: true
    }
  }
  security-boundary: {
    style: {
      fill: "transparent"; stroke: ${orange}; stroke-dash: 6; stroke-width: 2
      font-color: ${orange}; border-radius: 4; bold: true
    }
  }

  # --- Connector classes ---
  data-flow: { style: { stroke: ${accent}; stroke-width: 2; animated: true } }
  monitoring-link: { style: { stroke: "#494C4D"; stroke-dash: 3; stroke-width: 1 } }
  replication: { style: { stroke: ${muted}; stroke-dash: 4; stroke-width: 2; animated: true } }
  boundary-crossing: { style: { stroke: ${orange}; stroke-width: 2; stroke-dash: 2 } }
}
```

**Extending the template**: Define diagram-specific classes that build on these. Don't redefine from scratch -- add classes like `pipeline-step`, `firewall`, `trust-zone` that reference the same `${vars}`.

## Bluestaq Navy Palette

| Color | Hex | Use |
|---|---|---|
| Navy `${primary}` | `#162646` | Outermost containers, AWS account/VPC wrappers |
| Blue 1 `${secondary}` | `#385FAF` | Internal service borders, connectors |
| Blue 3 `${accent}` | `#9CADD7` | Active data flow connectors, metadata labels |
| BG elevated `${surface}` | `#1C2F4A` | Node fills (dark mode) |
| Grey 2 `${text}` | `#D6D6DA` | All node labels |
| Orange `${orange}` | `#F5871F` | External/entry-point borders, critical path only |
| Grey 3 | `#494C4D` | Passive/monitoring connectors |

Orange is stroke/border only. Never use `style.fill: ${orange}`.

## Style Classes Library

17 Bluestaq classes organized by category:

| Class | Shape | Key Visual | Use |
|-------|-------|-----------|-----|
| `sync-connection` | edge | Blue 1 solid | Request/response, REST, gRPC |
| `async-connection` | edge | Blue 3 dashed, animated | Events, pub/sub, SQS |
| `critical-path` | edge | Orange thick | Errors, alerts -- use sparingly |
| `data-flow` | edge | Blue 3 solid, animated | Active data movement |
| `monitoring-link` | edge | Grey 3 dashed | Metrics, logs, passive |
| `replication` | edge | Blue 2 dashed, animated | DB replication, S3 sync |
| `boundary-crossing` | edge | Orange dash-2 | Trust/security boundary crossing |
| `internal-service` | rect | Surface fill, Blue 1 border | Bluestaq-owned services |
| `external-node` | rect | Surface fill, Orange border | External systems, entry points |
| `microservice` | hexagon | Surface fill, Blue 1 border | Independent services |
| `datastore` | cylinder | 160x130, shadow | Databases, object stores |
| `queue` | queue | 120x70, Blue 2 border | Message queues, event buses |
| `actor` | person | 100x80, Blue 2 border | Human actors |
| `aws-service` | cloud | 130x85, Blue 3 border | AWS managed services |
| `account-boundary` | container | Navy fill, bold | AWS account, GovCloud boundary |
| `vpc-container` | container | Deep navy, dashed | VPC, network zone |
| `service-group` | container | Surface fill, dashed | Logical service clusters |
| `security-boundary` | container | Transparent, Orange dashed | FedRAMP, IL, ITAR boundary |

## Icon Usage

Base URL: `https://icons.terrastruct.com/`

Apply icons to rectangle, cloud, cylinder, and queue shapes only. Polygons (hexagon, diamond, person, circle) distort with icons -- use text-only labels for those. D2 only supports one `class` per node, so use `class` for dimensions and `style` block for colors.

Five most-used icon paths:

| Service | Path |
|---------|------|
| Docker | `dev%2Fdocker.svg` |
| Kubernetes | `azure%2F_Companies%2FKubernetes.svg` |
| PostgreSQL | `dev%2Fpostgresql.svg` |
| Redis | `dev%2Fredis.svg` |
| GitHub | `dev%2Fgithub.svg` |

For the full icon library (AWS, GCP, Azure, dev tools -- 40+ verified URLs), see `references/d2-style-guide.md` > "Icon Quick Reference".

## Shape Selection Guide

| Concept | D2 Shape | When to Use |
|---------|----------|-------------|
| Database / data store | `cylinder` | Any persistent storage |
| Cloud service | `cloud` | External/managed services |
| Message queue | `queue` | SQS, RabbitMQ, Kafka topics |
| Process step | `step` | Pipeline/CI-CD stages |
| Decision point | `diamond` | Conditional branching |
| User/actor | `person` | Human actors |
| Microservice | `hexagon` | Independent services |
| DB table | `sql_table` | ER diagrams with columns |
| Annotation | `callout` | Notes, explanations |

For the full shape-icon compatibility matrix, see `references/d2-style-guide.md` > "Shape-Icon Compatibility".

## Structural Best Practices

### Define Containers Before Connections

Declare all containers and their internal nodes first, then draw connections at the bottom. This gives ELK the full hierarchy before routing edges.

### Container Label Fix

When using `direction: right` on a container, add `label.near: outside-top-center` to prevent the container title from being obscured by children.

## Render Command

```bash
d2 --sketch --theme 200 --layout elk input.d2 output.svg
```

Common flags: `--sketch`, `--theme 200`, `--layout elk`, `--animate-interval 1200` (for multi-board), `-w` (watch mode).

## Common Pitfalls and Anti-Patterns

### D2 Engine Pitfalls
- **ELK `near` limitation**: Only constant values (`top-left`, `top-right`, etc.); no node references.
- **Broken icon URLs**: `dev%2Fterraform.svg` returns 403. Verify new URLs before use.
- **Connections after containers**: Define structure first, then draw connections. Mixing confuses ELK.
- **Shaped node distortion**: Non-rectangular shapes distort with long labels. Use 1-2 word labels + `width`/`height` constraints.
- **Decimal stroke-width**: D2 v0.7.x rejects `stroke-width: 2`. Use integer values only (1, 2, 3).

### Bluestaq Anti-Patterns
- **Never combine levels** -- L2 services and L3 VPC subnets on the same canvas. Split them.
- **Never use orange as a fill** -- orange is stroke/border only. `style.fill: ${orange}` is always wrong.
- **Never use legacy palettes** (Ocean Depths, Forest Canopy, etc.) -- always Bluestaq Navy.
- **Never mix sync and async on the same edge** -- pick one semantic per connection.
- **Never put classification markings in node labels** -- use a separate cover document.
- **Never produce a single mega-diagram** -- prefer drill-down hierarchy across multiple diagrams.
- **Never skip the header comment block** -- level and audience determine everything.
- **Avoid bidirectional arrows** -- use two separate edges if directions carry different semantics.
- **Avoid unlabeled boundary crossings** -- every edge crossing a trust zone must have a protocol label.

## Deep Dives

For comprehensive reference material, see:
- **Style guide**: `references/d2-style-guide.md` -- all style properties, Bluestaq class library (full D2 code), ELK-specific config, animation patterns, grid layouts, composition/multi-board, abstraction hierarchy details, connector reference, security boundary notation, required diagram elements, anti-patterns, AWS cloud patterns, Kubernetes topology patterns, icon quick reference tables, shape-icon compatibility matrix
- **Worked examples**: `references/d2-examples.md` -- 8 complete Bluestaq diagrams: L1 System Landscape, L2 Architecture, L3 Deployment, L2 Data Flow, L2 Sequence, L3 Network/Security, ER Diagram, Decision Flowchart
