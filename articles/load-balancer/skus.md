---
title: SKU di Azure Load Balancer
description: Panoramica degli SKU di Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: 874ecfc8c1c50816916fb0b04975477a1cbe0a71
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698088"
---
# <a name="azure-load-balancer-skus"></a>SKU di Azure Load Balancer

Sono disponibili due SKU di Azure Load Balancer.

## <a name="sku-comparison"></a><a name="skus"></a> Confronto tra SKU

Load Balancer supporta SKU Standard e Basic, che variano in termini di scalabilità degli scenari, funzionalità e prezzi. Tutti gli scenari possibili con Load Balancer Basic possono essere creati anche con Load Balancer Standard.

Esaminare la tabella seguente per confrontare e comprendere le differenze. Per altre informazioni, vedere [Azure Panoramica di Load Balancer Standard](./load-balancer-overview.md).

>[!NOTE]
> Microsoft consiglia di usare Load Balancer Standard.
Le macchine virtuali autonome, i set di disponibilità e i set di scalabilità di macchine virtuali possono essere collegati solo a uno SKU, non a entrambi. Se usati con gli indirizzi IP pubblici, lo SKU di Load Balancer e quello dell'indirizzo IP pubblico devono corrispondere. Gli SKU di Load Balancer e quelli degli indirizzi IP pubblici non sono modificabili.

| | Load Balancer Standard | Load Balancer di base |
| --- | --- | --- |
| **[Dimensioni del pool back-end](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)** | Supporta fino a 1000 istanze. | Supporta fino a 300 istanze. |
| **Endpoint del pool back-end** | Tutte le macchine virtuali o tutti i set di scalabilità di macchine virtuali in una singola rete virtuale. | Macchine virtuali in un singolo set di disponibilità o in un set di scalabilità di macchine virtuali. |
| **[Probe di integrità](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Comportamento in caso di inattività dei probe](./load-balancer-custom-probe-overview.md#probedown)** | Le connessioni TCP restano attive in caso di inattività di un probe dell'istanza __e__ di tutti i probe. | Le connessioni TCP restano attive in caso di inattività di un probe dell'istanza. Tutte le connessioni TCP vengono interrotte quando tutti i probe sono inattivi. |
| **Zone di disponibilità** | Front-end di zona e con ridondanza della zona per il traffico in ingresso e in uscita. | Non disponibile |
| **Diagnostica** | [Metriche multidimensionali di Monitoraggio di Azure](./load-balancer-standard-diagnostics.md) | [Log di Monitoraggio di Azure](./load-balancer-monitor-log.md) |
| **Porte a disponibilità elevata** | [Disponibile per il Load Balancer interno](./load-balancer-ha-ports-overview.md) | Non disponibile |
| **Sicurezza per impostazione predefinita** | Chiuso nei flussi in ingresso, a meno che non sia consentito da un gruppo di sicurezza di rete. Il traffico interno dalla rete virtuale al servizio di bilanciamento del carico interno è consentito. | Aperto per impostazione predefinita. Gruppo di sicurezza di rete facoltativo. |
| **Regole in uscita** | [Configurazione NAT in uscita dichiarativa](./load-balancer-outbound-connections.md#outboundrules) | Non disponibile |
| **TCP Reset per inattività** | [Disponibile in tutte le regole](./load-balancer-tcp-reset.md) | Non disponibile |
| **[Più front-end](./load-balancer-multivip-overview.md)** | In ingresso e [in uscita](./load-balancer-outbound-connections.md) | Solo in ingresso |
| **Operazioni di gestione** | La maggior parte delle operazioni < 30 secondi | in genere 60-90+ secondi |
| **Contratto di servizio** | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Non disponibile | 

Per altre informazioni, vedere [Limiti di Load Balancer](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer). Per informazioni dettagliate su Load Balancer Standard, vedere [panoramica](./load-balancer-overview.md), [prezzi](https://aka.ms/lbpricing) e [contratto di servizio](https://aka.ms/lbsla).

## <a name="limitations"></a>Limitazioni

- Gli SKU non sono modificabili. Non è possibile modificare lo SKU di una risorsa esistente.
- Una risorsa autonoma per macchine virtuali, una risorsa per un set di disponibilità o una risorsa per un set di scalabilità di macchine virtuali può essere riferita a uno SKU, non a entrambi.
- [Operazioni di spostamento](../azure-resource-manager/management/move-resource-group-and-subscription.md):
  - le operazioni di spostamento dei gruppi di risorse (all'interno della stessa sottoscrizione) **sono supportate** per Load Balancer Standard e l'indirizzo IP pubblico standard. 
  - Le [operazioni di spostamento dei gruppi di sottoscrizioni](../azure-resource-manager/management/move-support-resources.md) **non** sono supportate per le risorse Load Balancer Standard e indirizzo IP pubblico standard.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare il servizio Load Balancer, vedere [Creare un servizio Load Balancer Standard pubblico](quickstart-load-balancer-standard-public-portal.md).
- Informazioni sull'uso di [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).
- Informazioni sui [probe di integrità](load-balancer-custom-probe-overview.md).
- Informazioni sull'uso di [Load Balancer per le connessioni in uscita](load-balancer-outbound-connections.md).
- Informazioni su [Load Balancer Standard con regole di bilanciamento del carico di porte a disponibilità elevata](load-balancer-ha-ports-overview.md).
- Vedere altre informazioni sui [gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md).