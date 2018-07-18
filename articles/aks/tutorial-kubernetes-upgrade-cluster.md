---
title: 'Esercitazione su Kubernetes in Azure: aggiornare un cluster'
description: 'Esercitazione su Kubernetes in Azure: aggiornare un cluster'
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d66197b69a0804a49fabb72e9b97c77e000bdf88
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131644"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Esercitazione: Aggiornare Kubernetes in Azure Kubernetes Service (AKS)

È possibile aggiornare un cluster di Azure Kubernetes Service (AKS) tramite l'interfaccia della riga di comando di Azure. Per ridurre al minimo le interruzioni nelle applicazioni in esecuzione, i nodi Kubernetes vengono accuratamente [contrassegnati come non pianificabili e svuotati][kubernetes-drain] durante il processo di aggiornamento.

In questa esercitazione, la settima parte di sette, viene aggiornato un cluster Kubernetes. Le attività da completare comprendono:

> [!div class="checklist"]
> * Identificare le versioni corrente e disponibili di Kubernetes
> * Aggiornare i nodi Kubernetes
> * Convalidare la corretta esecuzione dell'aggiornamento

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. L'applicazione è stata quindi eseguita nel cluster Kubernetes.

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Esercitazione 1: Creare immagini del contenitore][aks-tutorial-prepare-app].

## <a name="get-cluster-versions"></a>Ottenere le versioni del cluster

Prima di aggiornare un cluster, usare il comando [az aks get-upgrades][] per verificare quali versioni di Kubernetes sono disponibili per l'aggiornamento.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

In questo esempio la versione corrente del nodo è *1.9.6* e le versioni di aggiornamento disponibili sono indicate nella colonna *Upgrades* (Aggiornamenti).

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------
default  myResourceGroup  1.9.6            1.9.6              1.10.3
```

## <a name="upgrade-cluster"></a>Aggiornare il cluster

Usare il comando [az aks upgrade][] per aggiornare i nodi del cluster. Nell'esempio seguente il cluster viene aggiornato alla versione *1.10.3*.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.10.3
```

Nell'esempio sintetico di output seguente la *kubernetesVersion* ora segnala *1.10.3*:

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
  "kubernetesVersion": "1.10.3",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Convalidare l'aggiornamento

Verificare che l'aggiornamento sia stato completato correttamente con il comando [az aks show][].

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Output:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.3               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiornato Kubernetes in un cluster del servizio contenitore di Azure. Sono state completate le attività seguenti:

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