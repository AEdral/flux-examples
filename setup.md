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
echo "apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: kustom-webapp-dev
  namespace: flux-system
spec:
  interval: 1m
  path: ./kustom-webapp/overlays/dev
  prune: true
  sourceRef:
    kind: GitRepository
    name: flux-system
  targetNamespace: default" > clusters/dev/kustomization.yaml

#git push


#verifica
flux get kustomizations -n flux-system
kubectl get all -n default

#
