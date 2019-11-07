---
title: Usare un controller di ingresso traefik personalizzato e configurare HTTPS
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: Informazioni su come configurare Azure Dev Spaces per l'uso di un controller di ingresso traefik personalizzato e configurare HTTPS usando il controller di ingresso
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
ms.openlocfilehash: c015fe8e7108f07d66d2464c4f8b6287e8f54446
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582315"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Usare un controller di ingresso traefik personalizzato e configurare HTTPS

Questo articolo illustra come configurare Azure Dev Spaces per l'uso di un controller di ingresso traefik personalizzato. Questo articolo illustra anche come configurare il controller di ingresso personalizzato per l'uso di HTTPS.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha un account, è possibile [crearne uno gratuito][azure-account-create].
* [L'interfaccia della riga di comando di Azure installata][az-cli].
* [Cluster Azure Kubernetes Service (AKS) con Azure Dev Spaces abilitata][qs-cli].
* [kubectl][kubectl] installato.
* [Helm 2.13 o versioni successive installato][helm-installed].
* [Un dominio personalizzato][custom-domain] con una [zona DNS][dns-zone] nello stesso gruppo di risorse del cluster AKS.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Configurare un controller di ingresso traefik personalizzato

Connettersi al cluster usando [kubectl][kubectl], il client da riga di comando Kubernetes. Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Creare uno spazio dei nomi Kubernetes per il controller di ingresso traefik e installarlo usando `helm`.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

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

```console
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

Aprire [values. YAML][values-yaml] e sostituire tutte le istanze di *< REPLACE_ME_WITH_HOST_SUFFIX >* con *traefik. MY_CUSTOM_DOMAIN* usando il dominio per *MY_CUSTOM_DOMAIN*. Sostituire anche *kubernetes.io/ingress.Class: traefik-azds # dev Spaces-specifico* con *kubernetes.io/ingress.Class: traefik # ingress Custom*. Di seguito è riportato un esempio di un file di `values.yaml` aggiornato:

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

Distribuire l'applicazione di esempio usando `helm install`.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

L'esempio precedente distribuisce l'applicazione di esempio nello spazio dei nomi *dev* .

Selezionare lo spazio di *sviluppo* con l'applicazione di esempio usando `azds space select` e visualizzare gli URL per accedere all'applicazione di esempio usando `azds list-uris`.

```console
azds space select -n dev
azds list-uris
```

L'output seguente Mostra gli URL di esempio di `azds list-uris`.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Passare al servizio *bikesharingweb* aprendo l'URL pubblico dal comando `azds list-uris`. Nell'esempio precedente l'URL pubblico per il servizio *bikesharingweb* è `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

Usare il comando `azds space select` per creare uno spazio figlio in *dev* ed elencare gli URL per accedere allo spazio dev figlio.

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

Passare al servizio *bikesharingweb* nello spazio dev figlio *AZUREUSER1* aprendo l'URL pubblico dal comando `azds list-uris`. Nell'esempio precedente, l'URL pubblico per il servizio *bikesharingweb* nello spazio dev figlio *azureuser1* è `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Configurare il controller di ingresso di traefik per l'uso di HTTPS

Creare un file di `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` simile all'esempio seguente. Aggiornare il valore di *posta elettronica* con il proprio indirizzo di posta elettronica, usato per generare il certificato con la crittografia.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

Aggiornare il servizio *traefik* usando `helm repo update` e includendo il file *traefik-values. YAML* creato.

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

Il comando precedente esegue una nuova versione del servizio traefik usando i valori di *traefik-values. YAML* e rimuove il servizio precedente. Il servizio traefik crea anche un certificato TLS mediante la crittografia e l'avvio del reindirizzamento del traffico Web per l'utilizzo di HTTPS.

> [!NOTE]
> Potrebbero essere necessari alcuni minuti prima che venga avviata la nuova versione del servizio traefik. È possibile controllare lo stato di avanzamento utilizzando `kubectl get pods --namespace traefik --watch`.

Passare all'applicazione di esempio nello spazio figlio *dev/azureuser1* . si noterà che si viene reindirizzati per l'uso di HTTPS. Si noti inoltre che la pagina viene caricata, ma il browser mostra alcuni errori. L'apertura della console del browser mostra che l'errore si riferisce a una pagina HTTPS che tenta di caricare le risorse HTTP. Ad esempio:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Per correggere l'errore, aggiornare [BikeSharingWeb/azds. YAML][azds-yaml] per usare *traefik* per *kubernetes.io/ingress.Class* e il dominio personalizzato per *$ (hostSuffix)* . Ad esempio:

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

Aggiornare [BikeSharingWeb/Package. JSON][package-json] con una dipendenza per il pacchetto *URL* .

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Aggiornare il metodo *getApiHostAsync* in [BikeSharingWeb/Pages/helper. js][helpers-js] per usare https:

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

Passare alla directory `BikeSharingWeb` e usare `azds up` per eseguire il servizio BikeSharingWeb aggiornato.

```console
cd BikeSharingWeb/
azds up
```

Passare all'applicazione di esempio nello spazio figlio *dev/azureuser1* . si noti che si viene reindirizzati per l'uso di HTTPS senza errori.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Sviluppo in team in Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml