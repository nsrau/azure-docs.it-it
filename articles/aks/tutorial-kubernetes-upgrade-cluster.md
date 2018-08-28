---
title: Esercitazione su Kubernetes in Azure - Aggiornare un cluster
description: In questa esercitazione sul servizio Kubernetes di Azure (AKS) viene illustrato come aggiornare un cluster AKS esistente alla versione più recente disponibile di Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 11e082ae235706613b0a60b12bc2b27896953508
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "41919577"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Esercitazione: Aggiornare Kubernetes in Azure Kubernetes Service (AKS)

Durante il ciclo di vita dell'applicazione e del cluster, potrebbe essere necessario eseguire l'aggiornamento alla versione più recente disponibile di Kubernetes e usare le nuove funzionalità. È possibile aggiornare un cluster di Azure Kubernetes Service (AKS) tramite l'interfaccia della riga di comando di Azure. Per ridurre al minimo le interruzioni nelle applicazioni in esecuzione, i nodi Kubernetes vengono accuratamente [contrassegnati come non pianificabili e svuotati][kubernetes-drain] durante il processo di aggiornamento.

In questa esercitazione, la settima parte di sette, viene aggiornato un cluster Kubernetes. Si apprenderà come:

> [!div class="checklist"]
> * Identificare le versioni corrente e disponibili di Kubernetes
> * Aggiornare i nodi Kubernetes
> * Convalidare la corretta esecuzione dell'aggiornamento

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. L'applicazione è stata quindi eseguita nel cluster Kubernetes. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Esercitazione 1: Creare immagini del contenitore][aks-tutorial-prepare-app].

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.44 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Ottenere le versioni disponibili del cluster

Prima di aggiornare un cluster, usare il comando [az aks get-upgrades][] per verificare quali versioni di Kubernetes sono disponibili per l'aggiornamento:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

In questo esempio la versione corrente è *1.9.6* e le versioni di aggiornamento disponibili sono indicate nella colonna *Upgrades*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------
default  myResourceGroup  1.9.9            1.9.9              1.10.3, 1.10.5, 1.10.6
```

## <a name="upgrade-a-cluster"></a>Aggiornare un cluster

Usare il comando [az aks upgrade][] per aggiornare il cluster AKS. Nell'esempio seguente il cluster viene aggiornato alla versione *1.10.6* di Kubernetes.

> [!NOTE]
> È possibile aggiornare solo una versione secondaria per volta. È ad esempio possibile eseguire l'aggiornamento da *1.9.6* a *1.10.3*, ma non da *1.9.6* direttamente a *1.11.x*. Per eseguire l'aggiornamento da *1.9.6* a *1.11.x*, eseguirlo prima da *1.9.6* a *1.10.3*, quindi eseguire un altro aggiornamento da *1.10.3* a *1.11.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.6
```

Nell'esempio sintetico di output seguente *kubernetesVersion* è ora *1.10.6*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Convalidare un aggiornamento

Verificare che l'aggiornamento sia stato completato correttamente usando il comando [az aks show][] come di seguito:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L'output di esempio seguente illustra il cluster AKS che esegue *KubernetesVersion 1.10.6*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiornato Kubernetes in un cluster del servizio contenitore di Azure. Si è appreso come:

> [!div class="checklist"]
> * Identificare le versioni corrente e disponibili di Kubernetes
> * Aggiornare i nodi Kubernetes
> * Convalidare la corretta esecuzione dell'aggiornamento

Seguire questo collegamento per altre informazioni sul servizio contenitore di Azure.

> [!div class="nextstepaction"]
> [Panoramica del servizio contenitore di Azure][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli