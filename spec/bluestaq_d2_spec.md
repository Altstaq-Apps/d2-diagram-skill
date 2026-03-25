# Bluestaq D2 Diagram Standards
## Agent Specification v1.0

> Extends the `d2-diagram` skill. Read that skill first. This spec adds the Bluestaq palette,
> diagram type taxonomy, abstraction hierarchy, security boundary notation, and 6 working templates.
> When generating any diagram for Bluestaq, this spec takes precedence over skill defaults.

---

## 1. Bluestaq Palette

Replace the skill's default palette vars block with this. Never use the skill's Ocean Depths,
Forest Canopy, or other default palettes for Bluestaq work.

```d2
vars: {
  d2-config: {
    sketch: true
    theme-id: 200
    layout-engine: elk
  }

  # Bluestaq Navy — primary brand palette
  primary:    "#162646"   # Navy — deep containers, primary nodes
  secondary:  "#385FAF"   # Blue 1 — internal service borders, connectors
  accent:     "#9CADD7"   # Blue 3 — active data flow connectors, labels
  surface:    "#1C2F4A"   # BG elevated — card/node fill on dark canvas
  text:       "#D6D6DA"   # Grey 2 — all body labels (AAA on dark bg)
  text-light: "#FFFFFF"   # White — labels on primary/secondary fills
  muted:      "#739BCF"   # Blue 2 — sublabels, metadata, group borders
  canvas:     "#152238"   # BG surface — diagram background

  # Orange — use sparingly (CTAs, external entry points, critical paths only)
  orange:     "#F5871F"

  # Semantic
  success:    "#4CAF80"
  warning:    "#F5871F"   # same as orange — intentional
  danger:     "#E24B4A"
  info:       "#9CADD7"
}
```

### Color Semantics for Diagrams

| Color | Hex | Use |
|---|---|---|
| Navy `${primary}` | `#162646` | Outermost containers, AWS account/VPC wrappers |
| Blue 1 `${secondary}` | `#385FAF` | Internal Bluestaq-owned service borders |
| Blue 3 `${accent}` | `#9CADD7` | Active data flow connectors, metadata labels |
| BG elevated `${surface}` | `#1C2F4A` | Node fills (dark mode) |
| Grey 2 `${text}` | `#D6D6DA` | All node labels |
| Orange `${orange}` | `#F5871F` | External/entry-point borders, critical path only |
| Grey 3 | `#494C4D` | Passive/monitoring connectors, dashed group borders |

---

## 2. Bluestaq Class Library

Extend the skill's core classes with these. Include both blocks in every diagram.

```d2
classes: {

  # --- Inherited from skill (include as-is) ---
  async-connection: {
    style: {
      stroke: ${accent}
      stroke-dash: 5
      animated: true
      stroke-width: 2
    }
  }
  sync-connection: {
    style: {
      stroke: ${secondary}
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

  # --- Bluestaq-specific node classes ---

  # Internal Bluestaq-owned service (most common node type)
  internal-service: {
    style: {
      fill: ${surface}
      stroke: ${secondary}
      stroke-width: 1.5
      font-color: ${text}
      border-radius: 6
      shadow: true
    }
  }

  # External system or user-facing entry point (use orange border)
  external-node: {
    style: {
      fill: ${surface}
      stroke: ${orange}
      stroke-width: 2
      font-color: ${text}
      border-radius: 6
    }
  }

  # Bluestaq microservice (hexagon shape)
  microservice: {
    shape: hexagon
    style: {
      fill: ${surface}
      stroke: ${secondary}
      stroke-width: 1.5
      font-color: ${text}
    }
  }

  # Data store
  datastore: {
    shape: cylinder
    width: 160
    height: 130
    label.near: outside-bottom-center
    style: {
      fill: ${surface}
      stroke: ${secondary}
      font-color: ${text}
      shadow: true
    }
  }

  # Message queue / event bus
  queue: {
    shape: queue
    width: 120
    height: 70
    style: {
      fill: ${surface}
      stroke: ${muted}
      font-color: ${text}
    }
  }

  # Human actor / user
  actor: {
    shape: person
    width: 100
    height: 80
    style: {
      fill: ${surface}
      stroke: ${muted}
      font-color: ${text}
    }
  }

  # AWS managed service
  aws-service: {
    shape: cloud
    width: 130
    height: 85
    style: {
      fill: ${surface}
      stroke: ${accent}
      font-color: ${text}
    }
  }

  # --- Bluestaq-specific container classes ---

  # Outermost boundary (AWS account, GovCloud boundary)
  account-boundary: {
    style: {
      fill: ${primary}
      stroke: ${secondary}
      stroke-width: 2
      font-color: ${text-light}
      border-radius: 16
      opacity: 0.95
      bold: true
    }
  }

  # VPC / network zone
  vpc-container: {
    style: {
      fill: "#0E1829"
      stroke: ${muted}
      stroke-dash: 4
      font-color: ${muted}
      border-radius: 12
      opacity: 0.9
      bold: true
    }
  }

  # Logical service group / cluster
  service-group: {
    style: {
      fill: ${surface}
      stroke: ${secondary}
      stroke-dash: 3
      font-color: ${text}
      border-radius: 8
      opacity: 0.85
      bold: true
    }
  }

  # Security / compliance boundary (FedRAMP, IL boundary)
  security-boundary: {
    style: {
      fill: "transparent"
      stroke: ${orange}
      stroke-dash: 6
      stroke-width: 2
      font-color: ${orange}
      border-radius: 4
      bold: true
    }
  }

  # --- Bluestaq-specific connector classes ---

  # Active data flow (UDL ingest/share)
  data-flow: {
    style: {
      stroke: ${accent}
      stroke-width: 2
      animated: true
    }
  }

  # Passive / monitoring / observability
  monitoring-link: {
    style: {
      stroke: "#494C4D"
      stroke-dash: 3
      stroke-width: 1
    }
  }

  # Replication / sync between data stores
  replication: {
    style: {
      stroke: ${muted}
      stroke-dash: 4
      stroke-width: 1.5
      animated: true
    }
  }

  # Trust boundary crossing (highlight when crossing security zones)
  boundary-crossing: {
    style: {
      stroke: ${orange}
      stroke-width: 2
      stroke-dash: 2
    }
  }
}
```

---

## 3. Abstraction Hierarchy (Bluestaq C4 Adaptation)

Use these four levels. Every diagram must declare its level in a comment at the top.
An agent producing a diagram without being told the level should default to L2.

| Level | Bluestaq Name | Audience | Scope | Connector Labels |
|---|---|---|---|---|
| **L1** | **Landscape** | Exec, customer | All Bluestaq products + major external dependencies | Short noun phrases |
| **L2** | **Architecture** | Engineering leads, customers | One product/system + its integrations | Verb phrases ("ingests", "queries") |
| **L3** | **Deployment** | DevOps, cloud architects | Infrastructure: VPCs, AZs, GovCloud, IAM | Protocol + port |
| **L4** | **Component** | Developers | Internals of one container: classes, APIs, modules | Method/event names |

### Level Selection Rules

- Default to **L2** when scope is ambiguous.
- Use **L1** only for executive briefings, capability overviews, or customer-facing one-pagers.
- Use **L3** when AWS infrastructure, GovCloud boundaries, VPCs, or deployment topology is the subject.
- Use **L4** rarely — only for specific technical deep-dives. Most of the value is at L1/L2.
- Never combine levels in a single diagram. If a diagram needs both L2 and L3 detail, produce two diagrams.

### Drill-Down Rule

Complex systems get a diagram per level, not everything on one canvas. Produce L1 first,
then offer to drill into any system as L2. Repeat for L3/L4. Each diagram links conceptually
to the next via matching node names.

---

## 4. Diagram Type Templates

### Render Command (all types)

```bash
d2 --sketch --theme 200 --layout elk diagram.d2 diagram.svg
```

---

### Type 1 — System Landscape (L1)

**When to use**: Executive audience, capability overview, customer-facing intro.
**What goes in**: Major product families, key external dependencies, government customers.
**What stays out**: Internal services, infrastructure, protocols.

