# kubernetesApplications

I am using the app of apps pattern in Argo CD to deploy and manage Kubernetes applications.

## sync waves

| name | sync wave |
| ---- | --------- |
| certManager | 10 |
| envoyGateway | 20 |
| gatewayApi | 30 |
| cilium | 40 |
| longhorn | 50 |
| argocd | 60 |
| coredns | 70 |

## dependency list

| name | dependency list |
| ---- | ---------------- |
| certManager | none |
| envoyGateway | none |
| gatewayApi | certManager, envoyGateway |
| cilium | gatewayApi |
| longhorn | gatewayApi |
| argocd | gatewayApi |
| coredns | gatewayApi |

I am putting the gateway api routes with each application. That means then that envoy gateway, gateway api, and
certificates have to be in place before the other applications get installed.

## pod security admission standard

By default, this cluster is using the level = `restricted` for all 3 modes = `enforce`, `audit`, and `warn`.
Every namespace receives this level unless explicity overridden.

## kustomize patches

Uses JSON6902 style most of the time. Argocd uses strategic merge patches.
