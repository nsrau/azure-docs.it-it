---
title: Sviluppare in Azure Kubernetes Service (AKS) con Helm
description: Usare Helm con AKS e Azure Container Registry per creare un pacchetto ed eseguire i contenitori di applicazioni in un cluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 1f67605918e093e9ab28aa88be777d27acd831ef
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82169569"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Guida introduttiva: sviluppare in Azure Kubernetes Service (AKS) con Helm

[Helm][helm] è uno strumento per la creazione di pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente agli strumenti di gestione pacchetti di Linux, come *APT* e *Yum*, Helm viene usato per gestire i grafici per Kubernetes, che sono pacchetti di risorse Kubernetes preconfigurate.

Questo articolo illustra come usare Helm per creare un pacchetto ed eseguire un'applicazione in AKS. Per altre informazioni sull'installazione di un'applicazione esistente con Helm, vedere [installare applicazioni esistenti con Helm in AKS][helm-existing].

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker installato e configurato. Docker offre pacchetti che consentono di configurare Docker in un sistema [Mac][docker-for-mac], [Windows][docker-for-windows] o [Linux][docker-for-linux].
* [Helm v3 installato][helm-install].

## <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container
Per usare Helm per eseguire l'applicazione nel cluster AKS, è necessario un Container Registry di Azure per archiviare le immagini del contenitore. L'esempio seguente usa [AZ ACR create][az-acr-create] per creare un record di record di registro denominato *MyHelmACR* nel gruppo di risorse *MyResourceGroup* con lo SKU *Basic* . È necessario specificare un nome univoco per il registro di sistema. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Lo SKU *Basic* è un punto di ingresso con costi ottimali a fini di sviluppo, che assicura l'equilibrio tra spazio di archiviazione e velocità effettiva.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

L'output è simile all'esempio seguente: Prendere nota del valore di *loginServer* per il record di registro di sistema perché verrà usato in un passaggio successivo. Nell'esempio seguente *myhelmacr.azurecr.io* è il *loginServer* per *myhelmacr*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Per usare l'istanza di ACR, è prima necessario eseguire l'accesso. Usare il comando [AZ ACR login][az-acr-login] per accedere. L'esempio seguente consente di accedere a un record di record di accesso denominato *MyHelmACR*.

```azurecli
az acr login --name MyHelmACR
```

Al termine, il comando restituisce un messaggio di *accesso riuscito* .

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

Creare un cluster del servizio Azure Kubernetes. Il comando seguente crea un cluster AKS denominato MyAKS e connette MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Per eseguire il pull delle immagini del contenitore ed eseguirle, il cluster AKS deve accedere al record di record di accesso. Il comando precedente concede anche l'accesso al cluster *MyAKS* al *MyHelmACR* ACR.

## <a name="connect-to-your-aks-cluster"></a>Connettersi al cluster AKS

Per connettersi al cluster Kubernetes dal computer locale si usa [kubectl][kubectl], il client da riga di comando di Kubernetes.

Se si usa Azure Cloud Shell, `kubectl` è già installato. È anche possibile installarlo in locale usando il comando [az servizio Azure Kubernetes install-cli][]:

```azurecli
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][]. Nell'esempio seguente vengono ottenute le credenziali per il cluster AKS denominato *MyAKS* in *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

Questa Guida introduttiva usa [un'applicazione Node. js di esempio dal repository di esempio Azure Dev Spaces][example-nodejs]. Clonare l'applicazione da GitHub e passare alla `dev-spaces/samples/nodejs/getting-started/webfrontend` directory.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Creare un Dockerfile

Creare un nuovo file *Dockerfile* usando quanto segue:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Compilare ed eseguire il push dell'applicazione di esempio in ACR

Ottenere l'indirizzo del server di accesso usando il comando [AZ ACR list][az-acr-list] e eseguendo una query per il *loginServer*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Usare Docker per compilare, contrassegnare ed effettuare il push del contenitore di applicazioni di esempio nell'ACR:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Creare il grafico Helm

Generare il grafico Helm usando il `helm create` comando.

```console
helm create webfrontend
```

Effettuare gli aggiornamenti seguenti a *WebFrontEnd/values. YAML*:

* Modificare `image.repository` in `<acrLoginServer>/webfrontend`
* Modificare `service.type` in `LoadBalancer`

Ad esempio:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Eseguire `appVersion` l' `v1` aggiornamento a in *WebFrontEnd/Chart. YAML*. Ad esempio

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Eseguire il grafico Helm

Usare il `helm install` comando per installare l'applicazione usando il grafico Helm.

```console
helm install webfrontend webfrontend/
```

Ci vogliono alcuni minuti perché il servizio restituisca un indirizzo IP pubblico. Per monitorare lo stato di avanzamento, usare il comando `kubectl get service` con il parametro *espressione di controllo*:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Passare al servizio di bilanciamento del carico dell'applicazione in un browser usando `<EXTERNAL-IP>` per visualizzare l'applicazione di esempio.

## <a name="delete-the-cluster"></a>Eliminare il cluster

Quando il cluster non è più necessario, usare il comando [AZ Group Delete][az-group-delete] per rimuovere il gruppo di risorse, il cluster AKS, il registro contenitori, le immagini del contenitore archiviate e tutte le risorse correlate.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete]. Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo di Helm, vedere la documentazione di Helm.

> [!div class="nextstepaction"]
> [Documentazione di Helm][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az servizio Azure Kubernetes get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az servizio Azure Kubernetes install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