```d2
# LEVEL: L1 — System Landscape
# AUDIENCE: Executive / customer
# Shows: Bluestaq product ecosystem and primary external relationships

direction: right

vars: {
  d2-config: { sketch: true; theme-id: 200; layout-engine: elk }
  primary: "#162646"; secondary: "#385FAF"; accent: "#9CADD7"
  surface: "#1C2F4A"; text: "#D6D6DA"; text-light: "#FFFFFF"
  muted: "#739BCF"; orange: "#F5871F"
}

classes: {
  # (include full Bluestaq class library here)
}

# --- Actors ---
gov_user: Government operator {
  class: actor
  icon: https://icons.terrastruct.com/general%2Fuser.svg
}

partner: Mission partner {
  class: actor
  icon: https://icons.terrastruct.com/general%2Fgroup.svg
}

# --- Bluestaq products ---
bluestaq: Bluestaq {
  class: account-boundary

  udl: Unified Data Library {
    class: service-group
    style.fill: "#1C2F4A"
  }

  gdm: Global Data Marketplace {
    class: service-group
    style.fill: "#1C2F4A"
  }

  platform: Platform Services {
    class: service-group
    style.fill: "#1C2F4A"
  }
}

# --- External systems ---
dod: DoD Data Sources {
  class: external-node
  shape: cloud
  style.stroke: ${orange}
}

aws_gov: AWS GovCloud {
  class: aws-service
  icon: https://icons.terrastruct.com/aws%2F_General%2FAWS-General_light-bg.svg
}

# --- Connections ---
gov_user -> bluestaq.udl: discovers & shares data { class: sync-connection }
partner -> bluestaq.gdm: accesses marketplace { class: sync-connection }
dod -> bluestaq.udl: ingests { class: data-flow }
bluestaq.udl -> bluestaq.gdm: published data { class: data-flow }
bluestaq.platform -> aws_gov: hosted on { class: monitoring-link }
```

---

### Type 2 — Architecture Overview (L2)

**When to use**: Default diagram type. System + integrations, team-level communication.
**What goes in**: Named services, APIs, data stores, external integrations, primary data flows.
**What stays out**: Infrastructure details, code-level internals.

```d2
# LEVEL: L2 — Architecture Overview
# AUDIENCE: Engineering leads, technical stakeholders
# Shows: UDL system architecture and integration points

direction: right

vars: {
  d2-config: { sketch: true; theme-id: 200; layout-engine: elk }
  primary: "#162646"; secondary: "#385FAF"; accent: "#9CADD7"
  surface: "#1C2F4A"; text: "#D6D6DA"; text-light: "#FFFFFF"
  muted: "#739BCF"; orange: "#F5871F"
}

classes: {
  # (include full Bluestaq class library here)
}

# --- External actors ---
client: Client application {
  class: external-node
  icon: https://icons.terrastruct.com/general%2Fdesktop.svg
}

data_source: External data source {
  class: external-node
  shape: cloud
}

# --- UDL system ---
udl: Unified Data Library {
  class: account-boundary

  api: API gateway {
    class: internal-service
    icon: https://icons.terrastruct.com/aws%2FNetworking%20&%20Content%20Delivery%2FAmazon-API-Gateway.svg
  }

  auth: Auth service {
    class: microservice
    icon: https://icons.terrastruct.com/aws%2FSecurity%2C%20Identity%2C%20&%20Compliance%2FAWS-IAM-Identity-Center.svg
  }

  ingest: Ingest pipeline {
    class: internal-service
    icon: https://icons.terrastruct.com/aws%2FAnalytics%2FAmazon-Kinesis.svg
    style.multiple: true
  }

  core: UDL core API {
    class: microservice
  }

  discovery: Discovery service {
    class: microservice
  }

  object_store: Object store {
    class: datastore
    icon: https://icons.terrastruct.com/aws%2FStorage%2FAmazon-Simple-Storage-Service-S3.svg
  }

  metadata: Metadata store {
    class: datastore
    icon: https://icons.terrastruct.com/dev%2Fpostgresql.svg
  }

  events: Event bus {
    class: queue
    icon: https://icons.terrastruct.com/aws%2FApplication%20Integration%2FAmazon-Simple-Queue-Service-SQS.svg
  }

  obs: Observability {
    class: aws-service
    style.stroke-dash: 3
  }
}

# --- Connections: ingress ---
client -> udl.api: HTTPS { class: sync-connection }
data_source -> udl.ingest: raw data { class: data-flow }

# --- Connections: internal ---
udl.api -> udl.auth: validates { class: sync-connection }
udl.api -> udl.core: routes { class: sync-connection }
udl.ingest -> udl.object_store: stores { class: data-flow }
udl.ingest -> udl.events: publishes { class: async-connection }
udl.core -> udl.object_store: reads { class: sync-connection }
udl.core -> udl.metadata: queries { class: sync-connection }
udl.discovery -> udl.metadata: indexes { class: sync-connection }
udl.events -> udl.discovery: triggers reindex { class: async-connection }

# --- Connections: observability (passive) ---
udl.core -> udl.obs: metrics { class: monitoring-link }
udl.ingest -> udl.obs: metrics { class: monitoring-link }
```

---

### Type 3 — Deployment / Infrastructure (L3)

**When to use**: AWS infrastructure, GovCloud, VPC topology, AZ layout, IAM boundaries.
**What goes in**: VPCs, subnets, AZs, managed services with region/AZ labels, security groups.
**What stays out**: Application-level logic, code internals.

```d2
# LEVEL: L3 — Deployment / Infrastructure
# AUDIENCE: DevOps, cloud architects
# Shows: UDL deployment on AWS GovCloud us-gov-west-1

direction: down

vars: {
  d2-config: { sketch: true; theme-id: 200; layout-engine: elk }
  primary: "#162646"; secondary: "#385FAF"; accent: "#9CADD7"
  surface: "#1C2F4A"; text: "#D6D6DA"; text-light: "#FFFFFF"
  muted: "#739BCF"; orange: "#F5871F"
}

classes: {
  az-container: {
    style: {
      fill: "#0E1829"
      stroke: ${muted}
      stroke-dash: 3
      font-color: ${muted}
      border-radius: 8
      bold: true
    }
  }
  subnet: {
    style: {
      fill: ${surface}
      stroke: ${secondary}
      stroke-dash: 2
      font-color: ${text}
      border-radius: 4
    }
  }
  ec2: {
    width: 110; height: 75
    icon: https://icons.terrastruct.com/aws%2FCompute%2FAmazon-EC2.svg
    style: {
      fill: ${surface}; stroke: ${secondary}
      font-color: ${text}; border-radius: 6
    }
  }
  # (include full Bluestaq class library here)
}

# --- GovCloud account boundary ---
govcloud: AWS GovCloud (us-gov-west-1) {
  class: account-boundary

  vpc: VPC 10.0.0.0/16 {
    class: vpc-container

    igw: Internet Gateway { class: aws-service }

    # Availability Zone A
    az_a: AZ us-gov-west-1a {
      class: az-container
      direction: down

      pub_a: Public subnet 10.0.1.0/24 {
        class: subnet
        alb_a: ALB node { class: ec2 }
      }

      priv_a: Private subnet 10.0.2.0/24 {
        class: subnet
        api_a: API pod { class: ec2; style.multiple: true }
        ingest_a: Ingest pod { class: ec2; style.multiple: true }
      }
    }

    # Availability Zone B
    az_b: AZ us-gov-west-1b {
      class: az-container
      direction: down

      pub_b: Public subnet 10.0.3.0/24 {
        class: subnet
        alb_b: ALB node { class: ec2 }
      }

      priv_b: Private subnet 10.0.4.0/24 {
        class: subnet
        api_b: API pod { class: ec2; style.multiple: true }
        ingest_b: Ingest pod { class: ec2; style.multiple: true }
      }
    }

    # Data layer (multi-AZ managed services)
    data: Data layer {
      class: service-group

      rds: RDS PostgreSQL (Multi-AZ) {
        class: datastore
        icon: https://icons.terrastruct.com/aws%2FDatabase%2FAmazon-RDS.svg
      }

      s3: S3 GovCloud {
        class: datastore
        icon: https://icons.terrastruct.com/aws%2FStorage%2FAmazon-Simple-Storage-Service-S3.svg
      }

      sqs: SQS queue {
        class: queue
        icon: https://icons.terrastruct.com/aws%2FApplication%20Integration%2FAmazon-Simple-Queue-Service-SQS.svg
      }
    }
  }

  # Shared services
  cloudwatch: CloudWatch { class: aws-service }
  iam: IAM { class: aws-service }
  waf: WAF { class: aws-service }
}

# --- External ---
internet: Internet { class: external-node; shape: cloud }

# --- Connections ---
internet -> govcloud.vpc.igw: HTTPS :443 { class: boundary-crossing }
govcloud.vpc.igw -> govcloud.vpc.az_a.pub_a.alb_a { class: sync-connection }
govcloud.vpc.igw -> govcloud.vpc.az_b.pub_b.alb_b { class: sync-connection }
govcloud.vpc.az_a.pub_a.alb_a -> govcloud.vpc.az_a.priv_a.api_a { class: sync-connection }
govcloud.vpc.az_b.pub_b.alb_b -> govcloud.vpc.az_b.priv_b.api_b { class: sync-connection }
govcloud.vpc.az_a.priv_a.api_a -> govcloud.vpc.data.rds { class: sync-connection }
govcloud.vpc.az_a.priv_a.ingest_a -> govcloud.vpc.data.s3: :443 { class: data-flow }
govcloud.vpc.az_a.priv_a.ingest_a -> govcloud.vpc.data.sqs { class: async-connection }
govcloud.vpc.data.rds -> govcloud.vpc.az_b.priv_b.api_b: replicas { class: replication }

# Observability (passive)
govcloud.vpc -> govcloud.cloudwatch: metrics/logs { class: monitoring-link }
govcloud.iam -> govcloud.vpc: enforces { class: monitoring-link }
govcloud.waf -> govcloud.vpc.igw: inspects { class: monitoring-link }
```

---

### Type 4 — Data Flow (UDL Ingest → Store → Share)

**When to use**: Showing how data moves through the UDL pipeline end-to-end.
**What goes in**: Data sources, transformation steps, storage, consumers. Emphasize
animated flows. Use `step` shape for pipeline stages.
**What stays out**: Infrastructure details, IAM, monitoring unless directly relevant.

```d2
# LEVEL: L2 — Data Flow
# AUDIENCE: Engineering, data architects, customer technical staff
# Shows: UDL data lifecycle — ingest, transform, store, discover, share

direction: right

vars: {
  d2-config: { sketch: true; theme-id: 200; layout-engine: elk }
  primary: "#162646"; secondary: "#385FAF"; accent: "#9CADD7"
  surface: "#1C2F4A"; text: "#D6D6DA"; text-light: "#FFFFFF"
  muted: "#739BCF"; orange: "#F5871F"
}

classes: {
  pipeline-step: {
    shape: step
    width: 140; height: 60
    style: {
      fill: ${surface}; stroke: ${secondary}
      font-color: ${text}; border-radius: 4
    }
  }
  # (include full Bluestaq class library here)
}

# --- Sources ---
sources: Data sources {
  class: service-group

  sensor: Sensor / satellite { class: external-node }
  agency: Agency system { class: external-node }
  partner_feed: Partner feed { class: external-node }
}

# --- Ingest ---
ingest_zone: Ingest {
  class: service-group

  adapter: Format adapter { class: pipeline-step }
  validator: Schema validator { class: pipeline-step }
  classifier: Classifier { class: pipeline-step }
  event_in: Ingest event {
    class: queue
    icon: https://icons.terrastruct.com/aws%2FApplication%20Integration%2FAmazon-Simple-Queue-Service-SQS.svg
  }
}

# --- Store ---
store_zone: Store {
  class: service-group

  raw: Raw object store {
    class: datastore
    icon: https://icons.terrastruct.com/aws%2FStorage%2FAmazon-Simple-Storage-Service-S3.svg
  }
  meta: Metadata catalog {
    class: datastore
    icon: https://icons.terrastruct.com/dev%2Fpostgresql.svg
  }
  index: Search index {
    class: datastore
    icon: https://icons.terrastruct.com/aws%2FAnalytics%2FAmazon-OpenSearch-Service.svg
  }
}

# --- Discover ---
discover_zone: Discover {
  class: service-group
  discovery_api: Discovery API { class: pipeline-step }
}

# --- Share ---
share_zone: Share {
  class: service-group
  share_api: Data share API { class: pipeline-step }
  notify: Notification service {
    class: queue
    style.stroke: ${orange}
  }
}

# --- Consumers ---
consumers: Consumers {
  class: service-group
  gov_consumer: Gov operator { class: actor }
  mission_app: Mission app { class: external-node }
  gdm_feed: GDM feed { class: internal-service }
}

# --- Connections: ingest flow ---
sources.sensor -> ingest_zone.adapter: raw data { class: data-flow }
sources.agency -> ingest_zone.adapter: raw data { class: data-flow }
sources.partner_feed -> ingest_zone.adapter: raw data { class: data-flow }
ingest_zone.adapter -> ingest_zone.validator { class: data-flow }
ingest_zone.validator -> ingest_zone.classifier { class: data-flow }
ingest_zone.classifier -> ingest_zone.event_in: classified { class: async-connection }

# --- Connections: store flow ---
ingest_zone.event_in -> store_zone.raw: stores object { class: data-flow }
ingest_zone.event_in -> store_zone.meta: writes metadata { class: data-flow }
store_zone.meta -> store_zone.index: indexes { class: sync-connection }

# --- Connections: discover + share ---
store_zone.index -> discover_zone.discovery_api: powers search { class: sync-connection }
discover_zone.discovery_api -> share_zone.share_api: resolves data location { class: sync-connection }
store_zone.raw -> share_zone.share_api: retrieves object { class: data-flow }
share_zone.share_api -> share_zone.notify: triggers { class: async-connection }

# --- Connections: consumers ---
share_zone.share_api -> consumers.gov_consumer { class: sync-connection }
share_zone.share_api -> consumers.mission_app { class: sync-connection }
share_zone.notify -> consumers.gdm_feed: event { class: async-connection }
```

