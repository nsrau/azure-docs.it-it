---
title: Aggiornare un cluster del servizio Azure Kubernetes
description: Informazioni su come aggiornare un cluster del servizio Azure Kubernetes (AKS) per ottenere le funzionalità e gli aggiornamenti di sicurezza più recenti.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 183e0a85f65d24dc7133307391931bea754a456d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632602"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Aggiornare un cluster del servizio Azure Kubernetes

Nel corso del ciclo di vita di un cluster del servizio Azure Kubernetes, è spesso necessario eseguire l'aggiornamento alla versione più recente di Kubernetes. È infatti importante applicare gli ultimi aggiornamenti della sicurezza di Kubernetes o eseguire l'aggiornamento per ottenere le funzionalità più nuove. In questo articolo viene illustrato come aggiornare i componenti master o un singolo pool di nodi predefinito in un cluster AKS.

Per i cluster AKS che utilizzano più pool di nodi o nodi di Windows Server (attualmente in anteprima in AKS), vedere Aggiornare un pool di [nodi in AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

> [!WARNING]
> Un aggiornamento del cluster AKS attiva un cordone e lo scarico dei nodi. Se è disponibile una quota di calcolo bassa, l'aggiornamento potrebbe non riuscire. Per ulteriori informazioni, vedere [quote di aumento.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)
> Se si esegue la distribuzione autoscaler del cluster, disabilitarla (è possibile ridimensionarla a zero repliche) durante l'aggiornamento, poiché è possibile che interferisca con il processo di aggiornamento. Il ridimensionamento automatico gestito gestisce automaticamente questa situazione. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificare la presenza di aggiornamenti disponibili per il cluster del servizio Azure Kubernetes

Per verificare quali versioni di Kubernetes sono disponibili per il cluster, usare il comando [az aks get-upgrades][az-aks-get-upgrades]. L'esempio seguente verifica se sono disponibili aggiornamenti per il cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando si aggiorna un cluster del servizio Azure Kubernetes, le versioni secondarie di Kubernetes non possono essere ignorate. Ad esempio, sono consentiti aggiornamenti compresi tra *1.12.x* -> *1.13.x* o *1.13.x* -> *1.14.x,* tuttavia *1.12.x* -> *1.14.x.*
>
> Per eseguire l'aggiornamento, da *1.12.x* -> *1.14.x*, eseguire prima l'aggiornamento da *1.12.x* -> *1.13.x*, quindi eseguire l'aggiornamento da *1.13.x* -> *1.14.x*.

L'output di esempio seguente mostra che il cluster può essere aggiornato alle versioni *1.13.9* e *1.13.10:*

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Se non è disponibile alcun aggiornamento, si otterrà:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio Azure Container

Con un elenco di versioni disponibili per il cluster del servizio Azure Kubernetes, usare il comando [az aks upgrade][az-aks-upgrade] per eseguire l'aggiornamento. Durante il processo di aggiornamento, AKS aggiunge un nuovo nodo al cluster che esegue la versione Kubernetes specificata, quindi [cordone][kubernetes-drain] con attenzione e scarica uno dei nodi precedenti per ridurre al minimo l'interruzione alle applicazioni in esecuzione. Quando il nuovo nodo viene confermato come pod dell'applicazione in esecuzione, il nodo precedente viene eliminato. Questo processo si ripete fino a quando tutti i nodi del cluster sono stati aggiornati.

L'esempio seguente aggiorna un cluster alla versione *1.13.10:*

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Per aggiornare il cluster sono necessari alcuni minuti, a seconda del numero di nodi di cui si dispone. 

> [!NOTE]
> Il completamento di un aggiornamento del cluster è totale consentito. Questo tempo viene calcolato prendendo `10 minutes * total number of nodes in the cluster`il prodotto di . Ad esempio, in un cluster a 20 nodi, le operazioni di aggiornamento devono avere esito positivo in 200 minuti o AKS non riuscirà l'operazione per evitare uno stato del cluster irreversibile. Per eseguire il ripristino in caso di errore di aggiornamento, ripetere l'operazione di aggiornamento dopo il timeout.

Per verificare che l'aggiornamento sia stato completato correttamente, usare il comando [az aks show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L'output di esempio seguente mostra che il cluster viene ora eseguito *1.13.10:*

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come aggiornare un cluster esistente del servizio Azure Kubernetes. Per altre informazioni sulla distribuzione e sulla gestione dei cluster del servizio Azure Kubernetes, vedere le relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni sul servizio Azure Kubernetes][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
