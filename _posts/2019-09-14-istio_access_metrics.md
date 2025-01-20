---
title: Istio Archtecture
date: 2019-09-14 11:37:46 +0545
categories: [kubernetes]
tags: [istio]
---

## Pilot
- Responsible for configuring the `Envoy` and `Mixer` at runtime.

## Proxy / Envoy
- `Sidecar` proxies per microservice to handle `ingress/egress` traffic
between services in the cluster and from a service to external services.
The proxies form a secure microservice mesh providing a rich set of
functions like `discovery`, `rich layer-7 routing`, `circuit breakers`,
`policy enforcement` and `telemetry recording/reporting` functions.

## Mixer
- Create a portability layer on top of infrastructure backends.
Enforce policies such as `ACLs`, `rate limits`, `quotas`, `authentication`,
`request tracing` and `telemetry collection` at an infrastructure level.

## Citadel / Istio CA (Certificate Authority)
- Secures service to service communication over TLS. Providing
a key management system to `automate key and certificate generation`,
`distribution`, `rotation`, and `revocation`.

## Ingress/Egress
- Configure path based routing for `inbound` and `outbound` external traffic.

## Control Plane API
- Underlying `Orchestrator` such as Kubernetes or Hashicorp Nomad.


## Archtecture

![Istio Archtecture](/assets/img/post/istio/istio-arch.png)


## Reference
### envoy
- A messenger or representative, especially one on a diplomatic mission.

### proxy
- The authority to represent someone else, especially in voting.

### ingress
- The action or fact of going in or entering.

### egress
- The action of going out of or leaving a place.

### telemetry
- The process of recording and transmitting the readings of an instrument.

### citadel
- A fortress, typically on high ground, protecting or dominating a city.

### in-bound
- Traveling toward a particular place, especially when returning to the original point of departure.

### out-bound
- Traveling away from a particular place, especially on the first leg of a round trip.

### orchestrate
- plan or coordinate the elements of (a situation) to produce a desired effect, especially surreptitiously.

### surreptitiously
- in a way that attempts to avoid notice or attention; 