---

### Type 5 — Sequence / Request-Response

**When to use**: Showing the temporal order of interactions between services for a specific
operation (auth flow, data request, ingest handshake). D2 has native sequence diagram support.
**What goes in**: Named actors, ordered messages, return paths, async vs sync distinction.
**What stays out**: Infrastructure, data stores (unless directly involved in the sequence).

```d2
# LEVEL: L2 — Sequence Diagram
# AUDIENCE: Developers, technical reviewers
# Shows: UDL authenticated data request flow

vars: {
  d2-config: {
    sketch: true
    theme-id: 200
    # Note: sequence diagrams use dagre internally regardless of layout-engine setting
  }
  secondary: "#385FAF"; accent: "#9CADD7"; orange: "#F5871F"
  surface: "#1C2F4A"; text: "#D6D6DA"; muted: "#739BCF"
}

shape: sequence_diagram

# --- Participants (declare in order of appearance left to right) ---
client: Client app
gateway: API gateway
auth: Auth service
core: UDL core API
meta: Metadata store
store: Object store

# --- Authentication ---
client -> gateway: GET /data/{id} \nAuthorization: Bearer <token>
gateway -> auth: validate token
auth -> gateway: 200 OK — claims
gateway -> core: forward request + claims

# --- Authorization + resolution ---
core -> meta: check ACL for resource
meta -> core: permitted — object key
core -> store: GET s3://udl-raw/{key}
store -> core: 200 OK — object bytes

# --- Response ---
core -> gateway: 200 OK — payload
gateway -> client: 200 OK — payload

# --- Error path ---
client -> gateway: GET /data/{bad-id}
gateway -> auth: validate token
auth -> gateway: 401 Unauthorized {
  style.stroke: "#E24B4A"
}
gateway -> client: 401 Unauthorized {
  style.stroke: "#E24B4A"
}
```

---

### Type 6 — Network / Security Topology

**When to use**: Trust zones, firewall rules, network segmentation, security boundaries.
Optionally annotate compliance scope (FedRAMP, IL4/IL5, ITAR) using the `security-boundary` class.
**What goes in**: Trust zones as containers, firewalls/WAFs as nodes, traffic flows with
protocol+port labels, trust boundary crossings in orange.
**What stays out**: Application-level logic, code.

