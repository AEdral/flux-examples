# HOWTO: Deploy di una Webapp con Kustomize e Flux CD

Questa guida spiega come effettuare il setup e il deploy automatico di una semplice webapp su Kubernetes usando Kustomize e Flux CD.

---

## 1. Prerequisiti
- Cluster Kubernetes funzionante (Minikube, Kind, Docker Desktop, Cloud, ecc.)
- `kubectl` configurato e funzionante
- `flux` CLI installata
- Accesso in scrittura al repository Git

---

## 2. Avvia il cluster Kubernetes

**Minikube:**
```sh
minikube start
```

**Kind:**
```sh
kind create cluster
```

**Docker Desktop:**
- Assicurati che Kubernetes sia attivo nelle impostazioni.

Verifica la connessione:
```sh
kubectl get nodes
```

---

## 3. Bootstrap di Flux (solo la prima volta)

Sostituisci `<tuo-utente-o-org>` e `<nome-repo>` con i tuoi dati:
```sh
flux bootstrap github \
  --owner=<tuo-utente-o-org> \
  --repository=<nome-repo> \
  --branch=main \
  --path=clusters/dev
```

---

## 4. Prepara i manifest Kustomize

- La struttura del progetto deve essere:
  - `kustom-webapp/base/` (deployment, service, kustomization.yaml)
  - `kustom-webapp/overlays/dev/` (kustomization.yaml, patch, config)
  - `clusters/dev/flux-system/kustom-webapp.yaml` (Kustomization di Flux)

---

## 5. Aggiungi la Kustomization di Flux per la webapp

Crea il file `clusters/dev/flux-system/kustom-webapp.yaml` con:
```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: kustom-webapp
  namespace: flux-system
spec:
  interval: 1m0s
  path: ./kustom-webapp/overlays/dev
  prune: true
  sourceRef:
    kind: GitRepository
    name: flux-system
    namespace: flux-system
  targetNamespace: default
```

---

## 6. Commit e push delle modifiche

```sh
git add .
git commit -m "Setup deploy automatico webapp con Flux"
git push
```

---

## 7. Controlla lo stato del deploy

```sh
kubectl get kustomizations -A
kubectl get all -n default
```

Devi vedere la Kustomization `kustom-webapp` in READY=True e i pod della tua app in esecuzione.

---

## 8. Accedi alla webapp dal browser

Trova la porta esposta dal Service:
```sh
kubectl get svc kustom-mywebapp-v1 -n default
```

Se usi Minikube:
```sh
minikube service kustom-mywebapp-v1 -n default
```
(apre direttamente il browser)

Altrimenti, apri il browser su:
```
http://localhost:<PORTA-NODEPORT>
```
(dove <PORTA-NODEPORT> è quella mostrata dal comando sopra, es: 30509)

---

## 9. Troubleshooting
- Se non vedi i pod o la Kustomization non è READY, controlla i log di Flux:
  ```sh
  kubectl -n flux-system logs deployment/flux-system
  ```
- Se la pagina non si apre, verifica che la porta sia corretta e che il cluster sia in esecuzione.

---

**Fine!** Ora hai un setup automatico e ripetibile per il deploy di una webapp su Kubernetes con Kustomize e Flux CD. 