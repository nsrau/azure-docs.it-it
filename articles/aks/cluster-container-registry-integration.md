---
title: Integrare Container Registry di Azure con il servizio Azure Kubernetes
description: Informazioni su come integrare Azure Kubernetes Service (AKS) con Azure Container Registry (ACR)
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: ec017901e36a01042485e9aeca2431c8a6838ab8
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536755"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Anteprima-eseguire l'autenticazione con Container Registry di Azure dal servizio Azure Kubernetes

Quando si usa Registro Azure Container con il servizio Azure Kubernetes, è necessario definire un meccanismo di autenticazione. Questo articolo illustra in dettaglio le configurazioni consigliate per l'autenticazione tra questi due servizi di Azure.

Con l'interfaccia della riga di comando di Azure è possibile configurare l'integrazione da AKS a ACR con pochi semplici comandi.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS](support-policies.md)
> * [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="before-you-begin"></a>Prima di iniziare

È necessario disporre di quanto segue:

* **Proprietario** o ruolo di **amministratore dell'account di Azure** nella sottoscrizione di **Azure**
* È necessaria anche l'interfaccia della riga di comando di Azure versione 2.0.70 o successiva e l'estensione AKS-Preview 0.4.8
* È necessario che Docker sia [installato](https://docs.docker.com/install/) nel client ed è necessario accedere all' [Hub Docker](https://hub.docker.com/)

## <a name="install-latest-aks-cli-preview-extension"></a>Installare la versione di anteprima dell'interfaccia della riga di comando AKS

È necessaria l'estensione **0.4.8 di AKS-Preview** o successiva.

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Creare un nuovo cluster AKS con l'integrazione con ACR

È possibile configurare l'integrazione di AKS e ACR durante la creazione iniziale del cluster AKS.  Per consentire a un cluster AKS di interagire con ACR, viene usata un' **entità servizio** Azure Active Directory. Il comando CLI seguente consente di creare un record di controllo di errore nel gruppo di risorse specificato e di configurare il ruolo **ACRPull** appropriato per l'entità servizio. Se il *nome* del registro contenitori non esiste, viene creato automaticamente un `aks<resource-group>acr` nome di ACR predefinito.  Specificare i valori validi per i parametri indicati di seguito.  I parametri tra parentesi quadre sono facoltativi.
```azurecli
az login
az aks create -n myAKSCluster -g myResourceGroup --enable-acr [--acr <acr-name-or-resource-id>]
```
Il completamento di questo passaggio può richiedere alcuni minuti.

## <a name="create-acr-integration-for-existing-aks-clusters"></a>Creare l'integrazione ACR per i cluster AKS esistenti

Integrare ACR con cluster ACR esistenti fornendo valori validi per **ACR-Name** e **ACR-Resource-ID** di seguito.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acr-resource-id>
```

## <a name="log-in-to-your-acr"></a>Accedere a ACR

Usare il comando seguente per accedere al record di controllo di accesso.  Sostituire il <acrname> parametro con il nome di ACR.  Ad esempio, il valore predefinito è **aks < il gruppo di risorse > ACR**.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Eseguire il pull di un'immagine dall'hub Docker ed eseguire il push nel record di registro contenitori

Eseguire il pull di un'immagine dall'hub Docker, contrassegnare l'immagine ed eseguirne il push nell'ACR.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>Aggiornare lo stato e verificare i Pod

Per verificare la distribuzione, seguire questa procedura.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Visualizzare il file YAML e modificare la proprietà Image sostituendo il valore con il server di accesso di ACR, l'immagine e il tag.

```
$ cat acr-nginx.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create