**Security boundary annotation** is optional. Include it only on customer-facing deliverables
that require it. When included, add a `# COMPLIANCE:` comment at the top naming the relevant
boundary (FedRAMP High, IL4, IL5, ITAR). Never include actual classification markings in
diagram files — use a separate cover document for that.

```d2
# LEVEL: L3 — Network / Security Topology
# AUDIENCE: Security engineers, customer ISSO, FedRAMP auditors
# COMPLIANCE: FedRAMP High boundary shown (optional annotation)
# Shows: UDL network zones, trust boundaries, traffic inspection points

direction: down

vars: {
  d2-config: { sketch: true; theme-id: 200; layout-engine: elk }
  primary: "#162646"; secondary: "#385FAF"; accent: "#9CADD7"
  surface: "#1C2F4A"; text: "#D6D6DA"; text-light: "#FFFFFF"
  muted: "#739BCF"; orange: "#F5871F"
}

classes: {
  firewall: {
    shape: hexagon
    width: 100; height: 80
    style: {
      fill: ${surface}; stroke: ${orange}
      stroke-width: 2; font-color: ${text}
    }
  }
  trust-zone: {
    style: {
      fill: ${primary}; stroke: ${secondary}
      stroke-width: 1.5; font-color: ${text-light}
      border-radius: 12; bold: true
    }
  }
  # (include full Bluestaq class library here)
}

# --- Untrusted zone ---
untrusted: Untrusted (Internet) {
  class: trust-zone
  style.fill: "#2D1515"
  style.stroke: ${orange}

  user_browser: End user { class: actor }
  partner_sys: Partner system { class: external-node }
}

# --- DMZ ---
dmz: DMZ {
  class: trust-zone
  style.fill: "#1A2020"
  style.stroke: ${muted}

  waf: WAF { class: firewall }
  alb: ALB / Load balancer { class: internal-service }
  vpn: VPN endpoint { class: firewall }
}

# --- FedRAMP boundary (optional — remove if not needed) ---
fedramp_boundary: FedRAMP High boundary {
  class: security-boundary

  # --- Restricted zone ---
  restricted: Restricted {
    class: trust-zone

    api_fw: API firewall { class: firewall }

    app_tier: Application tier {
      class: service-group
      api: UDL API pods { class: internal-service; style.multiple: true }
      ingest: Ingest pods { class: internal-service; style.multiple: true }
    }
  }

  # --- Data zone ---
  data_zone: Data zone {
    class: trust-zone
    style.fill: "#0E1829"

    db_fw: DB firewall { class: firewall }
    rds: RDS (Multi-AZ) { class: datastore }
    s3: S3 GovCloud { class: datastore }
  }
}

# --- Out-of-band management ---
mgmt: Management plane {
  class: trust-zone
  style.stroke-dash: 4

  cloudwatch: CloudWatch { class: aws-service }
  ssm: SSM / Session Manager { class: aws-service }
  iam: IAM { class: aws-service }
}

# --- Connections: untrusted → DMZ (boundary crossing) ---
untrusted.user_browser -> dmz.waf: HTTPS :443 { class: boundary-crossing }
untrusted.partner_sys -> dmz.vpn: IPSec { class: boundary-crossing }

# --- Connections: DMZ → restricted ---
dmz.waf -> dmz.alb: filtered { class: sync-connection }
dmz.alb -> fedramp_boundary.restricted.api_fw: HTTPS :443 { class: boundary-crossing }
dmz.vpn -> fedramp_boundary.restricted.api_fw: routed { class: boundary-crossing }

# --- Connections: internal ---
fedramp_boundary.restricted.api_fw -> fedramp_boundary.restricted.app_tier.api { class: sync-connection }
fedramp_boundary.restricted.app_tier.api -> fedramp_boundary.data_zone.db_fw { class: sync-connection }
fedramp_boundary.data_zone.db_fw -> fedramp_boundary.data_zone.rds { class: sync-connection }
fedramp_boundary.restricted.app_tier.ingest -> fedramp_boundary.data_zone.s3: :443 { class: data-flow }

# --- Connections: monitoring (passive, always dashed) ---
fedramp_boundary -> mgmt.cloudwatch: metrics/logs { class: monitoring-link }
mgmt.ssm -> fedramp_boundary.restricted.app_tier: session { class: monitoring-link }
mgmt.iam -> fedramp_boundary: enforces policies { class: monitoring-link }
```

