---
title: Limiti per risorse, SKU, aree
titleSuffix: Azure Kubernetes Service
description: Informazioni sulle quote predefinite, sulle dimensioni SKU delle macchine virtuali del nodo limitato e sulla disponibilità dell'area del servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 054d6ff4fc105d84192ac81feda97515f6cfae49
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886773"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quote, restrizioni sulle dimensioni delle macchine virtuali e disponibilità dell'area geografica nel servizio Azure Kubernetes (AKS)Quotas, virtual machine size restrictions, and region availability in Azure Kubernetes Service (AKS)

Tutti i servizi di Azure impostano limiti e quote predefiniti per risorse e funzionalità. Anche alcuni SKU di macchine virtuali (VM) sono limitati per l'uso.

Questo articolo descrive in dettaglio i limiti delle risorse predefinite per le risorse del servizio Azure Kubernetes (AKS) e la disponibilità di AKS nelle aree di Azure.This article details the default resource limits for Azure Kubernetes Service (AKS) resources and the availability of AKS in Azure regions.

## <a name="service-quotas-and-limits"></a>Quote e limiti del servizio

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastruttura sottoposta a provisioning

Tutte le altre limitazioni relative alla rete, al calcolo e all'archiviazione si applicano all'infrastruttura sottoposta a provisioning. Per i limiti rilevanti, vedere Limiti della sottoscrizione e del servizio di [Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md)

> [!IMPORTANT]
> Quando si aggiorna un cluster AKS, vengono temporaneamente utilizzate risorse aggiuntive. Queste risorse includono gli indirizzi IP disponibili in una subnet di rete virtuale o la quota vCPU della macchina virtuale. Se si utilizzano contenitori di Windows Server (attualmente in anteprima in AKS), l'unico approccio approvato per applicare gli aggiornamenti più recenti ai nodi consiste nell'eseguire un'operazione di aggiornamento. Un processo di aggiornamento del cluster non riuscito può indicare che non si dispone dello spazio di indirizzi IP o della quota vCPU disponibile per gestire queste risorse temporanee. Per ulteriori informazioni sul processo di aggiornamento dei nodi di Windows Server, vedere Aggiornare un pool di [nodi in AKS.][nodepool-upgrade]

## <a name="restricted-vm-sizes"></a>Dimensioni delle macchine virtuali limitate

Ogni nodo in un cluster AKS contiene una quantità fissa di risorse di calcolo, ad esempio vCPU e memoria. Se un nodo AKS contiene risorse di calcolo insufficienti, i pod potrebbero non essere eseguiti correttamente. Per garantire che i pod *kube-system* necessari e le applicazioni possano essere pianificati in modo affidabile, **non utilizzare i seguenti SKU delle macchine virtuali in AKS:**

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Per altre informazioni sui tipi di macchine virtuali e sulle relative risorse di calcolo, vedere Dimensioni per le macchine virtuali in Azure.For more information on VM types and their compute resources, see [Sizes for virtual machines in Azure.][vm-skus]

## <a name="region-availability"></a>Aree di disponibilità

Per un elenco più recente della distribuzione e dell'esecuzione dei cluster, vedere [Disponibilità dell'area AKS][region-availability].

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare alcune quote e alcuni limiti predefiniti. Se la risorsa supporta un aumento, richiedere l'aumento tramite una richiesta di supporto di [Azure][azure-support] (per Tipo **di problema**, selezionare **Quota**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
