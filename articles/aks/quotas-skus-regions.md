---
title: Le quote, SKU e aree di disponibilità in Azure Kubernetes Service (AKS)
description: Informazioni sulle quote predefinite, dimensioni SKU di VM di nodi con restrizioni e disponibilità delle aree di Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "64413005"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quote, limiti di dimensione di macchina virtuale e disponibilità per area geografica in Azure Kubernetes Service (AKS)

Tutti i servizi di Azure prevedono determinati limiti predefiniti e quote per le risorse e le funzionalità. Per le dimensioni del nodo, alcune macchine virtuali (VM) gli SKU sono quindi limitato per l'utilizzo.

Questo articolo illustra i limiti predefiniti per le risorse di Azure Kubernetes Service (AKS), nonché la disponibilità del servizio AKS nelle aree di Azure.

## <a name="service-quotas-and-limits"></a>Quote e limiti del servizio

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastruttura sottoposta a provisioning

Tutte le altre limitazioni relative alla rete, al calcolo e all'archiviazione si applicano all'infrastruttura sottoposta a provisioning. Per le limitazioni previste, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="restricted-vm-sizes"></a>Dimensioni delle macchine Virtuali con restrizioni

Ogni nodo in un cluster AKS contiene una quantità fissa di risorse di calcolo, ad esempio memoria e vCPU. Se un nodo AKS contiene le risorse di calcolo sufficienti, potrebbero non riuscire POD eseguito correttamente. Per garantire che la necessaria *kube system* POD e le applicazioni in modo affidabile possono essere pianificate, non è possibile utilizzare i seguenti SKU di VM nel servizio contenitore di AZURE:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Per altre informazioni sui tipi di VM e le relative risorse di calcolo, vedere [dimensioni delle macchine virtuali di Azure][vm-skus].

## <a name="region-availability"></a>Aree di disponibilità

Per l'elenco più recente in cui è possibile distribuire ed eseguire i cluster, vedere [AKS aree di disponibilità][region-availability].

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare alcune quote e alcuni limiti predefiniti. Per richiedere un aumento di una o più risorse che lo consentono, invia una [richiesta di supporto di Azure][azure-support] (seleziona "Quota" per **Tipo di problema**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
