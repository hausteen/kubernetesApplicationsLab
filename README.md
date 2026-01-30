# kubernetesApplications

I am using the app of apps pattern in Argo CD to deploy and manage Kubernetes applications.

## sync waves

| name | sync wave |
| ---- | --------- |
| namespaceCreation | 0 |
| customResourceDefinitions | 10 |
| certManager | 20 |
| argocd | 30 |
| cilium | 40 |
| longhorn | 50 |
| certificateCreation | 60 |
| envoyGateway | 70 |
| coredns | 80 |
| gatewayApiInfrastructure | 90 |
| gatewayApiProductionHome | 100 |

## dependency list

| name | dependency list |
| ---- | ---------------- |
| namespaceCreation | none |
| customResourceDefinitions | none |
| certManager | namespaceCreation |
| argocd | none |
| cilium | none |
| longhorn | namespaceCreation |
| certificateCreation | certManager |
| envoyGateway | customResourceDefinitions |
| coredns | namespaceCreation |
| gatewayApiInfrastructure | namespaceCreation, certManager |
| gatewayApiProductionHome | namespaceCreation, certManager, gatewayApiInfrastructure |

We need to give certManager and envoyGateway as much time for them both to set up their certificates before we start calling them.

## pod security admission standard

By default, this cluster is using the level = `restricted` for all 3 modes = `enforce`, `audit`, and `warn`.
Every namespace receives this level unless explicity overridden.

## kustomize patches

Uses JSON6902 style most of the time. Argocd uses strategic merge patches.
