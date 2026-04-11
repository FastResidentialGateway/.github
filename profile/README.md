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
┌────────────────────────────────────────────────────────────────┐
│┌────────────────┐                              Central Office  │
││    BNG/BRAS    │                                              │
││  PPPoE Server  │                                              │
│└────────────────┘                                              │
│        ▲                                                       │
│        │  PPPoE/IGMP/IPTV over VLAN                            │
│        ▼                                                       │
│┌────────────────┐    ┌─────────────────┐    ┌─────────────────┐│
││  FastRG Node   │───▶│   FastRG etcd   │◄───┤FastRG Controller││
││ (grpc: 50052)  │    │   (etcd:2379)   │    │  gRPC: 50051    ││
││PPPoE Client/NAT│    │                 │    │HTTP(s):8080/8443││
