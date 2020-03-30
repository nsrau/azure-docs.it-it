---
title: Integrare il Registro di sistema del contenitore di Azure con il servizio Azure KubernetesIntegrate Azure Container Registry with Azure Kubernetes Service
description: Informazioni su come integrare il servizio Azure Kubernetes con Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: f83faf05eb7099557d5b653e0b24591062c44d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368452"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Eseguire l'autenticazione con Registro Azure Container dal servizio Azure Kubernetes

Quando si usa Registro Azure Container con il servizio Azure Kubernetes, è necessario definire un meccanismo di autenticazione. Questo articolo fornisce esempi per la configurazione dell'autenticazione tra questi due servizi di Azure.This article provides examples for configuring authentication between these two Azure services.

È possibile configurare l'integrazione da AKS ad ACR in alcuni semplici comandi con l'interfaccia della riga di comando di Azure.You can set up the AKS to ACR integration in a few simple commands with the Azure CLI.

## <a name="before-you-begin"></a>Prima di iniziare

Gli esempi presentano i requisiti seguenti:

* Ruolo di **amministratore dell'account di Azure** nella sottoscrizione di **AzureOwner** or Azure account administrator role on the **Azure subscription**
* Interfaccia della riga di comando di Azure versione 2.0.73 o successivaAzure CLI version 2.0.73 or later

Per evitare di dover un ruolo di amministratore di account **Proprietario** o **Azure,** è possibile configurare manualmente un'entità servizio o usare un'entità servizio esistente per autenticare ACR da AKS. Per ulteriori informazioni, vedere [Autenticazione ACR con entità servizio](../container-registry/container-registry-auth-service-principal.md) o [Autenticazione da Kubernetes con un segreto pull](../container-registry/container-registry-auth-kubernetes.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Creare un nuovo cluster AKS con l'integrazione ACRCreate a new AKS cluster with ACR integration

È possibile configurare l'integrazione AKS e ACR durante la creazione iniziale del cluster AKS.  Per consentire a un cluster AKS di interagire con ACR, viene usata **un'entità servizio** di Azure Active Directory.To allow an AKS cluster to interact with ACR, an Azure Active Directory service principal is used. Il comando CLI seguente consente di autorizzare un aCR esistente nella sottoscrizione e di configurare il ruolo **ACRPull** appropriato per l'entità servizio. Fornire di seguito valori validi per i parametri specificati di seguito.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

In alternativa, è possibile specificare il nome ACR utilizzando un ID risorsa ACR, con il seguente formato:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Il completamento di questo passaggio potrebbe richiedere alcuni minuti.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Configurare l'integrazione ACR per i cluster AKS esistentiConfigure ACR integration for existing AKS clusters

Integrare un ACR esistente con i cluster AKS esistenti fornendo valori validi per **acr-name** o **acr-resource-id** come indicato di seguito.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

o

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

È inoltre possibile rimuovere l'integrazione tra un aCR e un cluster AKS con i seguenti

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

o

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Utilizzo di ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Importare un'immagine nell'ACR

Importare un'immagine dall'hub docker nel controllo dell'applicazione file di controllo dell'aria eseguendo quanto segue:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Distribuire l'immagine di esempio da ACR ad AKSDeploy the sample image from ACR to AKS

Assicurarsi di disporre delle credenziali AKS corrette

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Creare un file denominato **acr-nginx.yaml** che contiene quanto segue:

```yaml
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

Eseguire quindi questa distribuzione nel cluster AKS:Next, run this deployment in your AKS cluster:

```console
kubectl apply -f acr-nginx.yaml
```

È possibile monitorare la distribuzione eseguendo:You can monitor the deployment by running:

```console
kubectl get pods
```

Dovresti avere due capsule da corsa.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
