---
title: Disponibilità elevata di Azure Kubernetes Service (AKS) con contratto di servizio con tempo di esecuzione
description: Informazioni sull'offerta facoltativa del contratto di servizio per la disponibilità elevata per il server API del servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125724"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>CONTRATTO di servizio relativo al tempo di servizio di Azure Kubernetes Service (AKS)

Il contratto di servizio per il tempo di esecuzione è una funzionalità facoltativa che consente di ottenere un contratto di servizio con copertura finanziaria per un cluster Il contratto di servizio del tempo di esecuzione garantisce il 99,95% di disponibilità dell'endpoint server dell'API Kubernetes per i cluster che usano la [zona di disponibilità][availability-zones] e il 99,9% di disponibilità per i cluster che non usano le zone di disponibilità. AKS usa le repliche dei nodi master nei domini di aggiornamento e di errore per garantire che siano soddisfatti i requisiti del contratto di servizio.

I clienti che necessitano di contratti di servizio per motivi di conformità o per estendere i contratti di servizio ai clienti devono attivare questa funzionalità. I clienti con carichi di lavoro critici che necessitano di una maggiore disponibilità con un'opzione di contratto di servizio traggono vantaggio dall'abilitazione di questa funzionalità. Abilitare la funzionalità con zone di disponibilità per ottenere una maggiore disponibilità del server dell'API Kubernetes.  

I clienti possono creare cluster gratuiti senza limiti con un obiettivo del livello di servizio (SLO) del 99,5%.

> [!Important]
> Per i cluster con blocco in uscita, vedere [limitare il traffico in uscita](limit-egress-traffic.md) per aprire le porte appropriate per il contratto di servizio con tempo di esecuzione.

## <a name="sla-terms-and-conditions"></a>Termini e condizioni del contratto di contratto

Il contratto di servizio del tempo di esecuzione è una funzionalità a pagamento e abilitata per cluster. Il prezzo del contratto di servizio relativo al tempo di esecuzione è determinato dal numero di cluster e non dalle dimensioni dei cluster. Per ulteriori informazioni, è possibile visualizzare [i dettagli sui prezzi dei contratti di contratto di tempo](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

## <a name="region-availability"></a>Disponibilità area

Il contratto di esecuzione del tempo di esecuzione è disponibile nelle aree geografiche seguenti:

* Australia orientale
* Canada centrale
* Stati Uniti orientali
* Stati Uniti orientali 2
* Stati Uniti centro-meridionali
* Asia sud-orientale
* Stati Uniti occidentali 2

## <a name="before-you-begin"></a>Prima di iniziare

* L'interfaccia della riga di comando di Azure versione 2.7.0 o successiva

## <a name="creating-a-cluster-with-uptime-sla"></a>Creazione di un cluster con contratto di servizio con tempo di esecuzione

Per creare un nuovo cluster con il contratto di servizio con il tempo di esecuzione, usare l'interfaccia della riga di comando di Azure.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Usare il comando [az aks create][az-aks-create] per creare un cluster del servizio Azure Kubernetes. L'esempio seguente crea un cluster denominato *myAKSCluster* con un nodo. Viene inoltre abilitato Monitoraggio di Azure per i contenitori mediante il parametro *--enable-addons monitoring*.  Il completamento dell'operazione richiede diversi minuti.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster. Il frammento di codice JSON seguente mostra il livello a pagamento per lo SKU, che indica che il cluster è abilitato con contratto di servizio con tempo di esecuzione.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>Limitazioni

* Attualmente non è possibile aggiungere il contratto di servizio relativo al tempo di esecuzione ai cluster esistenti.
* Attualmente, non è possibile rimuovere il contratto di servizio per il tempo di esecuzione da un cluster AKS.  

## <a name="next-steps"></a>Passaggi successivi

Usare [zone di disponibilità][availability-zones] per aumentare la disponibilità elevata con i carichi di lavoro del cluster AKS.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
