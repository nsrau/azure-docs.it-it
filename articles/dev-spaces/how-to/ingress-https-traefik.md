---
title: Usare un controller di ingresso traefik personalizzato e configurare HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Informazioni su come configurare Azure Dev Spaces per l'uso di un controller di ingresso traefik personalizzato e configurare HTTPS usando il controller di ingresso
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
ms.custom: devx-track-js
ms.openlocfilehash: a30dae3b65a7e877dc20b4d6fae8de338024d3c7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973054"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Usare un controller di ingresso traefik personalizzato e configurare HTTPS

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Questo articolo illustra come configurare Azure Dev Spaces per l'uso di un controller di ingresso traefik personalizzato. Questo articolo illustra anche come configurare il controller di ingresso personalizzato per l'uso di HTTPS.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha un account, è possibile [crearne uno gratuito][azure-account-create].
* [L'interfaccia della riga di comando di Azure installata][az-cli].
* [Cluster Azure Kubernetes Service (AKS) con Azure Dev Spaces abilitato] [QS-CLI].
* [kubectl][kubectl] installato.
* [Helm 3 installato][helm-installed].
* [Un dominio personalizzato][custom-domain] con una [zona DNS][dns-zone]. Questo articolo presuppone che il dominio personalizzato e la zona DNS si trovino nello stesso gruppo di risorse del cluster AKS, ma è possibile usare un dominio personalizzato e una zona DNS in un gruppo di risorse diverso.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Configurare un controller di ingresso traefik personalizzato

Connettersi al cluster usando [kubectl][kubectl], il client da riga di comando Kubernetes. Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco di nodi del cluster.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Aggiungere il [repository Helm stabile ufficiale][helm-stable-repo], che contiene il grafico Helm del controller di ingresso traefik.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Creare uno spazio dei nomi Kubernetes per il controller di ingresso traefik e installarlo usando `helm` .

> [!NOTE]
> Se nel cluster AKS non è abilitato il controllo degli accessi in base al ruolo, rimuovere il parametro *--set RBAC. Enabled = true* .

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> L'esempio precedente crea un endpoint pubblico per il controller di ingresso. Se invece è necessario usare un endpoint privato per il controller di ingresso, aggiungere il comando *--set service. Annotations. Service \\ . Beta \\ . kubernetes \\ . io/Azure-Load-Balancer-Internal "= true* parametro per il comando *Helm install* .
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Questo endpoint privato viene esposto all'interno della rete virtuale in cui è distribuito il cluster AKS.

Ottenere l'indirizzo IP del servizio controller di ingresso traefik usando [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

L'output di esempio Mostra gli indirizzi IP per tutti i servizi nello spazio dei nomi *traefik* .

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Aggiungere un *record a* nella zona DNS con l'indirizzo IP esterno del servizio traefik usando il comando [AZ network DNS record-set a Add-record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Nell'esempio precedente viene aggiunto *un record a* nella zona DNS *MY_CUSTOM_DOMAIN* .

In questo articolo verrà usata l'[applicazione di esempio Bike Sharing di Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) per illustrare l'uso di Azure Dev Spaces. Clonare l'applicazione da GitHub e passare alla relativa directory:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Aprire [values. YAML][values-yaml] ed effettuare gli aggiornamenti seguenti:
* Sostituire tutte le istanze di *<REPLACE_ME_WITH_HOST_SUFFIX>* con *traefik. MY_CUSTOM_DOMAIN* utilizzo del dominio per *MY_CUSTOM_DOMAIN*. 
* Sostituire *kubernetes.io/ingress.Class: traefik-azds # dev Spaces-specifico* con *kubernetes.io/ingress.Class: traefik # in ingresso personalizzato*. 

Di seguito è riportato un esempio di un `values.yaml` file aggiornato:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Salvare le modifiche e chiudere il file.

Creare lo spazio di *sviluppo* con l'applicazione di esempio usando `azds space select` .

```console
azds space select -n dev -y
```

Distribuire l'applicazione di esempio utilizzando `helm install` .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

L'esempio precedente distribuisce l'applicazione di esempio nello spazio dei nomi *dev* .

Consente di visualizzare gli URL per accedere all'applicazione di esempio tramite `azds list-uris` .

```console
azds list-uris
```

L'output seguente Mostra gli URL di esempio da `azds list-uris` .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Passare al servizio *bikesharingweb* aprendo l'URL pubblico dal comando `azds list-uris`. Nell'esempio precedente l'URL pubblico per il servizio *bikesharingweb* è `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

> [!NOTE]
> Se viene visualizzata una pagina di errore anziché il servizio *bikesharingweb* , verificare di aver aggiornato **sia** l'annotazione *kubernetes.io/ingress.Class* che l'host nel file *values. YAML* .

Usare il `azds space select` comando per creare uno spazio figlio in *dev* ed elencare gli URL per accedere allo spazio dev figlio.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

L'output seguente Mostra gli URL di esempio di `azds list-uris` per accedere all'applicazione di esempio nello spazio dev figlio *azureuser1* .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Passare al servizio *bikesharingweb* nello spazio dev figlio *AZUREUSER1* aprendo l'URL pubblico dal `azds list-uris` comando. Nell'esempio precedente, l'URL pubblico per il servizio *bikesharingweb* nello spazio dev figlio *azureuser1* è `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Configurare il controller di ingresso di traefik per l'uso di HTTPS

Usare [Cert-Manager][cert-manager] per automatizzare la gestione del certificato TLS quando si configura il controller di ingresso di traefik per l'uso di HTTPS. Utilizzare `helm` per installare il grafico *CertManager* .

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Creare un `letsencrypt-clusterissuer.yaml` file e aggiornare il campo del messaggio di posta elettronica con l'indirizzo di posta elettronica.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
      - http01:
          ingress:
            class: traefik
```

> [!NOTE]
> Per i test, è disponibile anche un [server di gestione temporanea][letsencrypt-staging-issuer] che è possibile usare per la *ClusterIssuer*.

Usare `kubectl` per applicare `letsencrypt-clusterissuer.yaml` .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Rimuovere i *traefik* *ClusterRole* e *ClusterRoleBinding*precedenti, quindi aggiornare traefik per usare HTTPS usando `helm` .

> [!NOTE]
> Se nel cluster AKS non è abilitato il controllo degli accessi in base al ruolo, rimuovere il parametro *--set RBAC. Enabled = true* .

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Ottenere l'indirizzo IP aggiornato del servizio controller di ingresso traefik usando [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

L'output di esempio Mostra gli indirizzi IP per tutti i servizi nello spazio dei nomi *traefik* .

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Aggiungere un *record a* nella zona DNS con il nuovo indirizzo IP esterno del servizio traefik usando il comando [AZ network DNS record-set a Add-record][az-network-dns-record-set-a-add-record] e rimuovere il record *a* precedente usando [AZ network DNS record-set a Remove-record][az-network-dns-record-set-a-remove-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

L'esempio precedente aggiorna il record *a* nella zona DNS *MY_CUSTOM_DOMAIN* per usare *PREVIOUS_EXTERNAL_IP*.

Aggiornare [values. YAML][values-yaml] per includere i dettagli relativi all'uso di *Cert-Manager* e HTTPS. Di seguito è riportato un esempio di un `values.yaml` file aggiornato:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Aggiornare l'applicazione di esempio tramite `helm` :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Passare all'applicazione di esempio nello spazio figlio *dev/azureuser1* . si noterà che si viene reindirizzati per l'uso di HTTPS.

> [!IMPORTANT]
> Potrebbero essere necessari 30 minuti o più per completare le modifiche al DNS e l'applicazione di esempio è accessibile.

Si noti inoltre che la pagina viene caricata, ma il browser mostra alcuni errori. L'apertura della console del browser mostra che l'errore si riferisce a una pagina HTTPS che tenta di caricare le risorse HTTP. Ad esempio:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Per correggere l'errore, aggiornare [BikeSharingWeb/azds. YAML][azds-yaml] per usare *traefik* per *kubernetes.io/ingress.Class* e il dominio personalizzato per *$ (hostSuffix)*. Ad esempio:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Aggiornare [BikeSharingWeb/package.json][package-json] con una dipendenza per il pacchetto *URL* .

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Aggiornare il metodo *getApiHostAsync* in [BikeSharingWeb/lib/helpers.js][helpers-js] per usare https:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Passare alla `BikeSharingWeb` Directory e usare `azds up` per eseguire il servizio BikeSharingWeb aggiornato.

```console
cd ../BikeSharingWeb/
azds up
```

Passare all'applicazione di esempio nello spazio figlio *dev/azureuser1* . si noti che si viene reindirizzati per l'uso di HTTPS senza errori.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml