#choco install fluxcd

flux install
kubectl get pods -n flux-system

flux bootstrap github \
  --owner=AEdral \
  --repository=<nome-repo> \
  --branch=main \
  --path=clusters/prod \
  --personal

