---
title: Quote, SKU e aree di disponibilità in Azure Kubernetes Service (AKS)
description: Informazioni sulle quote predefinite, dimensioni SKU di VM di nodi con restrizioni e disponibilità delle aree di Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: 318846cddecdf020e2e751d3a0b9e05fc83bba73
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614542"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quote, limiti di dimensione di macchina virtuale e disponibilità per area geografica in Azure Kubernetes Service (AKS)

Tutti i servizi di Azure impostare le quote per le risorse e le funzionalità e i limiti predefiniti. Determinati SKU delle macchine virtuali (VM) sono anche limitate per l'uso.

Questo articolo illustra i limiti predefiniti per le risorse di Azure Kubernetes Service (AKS) e la disponibilità del servizio contenitore di AZURE in aree di Azure.

## <a name="service-quotas-and-limits"></a>Quote e limiti del servizio

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastruttura sottoposta a provisioning

Tutte le altre limitazioni relative alla rete, al calcolo e all'archiviazione si applicano all'infrastruttura sottoposta a provisioning. Per le limitazioni previste, vedere [sottoscrizione di Azure e limiti dei servizi](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> Quando si aggiorna un cluster AKS, temporaneamente vengono utilizzate risorse aggiuntive. Queste risorse includono gli indirizzi IP disponibili in una subnet della rete virtuale o quota vCPU della macchina virtuale. Se si usano contenitori di Windows Server (attualmente in anteprima nel servizio contenitore di AZURE), l'unico approccio possibile approvata per applicare gli aggiornamenti più recenti per i nodi consiste nell'eseguire un'operazione di aggiornamento. Un processo di aggiornamento del cluster non riuscita può indicare che non è necessario l'IP indirizzo spazio o vCPU quota disponibile per gestire queste risorse temporanee. Per altre informazioni sul processo di aggiornamento nodo Windows Server, vedere [esegue l'aggiornamento di un pool di nodi in AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Dimensioni delle macchine Virtuali con restrizioni

Ogni nodo in un cluster AKS contiene una quantità fissa di risorse di calcolo, ad esempio memoria e vCPU. Se un nodo AKS contiene le risorse di calcolo sufficienti, POD non venga eseguito correttamente. Per garantire che la necessaria *kube system* POD e le applicazioni in modo affidabile può essere pianificato, non usare i seguenti SKU di VM nel servizio contenitore di AZURE:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Per altre informazioni sui tipi di VM e le relative risorse di calcolo, vedere [dimensioni delle macchine virtuali in Azure][vm-skus].

## <a name="region-availability"></a>Aree di disponibilità

Per l'elenco più recente in cui è possibile distribuire ed eseguire i cluster, vedere [disponibilità delle aree del servizio contenitore di AZURE][region-availability].

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare alcune quote e alcuni limiti predefiniti. Se la risorsa supporta un aumento, richiedere l'aumento tramite un [richiesta di supporto tecnico di Azure][azure-support] (per **tipo di problema**, selezionare **Quota**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
