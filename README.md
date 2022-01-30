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



##### Create cloudflare tls secret:
kubectl create secret tls cloudflare-tls --key origin-ca.pk --cert origin-ca.crt -n=default (remember to create in all namespaces where it's needed)

##### Create oauth secrets:
kubectl -n=default create secret generic oauthproxy --from-literal=clientid=<some-clientid> --from-literal=clientsecret=<some-clientsecret> --from-literal=cookiesecret=cookiesecret=<generate cookie secret string>

To test this setup portainer since we need persisting volume for that 
This is the install guide from portainer: https://docs.portainer.io/v/ce-2.9/start/install/server/kubernetes/baremetal


setup nginx-ingress and metal-lb this guide was helpful https://fabianlee.org/2021/09/13/kubernetes-k3s-with-multiple-metallb-endpoints-and-nginx-ingress-controllers/ but I'm not going to use multiple nodes

after setting that up create ingress config for portainer 

Finally figured out how to test the ingress setup
first get the external ip og the ingress controller
`kubectl get ing -A`
since I setup localhost in the portainer ingress config we need to verify that it works like this
curl -H "Host: localhost" --insecure https://192.168.1.81/

Setting up a domain, create A name records that point to your external ip, expose ingress controller port to 443
Create cert as a secret and reference the correct name (secrets are not shared across namespaces)


next figure out how to setup authentication for nginx-ingress

~~setup keycloak, expose with ingress (data does not persist until postgres is setup)
setup postgres so we can store data in keycloak~~~

after spending too much time on keycloack and it behaving strangely, randomly not showing newly created clients in the admin UI, I decided to just go with google and create a new project and use that as authentication

Look at ingress in portainer server, after nothing working I found out that the ingress instance that handles oauth in the portainer namespace was unable to call oauth-proxy in the default namespace, hence the file oauth-reference in portainer namespace. (found that out after looking throught the logs of the ingress controller in the ingress-nginx namespace)

So we are running two instances of ingress in the portainer namespace:
* First one that's bound to the /oauth2 subpath that communicates with the oauth service throught the oauth service reference in default namespace. 

* Second one is bound to the root of the hostname (portainer.kristinn.pro) and with annotation to check if the user is authenticated through the first ingress instance



After trying to setup transmission and trying to bind to disk1, relised pv and pvc have one to one relations so I was unable to use the same disk for portainer and transmission persisting data I decided to setup nfs server on the host and change all the volume bindings and directly bind to nfs volumes and skip the pv and pvc setup I managed to run both portainer and transmission. 


Before setting up plex I want to be able to use GPU to transcode videos, is it possible to use gpu withing docker (or kubernetes ? 🤔)
after short googling it seem's like this is a thing
https://github.com/NVIDIA/k8s-device-plugin#preparing-your-gpu-nodes 

After messing around trying to enable this for containerd (usded by k3s) it's not worth the time at this moment since I'm running a pretty outaded GPU, but should be straight forward with newer hardware

