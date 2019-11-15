---
title: File di inclusione
description: File di inclusione
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 8b08e0ced0c7094890a80c37452c7f1b001fe511
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888675"
---
| | SKU Standard | SKU Basic |
| --- | --- | --- |
| Dimensioni del pool back-end | Supporta fino a 1000 istanze. | Supporta fino a 100 istanze. |
| Endpoint del pool di back-end | Qualsiasi macchina virtuale in una singola rete virtuale, tra cui insiemi di macchine virtuali, set di disponibilità e set di scalabilità di macchine virtuali. | Macchine virtuali in un singolo set di disponibilità o in un set di scalabilità di macchine virtuali. |
| [Probe di integrità](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento in caso di inattività dei probe](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Le connessioni TCP restano attive in caso di inattività di un probe dell'istanza __e__ di tutti i probe. | Le connessioni TCP restano attive in caso di inattività di un probe dell'istanza. Tutte le connessioni TCP terminano se sono inattivi tutti i probe. |
| Zone di disponibilità | Front-end di zona e con ridondanza della zona per il traffico in ingresso e in uscita. I mapping dei flussi in uscita non subiscono conseguenze in caso di un errore di zona. Bilanciamento del carico tra zone. | Non disponibile |
| Diagnostica | Monitoraggio di Azure. Metriche multidimensionali che includono contatori di byte e pacchetti. Stato probe di integrità. Tentativi di connessione (TCP SYN). Integrità delle connessioni in uscita (flussi SNAT riusciti e non riusciti). Misurazioni del piano dati attivo. | Azure Log Analytics solo per bilanciamento del carico pubblico. Avviso di esaurimento SNAT. Conteggio integrità del pool back-end. |
| Porte a disponibilità elevata | Servizio di bilanciamento del carico interno | Non disponibile |
| Protezione per impostazione predefinita | L'IP pubblico, gli endpoint di bilanciamento del carico pubblico e gli endpoint di bilanciamento del carico interno sono chiusi per i flussi in ingresso a meno che non siano consentiti da un gruppo di sicurezza di rete. | Aperto per impostazione predefinita. Gruppo di sicurezza di rete facoltativo. |
| [Connessioni in uscita](../articles/load-balancer/load-balancer-outbound-connections.md) | È possibile definire in modo esplicito un processo NAT in uscita basato su pool con [regole in uscita](../articles/load-balancer/load-balancer-outbound-rules-overview.md). È possibile usare più front-end con rifiuto esplicito in base alla regola di bilanciamento del carico. È _necessario_ creare in modo esplicito uno scenario in uscita affinché la macchina virtuale, il set di disponibilità o il set di scalabilità di macchine virtuali possa usare la connettività in uscita. Gli endpoint del servizio di rete virtuale possono essere raggiunti senza definire la connettività in uscita e non vengono considerati relativamente ai dati elaborati. Tutti gli indirizzi IP pubblici, inclusi i servizi PaaS di Azure non disponibili come endpoint del servizio di rete virtuale, devono essere raggiunti tramite connettività in uscita e vengono considerati relativamente ai dati elaborati. Quando una macchina virtuale, un set di disponibilità o un set di scalabilità di macchine virtuali viene servito solo da un servizio di bilanciamento del carico interno, le connessioni in uscita tramite conversione SNAT predefinita non sono disponibili. Usare in alternativa le [regole in uscita](../articles/load-balancer/load-balancer-outbound-rules-overview.md). La programmazione SNAT in uscita dipende dal protocollo di trasporto della regola di bilanciamento del carico in ingresso. | Front-end singolo selezionato in modo casuale quando sono presenti più front-end. Quando una macchina virtuale, un set di disponibilità o un set di scalabilità di macchine virtuali viene servito solo da un servizio di bilanciamento del carico interno, viene usata la conversione SNAT predefinita. |
| [Regole in uscita](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Configurazione NAT in uscita dichiarativa, con indirizzi IP pubblici, prefissi IP pubblici o entrambi. Timeout di inattività in uscita configurabile (4-120 minuti). Allocazione della porta SNAT personalizzata | Non disponibile |
| [TCP Reset per inattività](../articles/load-balancer/load-balancer-tcp-reset.md) | Abilitazione di TCP Reset (RST TCP) in caso di timeout per inattività per qualsiasi regola | Non disponibile |
| [Più front-end](../articles/load-balancer/load-balancer-multivip-overview.md) | In ingresso e [in uscita](../articles/load-balancer/load-balancer-outbound-connections.md) | Solo in ingresso |
| Operazioni di gestione | La maggior parte delle operazioni < 30 secondi | in genere 60-90+ secondi |
| Contratto di servizio | 99,99% per il percorso dei dati con due macchine virtuali integre. | Non applicabile | 
| Prezzi | Addebito in base al numero di regole e dati elaborati in ingresso e in uscita associati alla risorsa. | Nessun addebito |
|  |  |  |
