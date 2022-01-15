# Homelab

## Setup:
* Install k3s cluster
    - install kubectl
    - Once the cluster is up and running you can copy the config to ~/.kube/config on a remote machine to run kubectl there (remember to update the ip address)
* Install fluxcd cli

### FluxCD setup
Run flux bootstrap
```
flux bootstrap github \
  --owner=kristinn93 \
  --repository=homelab \
  --path=clusters/default \
  --personal
```

To upgrade flux either run 
`flux reconcile source git flux-system` or the bootstrap command again



