Welcome! This is my "homelab".

## Background

As I have learned Kubernetes over time, who knows how many times I've restructured my homelab, broke it, fixed it, renamed it, merged it, split it, the list goes on. Its all part of the learning process as I have got my hands dirty.

This is my best version so far and it will keep becoming better.

## Repository structure

### Clusters

This is a monorepo.

There are many independent clusters at play here and all are found under the "clusters" folder.

My repo is unique because each cluster is fully independent, meaning that here are 0 shared applications by default. This is by design. I didn't want a shared "base" of applications because sometimes I'm testing totally different things and that shared "base" of applications would be a problem. I still can effectively create a shared "base" of applications if I want to, so its not as if that's off the table; you just have explicitly say that you want that.

### Gitops

The repo is currently using Flux CD. I really like the mental model that Flux uses (its a beautiful design in my opinion). It solves many of my problems that I had with Argocd.

### How the codebase all fits together

During cluster bootstrap, I install Flux Operator. I give it a Flux Instance file and I give it my Github app credential. This Flux Instance file creates a Flux gitrepository and Flux kustomization object (both called flux-system), which point to my github repo and clusters/\<name\> folder respectively. The flux-system kustomization object sees the clusters/\<name\>/kustomization.yaml file which points at the clusters/\<name\>/resources/kustomize-controller.yaml file. That clusters/\<name\>/resources/kustomize-controller.yaml file points at the applications/ and configurations/ folders in the correct order to deploy everything.

Read the clusters/\<name\>/resources/kustomize-controller.yaml file in order to see what is in each cluster. It should match the below tables for each cluster.

### Namespaces

I'm thinking of installing all the required namespaces at the beginning. There's a namespace file in the resources folder.

### Patches

There is a clusters/\<name\>/patches/ folder that will contain the kustomization patches for files in the applications and configurations folders. This is how I'll be able to modify things at the single cluster level without messing with the other clusters.

## Installation

### For "clusters/lab":

| Install Order | Name                                     | Depends On |
| ------------- | ---------------------------------------- | ---------- |
| 0             | clusters/lab/resources/namespace         | nothing |
| 0             | applications/gateway-api-crds            | nothing |
| 0             | applications/cilium                      | nothing |
| 1             | applications/cert-manager                | applications/cilium |
| 1             | applications/longhorn                    | applications/cilium |
| 1             | applications/nginx-gateway-fabric        | applications/cilium, applications/gateway-api-crds |
| 1             | applications/coredns                     | applications/cilium |
| 1             | applications/istio                       | applications/cilium |
| 1             | applications/external-secrets-operator   | applications/cilium |
| 1             | applications/cloudnativepg               | applications/cilium |
| 1             | configurations/cilium/phase1             | applications/cilium |
| 2             | applications/trust-manager               | applications/cert-manager |
| 2             | configurations/cert-manager              | applications/cert-manager |
| 2             | configurations/istio                     | applications/istio |
| 3             | human manually creates the copy of the hunt-internal-certificate-authority for trust-manager. the command is in configurations/trust-manager/bundle step 3. | configurations/cert-manager |
| 3             | configurations/gateway-api               | configurations/cert-manager, applications/nginx-gateway-fabric |
| 4             | configurations/trust-manager             | applications/trust-manager, human manually creates the copy of the hunt-internal-certificate-authority for trust-manager. the command is in configurations/trust-manager/bundle step 3. |
| 4             | configurations/coredns                   | applications/coredns, configurations/gateway-api |
| 4             | configurations/cilium/phase2             | applications/cilium, configurations/gateway-api |
| 4             | configurations/longhorn                  | applications/longhorn, configurations/gateway-api |
| 5             | applications/openbao                     | applications/longhorn, configurations/trust-manager |
| 6             | configurations/openbao                   | applications/openbao, configurations/gateway-api |
| 6             | configurations/external-secrets-operator | applications/external-secrets-operator, applications/openbao, configurations/trust-manager |
| 7             | configurations/cloudnativepg             | applications/cloudnativepg, applications/longhorn, configurations/external-secrets-operator |
| 8             | applications/authentik                   | configurations/cloudnativepg |

### For "clusters/home-staging":

### For "clusters/home-production":
