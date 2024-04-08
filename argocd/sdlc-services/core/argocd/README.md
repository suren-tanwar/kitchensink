# ArgoCD Installation on your local machine
Follow the basic installation as described here:\
https://argo-cd.readthedocs.io/en/stable/getting_started/
- Install ArgoCD (with UI)
- Install ArgoCD CLI
- Install Ingress ( see: kubectl apply -f local/route.yml )
- Get the ArgoCD password
- Login using the client\
argocd login argocd.127.0.0.1.nip.io

## install local dns server
In case the command "ping argocd.127.0.0.1.nip.io" doesn't work on your computer and you are on Mac or Linux you should install the dnsmask tool.\
https://allanphilipbarku.medium.com/setup-automatic-local-domains-with-dnsmasq-on-macos-ventura-b4cd460d8cb3

For windows I did not test any alternative

## changes in configMap: argocd-cmd-params-cm
When installed with the standard configuration ArgoCD does not accept self signed certificates.
Some more background information you can find here:\
https://stackoverflow.com/questions/71692891/argocd-traefik-2-x-how-to-configure-argocd-server-deployment-to-run-with-tls\

To enable self signed certificates you need to patch the standard ArgoCD configuration: 
- execute: kubectl patch configmap argocd-cmd-params-cm -n argocd --type merge -p $'data:\n  server.insecure: "true"'
- restart argocd-server by scaling down / up - either using kubectl or Lens desktop

Now you should be able to login to the ArgoCD UI: https://argocd.127.0.0.1.nip.io

User: admin\
Pwd: get with: "argocd admin initial-password -n argocd"

## ArgoCD configuration (using UI)
### connect to the Gitlab project
Please enter in Settings/Repository:
- connection method: via https
- Type: git
- Project: default
- Repository URL: https://git.spicter.io/spicter/kitchensink.git
- Username: your gitlab user name
- Password: your gitlab password

### configure your local kubernetes server
Please go to Settings/Clusters:
- click on the cluster "https://kubernetes.default.svc"
- click "edit"
- change "Name" to "local" and save it\
this is necessary because ArgoCD is looking for a folder "local" to deploy to this local cluster 