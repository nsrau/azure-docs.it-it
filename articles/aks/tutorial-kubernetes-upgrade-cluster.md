---
title: Esercitazione su Kubernetes in Azure - Aggiornare un cluster
description: In questa esercitazione sul servizio Azure Kubernetes viene illustrato come aggiornare un cluster servizio Azure Kubernetes esistente alla versione più recente disponibile di Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 9fe02c9b563259abb51a1a768c7facdf1bf601f7
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898829"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Esercitazione: Aggiornare Kubernetes nel servizio Azure Kubernetes

Durante il ciclo di vita dell'applicazione e del cluster, potrebbe essere necessario eseguire l'aggiornamento alla versione più recente disponibile di Kubernetes e usare le nuove funzionalità. È possibile aggiornare un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure.

In questa esercitazione, la settima parte di sette, viene aggiornato un cluster Kubernetes. Si apprenderà come:

> [!div class="checklist"]
> * Identificare le versioni corrente e disponibili di Kubernetes
> * Aggiornare i nodi Kubernetes
> * Convalidare la corretta esecuzione dell'aggiornamento

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore. L'immagine è stata poi caricata in Registro Azure Container ed è stato creato un cluster del servizio Azure Kubernetes. L'applicazione è stata quindi distribuita nel cluster del servizio Azure Kubernetes. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, iniziare con l'[Esercitazione 1: Creare immagini del contenitore][aks-tutorial-prepare-app].

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.53 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Ottenere le versioni disponibili del cluster

Prima di aggiornare un cluster, usare il comando [az servizio Azure Kubernetes get-upgrades][] per verificare quali versioni di Kubernetes sono disponibili per l'aggiornamento:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

In questo esempio la versione corrente è *1.13.10* e le versioni disponibili sono indicate nella colonna *Upgrades*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.13.10          1.13.10            1.14.5, 1.14.6
```

## <a name="upgrade-a-cluster"></a>Aggiornare un cluster

Per ridurre al minimo le interruzioni nelle applicazioni in esecuzione, i nodi del servizio Azure Kubernetes vengono accuratamente bloccati e svuotati. In questo processo vengono eseguiti i passaggi seguenti:

1. L'utilità di pianificazione di Kubernetes impedisce la pianificazione di altri pod in un nodo che deve essere aggiornato.
1. I pod in esecuzione nel nodo vengono pianificati in altri nodi del cluster.
1. Viene creato un nodo che esegue i componenti più recenti di Kubernetes.
1. Quando il nuovo nodo è pronto ed è stato aggiunto al cluster, l'utilità di pianificazione di Kubernetes avvia l'esecuzione dei pod in tale nodo.
1. Il nodo precedente viene eliminato e il nodo successivo del cluster avvia il processo di blocco e svuotamento.

Usare il comando [az servizio Azure Kubernetes upgrade][] per aggiornare il cluster servizio Azure Kubernetes. Nell'esempio seguente il cluster viene aggiornato alla versione *1.14.6* di Kubernetes.

> [!NOTE]
> È possibile aggiornare solo una versione secondaria per volta. È ad esempio possibile eseguire l'aggiornamento da *1.12.x* a *1.13.x*, ma non da *1.12.x* direttamente a *1.14.x*. Per eseguire l'aggiornamento da *1.12* a *1.14.x*, eseguirlo prima da *1.12* a *1.13.x*, quindi eseguire un altro aggiornamento da *1.13.x* a *1.14.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.14.6
```

Nell'esempio sintetico di output seguente il valore di *kubernetesVersion* è ora *1.14.6*:

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
  "kubernetesVersion": "1.14.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Convalidare un aggiornamento

Verificare che l'aggiornamento sia stato completato correttamente usando il comando [az servizio Azure Kubernetes show][] come di seguito:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L'output di esempio seguente illustra il cluster del servizio Azure Kubernetes che esegue *KubernetesVersion 1.14.6*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.14.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Eliminare il cluster

Dal momento che questa esercitazione è l'ultima parte della serie, può essere opportuno eliminare il cluster del servizio Azure Kubernetes. Siccome i nodi Kubernetes vengono eseguiti sulle macchine virtuali Azure (VM), i costi correlati continueranno a essere addebitati anche quando il cluster non è più usato. Usare il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiornato Kubernetes in un cluster del servizio Azure Container. Si è appreso come:

> [!div class="checklist"]
> * Identificare le versioni corrente e disponibili di Kubernetes
> * Aggiornare i nodi Kubernetes
> * Convalidare la corretta esecuzione dell'aggiornamento

Seguire questo collegamento per altre informazioni sul servizio Azure Container.

> [!div class="nextstepaction"]
> [Panoramica del servizio Azure Container][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az servizio Azure Kubernetes show]: /cli/azure/aks#az-aks-show
[az servizio Azure Kubernetes get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az servizio Azure Kubernetes upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
