---
title: Usare il servizio Azure Kubernetes con il contratto di servizio relativo al tempo di attività
description: Informazioni sull'offerta opzionale del contratto di servizio relativo al tempo di attività per il server API del servizio Azure Kubernetes.
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: references_regions
ms.openlocfilehash: 2df0ad675f03b25363ab0f5b13dceb762a657ed7
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299554"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Servizio Azure Kubernetes: contratto di servizio relativo al tempo di attività

Il contratto di servizio relativo al tempo di attività è una funzionalità facoltativa che consente di ottenere un contratto di servizio con copertura finanziaria più elevata per un cluster. Il contratto di servizio relativo al tempo di attività garantisce il 99,95% di disponibilità dell'endpoint del server dell'API Kubernetes per i cluster che usano [zone di disponibilità][availability-zones] e il 99,9% della disponibilità per i cluster che non usano zone di disponibilità. Il servizio Azure Kubernetes usa le repliche dei nodi master nei domini di aggiornamento e di errore per garantire che siano soddisfatti i requisiti del contratto di servizio.

I clienti che necessitano di un contratto di servizio per soddisfare i requisiti di conformità o richiedere l'estensione di un contratto di servizio agli utenti finali devono abilitare questa funzionalità. Anche i clienti con carichi di lavoro critici che trarranno vantaggio da un contratto di servizio relativo al tempo di attività possono usufruire di questa opzione. L'uso di un contratto di servizio relativo al tempo di attività con zone di disponibilità consente una disponibilità più elevata per il tempo di attività del server dell'API Kubernetes.  

I clienti possono comunque creare cluster gratuiti senza limiti con un obiettivo del livello di servizio pari al 99,5% e optare per il tempo di attività relativo all'obiettivo del livello di servizio o al contratto di servizio in base alle esigenze.

> [!Important]
> Per i cluster con blocco in uscita, vedere la pagina relativa a come [limitare il traffico in uscita](limit-egress-traffic.md) per aprire le porte appropriate.

## <a name="sla-terms-and-conditions"></a>Termini e condizioni del contratto di servizio

Il contratto di servizio relativo al tempo di attività è una funzionalità a pagamento abilitata per cluster. Il prezzo del contratto di servizio relativo al tempo di attività è determinato dal numero di cluster discreti e non dalle dimensioni dei singoli cluster. Per altre informazioni, consultare i [dettagli sui prezzi del contratto di servizio relativo al tempo di attività](https://azure.microsoft.com/pricing/details/kubernetes-service/).

## <a name="before-you-begin"></a>Prima di iniziare

* Interfaccia della riga di comando di Azure, versione 2.7.0 o successiva

## <a name="creating-a-cluster-with-uptime-sla"></a>Creazione di un cluster con il contratto di servizio relativo al tempo di attività

Per creare un nuovo cluster con il contratto di servizio relativo al tempo di attività, usare l'interfaccia della riga di comando di Azure.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Usare il comando [az aks create][az-aks-create] per creare un cluster del servizio Azure Kubernetes. L'esempio seguente crea un cluster denominato *myAKSCluster* con un nodo. Viene inoltre abilitato Monitoraggio di Azure per i contenitori mediante il parametro *--enable-addons monitoring*.  Il completamento dell'operazione richiede diversi minuti.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster. Il frammento di codice JSON seguente mostra il livello a pagamento per lo SKU, che indica che il cluster è abilitato con contratto di servizio relativo al tempo di attività.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>Limitazioni

* Attualmente non è consentita la conversione come cluster esistente per abilitare il contratto di servizio relativo al tempo di attività.
* Attualmente non è possibile rimuovere il contratto di servizio relativo al tempo di attività da un cluster del servizio Azure Kubernetes dopo la creazione con abilitazione.  
* I cluster privati non sono attualmente supportati.

## <a name="next-steps"></a>Passaggi successivi

Usare [zone di disponibilità][availability-zones] per aumentare la disponibilità elevata con i carichi di lavoro del cluster del servizio Azure Kubernetes.
Configurare il cluster per [limitare il traffico in uscita](limit-egress-traffic.md).

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
