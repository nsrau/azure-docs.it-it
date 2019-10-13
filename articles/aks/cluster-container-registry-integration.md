---
title: Integrare Container Registry di Azure con il servizio Azure Kubernetes
description: Informazioni su come integrare Azure Kubernetes Service (AKS) con Azure Container Registry (ACR)
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: bbd08e49256886a1df334cbf36e6e468bb8f3895
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286776"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Eseguire l'autenticazione con Registro Azure Container dal servizio Azure Kubernetes

Quando si usa Registro Azure Container con il servizio Azure Kubernetes, è necessario definire un meccanismo di autenticazione. Questo articolo fornisce esempi per la configurazione dell'autenticazione tra questi due servizi di Azure.

Con l'interfaccia della riga di comando di Azure è possibile configurare l'integrazione da AKS a ACR con pochi semplici comandi.

## <a name="before-you-begin"></a>Prima di iniziare

Questi esempi richiedono:

* **Proprietario** o ruolo di **amministratore dell'account di Azure** nella sottoscrizione di **Azure**
* Versione 2.0.73 o successiva dell'interfaccia della riga di comando di Azure

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Creare un nuovo cluster AKS con l'integrazione con ACR

È possibile configurare l'integrazione di AKS e ACR durante la creazione iniziale del cluster AKS.  Per consentire a un cluster AKS di interagire con ACR, viene usata un' **entità servizio** Azure Active Directory. Il seguente comando dell'interfaccia della riga di comando consente di autorizzare un registro di sistema esistente nella sottoscrizione e di configurare il ruolo **ACRPull** appropriato per l'entità servizio. Specificare i valori validi per i parametri indicati di seguito.  I parametri tra parentesi quadre sono facoltativi.
```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --attach-acr $MYACR

```
In alternativa, è possibile specificare il nome dell'ACR usando un ID di risorsa ACR, che ha il formato seguente:

/Subscriptions/\<subscription-ID @ no__t-1/resourceGroups/\<resource-Group-Name @ no__t-3/Providers/Microsoft. ContainerRegistry/registris/\<name @ no__t-5 
 
```azurecli
az aks create -n myAKSCluster -g myResourceGroup --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Il completamento di questo passaggio può richiedere alcuni minuti.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Configurare l'integrazione di ACR per i cluster AKS esistenti

Integrare un ACR esistente con i cluster AKS esistenti fornendo valori validi per **ACR-Name** o **ACR-Resource-ID** come indicato di seguito.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
o
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

È anche possibile rimuovere l'integrazione tra un ACR e un cluster AKS con i seguenti
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
oppure
```
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Uso di ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importare un'immagine nell'ACR

Importare un'immagine dall'hub Docker nell'ACR eseguendo questa procedura:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Distribuire l'immagine di esempio da ACR ad AKS

Assicurarsi di disporre delle credenziali AKS appropriate

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Creare un file denominato **ACR-nginx. YAML** che contenga quanto segue:

```
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
```

Eseguire quindi questa distribuzione nel cluster AKS:
```
kubectl apply -f acr-nginx.yaml
```

È possibile monitorare la distribuzione eseguendo:
```
kubectl get pods
```
Sono presenti due pod in esecuzione.
```
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
