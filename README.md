# kubernetesApplications

I am using the app of apps pattern in Argo CD to deploy and manage Kubernetes applications.

## sync waves

| name | sync wave |
| ---- | --------- |
| namespaceCreation | 0 |
| customResourceDefinitions | 10 |
| argocd | 20 |
| cilium | 30 |
| certManager | 40 |
| coredns | 41 |
| envoyGateway | 50 |
| longhorn | 60 |
| gatewayApi | 70 |

## dependency list

| name | dependency list |
| ---- | ---------------- |
| namespaceCreation | none |
| customResourceDefinitions | namespaceCreation |
| argocd | none |
| cilium | none |
| certManager | namespaceCreation |
| coredns | namespaceCreation |
| envoyGateway | namespaceCreation, certmanager |
| longhorn | namespaceCreation |
| gatewayApi | namespaceCreation, certManager, envoyGateway |

## pod security admission standard

By default, this cluster is using the level = `restricted` for all 3 modes = `enforce`, `audit`, and `warn`.
Every namespace receives this level unless explicity overridden.
