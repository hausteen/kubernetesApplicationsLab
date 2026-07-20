Welcome! This is my "homelab".

## Background

As I have learned Kubernetes over time, who knows how many times I have restructured my homelab, broken it, fixed it, renamed it, merged it, split it, the list goes on. Its all part of the learning process as I have got my hands dirty.

This is my best version so far and it will keep becoming better.

## Repository structure

### Clusters

This is a monorepo.

There are many clusters at play here and all are found under the "clusters/overlays" folder.

By default, each cluster is designed to be fully independent from the other clusters. In addition, I can create a shared "base" of applications for multiple clusters if I want/need to. Normally, I'll avoid it since I test with a bunch of different clusters, with wildly different software and configurations. Kustomize allows you base off any kustomization folder; so I can base off any cluster in the clusters/overlays/ or clusters/base folder.

### File / folder structure

```
git-repository
    clusters
        base
            empty until I want to share between overlays
        overlays
            <cluster name>
                kustomization.yaml
                patches
                resources
                    kustomize-controller.yaml
    manifests
        <software name>
            pre-install-<optional-number>
                base
                    empty until I want to share between overlays
                overlays
                    <cluster name>
                        kustomization.yaml
                        patches
                        resources
            install
                base
                    empty until I want to share between overlays
                overlays
                    <cluster name>
                        kustomization.yaml
                        patches
                        resources
            post-install-<optional number>
                base
                    empty until I want to share between overlays
                overlays
                    <cluster name>
                        kustomization.yaml
                        patches
                        resources
```

Question: Why are there so many base and overlay folders?
Answer: This is strategic. It allows me to:
- set up fully independent clusters.
- control software dependency installation order easily by using software name and time (the pre-install, install, post-install folders)
- set up shared bases when needed (a cluster doesn't have to use the shared base if it doesn't want to).
- base off other overlays for quick testing
- it allows me to keep installation and configuration in the same "mainfests" folder, split by software name. It's easier for me to mentally reason through.

### Gitops

The repo is currently using Flux CD. I really like the mental model that Flux uses (its a beautiful design in my opinion). It solves many of my problems that I had with Argocd.
I did steal Argocd's sync phases / waves idea since that's useful (pre-install, install, post-install is what I'm calling the phases). It allows me to define dependencies that Flux CD can enforce easily.

### How the codebase all fits together

During cluster bootstrap, I install Flux Operator. I give it a Flux Instance file and I give it my Github app credential. This Flux Instance file creates a Flux gitrepository and Flux kustomization object (both called flux-system), which point to my github repo and clusters/overlays/name folder respectively. The flux-system kustomization object sees the clusters/overlays/name/kustomization.yaml file which points at the clusters/overlays/name/resources/kustomize-controller.yaml file. That clusters/overlays/name/resources/kustomize-controller.yaml file points at the manifests/name/* folders in the correct order to deploy everything.

Read the clusters/overlays/name/resources/kustomize-controller.yaml file in order to see what is in each cluster. It should match the below tables for each cluster.

## Installation

### For "clusters/overlays/lab":

| Install Order | Name                                      | Path                                                                | Depends On |
| ------------- | ----------------------------------------- | ------------------------------------------------------------------- | ---------- |
| 0             | namespaces-install                        | manifests/namespaces/install/overlays/lab                           | nothing |
| 0             | nginx-gateway-fabric-pre-install          | manifests/nginx-gateway-fabric/pre-install/overlays/lab             | nothing |
| 0             | cilium-install                            | manifests/cilium/install/overlays/lab                               | nothing |
| 1             | cert-manager-install                      | manifests/cert-manager/install/overlays/lab                         | cilium-install |
| 1             | longhorn-install                          | manifests/longhorn/install/overlays/lab                             | cilium-install |
| 1             | nginx-gateway-fabric-install              | manifests/nginx-gateway-fabric/install/overlays/lab                 | cilium-install, nginx-gateway-fabric-pre-install |
| 1             | coredns-install                           | manifests/coredns/install/overlays/lab                              | cilium-install |
| 1             | istio-install                             | manifests/istio/install/overlays/lab                                | cilium-install |
| 1             | external-secrets-operator-install         | manifests/external-secrets-operator/install/overlays/lab            | cilium-install |
| 1             | cloudnativepg-install                     | manifests/cloudnativepg/install/overlays/lab                        | cilium-install |
| 1             | cilium-post-install-1                     | manifests/cilium/post-install-1/overlays/lab                        | cilium-install |
| 2             | trust-manager-install                     | manifests/trust-manager/install/overlays/lab                        | cert-manager-install |
| 2             | cert-manager-post-install                 | manifests/cert-manager/post-install/overlays/lab                    | cert-manager-install |
| 2             | istio-post-install                        | manifests/istio/post-install/overlays/lab                           | istio-install |
| 3             | human manually creates the copy of the hunt-internal-certificate-authority for trust-manager. the command is in manifests/trust-manager/post-install/overlays/lab/resources/bundle.yaml step 3. | | cert-manager-post-install |
| 3             | nginx-gateway-fabric-post-install         | manifests/nginx-gateway-fabric/post-install/overlays/lab            | cert-manager-post-install, nginx-gateway-fabric-install |
| 4             | trust-manager-post-install                | manifests/trust-manager/post-install/overlays/lab                   | trust-manager-install, human manually creates the copy of the hunt-internal-certificate-authority for trust-manager. |
| 4             | coredns-post-install                      | manifests/coredns/post-install/overlays/lab                         | coredns-install, nginx-gateway-fabric-post-install |
| 4             | cilium-post-install-2                     | manifests/cilium/post-install-2/overlays/lab                        | cilium-install, nginx-gateway-fabric-post-install |
| 4             | longhorn-post-install                     | manifests/longhorn/post-install/overlays/lab                        | longhorn-install, nginx-gateway-fabric-post-install |
| 5             | openbao-install                           | manifests/openbao/install/overlays/lab                              | longhorn-install, trust-manager-post-install |
| 6             | openbao-post-install                      | manifests/openbao/post-install/overlays/lab                         | openbao-install, nginx-gateway-fabric-post-install |
| 6             | external-secrets-operator-post-install    | manifests/external-secrets-operator/post-install/overlays/lab       | external-secrets-operator-install, openbao-install, trust-manager-post-install |
