---
title: Versioni Kubernetes supportate nel servizio Azure Kubernetes
description: Comprendere i criteri di supporto di della versione di Kubernetes e il ciclo di vita dei cluster nel servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 09/21/2018
ms.author: saudas
ms.openlocfilehash: 3e8342a719bf9ae7174195f88b97972d7f13193c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465786"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versioni Kubernetes supportate nel servizio Azure Kubernetes

La community di Kubernetes rilascia le versioni secondarie all'incirca ogni tre mesi. Queste versioni includono miglioramenti e nuove funzionalità. Le versioni delle patch sono più frequenti (in alcuni casi settimanali) e sono destinate esclusivamente a correzioni di bug importanti in una versione secondaria. Le versioni delle patch consentono di risolvere le vulnerabilità della sicurezza o i bug rilevanti con impatto su un numero elevato di clienti e prodotti in esecuzione in ambienti di produzione basati su Kubernetes.

Una versione secondaria di Kubernetes è resa disponibile in [aks-engine][aks-engine] dal primo giorno. L'obiettivo del livello di servizio (SLO) del servizio Azure Kubernetes è quello di rilasciare la versione secondaria per i cluster servizio Azure Kubernetes entro 30 giorni, fermo restando la stabilità della versione.

## <a name="kubernetes-version-support-policy"></a>Criteri di supporto della versione di Kubernetes

Il servizio Azure Kubernetes supporta quattro versioni secondarie di Kubernetes:

- La versione secondaria corrente che viene rilasciata upstream (n)
- Tre versioni secondarie precedenti. Ogni versione secondaria supportata supporta anche due patch stabili.

Se, ad esempio, il servizio Azure Kubernetes introduce oggi *1.11.x*, il supporto viene fornito anche per *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8F* (in cui le versioni delle patch sono le due build stabili più recenti).

Quando viene introdotta una nuova versione secondaria, vengono ritirate la versioni secondaria precedente e le versioni delle patch supportate. 15 giorni prima del rilascio della nuova versione secondaria e il futuro ritiro della versione, viene fatto un annuncio tramite i [canali di aggiornamento di Azure][azure-update-channel]. Nell'esempio precedente in cui viene rilasciata la versione *1.11.x*, le versioni ritirate sono *1.7.g* + *1.7.h*.

Quando si distribuisce un cluster del servizio Azure Kubernetes nel portale o con l'interfaccia della riga di comando di Azure, il cluster è sempre impostato sulla versione secondaria n-1 e la patch più recente. Se, ad esempio, il servizio Azure Kubernetes supporta *1.11.x*, *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8F*, la versione predefinita per i nuovi cluster è *1.10.b*.

## <a name="list-currently-supported-versions"></a>Elencare le versioni attualmente supportate

Per scoprire quali versioni sono attualmente disponibili per la sottoscrizione e l'area in uso, usare il comando [az servizio Azure Kubernetes get-versions][az-aks-get-versions]. L'esempio seguente elenca le versioni di Kubernetes disponibili per l'area *EastUS*:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

L'output è simile all'esempio seguente, in cui è mostrato che la versione *1.12.4* di Kubernetes è la versione più recente disponibile:

```
KubernetesVersion    Upgrades
-------------------  -----------------------
1.12.4               None available
1.11.6               1.12.4
1.11.5               1.11.6, 1.12.4
1.10.12              1.11.5, 1.11.6
1.10.9               1.10.12, 1.11.5, 1.11.6
1.9.11               1.10.9, 1.10.12
1.9.10               1.9.11, 1.10.9, 1.10.12
1.8.15               1.9.10, 1.9.11
1.8.14               1.8.15, 1.9.10, 1.9.11
```

## <a name="faq"></a>Domande frequenti

**Cosa accade quando un cliente esegue l'aggiornamento di un cluster Kubernetes con una versione secondaria che non è supportata?**

Se si usa la versione *n-4*, si è all'esterno dello SLO. Se l'aggiornamento dalla versione n-4 a n-3 ha esito positivo, si è nuovamente dello SLO. Ad esempio: 

- Se le versioni supportate del servizio Azure Kubernetes sono *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8F* e si usa *1.7.g* o *1.7.h*, si è all'esterno dello SLO.
- Se l'aggiornamento da *1.7.g* o *1.7.h* a *1.8.e* o *1.8.f* ha esito positivo, si è nuovamente dello SLO.

Gli aggiornamenti alle versioni precedenti a *n-4* non sono supportati. In questi casi, è consigliabile creare nuovi cluster del servizio Azure Kubernetes e ridistribuire i carichi di lavoro.

**Cosa accade quando un cliente ridimensiona un cluster Kubernetes con una versione secondaria che non è supportata?**

Per le versioni secondarie non supportate dal servizio Azure Kubernetes, il ridimensionamento o la scalabilità orizzontale continuano a funzionare senza problemi.

**Un cliente può usare permanentemente una versione di Kubernetes?**

Sì. Tuttavia, se il cluster non è in una delle versioni supportate dal servizio Azure Kubernetes, esso è all'esterno dello SLO servizio Azure Kubernetes. Azure non aggiorna o elimina automaticamente il cluster.

**Quale versione effettua il supporto del master se il cluster agente non è presente in una delle versioni supportate del servizio Azure Kubernetes?**

Il master viene aggiornato automaticamente alla versione supportata più recente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiornare il cluster, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
