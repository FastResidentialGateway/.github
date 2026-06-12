# Fast Residential Gateway

This is an SDN-enabled and open source Residential Gateway system, designed to work together with the [Fast Residential Gateway Node](https://github.com/FastResidentialGateway/fastrg-node) dataplane and [Fast Residential Gateway Controller](https://github.com/FastResidentialGateway/fastrg-controller) control plane deployed at the Central Office. Its purpose is to enable more efficient and centralized management of residential broadband networks ranging from 1 Gbps up to 25 Gbps, while achieving zero-touch deployment of new broadband subscribers.

## Features / Key Capabilities

- SDN-based architecture – Provides programmability and centralized control for residential broadband networks via REST API and gRPC.

- Seamless dataplane integration – Works in tandem with the Fast Residential Gateway Node deployed in the Central Office.

- High-speed broadband support – Scales from 1 Gbps to 25 Gbps for next-generation residential access network.
    - Multiple subscriber in one system
    - Support PPPoE Client
    - Support DHCP server for per-subscriber LAN users
    - Support VLAN tagging for subscriber traffic
    - Support SNAT and port forwarding for subscriber traffic
    - DNS proxy

- Zero-touch provisioning – Automates subscriber onboarding with no manual intervention required.

- Centralized management – Simplifies operations by consolidating control into a single controller plane.

- CAPEX and OPEX reduction – Service provider can only deploy a small cheap ONT device with bridge only functionality in subscriber's residence.

- Network security reduction – Centralized control reduces the attack surface and simplifies security management.

- Flexible configuration - Support dynamic subscriber and HSI(High Speed Internet) configuration changes via API without service disruption.

## Service Architecture

```
FastRG Components Overview

┌──────┐                                  CONFIG STORAGE           DATA PLANE
│FastRG│   ┌────────────┐if primary failed┌────────────┐           ┌────────────────────────┐
│ User │──▶│ FastRG CLI │ ─ ─ ─ ─ ─ ─ ─ ─▶│ etcd :2379 │           │ Backbone / Core Network│
└─┬────┘   └───────┬─┬──┘                 └─▲───────┬──┘           │ Internet uplink        │
  │                │ └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─│─ ─ ─ ─│─ ─ ─ ─ ┐     └────────────▲───────────┘
  │                │     if etcd unavailable│       │        │                  │ routed IP
  │                │                        │       │watch   │     ┌────────────▼───────────┐
  ▼                │                        │       │config  │     │ BNG / BRAS             │
┌─────────────────┐│                        │       │        │     │ PPPoE server           │
│FastRG Controller││                        │       │        │     └───────────▲────────────┘
│web frontend     ││                        │       │        │                 │ PPPoE / IPoE
│http(s):8080/8443││ primary path           │       │        │     ┌───────────▼────────────┐
└──────┬──────────┘│                        │       │        └ ─ ─▶│ FastRG Node            │
       │           │                        │       └──────────────┤ PPPoE client/NAT       │
       ▼           ▼                        │     ┌────────────────│ DHCP server            │
┌────────────────────────┐  write config    │     │ write events   │ gRPC :50052            │
│ FastRG Controller      │──────────────────┘     │                └───────────▲────────────┘ 
│ REST :8443             │        ┌───────────────▼────────┐                   │ IPoE over VLAN
│ gRPC :50051            │consume │ Kafka                  │        ┌──────────▼─────────────┐
│ Prometheus: 55688      │◄───────│ FastRG node events     │        │ OLT                    │
└──────┬──────────▲──────┘        └────────────────────────┘        │ GPON aggregation       │
       │          │                                                 └─────▲────────────▲─────┘
       ▼          │                                                       │ PON        │ PON
┌────────────────────────┐                                                ▼            ▼
│ PostgreSQL             │                                          ┌──────────┐  ┌──────────┐
│ FastRG config history  │                                          │ ONT      │  │ ONT      │
│ FastRG node events     │                                          └────▲─────┘  └────▲─────┘
└────────────────────────┘                                               │ IPoE        │ IPoE
                                                                  ┌──────▼─────┐  ┌────▼───────┐
                                                                  │Subscriber 1│  │Subscriber 2│ ...
                                                                  │DHCP client │  │DHCP client │
                                                                  └────────────┘  └────────────┘
```
