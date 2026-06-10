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

todo

## Installation order

### For "clusters/lab":

1.  applications/cilium
2.  configurations/cilium/phase1
3.  applications/gateway-api-crds
4.  applications/cert-manager
5.  configurations/cert-manager
6.  applications/trust-manager
7.  human: manually create the copy of the hunt internal certificate authority for trust manager. the command is in configurations/trust-manager/bundle step 3.
8.  configurations/trust-manager
9.  applications/longhorn
10. applications/nginx-gateway-fabric
11. applications/openbao
12. applications/external-secrets-operator
13. applications/cloudnativepg
14. applications/coredns
15. applications/istio
16. configurations/gateway-api
17. configurations/external-secrets-operator
18. configurations/cloudnativepg
19. configurations/istio
20. configurations/coredns
21. configurations/cilium/phase2
22. configurations/longhorn
23. configurations/openbao

### For "clusters/home-staging":

### For "clusters/home-production":
