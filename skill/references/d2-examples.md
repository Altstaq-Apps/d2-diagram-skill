# D2 Diagram Examples -- Complete Renderable Bluestaq Diagrams

## Table of Contents

- [1. L1 System Landscape](#1-l1-system-landscape)
- [2. L2 Architecture Overview](#2-l2-architecture-overview)
- [3. L3 Deployment / Infrastructure](#3-l3-deployment--infrastructure)
- [4. L2 Data Flow](#4-l2-data-flow)
- [5. L2 Sequence Diagram](#5-l2-sequence-diagram)
- [6. L3 Network / Security Topology](#6-l3-network--security-topology)
- [7. L2 ER Diagram](#7-l2-er-diagram)
- [8. L2 Decision Flowchart](#8-l2-decision-flowchart)

---

## 1. L1 System Landscape

Executive-level overview of the Bluestaq product ecosystem. Full Bluestaq class library included.

```d2
# LEVEL: L1 -- System Landscape
# AUDIENCE: Executive / customer
# COMPLIANCE: none
# Shows: Bluestaq product ecosystem and primary external relationships

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

# --- Actors ---
gov_user: Government operator {
  class: actor
}

partner: Mission partner {
  class: actor
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
  # AWS General icon removed (403). Using text label only.
}

# --- Connections ---
gov_user -> bluestaq.udl: discovers & shares data { class: sync-connection }
partner -> bluestaq.gdm: accesses marketplace { class: sync-connection }
dod -> bluestaq.udl: ingests { class: data-flow }
bluestaq.udl -> bluestaq.gdm: published data { class: data-flow }
bluestaq.platform -> aws_gov: hosted on { class: monitoring-link }
```

```bash
d2 --sketch --theme 200 --layout elk landscape.d2 landscape.svg
```

---

## 2. L2 Architecture Overview

Default diagram type. One system + its integrations.

```d2
# LEVEL: L2 -- Architecture Overview
# AUDIENCE: Engineering leads, technical stakeholders
# COMPLIANCE: none
# Shows: UDL system architecture and integration points

direction: right

vars: {
  d2-config: { sketch: true; theme-id: 200; layout-engine: elk }
  primary: "#162646"; secondary: "#385FAF"; accent: "#9CADD7"
  surface: "#1C2F4A"; text: "#D6D6DA"; text-light: "#FFFFFF"
  muted: "#739BCF"; orange: "#F5871F"
}

classes: {
  # (full Bluestaq class library -- see style guide)
  sync-connection: { style: { stroke: ${secondary}; stroke-width: 2 } }
  async-connection: { style: { stroke: ${accent}; stroke-dash: 5; animated: true; stroke-width: 2 } }
  internal-service: { style: { fill: ${surface}; stroke: ${secondary}; stroke-width: 2; font-color: ${text}; border-radius: 6; shadow: true } }
  external-node: { style: { fill: ${surface}; stroke: ${orange}; stroke-width: 2; font-color: ${text}; border-radius: 6 } }
  microservice: { shape: hexagon; style: { fill: ${surface}; stroke: ${secondary}; stroke-width: 2; font-color: ${text} } }
  datastore: { shape: cylinder; width: 160; height: 130; label.near: outside-bottom-center; style: { fill: ${surface}; stroke: ${secondary}; font-color: ${text}; shadow: true } }
  queue: { shape: queue; width: 120; height: 70; style: { fill: ${surface}; stroke: ${muted}; font-color: ${text} } }
  aws-service: { shape: cloud; width: 130; height: 85; style: { fill: ${surface}; stroke: ${accent}; font-color: ${text} } }
  account-boundary: { style: { fill: ${primary}; stroke: ${secondary}; stroke-width: 2; font-color: ${text-light}; border-radius: 16; opacity: 0.95; bold: true } }
  service-group: { style: { fill: ${surface}; stroke: ${secondary}; stroke-dash: 3; font-color: ${text}; border-radius: 8; opacity: 0.85; bold: true } }
  data-flow: { style: { stroke: ${accent}; stroke-width: 2; animated: true } }
  monitoring-link: { style: { stroke: "#494C4D"; stroke-dash: 3; stroke-width: 1 } }
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

```bash
d2 --sketch --theme 200 --layout elk architecture.d2 architecture.svg
```

---

## 3. L3 Deployment / Infrastructure

AWS GovCloud deployment with VPCs, AZs, and data layer.

```d2
# LEVEL: L3 -- Deployment / Infrastructure
# AUDIENCE: DevOps, cloud architects
# COMPLIANCE: none
# Shows: UDL deployment on AWS GovCloud us-gov-west-1

direction: down

vars: {
  d2-config: { sketch: true; theme-id: 200; layout-engine: elk }
  primary: "#162646"; secondary: "#385FAF"; accent: "#9CADD7"
  surface: "#1C2F4A"; text: "#D6D6DA"; text-light: "#FFFFFF"
  muted: "#739BCF"; orange: "#F5871F"
}

classes: {
  # (full Bluestaq class library -- see style guide)
  sync-connection: { style: { stroke: ${secondary}; stroke-width: 2 } }
  async-connection: { style: { stroke: ${accent}; stroke-dash: 5; animated: true; stroke-width: 2 } }
  internal-service: { style: { fill: ${surface}; stroke: ${secondary}; stroke-width: 2; font-color: ${text}; border-radius: 6; shadow: true } }
  external-node: { style: { fill: ${surface}; stroke: ${orange}; stroke-width: 2; font-color: ${text}; border-radius: 6 } }
  datastore: { shape: cylinder; width: 160; height: 130; label.near: outside-bottom-center; style: { fill: ${surface}; stroke: ${secondary}; font-color: ${text}; shadow: true } }
  queue: { shape: queue; width: 120; height: 70; style: { fill: ${surface}; stroke: ${muted}; font-color: ${text} } }
  aws-service: { shape: cloud; width: 130; height: 85; style: { fill: ${surface}; stroke: ${accent}; font-color: ${text} } }
  account-boundary: { style: { fill: ${primary}; stroke: ${secondary}; stroke-width: 2; font-color: ${text-light}; border-radius: 16; opacity: 0.95; bold: true } }
  vpc-container: { style: { fill: "#0E1829"; stroke: ${muted}; stroke-dash: 4; font-color: ${muted}; border-radius: 12; opacity: 0.9; bold: true } }
  service-group: { style: { fill: ${surface}; stroke: ${secondary}; stroke-dash: 3; font-color: ${text}; border-radius: 8; opacity: 0.85; bold: true } }
  data-flow: { style: { stroke: ${accent}; stroke-width: 2; animated: true } }
  monitoring-link: { style: { stroke: "#494C4D"; stroke-dash: 3; stroke-width: 1 } }
  replication: { style: { stroke: ${muted}; stroke-dash: 4; stroke-width: 2; animated: true } }
  boundary-crossing: { style: { stroke: ${orange}; stroke-width: 2; stroke-dash: 2 } }

  # Diagram-specific classes
  az-container: {
    style: {
      fill: "#0E1829"; stroke: ${muted}; stroke-dash: 3
      font-color: ${muted}; border-radius: 8; bold: true
    }
  }
  subnet: {
    style: {
      fill: ${surface}; stroke: ${secondary}; stroke-dash: 2
      font-color: ${text}; border-radius: 4
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

```bash
d2 --sketch --theme 200 --layout elk deployment.d2 deployment.svg
```

---

## 4. L2 Data Flow

End-to-end data lifecycle: ingest, transform, store, discover, share.

```d2
# LEVEL: L2 -- Data Flow
# AUDIENCE: Engineering, data architects, customer technical staff
# COMPLIANCE: none
# Shows: UDL data lifecycle -- ingest, transform, store, discover, share

direction: right

vars: {
  d2-config: { sketch: true; theme-id: 200; layout-engine: elk }
  primary: "#162646"; secondary: "#385FAF"; accent: "#9CADD7"
  surface: "#1C2F4A"; text: "#D6D6DA"; text-light: "#FFFFFF"
  muted: "#739BCF"; orange: "#F5871F"
}

classes: {
  # (full Bluestaq class library -- see style guide)
  sync-connection: { style: { stroke: ${secondary}; stroke-width: 2 } }
  async-connection: { style: { stroke: ${accent}; stroke-dash: 5; animated: true; stroke-width: 2 } }
  external-node: { style: { fill: ${surface}; stroke: ${orange}; stroke-width: 2; font-color: ${text}; border-radius: 6 } }
  internal-service: { style: { fill: ${surface}; stroke: ${secondary}; stroke-width: 2; font-color: ${text}; border-radius: 6; shadow: true } }
  actor: { shape: person; width: 100; height: 80; style: { fill: ${surface}; stroke: ${muted}; font-color: ${text} } }
  datastore: { shape: cylinder; width: 160; height: 130; label.near: outside-bottom-center; style: { fill: ${surface}; stroke: ${secondary}; font-color: ${text}; shadow: true } }
  queue: { shape: queue; width: 120; height: 70; style: { fill: ${surface}; stroke: ${muted}; font-color: ${text} } }
  service-group: { style: { fill: ${surface}; stroke: ${secondary}; stroke-dash: 3; font-color: ${text}; border-radius: 8; opacity: 0.85; bold: true } }
  data-flow: { style: { stroke: ${accent}; stroke-width: 2; animated: true } }

  # Diagram-specific
  pipeline-step: {
    shape: step
    width: 140; height: 60
    style: {
      fill: ${surface}; stroke: ${secondary}
      font-color: ${text}; border-radius: 4
    }
  }
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

```bash
d2 --sketch --theme 200 --layout elk dataflow.d2 dataflow.svg
```

---

## 5. L2 Sequence Diagram

Temporal order of interactions for an authenticated data request.

```d2
# LEVEL: L2 -- Sequence Diagram
# AUDIENCE: Developers, technical reviewers
# COMPLIANCE: none
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
auth -> gateway: 200 OK -- claims
gateway -> core: forward request + claims

# --- Authorization + resolution ---
core -> meta: check ACL for resource
meta -> core: permitted -- object key
core -> store: GET s3://udl-raw/{key}
store -> core: 200 OK -- object bytes

# --- Response ---
core -> gateway: 200 OK -- payload
gateway -> client: 200 OK -- payload

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

```bash
d2 --sketch --theme 200 sequence.d2 sequence.svg
```

---

## 6. L3 Network / Security Topology

Trust zones, firewall rules, FedRAMP High boundary with security-boundary class.

```d2
# LEVEL: L3 -- Network / Security Topology
# AUDIENCE: Security engineers, customer ISSO, FedRAMP auditors
# COMPLIANCE: FedRAMP High boundary shown
# Shows: UDL network zones, trust boundaries, traffic inspection points

direction: down

vars: {
  d2-config: { sketch: true; theme-id: 200; layout-engine: elk }
  primary: "#162646"; secondary: "#385FAF"; accent: "#9CADD7"
  surface: "#1C2F4A"; text: "#D6D6DA"; text-light: "#FFFFFF"
  muted: "#739BCF"; orange: "#F5871F"
}

classes: {
  # (full Bluestaq class library -- see style guide)
  sync-connection: { style: { stroke: ${secondary}; stroke-width: 2 } }
  async-connection: { style: { stroke: ${accent}; stroke-dash: 5; animated: true; stroke-width: 2 } }
  internal-service: { style: { fill: ${surface}; stroke: ${secondary}; stroke-width: 2; font-color: ${text}; border-radius: 6; shadow: true } }
  external-node: { style: { fill: ${surface}; stroke: ${orange}; stroke-width: 2; font-color: ${text}; border-radius: 6 } }
  actor: { shape: person; width: 100; height: 80; style: { fill: ${surface}; stroke: ${muted}; font-color: ${text} } }
  datastore: { shape: cylinder; width: 160; height: 130; label.near: outside-bottom-center; style: { fill: ${surface}; stroke: ${secondary}; font-color: ${text}; shadow: true } }
  aws-service: { shape: cloud; width: 130; height: 85; style: { fill: ${surface}; stroke: ${accent}; font-color: ${text} } }
  service-group: { style: { fill: ${surface}; stroke: ${secondary}; stroke-dash: 3; font-color: ${text}; border-radius: 8; opacity: 0.85; bold: true } }
  security-boundary: { style: { fill: "transparent"; stroke: ${orange}; stroke-dash: 6; stroke-width: 2; font-color: ${orange}; border-radius: 4; bold: true } }
  data-flow: { style: { stroke: ${accent}; stroke-width: 2; animated: true } }
  monitoring-link: { style: { stroke: "#494C4D"; stroke-dash: 3; stroke-width: 1 } }
  boundary-crossing: { style: { stroke: ${orange}; stroke-width: 2; stroke-dash: 2 } }

  # Diagram-specific classes
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
      stroke-width: 2; font-color: ${text-light}
      border-radius: 12; bold: true
    }
  }
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

# --- FedRAMP boundary ---
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

# --- Connections: untrusted -> DMZ (boundary crossing) ---
untrusted.user_browser -> dmz.waf: HTTPS :443 { class: boundary-crossing }
untrusted.partner_sys -> dmz.vpn: IPSec { class: boundary-crossing }

# --- Connections: DMZ -> restricted ---
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

```bash
d2 --sketch --theme 200 --layout elk network-security.d2 network-security.svg
```

---

## 7. L2 ER Diagram

Database schema with sql_table shapes and ER cardinality notation. Adapted from generic example with Bluestaq palette and header.

```d2
# LEVEL: L2 -- ER Diagram
# AUDIENCE: Developers, database administrators
# COMPLIANCE: none
# Shows: Application database schema with relationships

direction: right

vars: {
  d2-config: {
    sketch: true
    theme-id: 200
    layout-engine: elk
  }
  primary: "#162646"
  secondary: "#385FAF"
  accent: "#9CADD7"
  surface: "#1C2F4A"
  text: "#D6D6DA"
  text-light: "#FFFFFF"
  muted: "#739BCF"
}

users: users {
  shape: sql_table
  id: int {constraint: primary_key}
  email: varchar(255) {constraint: unique}
  username: varchar(100) {constraint: unique}
  password_hash: varchar(255)
  avatar_url: text
  created_at: timestamp
  updated_at: timestamp
}

profiles: profiles {
  shape: sql_table
  id: int {constraint: primary_key}
  user_id: int {constraint: foreign_key}
  display_name: varchar(200)
  bio: text
  location: varchar(100)
  website: varchar(255)
  created_at: timestamp
}

posts: posts {
  shape: sql_table
  id: int {constraint: primary_key}
  author_id: int {constraint: foreign_key}
  title: varchar(300)
  slug: varchar(300) {constraint: unique}
  body: text
  status: enum(draft,published,archived)
  published_at: timestamp
  created_at: timestamp
  updated_at: timestamp
}

comments: comments {
  shape: sql_table
  id: int {constraint: primary_key}
  post_id: int {constraint: foreign_key}
  author_id: int {constraint: foreign_key}
  parent_id: int {constraint: foreign_key}
  body: text
  created_at: timestamp
}

tags: tags {
  shape: sql_table
  id: int {constraint: primary_key}
  name: varchar(100) {constraint: unique}
  slug: varchar(100) {constraint: unique}
}

post_tags: post_tags {
  shape: sql_table
  post_id: int {constraint: foreign_key}
  tag_id: int {constraint: foreign_key}
}

# Relationships with ER cardinality
users -> profiles: {
  source-arrowhead.shape: cf-one-required
  target-arrowhead.shape: cf-one
  style.stroke: ${secondary}
}

users -> posts: {
  source-arrowhead.shape: cf-one-required
  target-arrowhead.shape: cf-many
  style.stroke: ${secondary}
}

users -> comments: {
  source-arrowhead.shape: cf-one-required
  target-arrowhead.shape: cf-many
  style.stroke: ${secondary}
}

posts -> comments: {
  source-arrowhead.shape: cf-one-required
  target-arrowhead.shape: cf-many
  style.stroke: ${secondary}
}

# Self-loop: use short label to avoid overlapping the node
comments -> comments: replies {
  source-arrowhead.shape: cf-one
  target-arrowhead.shape: cf-many
  style.stroke: ${muted}
  style.stroke-dash: 3
}

posts -> post_tags: {
  source-arrowhead.shape: cf-one-required
  target-arrowhead.shape: cf-many
  style.stroke: ${secondary}
}

tags -> post_tags: {
  source-arrowhead.shape: cf-one-required
  target-arrowhead.shape: cf-many
  style.stroke: ${secondary}
}
```

```bash
d2 --sketch --theme 200 --layout elk er-diagram.d2 er-diagram.svg
```

---

## 8. L2 Decision Flowchart

Request processing flowchart with diamond decisions, callout annotations, and branching paths. Adapted with Bluestaq palette.

```d2
# LEVEL: L2 -- Decision Flowchart
# AUDIENCE: Developers, API designers
# COMPLIANCE: none
# Shows: API request processing decision tree

direction: down

vars: {
  d2-config: {
    sketch: true
    theme-id: 200
    layout-engine: elk
  }
  primary: "#162646"
  secondary: "#385FAF"
  accent: "#9CADD7"
  surface: "#1C2F4A"
  text: "#D6D6DA"
  text-light: "#FFFFFF"
  muted: "#739BCF"
  orange: "#F5871F"
  success: "#4CAF80"
  danger: "#E24B4A"
}

classes: {
  decision: {
    shape: diamond
    style: {
      fill: ${surface}
      stroke: ${orange}
      font-color: ${text}
      bold: true
    }
  }
  process: {
    style: {
      fill: ${surface}
      stroke: ${secondary}
      font-color: ${text}
      border-radius: 6
      shadow: true
    }
  }
  terminal: {
    shape: oval
    style: {
      fill: ${primary}
      stroke: ${secondary}
      font-color: ${text-light}
      bold: true
    }
  }
  success-end: {
    shape: oval
    style: {
      fill: "#1A3A2A"
      stroke: ${success}
      font-color: ${success}
      bold: true
    }
  }
  failure-end: {
    shape: oval
    style: {
      fill: "#3A1A1A"
      stroke: ${danger}
      font-color: ${danger}
      bold: true
    }
  }
  note: {
    shape: callout
    style: {
      fill: ${surface}
      stroke: ${muted}
      font-color: ${text}
      font-size: 13
    }
  }
  flow: {
    style: {
      stroke: ${secondary}
      stroke-width: 2
    }
  }
  yes-flow: {
    style: {
      stroke: ${success}
      stroke-width: 2
    }
  }
  no-flow: {
    style: {
      stroke: ${danger}
      stroke-width: 2
    }
  }
}

start: Incoming Request { class: terminal }

auth-check: Authenticated? { class: decision }

rate-limit: Rate Limited? { class: decision }

validate: Validate Input { class: process }

valid-check: Valid? { class: decision }

process-req: Process Request { class: process }

cache-check: In Cache? { class: decision }

cache-hit: Return Cached Response { class: process }

db-query: Query Database { class: process }

found-check: Found? { class: decision }

format: Format Response { class: process }

cache-store: Store in Cache { class: process }

success: 200 OK { class: success-end }
not-found: 404 Not Found { class: failure-end }
unauthorized: 401 Unauthorized { class: failure-end }
rate-limited: 429 Too Many Requests { class: failure-end }
bad-request: 400 Bad Request { class: failure-end }

# Auth note
auth-note: "Check JWT token\nor API key" { class: note }

# Cache note
cache-note: "TTL: 5 minutes\nLRU eviction" { class: note }

# Flow
start -> auth-check { class: flow }
auth-check -> rate-limit: Yes { class: yes-flow }
auth-check -> unauthorized: No { class: no-flow }

rate-limit -> validate: No { class: yes-flow }
rate-limit -> rate-limited: Yes { class: no-flow }

validate -> valid-check { class: flow }
valid-check -> process-req: Yes { class: yes-flow }
valid-check -> bad-request: No { class: no-flow }

process-req -> cache-check { class: flow }
cache-check -> cache-hit: Yes { class: yes-flow }
cache-check -> db-query: No { class: no-flow }

cache-hit -> success { class: yes-flow }

db-query -> found-check { class: flow }
found-check -> format: Yes { class: yes-flow }
found-check -> not-found: No { class: no-flow }

format -> cache-store { class: flow }
cache-store -> success { class: yes-flow }
```

```bash
d2 --sketch --theme 200 --layout elk flowchart.d2 flowchart.svg
```
