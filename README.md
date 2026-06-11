# Letter Of Credit

BIAN Service Domain microservice — part of the [bian-platform](../../bian-platform/) landscape.

| | |
|---|---|
| **Business Area** | Operations and Execution |
| **Business Domain** | Loans and Deposits |
| **Functional Pattern** | Fulfill |
| **Asset Type** | Letter Of Credit |
| **Control Record** | Letter Of Credit Fulfillment Arrangement |
| **K8s Namespace** | `bian-operations` |
| **Stack** | Java 21 · Spring Boot 3 · Resilience4j · Cilium mesh |

> ⚠️ **Phase 1 (shallow):** real REST API over an in-memory store. Phase 2 replaces the store with per-domain persistence and real domain logic. This repo was stamped from `bian-platform/generator` — regenerate rather than hand-editing boilerplate.

## BIAN Semantic API

| Method | Path | BIAN action term |
|---|---|---|
| GET | `/v1/service-domain` | — (SD metadata) |
| POST | `/v1/letter-of-credit-fulfillment-arrangement/initiate` | Initiate |
| GET | `/v1/letter-of-credit-fulfillment-arrangement` | Retrieve (list) |
| GET | `/v1/letter-of-credit-fulfillment-arrangement/{crId}/retrieve` | Retrieve |
| PUT | `/v1/letter-of-credit-fulfillment-arrangement/{crId}/update` | Update |
| PUT | `/v1/letter-of-credit-fulfillment-arrangement/{crId}/control` | Control — body `{"action": "suspend"\|"resume"\|"terminate"}` |

OpenAPI UI: `/swagger-ui.html` · Health: `/actuator/health` · Metrics: `/actuator/prometheus`

## Run locally

```bash
mvn spring-boot:run
curl localhost:8080/v1/service-domain

# lifecycle smoke test
curl -X POST localhost:8080/v1/letter-of-credit-fulfillment-arrangement/initiate -H 'content-type: application/json' -d '{"note":"hello"}'
```

## Build & containerize

```bash
mvn -B verify
docker build -t bian/sd-letter-of-credit:0.1.0 .
```

## Deploy (Helm → K8s with Cilium mesh)

```bash
helm upgrade --install sd-letter-of-credit ./helm -n bian-operations
```

Exposed through the platform Gateway at path prefix `/sd-letter-of-credit` (Cilium Gateway API). Mesh policy (`CiliumNetworkPolicy`) allows: gateway ingress, same-area peers, Prometheus — everything else denied.
