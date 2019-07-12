---
title: Aggiornare un cluster del servizio Azure Kubernetes
description: Informazioni su come aggiornare un cluster del servizio Azure Kubernetes
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: dd88b5a044fe495da374178be8774f45bdd30f61
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614054"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Aggiornare un cluster del servizio Azure Kubernetes

Nel corso del ciclo di vita di un cluster del servizio Azure Kubernetes, è spesso necessario eseguire l'aggiornamento alla versione più recente di Kubernetes. È infatti importante applicare gli ultimi aggiornamenti della sicurezza di Kubernetes o eseguire l'aggiornamento per ottenere le funzionalità più nuove. Questo articolo illustra come aggiornare i componenti master o un singolo pool di nodi predefinito in un cluster AKS.

Per AKS i cluster che usano più pool di nodi o i nodi del Server di Windows (sia attualmente in anteprima nel servizio contenitore di AZURE), vedere [esegue l'aggiornamento di un pool di nodi in AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo è necessario eseguire il comando di Azure versione 2.0.65 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificare la presenza di aggiornamenti disponibili per il cluster del servizio Azure Kubernetes

Per controllare quali versioni di Kubernetes sono disponibili per il cluster, usare il [az aks get-aggiornamenti][az-aks-get-upgrades] comando. L'esempio seguente verifica se sono disponibili aggiornamenti per il cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando si aggiorna un cluster del servizio Azure Kubernetes, le versioni secondarie di Kubernetes non possono essere ignorate. Ad esempio, aggiornamenti tra *1.11.x* -> *1.12.x* oppure *1.12.x* -> *1.13.x* sono consentite, tuttavia *1.11.x* -> *1.13.x* non.
>
> Eseguire l'aggiornamento, dalla *1.11.x* -> *1.13.x*, prima l'aggiornamento da *1.11.x* -> *1.12.x*, quindi eseguire l'aggiornamento dal *1.12.x* -> *1.13.x*.

L'output di esempio seguente mostra che il cluster può essere aggiornato alla versione *1.12.7* oppure *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio Azure Container

Con un elenco delle versioni disponibili per il cluster AKS, usare il [aggiornamento az aks][az-aks-upgrade] command to upgrade. During the upgrade process, AKS adds a new node to the cluster that runs the specified Kubernetes version, then carefully [cordon and drains][kubernetes-drain] uno dei nodi del vecchi per minimizzare le interruzioni nelle applicazioni in esecuzione. Quando il nuovo nodo viene confermato come in esecuzione i POD dell'applicazione, il nodo precedente viene eliminato. Questo processo viene ripetuto fino a quando non sono stati aggiornati tutti i nodi del cluster.

Nell'esempio seguente consente di aggiornare un cluster alla versione *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Per aggiornare il cluster sono necessari alcuni minuti, a seconda del numero di nodi di cui si dispone.

Per verificare che l'aggiornamento abbia esito positivo, utilizzare il [show di az aks][az-aks-show] comando:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L'output di esempio seguente mostra che il cluster viene ora eseguita *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come aggiornare un cluster esistente del servizio Azure Kubernetes. Per altre informazioni sulla distribuzione e sulla gestione dei cluster del servizio Azure Kubernetes, vedere le relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni su servizio contenitore di AZURE][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
