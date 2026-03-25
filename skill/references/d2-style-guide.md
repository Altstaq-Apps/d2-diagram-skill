# D2 Style Guide -- Complete Reference

## Table of Contents

- [All Style Properties](#all-style-properties)
- [Bluestaq Navy Palette](#bluestaq-navy-palette)
- [Bluestaq Class Library](#bluestaq-class-library)
- [Abstraction Hierarchy (L1-L4)](#abstraction-hierarchy-l1-l4)
- [Connector Reference](#connector-reference)
- [Security Boundary Notation](#security-boundary-notation)
- [Required Diagram Elements](#required-diagram-elements)
- [Anti-Patterns](#anti-patterns)
- [Font Recommendations](#font-recommendations)
- [ELK-Specific Configuration](#elk-specific-configuration)
- [Animation Patterns](#animation-patterns)
- [Grid Layouts](#grid-layouts)
- [Composition and Multi-Board](#composition-and-multi-board)
- [Fill Pattern Reference](#fill-pattern-reference)
- [Advanced Connection Styling](#advanced-connection-styling)
- [Container Styling](#container-styling)
- [Layout Techniques](#layout-techniques)
- [Glob Patterns for Bulk Styling](#glob-patterns-for-bulk-styling)
- [Variables and Imports](#variables-and-imports)
- [Markdown and LaTeX in Labels](#markdown-and-latex-in-labels)
- [Icon Position Control](#icon-position-control)
- [Tooltip and Link](#tooltip-and-link)
- [Direction Control](#direction-control)
- [Icon Spacing and Positioning](#icon-spacing-and-positioning)
- [Shape-Icon Compatibility](#shape-icon-compatibility)
- [Icon Quick Reference](#icon-quick-reference)
- [AWS Cloud Architecture Patterns](#aws-cloud-architecture-patterns)
- [Kubernetes Topology Patterns](#kubernetes-topology-patterns)

---

## All Style Properties

### Node Style Properties

```d2
my-node: {
  style: {
    # Colors
    fill: "#1C2F4A"          # Background color
    stroke: "#385FAF"        # Border color
    font-color: "#D6D6DA"    # Text color

    # Dimensions
    stroke-width: 2          # Border width (default: 2)
    border-radius: 12        # Corner rounding (0-20 typical)
    opacity: 0.9             # 0.0 to 1.0

    # Effects
    shadow: true             # Drop shadow
    3d: true                 # 3D extrusion effect
    multiple: true           # Stacked/multiple instances look

    # Typography
    bold: true
    italic: false
    underline: false
    font-size: 16            # In pixels
    font: "mono"             # "mono" for monospace

    # Fill patterns
    fill-pattern: dots       # dots | lines | grain | paper
  }
}
```

### Connection Style Properties

```d2
A -> B: {
  style: {
    stroke: "#385FAF"
    stroke-width: 2
    stroke-dash: 5           # Dash length (0 = solid)
    opacity: 0.8
    animated: true           # Flowing animation
    bold: true               # Bold label
    italic: false
    underline: false
    font-size: 14
    font-color: "#D6D6DA"
    font: "mono"
    fill: "transparent"      # Label background
  }
}
```

### Container Style Properties

Containers inherit node properties plus:

```d2
my-container: {
  style: {
    fill: "#162646"
    stroke: "#385FAF"
    border-radius: 16
    opacity: 0.9
    font-color: "#FFFFFF"
    bold: true
    double-border: true      # Double-line border
    fill-pattern: dots
  }

  # Nested children
  child-a
  child-b
}
```

## Bluestaq Navy Palette

The single palette for all Bluestaq diagrams. Never use legacy palettes (Ocean Depths, Forest Canopy, Sunset Ember, Arctic Frost, Berry Noir, Slate Modern).

```d2
vars: {
  d2-config: {
    sketch: true
    theme-id: 200
    layout-engine: elk
  }

  # Bluestaq Navy -- primary brand palette
  primary:    "#162646"   # Navy -- deep containers, primary nodes
  secondary:  "#385FAF"   # Blue 1 -- internal service borders, connectors
  accent:     "#9CADD7"   # Blue 3 -- active data flow connectors, labels
  surface:    "#1C2F4A"   # BG elevated -- card/node fill on dark canvas
  text:       "#D6D6DA"   # Grey 2 -- all body labels (AAA on dark bg)
  text-light: "#FFFFFF"   # White -- labels on primary/secondary fills
  muted:      "#739BCF"   # Blue 2 -- sublabels, metadata, group borders

  # Orange -- use sparingly (CTAs, external entry points, critical paths only)
  orange:     "#F5871F"

  # Semantic
  success:    "#4CAF80"
  warning:    "#F5871F"   # same as orange -- intentional
  danger:     "#E24B4A"
}
```

### Color Semantics

| Color | Hex | Use |
|---|---|---|
| Navy `${primary}` | `#162646` | Outermost containers, AWS account/VPC wrappers |
| Blue 1 `${secondary}` | `#385FAF` | Internal service borders, connectors |
| Blue 3 `${accent}` | `#9CADD7` | Active data flow connectors, metadata labels |
| BG elevated `${surface}` | `#1C2F4A` | Node fills (dark mode) |
| Grey 2 `${text}` | `#D6D6DA` | All node labels |
| Orange `${orange}` | `#F5871F` | External/entry-point borders, critical path only |
| Grey 3 | `#494C4D` | Passive/monitoring connectors |

**Contrast rules**: Use `${text-light}` (#FFFFFF) on dark fills (`${primary}`, `${secondary}`). Use `${text}` (#D6D6DA) on `${surface}` fills. Orange is stroke/border only -- never use `style.fill: ${orange}`.

### Dark Mode Note

Bluestaq Navy IS the dark theme (theme-id 200 / Terminal). No separate dark mode variant needed.

## Bluestaq Class Library

17 classes organized by category. Include in every diagram.

### Connection Classes (3)

```d2
classes: {
  sync-connection: {
    style: {
      stroke: ${secondary}
      stroke-width: 2
    }
  }
  async-connection: {
    style: {
      stroke: ${accent}
      stroke-dash: 5
      animated: true
      stroke-width: 2
    }
  }
  critical-path: {
    style: {
      stroke: ${orange}
      stroke-width: 3
      bold: true
    }
  }
}
```

### Node Classes (7)

```d2
classes: {
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
}
```

### Container Classes (4)

```d2
classes: {
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
}
```

### Connector Classes (4)

```d2
classes: {
  data-flow: { style: { stroke: ${accent}; stroke-width: 2; animated: true } }
  monitoring-link: { style: { stroke: "#494C4D"; stroke-dash: 3; stroke-width: 1 } }
  replication: { style: { stroke: ${muted}; stroke-dash: 4; stroke-width: 2; animated: true } }
  boundary-crossing: { style: { stroke: ${orange}; stroke-width: 2; stroke-dash: 2 } }
}
```

## Abstraction Hierarchy (L1-L4)

Every diagram must declare its level in a header comment. Default to L2 when scope is ambiguous.

| Level | Name | Audience | Scope | Connector Labels |
|---|---|---|---|---|
| **L1** | **Landscape** | Exec, customer | All products + major external dependencies | Short noun phrases |
| **L2** | **Architecture** | Engineering leads | One product/system + its integrations | Verb phrases ("ingests", "queries") |
| **L3** | **Deployment** | DevOps, cloud architects | Infrastructure: VPCs, AZs, GovCloud, IAM | Protocol + port |
| **L4** | **Component** | Developers | Internals of one container: classes, APIs, modules | Method/event names |

### Level Selection Rules

- Default to **L2** when scope is ambiguous.
- Use **L1** only for executive briefings, capability overviews, or customer-facing one-pagers.
- Use **L3** when AWS infrastructure, GovCloud boundaries, VPCs, or deployment topology is the subject.
- Use **L4** rarely -- only for specific technical deep-dives.
- **Never combine levels** in a single diagram. If both L2 and L3 detail are needed, produce two diagrams.

### Drill-Down Rule

Complex systems get a diagram per level, not everything on one canvas. Produce L1 first, then offer to drill into any system as L2. Repeat for L3/L4. Each diagram links conceptually to the next via matching node names.

## Connector Reference

| Class | Stroke | Dash | Animated | Use |
|---|---|---|---|---|
| `sync-connection` | Blue 1 | solid | no | Request/response, REST, gRPC |
| `async-connection` | Blue 3 | dashed | yes | Events, pub/sub, SQS, SNS |
| `data-flow` | Blue 3 | solid | yes | Active data movement (ingest/share) |
| `monitoring-link` | Grey 3 | dashed | no | Metrics, logs, CloudWatch, passive links |
| `replication` | Blue 2 | dashed | yes | DB replication, S3 sync |
| `critical-path` | Orange | solid | no | Critical path, errors, alerts -- use sparingly |
| `boundary-crossing` | Orange | dash-2 | no | Traffic crossing a trust or security boundary |

### Label Rules by Level

- **L1**: Short noun phrases only ("raw data", "ingests")
- **L2**: Verb phrases ("stores object", "validates token", "publishes event")
- **L3**: Protocol + port ("HTTPS :443", "IPSec", "SQL :5432")
- **L4**: Method or event name ("POST /ingest", "DataReady event")
- **Sequence diagrams**: Full request description on outgoing, status code on return

## Security Boundary Notation

Include only when the diagram is a customer-facing deliverable requiring compliance context.

### Rules

1. Use the `security-boundary` class (orange dashed border) as a wrapping container.
2. Name the container with the compliance scope: `FedRAMP High boundary`, `IL4 boundary`, `ITAR boundary`.
3. Add `# COMPLIANCE: <scope>` comment at the top of the file.
4. Never write classification markings (SECRET, CUI, etc.) in diagram node labels.
5. For IL level boundaries, use separate containers -- never nest IL5 inside IL4 in the same diagram.
6. Trust zone crossings that cross the boundary must use `boundary-crossing` class (orange).

### IL Boundary Color Convention

| Scope | Container border | Label |
|---|---|---|
| Unclassified / FedRAMP | Orange dashed (`${orange}`) | "FedRAMP High boundary" |
| IL4 | Orange dashed | "IL4 boundary" |
| IL5 | Orange dashed + stroke-width: 3 | "IL5 boundary" |
| ITAR | Orange dashed + stroke-dash: 2 | "ITAR boundary" |

## Required Diagram Elements

Every Bluestaq diagram must include:

1. **Header comment block** -- level, audience, compliance scope (if applicable), what it shows
2. **vars block** -- full Bluestaq Navy palette (never legacy palettes)
3. **classes block** -- full Bluestaq class library
4. **Legend** (L3 and network diagrams only) -- callout node listing connector and border meanings
5. **Render command** -- after the code block

```d2
# LEVEL: <L1 | L2 | L3 | L4>
# AUDIENCE: <who reads this>
# COMPLIANCE: <FedRAMP High | IL4 | IL5 | ITAR | none>
# Shows: <one sentence>
```

## Anti-Patterns

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

### D2 Engine Pitfalls

- **ELK `near` limitation**: Only constant values (`top-left`, `top-right`, etc.); no node references.
- **Broken icon URLs**: Some URLs (e.g., `dev%2Fterraform.svg`, `aws%2F_General%2FAWS-General_light-bg.svg`) return 403. Verify new URLs before use.
- **Connections after containers**: Define structure first, then draw connections. Mixing confuses ELK.
- **Shaped node distortion**: Non-rectangular shapes distort with long labels. Use 1-2 word labels + `width`/`height` constraints.
- **Decimal stroke-width**: D2 v0.7.x rejects `stroke-width: 2`. Use integer values only (1, 2, 3).

## Font Recommendations

D2 supports `font` property with two values:
- Default sans-serif (used automatically)
- `"mono"` for monospace

For labels that represent code, IDs, or technical identifiers, use mono:

```d2
api-endpoint: "GET /api/v1/users" {
  style.font: "mono"
  style.font-size: 14
}
```

## ELK-Specific Configuration

ELK (Eclipse Layout Kernel) is the default layout engine for all Bluestaq diagrams.

### Container Width/Height (ELK-Exclusive)

ELK allows explicit `width` and `height` on containers to control sizing. Other engines ignore these:

```d2
web-tier: Web Tier {
  width: 350    # Prevents ELK from stretching containers
  height: 200
  app-a: Frontend A
  app-b: Frontend B
}
```

Use width constraints when ELK stretches containers too far horizontally.

### Container-to-Container Routing

ELK excels at routing edges between containers. Connections between deeply nested children route cleanly through container boundaries:

```d2
# ELK routes this cleanly through vpc boundaries
vpc-a.private.app -> vpc-b.private.db: Cross-VPC peering
```

### SQL Table Exact-Row Routing

With ELK, connections to `sql_table` nodes can target specific rows (columns):

```d2
orders.user_id -> users.id
```

### ELK Limitations

- **`near` keyword**: Only supports constant values (`top-left`, `top-center`, `top-right`, `center-left`, `center-center`, `center-right`, `bottom-left`, `bottom-center`, `bottom-right`). Node references like `near: some-node` cause compile errors.
- **Strictly hierarchical**: ELK enforces a strict tree hierarchy. Overlapping containers or shared children aren't supported.
- **Unnecessary bends**: ELK occasionally adds extra bends in edge routing. Use port simulation (tiny circle nodes) to guide edge entry/exit points.
- **Direction inheritance**: Child containers inherit the parent's `direction` unless explicitly overridden.

## Animation Patterns

### Connection Animations

Apply `animated: true` to any connection to get flowing dots along the path:

```d2
producer -> queue: events {
  style.animated: true
  style.stroke-dash: 5
}
```

Best practices:
- Use animated connections for async/event-driven flows
- Pair with `stroke-dash` for visual distinction from sync connections
- Don't animate every connection -- reserve for emphasis

### Multi-Board Step Animations

D2 supports multi-board diagrams with `layers`, `scenarios`, and `steps`. Steps animate between boards:

```d2
title: Deployment Process

step1: {
  server: App Server {
    version: v1.0
  }
}

steps: {
  step2: {
    server: App Server {
      version: v2.0
      style.fill: ${success}
    }
  }
  step3: {
    server: App Server {
      version: v2.0
      style.fill: ${success}
    }
    status: "Deploy Complete" {
      shape: callout
    }
  }
}
```

Render with animation interval:

```bash
d2 --animate-interval 1200 deploy.d2 deploy.svg
```

### Layers

Layers create separate boards accessible via navigation:

```d2
title: System Overview

layer1: {
  # Overview diagram
}

layers: {
  detailed: {
    # Detailed view
  }
  monitoring: {
    # Monitoring view
  }
}
```

## Grid Layouts

### Basic Grid Properties

Use grid layouts to arrange nodes in rows and columns. Set `grid-rows` or `grid-columns` (or both) on a container:

```d2
dashboard: {
  grid-rows: 2
  grid-columns: 3
  grid-gap: 16
}
```

- `grid-rows`: Number of rows
- `grid-columns`: Number of columns
- `grid-gap`: Uniform gap between cells (in pixels)
- `vertical-gap`: Override gap for vertical spacing only
- `horizontal-gap`: Override gap for horizontal spacing only

If only `grid-rows` is set, columns auto-fill. If only `grid-columns` is set, rows auto-fill.

### Dashboard Layout (Bluestaq)

```d2
grid-rows: 2
grid-columns: 3
grid-gap: 16

metric1: "Requests/sec\n12,450" {
  style: {
    fill: ${surface}
    stroke: ${secondary}
    font-color: ${text}
    border-radius: 12
    bold: true
    font-size: 18
  }
}
metric2: "Latency p99\n45ms" {
  style: {
    fill: ${surface}
    stroke: ${success}
    font-color: ${text}
    border-radius: 12
    bold: true
    font-size: 18
  }
}
metric3: "Error Rate\n0.02%" {
  style: {
    fill: ${surface}
    stroke: ${orange}
    font-color: ${text}
    border-radius: 12
    bold: true
    font-size: 18
  }
}
```

### Multi-Tier Pattern Using Grids

Use grids within containers to arrange resources in tiers:

```d2
vpc: VPC {
  class: vpc-container

  public-subnet: Public Subnet {
    grid-rows: 1
    grid-columns: 3
    grid-gap: 12

    alb: ALB { class: internal-service }
    nat: NAT Gateway { class: internal-service }
    bastion: Bastion Host { class: internal-service }
  }

  private-subnet: Private Subnet {
    grid-rows: 1
    grid-columns: 4
    grid-gap: 12

    app-1: App Server 1 { class: internal-service }
    app-2: App Server 2 { class: internal-service }
    app-3: App Server 3 { class: internal-service }
    worker: Worker { class: internal-service }
  }

  data-subnet: Data Subnet {
    grid-rows: 1
    grid-columns: 3
    grid-gap: 12

    rds-primary: RDS Primary { class: datastore }
    rds-replica: RDS Replica { class: datastore }
    redis: ElastiCache { class: datastore }
  }
}
```

### Handling 50+ Node Diagrams

For large diagrams with many nodes:

1. **Group into grid containers**: Put related nodes in grid containers to control density
2. **Use `multiple: true`** instead of showing every replica: One node with stacked appearance represents N instances
3. **Collapse detail into layers**: Use multi-board `layers` to separate overview from detailed views
4. **Grid + nested containers**: Top-level grid arranges subsystems, each subsystem is a container with its own internal layout
5. **Limit grid children to 8-12**: Beyond that, consider nesting another level

## Composition and Multi-Board

D2 supports three composition mechanisms for creating multi-board diagrams.

### Layers

Layers create independent boards with no inheritance between them. Use for different views of the same system:

```d2
title: System Architecture

# Base board content here
api: API Server
db: Database
api -> db

layers: {
  security-view: {
    # Completely independent board
    firewall: Firewall
    waf: WAF
    firewall -> waf
  }
  monitoring-view: {
    # Another independent board
    prometheus: Prometheus
    grafana: Grafana
    prometheus -> grafana
  }
}
```

### Scenarios

Scenarios inherit from the base board and add/modify elements. Use for showing variants:

```d2
title: Production Setup

api: API Server { style.fill: ${surface} }
db: Database { shape: cylinder }
api -> db

scenarios: {
  high-availability: {
    api.style.multiple: true
    db.style.multiple: true
    lb: Load Balancer
    lb -> api
  }
  disaster-recovery: {
    dr-region: DR Region {
      api-dr: API Server { style.fill: ${danger} }
      db-dr: Database { shape: cylinder }
    }
    db -> dr-region.db-dr: replication {
      style.animated: true
    }
  }
}
```

### Steps

Steps create sequential boards where each step inherits from the previous one. Use for animated walkthroughs:

```d2
title: Request Flow

client: Client
api: API Server
db: Database

steps: {
  1: {
    client -> api: "1. HTTP Request" {
      style.animated: true
    }
  }
  2: {
    api -> db: "2. Query DB" {
      style.animated: true
    }
  }
  3: {
    db -> api: "3. Return Data" {
      style.stroke: ${success}
    }
  }
  4: {
    api -> client: "4. HTTP Response" {
      style.stroke: ${success}
    }
  }
}
```

Render steps with animation: `d2 --animate-interval 1500 flow.d2 flow.svg`

### When to Use Each

| Mechanism | Inheritance | Best For |
|-----------|-------------|----------|
| `layers` | None | Independent views of same system |
| `scenarios` | From base board | Deployment variants, failure modes |
| `steps` | From previous step | Animated walkthroughs, request flows |

## Fill Pattern Reference

```d2
# Available fill patterns
dots: "Dots Pattern" { style.fill-pattern: dots }
lines: "Lines Pattern" { style.fill-pattern: lines }
grain: "Grain Pattern" { style.fill-pattern: grain }
paper: "Paper Pattern" { style.fill-pattern: paper }
```

Fill patterns work well with sketch mode for an organic, textured feel. Best on containers and large nodes.

## Advanced Connection Styling

### Self-Referencing Connections (Self-Loops)

Self-loops (`X -> X`) are prone to label overlap:

1. **Keep labels very short** -- 1-2 words max
2. **Use a callout annotation** for longer descriptions
3. **Add padding or dimensions** to the node if the self-loop still overlaps

```d2
# GOOD: short label only
comments -> comments: replies {
  style.stroke-dash: 3
}

# ALSO GOOD: callout annotation (ELK only supports constant near values)
replies-note: "parent_id references\ncomments.id" {
  shape: callout
  near: top-right
  style.font-size: 13
}
```

### Arrowhead Types

```d2
# Standard arrows
A -> B                    # Default arrow
A <- B                    # Reverse arrow
A <-> B                   # Bidirectional
A -- B                    # No arrowheads (line)

# Custom arrowheads
A -> B: {
  target-arrowhead: {
    shape: diamond        # diamond, arrow, triangle, cf-one, cf-one-required, cf-many, cf-many-required
    style.filled: true    # Filled or hollow
  }
  source-arrowhead: {
    shape: diamond
    style.filled: false
  }
}
```

### ER Notation

```d2
# One-to-many
users -> orders: {
  source-arrowhead.shape: cf-one-required
  target-arrowhead.shape: cf-many
}

# Many-to-many (through junction)
students -> enrollments: {
  source-arrowhead.shape: cf-many
  target-arrowhead.shape: cf-many
}

# One-to-one
user -> profile: {
  source-arrowhead.shape: cf-one-required
  target-arrowhead.shape: cf-one-required
}

# Optional relationships
user -> avatar: {
  source-arrowhead.shape: cf-one-required
  target-arrowhead.shape: cf-one
}
```

## Container Styling

### Nested Containers (Bluestaq)

```d2
govcloud: AWS GovCloud {
  class: account-boundary

  vpc: Production VPC {
    class: vpc-container

    public-subnet: Public Subnet {
      class: service-group

      alb: ALB {
        icon: https://icons.terrastruct.com/aws%2FNetworking%20%26%20Content%20Delivery%2FElastic-Load-Balancing.svg
        class: internal-service
      }
    }

    private-subnet: Private Subnet {
      class: service-group

      app: App Server {
        icon: https://icons.terrastruct.com/aws%2FCompute%2FAmazon-EC2.svg
        class: internal-service
      }
    }
  }
}
```

### Double Border for Emphasis

```d2
critical-zone: "Critical Infrastructure" {
  style: {
    double-border: true
    stroke: ${danger}
    fill: ${surface}
    font-color: ${danger}
    bold: true
  }
}
```

## Layout Techniques

### Port Simulation for Clean Ingress/Egress

Create tiny inner nodes as traffic entry points on containers:

```d2
cloud: Cloud Environment {
  ingress: "" {
    shape: circle
    width: 8
    height: 8
    style.fill: ${secondary}
    style.stroke: ${primary}
  }
  ingress -> web-tier
}
user -> cloud.ingress: HTTPS
```

### Anchor Node for Layout Guidance

If ELK doesn't produce the expected left-to-right flow, add a hidden anchor node:

```d2
_anchor: "" {
  near: top-left
  style: {
    stroke: transparent
    fill: transparent
  }
}
```

### Horizontal Child Layout

Use `direction: right` on a container to arrange children side-by-side. Add `label.near: outside-top-center` to prevent the container title from being obscured:

```d2
topic: "Orders (3 partitions)" {
  direction: right
  label.near: outside-top-center
  p0: "P0" { shape: queue; width: 80; height: 70 }
  p1: "P1" { shape: queue; width: 80; height: 70 }
  p2: "P2" { shape: queue; width: 80; height: 70 }
}
```

### Rigid Dimensioning

Control container sizing when ELK stretches things too far:

```d2
web-tier: Web Tier {
  width: 350
  app-a: Frontend A
  app-b: Frontend B
}
```

## Glob Patterns for Bulk Styling

Apply styles to multiple elements at once:

```d2
# Style all nodes
*.style.border-radius: 8

# Style all connections
(* -> *)[*].style.stroke-width: 2

# Style all nodes in a container
my-container.*.style.fill: ${surface}

# Style specific connections from a node
(server -> *)[*].style.stroke: ${secondary}

# Style all descendants (recursive)
vpc.**.style.font-color: ${text}
```

## Variables and Imports

### Variables for Reuse

```d2
vars: {
  icon-base: https://icons.terrastruct.com
  aws-base: https://icons.terrastruct.com/aws%2F
}

ec2: EC2 Instance {
  icon: ${aws-base}Compute%2FAmazon-EC2.svg
}
```

Variable interpolation in icon URLs works with the `${var}` syntax.

### Imports

Split large diagrams across files:

```d2
# main.d2
...@classes.d2    # Import classes
...@palette.d2    # Import palette vars

# classes.d2
classes: {
  primary-node: {
    style.fill: ${primary}
  }
}
```

The `...@filename` syntax spreads the imported file's contents at that position.

## Markdown and LaTeX in Labels

### Markdown Labels

```d2
explanation: |md
  ## Architecture Overview

  This system handles **12M requests/day** with:
  - Auto-scaling compute
  - Multi-region failover
  - 99.99% SLA
|
```

### LaTeX Labels

```d2
formula: |latex
  \\frac{requests}{second} = \\frac{N \\cdot throughput}{latency}
|
```

### Code Blocks in Labels

```d2
config: |md
  ```yaml
  replicas: 3
  memory: 2Gi
  cpu: 500m
  ```
|
```

## Icon Position Control

The `near` keyword places labels and standalone nodes relative to other elements. Available positions:

```
top-left      top-center      top-right
center-left   center-center   center-right
bottom-left   bottom-center   bottom-right
```

Also available for `label.near`: `outside-top-left`, `outside-top-center`, `outside-top-right`, `outside-bottom-left`, `outside-bottom-center`, `outside-bottom-right`, `outside-left-center`, `outside-right-center`.

## Tooltip and Link

Add interactivity to SVG output:

```d2
server: App Server {
  tooltip: "Runs on EC2 t3.large\n4 vCPU, 8 GB RAM"
  link: https://console.aws.amazon.com/ec2
}
```

## Direction Control

```d2
direction: right    # left, right, up, down

# Override direction for specific containers
sub-flow: {
  direction: down
  a -> b -> c
}
```

## Icon Spacing and Positioning

D2 doesn't support internal padding/margin properties. Icons and text are rendered as a unified element within the node boundary.

### Recommended Sizes for Constrained Shapes

```d2
# Cloud service with icon (130 x 85 pixels -- matches aws-service class)
service: Service {
  shape: cloud
  icon: https://icons.terrastruct.com/aws%2FNetworking%20%26%20Content%20Delivery%2FAmazon-API-Gateway.svg
  width: 130
  height: 85
}

# Database with icon (160 x 130 pixels -- matches datastore class)
database: MySQL Database {
  shape: cylinder
  icon: https://icons.terrastruct.com/dev%2Fmysql.svg
  width: 160
  height: 130
  label.near: outside-bottom-center
}

# Message queue with icon (120 x 70 pixels -- matches queue class)
queue: Event Queue {
  shape: queue
  icon: https://icons.terrastruct.com/aws%2FApplication%20Integration%2FAmazon-Simple-Queue-Service-SQS.svg
  width: 120
  height: 70
}
```

### Spacing Techniques

- Add `\n\n` in labels for visual separation between icon and description text
- For polygons (hexagon, diamond): skip icons, use short text-only labels (1-2 words)
- Icon-only nodes with separate label nodes for precise placement
- Add `width` to containers to prevent child border crowding

## Shape-Icon Compatibility

### Safe for Icons (with dimension constraints)

| Shape | Constraints | Notes |
|-------|-------------|-------|
| **Rectangle** (default) | None needed | Built-in text padding, icons never overlap |
| **Cloud** | `width: 130, height: 85` | Prevents icon-text overlap |
| **Cylinder** | `width: 160, height: 130` + `label.near: outside-bottom-center` | Text below the cylinder |
| **Queue** | `width: 120, height: 70` | Prevents icon-text overlap |
| **Step** | Short label (1-2 words) | Works for pipeline stages |

### Avoid Icons (use text-only labels)

| Shape | Reason |
|-------|--------|
| **Circle** | Too small, icon dominates text |
| **Hexagon** | Non-rectangular, stretches and distorts |
| **Diamond** | Stretches to fit text, icon-text overlap |
| **Person** | Polygon distorts with long labels |
| **Oval** | Non-rectangular, icon overlap |
| **Package** | Too compact for icon + text |
| **Document** | Too compact for icon + text |
| **Class** | Reserved for class diagram columns |
| **SQL Table** | Columns take precedence, icons conflict |

### One Class Per Node

D2 only supports one `class` per node. To combine icon sizing with other styling, use `class` for dimensions and a `style` block for colors:

```d2
service: Service {
  class: internal-service
  icon: https://icons.terrastruct.com/dev%2Fdocker.svg
  style: { fill: ${primary}; font-color: ${text-light} }
}
```

## Icon Quick Reference

Base URL: `https://icons.terrastruct.com/`

### AWS

| Service | Path |
|---------|------|
| EC2 | `aws%2FCompute%2FAmazon-EC2.svg` |
| Lambda | `aws%2FCompute%2FAWS-Lambda.svg` |
| ECS | `aws%2FCompute%2FAmazon-Elastic-Container-Service.svg` |
| EKS | `aws%2FCompute%2FAmazon-Elastic-Kubernetes-Service.svg` |
| S3 | `aws%2FStorage%2FAmazon-Simple-Storage-Service-S3.svg` |
| RDS | `aws%2FDatabase%2FAmazon-RDS.svg` |
| DynamoDB | `aws%2FDatabase%2FAmazon-DynamoDB.svg` |
| ElastiCache | `aws%2FDatabase%2FAmazon-ElastiCache.svg` |
| SQS | `aws%2FApplication%20Integration%2FAmazon-Simple-Queue-Service-SQS.svg` |
| SNS | `aws%2FApplication%20Integration%2FAmazon-Simple-Notification-Service-SNS.svg` |
| CloudFront | `aws%2FNetworking%20%26%20Content%20Delivery%2FAmazon-CloudFront.svg` |
| API Gateway | `aws%2FNetworking%20%26%20Content%20Delivery%2FAmazon-API-Gateway.svg` |
| ELB | `aws%2FNetworking%20%26%20Content%20Delivery%2FElastic-Load-Balancing.svg` |
| VPC | `aws%2FNetworking%20%26%20Content%20Delivery%2FAmazon-VPC.svg` |
| Kafka (MSK) | `aws%2FAnalytics%2FAmazon-Managed-Streaming-for-Kafka.svg` |
| Kinesis | `aws%2FAnalytics%2FAmazon-Kinesis.svg` |
| OpenSearch | `aws%2FAnalytics%2FAmazon-OpenSearch-Service.svg` |
| IAM Identity Center | `aws%2FSecurity%2C%20Identity%2C%20%26%20Compliance%2FAWS-IAM-Identity-Center.svg` |
| AWS General | `aws%2F_General%2FAWS-General_light-bg.svg` (403 -- broken) |

### GCP

| Service | Path |
|---------|------|
| Compute Engine | `gcp%2FProducts%20and%20services%2FCompute%2FCompute%20Engine.svg` |
| Cloud Functions | `gcp%2FProducts%20and%20services%2FCompute%2FCloud%20Functions.svg` |
| Cloud Run | `gcp%2FProducts%20and%20services%2FCompute%2FCloud%20Run.svg` |
| App Engine | `gcp%2FProducts%20and%20services%2FCompute%2FApp%20Engine.svg` |
| Cloud SQL | `gcp%2FProducts%20and%20services%2FDatabases%2FCloud%20SQL.svg` |
| Cloud Storage | `gcp%2FProducts%20and%20services%2FStorage%2FCloud%20Storage.svg` |
| Pub/Sub | `gcp%2FProducts%20and%20services%2FData%20Analytics%2FCloud%20PubSub.svg` |
| BigQuery | `gcp%2FProducts%20and%20services%2FData%20Analytics%2FBigQuery.svg` |

### Azure

| Service | Path |
|---------|------|
| SQL Database | `azure%2FDatabases%20Service%20Color%2FSQL%20Databases.svg` |
| Cosmos DB | `azure%2FDatabases%20Service%20Color%2FAzure%20Cosmos%20DB.svg` |
| Functions | `azure%2FCompute%20Service%20Color%2FFunction%20Apps.svg` |
| Blob Storage | `azure%2FStorage%20Service%20Color%2FBlob%20Storage.svg` |
| Load Balancer | `azure%2FNetworking%20Service%20Color%2FLoad%20Balancers.svg` |
| Kubernetes | `azure%2FContainer%20Service%20Color%2FKubernetes%20Services.svg` |
| Service Bus | `azure%2FIntegration%20Service%20Color%2FAzure%20Service%20Bus.svg` |

### Dev Tools and Infra

| Tool | Path |
|------|------|
| Docker | `dev%2Fdocker.svg` |
| Kubernetes | `azure%2F_Companies%2FKubernetes.svg` |
| Redis | `dev%2Fredis.svg` |
| PostgreSQL | `dev%2Fpostgresql.svg` |
| MySQL | `dev%2Fmysql.svg` |
| MongoDB | `dev%2Fmongodb.svg` |
| Nginx | `dev%2Fnginx.svg` |
| GitHub | `dev%2Fgithub.svg` |
| Python | `dev%2Fpython.svg` |
| Go | `dev%2Fgo.svg` |
| Node.js | `dev%2Fnodejs.svg` |
| React | `dev%2Freact.svg` |
| Terraform | `dev%2Fterraform.svg` (may be unavailable -- 403) |
| AWS Shield (for SGs) | `aws%2FSecurity%2C%20Identity%2C%20%26%20Compliance%2FAWS-Shield.svg` |
| Network | `infra%2F014-network.svg` |
| General User | `general%2Fuser.svg` |
| General Group | `general%2Fgroup.svg` |
| General Desktop | `general%2Fdesktop.svg` |

## AWS Cloud Architecture Patterns

### 5-Level Nesting Pattern (Bluestaq)

AWS diagrams benefit from deep container nesting that mirrors actual infrastructure hierarchy:

```
Region -> VPC -> Subnet -> Security Group -> Resources
```

```d2
govcloud: AWS GovCloud (us-gov-west-1) {
  class: account-boundary

  vpc: Production VPC {
    class: vpc-container

    public-subnet: Public Subnet (10.0.1.0/24) {
      class: service-group
      alb: ALB { class: internal-service }
      nat: NAT Gateway { class: internal-service }
    }

    private-subnet: Private Subnet (10.0.2.0/24) {
      class: service-group
      ecs: ECS Cluster { class: internal-service; style.multiple: true }
    }

    data-subnet: Data Subnet (10.0.3.0/24) {
      class: service-group
      rds: RDS Primary { class: datastore }
      redis: ElastiCache { class: datastore }
    }
  }
}
```

### VPC Tier Visual Distinction

Use different container classes per tier:

- **Public tier**: `service-group` (default)
- **Private tier**: `service-group` (default)
- **Data tier**: `service-group` (default)
- **Security boundary**: `security-boundary` (orange dashed)

Use `multiple: true` on auto-scaled resources.

### Grid Layout for Resources Within Tiers

When a subnet has many resources, use grids to arrange them:

```d2
private-subnet: Private Subnet {
  class: service-group
  grid-rows: 2
  grid-columns: 3
  grid-gap: 10

  svc-1: Order Service { class: internal-service }
  svc-2: Payment Service { class: internal-service }
  svc-3: User Service { class: internal-service }
  svc-4: Notification Service { class: internal-service }
  svc-5: Search Service { class: internal-service }
  svc-6: Analytics Service { class: internal-service }
}
```

## Kubernetes Topology Patterns

### Cluster Hierarchy

Kubernetes diagrams follow a natural container hierarchy:

```
Cluster -> Namespace -> Deployment/StatefulSet -> Pod -> Container
```

### Replica Sets with `multiple: true`

Show replicated pods without drawing each individually:

```d2
deployment: API Deployment {
  class: service-group

  pods: Pods (3 replicas) {
    class: internal-service
    style.multiple: true
    container: api-server {
      icon: https://icons.terrastruct.com/dev%2Fgo.svg
    }
  }
}
```

### Service Mesh Connections

Use animated edges for service mesh (Istio/Linkerd) sidecar communication:

```d2
# Sidecar pattern
pod: Pod {
  class: service-group

  app: App Container { class: internal-service }
  sidecar: Envoy Proxy {
    style: {
      fill: ${surface}
      stroke: ${muted}
      font-color: ${text}
    }
  }
  app -> sidecar: localhost {
    class: async-connection
  }
}
```

### Init Sequence via Sequence Diagrams

Use D2's `shape: sequence_diagram` for pod initialization sequences:

```d2
shape: sequence_diagram

kubelet: Kubelet
init: Init Container
app: App Container
sidecar: Sidecar

kubelet -> init: Start init
init -> init: Run migrations
init -> kubelet: Exit 0
kubelet -> sidecar: Start sidecar
kubelet -> app: Start app
app -> sidecar: Health check
```

### Namespace Color Coding (Bluestaq)

Use the `service-group` class for all namespaces. Differentiate with subtle fill overrides:

- **Platform namespace**: `style.fill: ${primary}`
- **Application namespace**: `style.fill: ${surface}`
- **Monitoring namespace**: `style.fill: "#0E1829"`

### Ingress-to-Service-to-Deployment Pattern

```d2
cluster: K8s Cluster {
  class: account-boundary
  icon: https://icons.terrastruct.com/azure%2F_Companies%2FKubernetes.svg

  ingress: Ingress Controller {
    icon: https://icons.terrastruct.com/dev%2Fnginx.svg
    class: internal-service
  }

  ns-app: app namespace {
    class: service-group

    svc-api: api-svc (ClusterIP) { class: internal-service }
    deploy-api: API Deployment {
      class: internal-service
      style.multiple: true
    }
    svc-api -> deploy-api { class: sync-connection }
  }

  ingress -> ns-app.svc-api: /api/* { class: sync-connection }
}
```
