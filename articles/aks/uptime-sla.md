---
title: Usare il servizio Azure Kubernetes con il contratto di servizio relativo al tempo di attività
description: Informazioni sull'offerta opzionale del contratto di servizio relativo al tempo di attività per il server API del servizio Azure Kubernetes.
services: container-service
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: references_regions
ms.openlocfilehash: 6523e16bfe3bf0592b78da544d7d52dc3d969af4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88749125"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Servizio Azure Kubernetes: contratto di servizio relativo al tempo di attività

Il contratto di servizio relativo al tempo di attività è una funzionalità facoltativa che consente di ottenere un contratto di servizio con copertura finanziaria più elevata per un cluster. Il contratto di servizio relativo al tempo di attività garantisce il 99,95% di disponibilità dell'endpoint del server dell'API Kubernetes per i cluster che usano [zone di disponibilità][availability-zones] e il 99,9% della disponibilità per i cluster che non usano zone di disponibilità. Il servizio Azure Kubernetes usa le repliche dei nodi master nei domini di aggiornamento e di errore per garantire che siano soddisfatti i requisiti del contratto di servizio.

I clienti che necessitano di un contratto di servizio per soddisfare i requisiti di conformità o richiedere l'estensione di un contratto di servizio agli utenti finali devono abilitare questa funzionalità. Anche i clienti con carichi di lavoro critici che trarranno vantaggio da un contratto di servizio relativo al tempo di attività possono usufruire di questa opzione. L'uso di un contratto di servizio relativo al tempo di attività con zone di disponibilità consente una disponibilità più elevata per il tempo di attività del server dell'API Kubernetes.  

I clienti possono comunque creare cluster gratuiti senza limiti con un obiettivo del livello di servizio pari al 99,5% e optare per il tempo di attività relativo all'obiettivo del livello di servizio o al contratto di servizio in base alle esigenze.

> [!Important]
> Per i cluster con blocco in uscita, vedere la pagina relativa a come [limitare il traffico in uscita](limit-egress-traffic.md) per aprire le porte appropriate.

## <a name="region-availability"></a>Aree di disponibilità

Il contratto di servizio per il tempo di esecuzione è disponibile nelle aree pubbliche e nelle aree di Azure per enti pubblici in cui [è supportato](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)

* Azure Cina 21Vianet attualmente non è supportato.

## <a name="limitations"></a>Limitazioni

* I cluster privati non sono attualmente supportati.

## <a name="sla-terms-and-conditions"></a>Termini e condizioni del contratto di servizio

Il contratto di servizio relativo al tempo di attività è una funzionalità a pagamento abilitata per cluster. Il prezzo del contratto di servizio relativo al tempo di attività è determinato dal numero di cluster discreti e non dalle dimensioni dei singoli cluster. Per altre informazioni, consultare i [dettagli sui prezzi del contratto di servizio relativo al tempo di attività](https://azure.microsoft.com/pricing/details/kubernetes-service/).

## <a name="before-you-begin"></a>Prima di iniziare

* Installare l' [interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest) della riga di comando di Azure versione 2.8.0 o successiva

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Creazione di un nuovo cluster con contratto di servizio con tempo di esecuzione

> [!NOTE]
> Attualmente, se si Abilita il contratto di servizio per il tempo di esecuzione, non è possibile rimuoverlo da un cluster.

Per creare un nuovo cluster con il contratto di servizio relativo al tempo di attività, usare l'interfaccia della riga di comando di Azure.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Usare il [`az aks create`][az-aks-create] comando per creare un cluster AKS. L'esempio seguente crea un cluster denominato *myAKSCluster* con un nodo. Il completamento di questa operazione richiede alcuni minuti:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster. Il frammento di codice JSON seguente mostra il livello a pagamento per lo SKU, che indica che il cluster è abilitato con contratto di servizio con tempo di esecuzione:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Modificare un cluster esistente per usare il contratto di servizio con tempo di esecuzione

Facoltativamente, è possibile aggiornare i cluster esistenti per utilizzare il contratto di servizio con tempo di esecuzione.

Se è stato creato un cluster AKS con i passaggi precedenti, eliminare il gruppo di risorse:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Creare un nuovo gruppo di risorse:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Creare un nuovo cluster e non usare il contratto di servizio per il tempo di esecuzione:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Usare il [`az aks update`][az-aks-nodepool-update] comando per aggiornare il cluster esistente:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Il frammento di codice JSON seguente mostra il livello a pagamento per lo SKU, che indica che il cluster è abilitato con contratto di servizio con tempo di esecuzione:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="clean-up"></a>Eseguire la pulizia

Per evitare addebiti, pulire le risorse create. Per eliminare il cluster, usare il [`az group delete`][az-group-delete] comando per eliminare il gruppo di risorse AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Passaggi successivi

Usare [zone di disponibilità][availability-zones] per aumentare la disponibilità elevata con i carichi di lavoro del cluster del servizio Azure Kubernetes.

Configurare il cluster per [limitare il traffico in uscita](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-group-delete]: /cli/azure/group#az-group-delete
