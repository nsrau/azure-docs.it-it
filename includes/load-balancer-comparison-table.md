---
title: File di inclusione
description: File di inclusione
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78202554"
---
| | Load Balancer Standard | Load Balancer di base |
| --- | --- | --- |
| [Dimensioni del pool back-end](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Supporta fino a 1000 istanze. | Supporta fino a 300 istanze. |
| Endpoint di pool back-end | Tutte le macchine virtuali o tutti i set di scalabilità di macchine virtuali in una singola rete virtuale. | Macchine virtuali in un singolo set di disponibilità o in un set di scalabilità di macchine virtuali. |
| [Probe di integrità](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento in caso di inattività dei probe](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Le connessioni TCP restano attive in caso di inattività di un probe dell'istanza __e__ di tutti i probe. | Le connessioni TCP restano attive in caso di inattività di un probe dell'istanza. Tutte le connessioni TCP terminano se sono inattivi tutti i probe. |
| Zone di disponibilità | Front-end di zona e con ridondanza della zona per il traffico in ingresso e in uscita. | Non disponibile |
| Diagnostica | [Metriche multidimensionali di Monitoraggio di Azure](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Log di Monitoraggio di Azure](../articles/load-balancer/load-balancer-monitor-log.md) |
| Porte a disponibilità elevata | [Disponibile per il Load Balancer interno](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Non disponibile |
| Protezione per impostazione predefinita | Chiuso nei flussi in ingresso, a meno che non sia consentito da un gruppo di sicurezza di rete. Si noti che il traffico interno dalla rete virtuale al servizio di bilanciamento del carico interno è consentito. | Aperto per impostazione predefinita. Gruppo di sicurezza di rete facoltativo. |
| Regole in uscita | [Configurazione NAT in uscita dichiarativa](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Non disponibile |
| TCP Reset in caso di inattività | [Disponibile in tutte le regole](../articles/load-balancer/load-balancer-tcp-reset.md) | Non disponibile |
| [Più front-end](../articles/load-balancer/load-balancer-multivip-overview.md) | In ingresso e [in uscita](../articles/load-balancer/load-balancer-outbound-connections.md) | Solo in ingresso |
| Operazioni di gestione | La maggior parte delle operazioni < 30 secondi | in genere 60-90+ secondi |
| Contratto di servizio | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Non disponibile | 
