---
title: Aggiornare un cluster del servizio Azure Kubernetes
description: Informazioni su come aggiornare un cluster del servizio Azure Kubernetes
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028441"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Aggiornare un cluster del servizio Azure Kubernetes

Nel corso del ciclo di vita di un cluster del servizio Azure Kubernetes, è spesso necessario eseguire l'aggiornamento alla versione più recente di Kubernetes. È infatti importante applicare gli ultimi aggiornamenti della sicurezza di Kubernetes o eseguire l'aggiornamento per ottenere le funzionalità più nuove. Questo articolo illustra come aggiornare un cluster esistente del servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede che sia in esecuzione l'interfaccia della riga di comando di Azure 2.0.56 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificare la presenza di aggiornamenti disponibili per il cluster del servizio Azure Kubernetes

Per verificare quali versioni di Kubernetes sono disponibili per il cluster, usare il comando [az aks get-upgrades][az-aks-get-upgrades]. L'esempio seguente verifica se sono disponibili aggiornamenti per il cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando si aggiorna un cluster del servizio Azure Kubernetes, le versioni secondarie di Kubernetes non possono essere ignorate. Sono consentiti, ad esempio, gli aggiornamenti *1.10.x* -> *1.11.x* o *1.11.x* -> *1.12.x*, ma non *1.10.x* -> *1.12.x*.
>
> Per eseguire l'aggiornamento *1.10.x* -> *1.12.x*, eseguire prima l'aggiornamento *1.10.x* -> *1.11.x* e quindi l'aggiornamento *1.11.x* -> *1.12.x*.

L'output di esempio seguente mostra che il cluster può essere aggiornato alla versione *1.11.5* o *1.11.6*:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio Azure Container

Con un elenco di versioni disponibili per il cluster del servizio Azure Kubernetes, usare il comando [az aks upgrade][az-aks-upgrade] per eseguire l'aggiornamento. Durante il processo di aggiornamento, il servizio Azure Kubernetes aggiunge un nuovo nodo al cluster e quindi [blocca e svuota][kubernetes-drain] attentamente un nodo alla volta per ridurre al minimo le interruzioni delle applicazioni in esecuzione. Nell'esempio seguente un cluster viene aggiornato alla versione *1.11.6*:

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

Per aggiornare il cluster sono necessari alcuni minuti, a seconda del numero di nodi di cui si dispone.

Per verificare che l'aggiornamento sia stato completato correttamente, usare il comando [az aks show][az-aks-show]:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L'output di esempio seguente mostra che ora nel cluster viene eseguita la versione *1.11.6*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come aggiornare un cluster esistente del servizio Azure Kubernetes. Per altre informazioni sulla distribuzione e sulla gestione dei cluster del servizio Azure Kubernetes, vedere le relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni sul servizio Azure Kubernetes][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-upgrades]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-upgrade]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-upgrade
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show