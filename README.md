# kubernetesApplications

I am using the app of apps pattern in Argo CD to deploy and manage Kubernetes applications.

## dependency order / sync wave

| name | sync wave | reason |
| ---- | --------- | ------ |
| argocd | 10 | the less it manages before it self-manages, the better. this enables argocd to build helm manifests, which we need |
| cilium | 20 | this is the cni and needs to be configured asap |
| certManager | 30 | get certificates up so they are available for other services |
| istio | 40 | overlay networking service mesh |
| longhorn | 50 | storage |
| gatewayApi | 60 | gateways and routes. needs istio |
