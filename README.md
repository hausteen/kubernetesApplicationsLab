# kubernetesApplications

I am using the app of apps pattern in Argo CD to deploy and manage Kubernetes applications.

## sync waves

| name | sync wave | reason |
| ---- | --------- | ------ |
| namespaceCreation | 0 | namespaces have to exist first |
| customResourceDefinitions | 10 | needed for other applications. can be namespace scoped so namespace must exist first |
| argocd | 20 | the less it manages before it self-manages, the better. |
| cilium | 30 | this is the cni and needs to be configured asap |
| certManager | 40 | get certificates up so they are available for other services. |
| envoyGateway | 50 | |
| longhorn | 60 | storage |
| gatewayApi | 70 | gateways and routes |

## dependency order

| name | dependency order | reason |
| ---- | ---------------- | ------ |
| namespaceCreation | run first | namespaces must exist before everything else |
| customResourceDefinitions | needs namespaceCreation | these can be namespace scoped |
| argocd | needs namespaceCreation | to install other applications. the argocd namespace already exists by the time this repo's code is running |
| cilium | run whenever | has no dependencies that have not been met by the time this repo's code is running |
| certManager | needs namespaceCreation |  |
| envoyGateway | needs namespaceCreation, certmanager |  |
| longhorn | needs namespaceCreation |  |
| gatewayApi | needs namespaceCreation, certManager, envoyGateway |  |
