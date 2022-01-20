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


setup nginx-ingress and metal-lb this guide was helpful https://fabianlee.org/2021/09/13/kubernetes-k3s-with-multiple-metallb-endpoints-and-nginx-ingress-controllers/ but I'm not going to use multiple nodes

after setting that up create ingress config for portainer 

Finally figured out how to test the ingress setup
first get the external ip og the ingress controller
`kubectl get ing -A`
since I setup localhost in the portainer ingress config we need to verify that it works like this
curl -H "Host: localhost" --insecure https://192.168.1.81/



TODO:
- [ ] Create new storage classes for hard disks, going to keep local-storage.yaml as disk1 and use that as persisting volume for none media related stuff
- [ ] get a domain and setup tls certs
- [ ] forward ingress port 443 and setup dns records