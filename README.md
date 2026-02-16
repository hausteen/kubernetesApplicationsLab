# kubernetesApplicationsLab

I am using the app of apps pattern in Argo CD to deploy and manage Kubernetes applications.

## sync waves

| name | sync wave |
| ---- | --------- |
| namespaceCreation | 0 |
| customResourceDefinitions | 10 |
| certManager |  |
| argocd |  |
| longhorn |  |
| certificateCreation |  |
| coredns |  |
| gatewayApi |  |

## dependency list

| name | dependency list |
| ---- | ---------------- |
| namespaceCreation | none |
| customResourceDefinitions | none |
| certManager | namespaceCreation |
| argocd | none |
| longhorn | namespaceCreation |
| certificateCreation | certManager |
| coredns | namespaceCreation |
| gatewayApiProductionHome | namespaceCreation, certManager, gatewayApi |

## pod security admission standard

By default, this cluster is using the level = `restricted` for all 3 modes = `enforce`, `audit`, and `warn`.
Every namespace receives this level unless explicity overridden.

## kustomize patches

Uses JSON6902 style most of the time. Argocd uses strategic merge patches.
