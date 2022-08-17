# repository structure

The architecture of this repo is divided into three main parts, represented by the three directories in the root level:

- `applications`: this directory contains all deployments of enduser applications
- `clusters`: entrypoint for [fluxcd](https://fluxcd.io), each kubernetes cluster has its own subdirectory, represented by the name of the cluster. This directory also implements the `application` and `infrastructure` directory.
- `infrastructure`: contains all infrastrucutre related stuff like operators and system-deployments (e.g. cert-manager, ingress, issuers, argocd)


This architecture aims to reduce as much code redundancy as possible without compromising flexibility.
It's possible to add multiple kubernetes cluster, where each can have a different set of operators with adapted configurations.