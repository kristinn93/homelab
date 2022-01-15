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



Setup local disks, this was helpful: https://lapee79.github.io/en/article/use-a-local-disk-by-local-volume-static-provisioner-in-kubernetes/
View ./storage to see the setup


To test this setup portainer since we need persisting volume for that 
This is the install guide from portainer: https://docs.portainer.io/v/ce-2.9/start/install/server/kubernetes/baremetal