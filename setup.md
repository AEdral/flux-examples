#install fluxcd (if is not installed)
choco install fluxcd

#start it
flux install
kubectl get pods -n flux-system

#setup the flux config
flux bootstrap github \
  --owner=AEdral \
  --repository=flux-examples \
  --branch=main \
  --path=clusters/dev \
  --personal


#setup cluster
mkdir clusters
mkdir clusters/dev
touch clusters/dev/kustomization.yaml

#git add * git commit -m "" git push


#verifica
flux get kustomizations -n flux-system
kubectl get all -n default