---

## 5. Connector Reference

| Class | Stroke | Dash | Animated | Use |
|---|---|---|---|---|
| `sync-connection` | Blue 1 | solid | no | Request/response, REST, gRPC |
| `async-connection` | Blue 3 | dashed | yes | Events, pub/sub, SQS, SNS |
| `data-flow` | Blue 3 | solid | yes | Active data movement (UDL ingest/share) |
| `monitoring-link` | Grey 3 | dashed | no | Metrics, logs, CloudWatch, passive links |
| `replication` | Blue 2 | dashed | yes | DB replication, S3 sync |
| `critical-path` | Orange | solid | no | Critical path, errors, alerts — use sparingly |
| `boundary-crossing` | Orange | dash-2 | no | Traffic crossing a trust or security boundary |

**Label rules:**
- L1: Short noun phrases only ("raw data", "ingests")
- L2: Verb phrases ("stores object", "validates token", "publishes event")
- L3: Protocol + port ("HTTPS :443", "IPSec", "SQL :5432")
- L4: Method or event name ("POST /ingest", "DataReady event")
- Sequence diagrams: Full request description on outgoing, status code on return

---

## 6. Security Boundary Notation

Include only when the diagram is a customer-facing deliverable requiring compliance context.

### Rules

1. Use the `security-boundary` class (orange dashed border) as a wrapping container.
2. Name the container with the compliance scope: `FedRAMP High boundary`, `IL4 boundary`, `ITAR boundary`.
3. Add `# COMPLIANCE: <scope>` comment at the top of the file.
4. Never write classification markings (SECRET, CUI, etc.) in diagram node labels.
5. For IL level boundaries, use separate containers — never nest IL5 inside IL4 in the same diagram.
6. Trust zone crossings that cross the boundary must use `boundary-crossing` class (orange).

### IL Boundary Color Convention

| Scope | Container border | Label |
|---|---|---|
| Unclassified / FedRAMP | Orange dashed (`${orange}`) | "FedRAMP High boundary" |
| IL4 | Orange dashed | "IL4 boundary" |
| IL5 | Orange dashed + stroke-width: 3 | "IL5 boundary" |
| ITAR | Orange dashed + stroke-dash: 2 | "ITAR boundary" |

---

## 7. Required Diagram Elements

Every Bluestaq diagram must include:

1. **Header comment block** — level, audience, compliance scope (if applicable), what it shows
2. **vars block** — full Bluestaq palette (never default palette)
3. **classes block** — full Bluestaq class library
4. **Legend** (L3 and network diagrams only) — callout node listing connector and border meanings
5. **Render command** — after the code block

```d2
# LEVEL: <L1 | L2 | L3 | L4>
# AUDIENCE: <who reads this>
# COMPLIANCE: <FedRAMP High | IL4 | IL5 | ITAR | none>
# Shows: <one sentence>
```

---

## 8. Anti-Patterns

- **Never combine levels** — L2 services and L3 VPC subnets on the same canvas. Split them.
- **Never use orange as a fill** — orange is stroke/border only. `style.fill: ${orange}` is always wrong.
- **Never use default skill palettes** (Ocean Depths, etc.) — always use the Bluestaq Navy palette.
- **Never mix sync and async on the same edge** — pick one semantic per connection.
- **Never put classification markings in node labels** — use a separate cover document.
- **Never produce a single mega-diagram** — prefer drill-down hierarchy across multiple diagrams.
- **Never skip the header comment block** — level and audience determine everything about the diagram.
- **Avoid bidirectional arrows** — use two separate edges with labels if both directions carry different semantics. Reserve `<->` for genuine bidirectional equivalence (DB replication).
- **Avoid unlabeled boundary crossings** — every edge crossing a trust zone or security boundary must have a protocol label.
