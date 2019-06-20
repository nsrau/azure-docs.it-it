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
ms.openlocfilehash: 1d3ce900f7354b31e999c12b8e1eb0e23d391fcb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180568"
---
| | SKU Standard | SKU Basic |
| --- | --- | --- |
| Dimensioni del pool back-end | Supporta fino a 1000 istanze. | Supporta fino a 100 istanze. |
| Endpoint di pool back-end | Qualsiasi macchina virtuale in una sola rete virtuale, tra cui un insieme di macchine virtuali, set di disponibilità, set di scalabilità di macchine virtuali. | Macchine virtuali in un singolo set di disponibilità o in un set di scalabilità di macchine virtuali. |
| [Probe di integrità](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento in caso di inattività dei probe](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Le connessioni TCP restano attive in caso di inattività dei probe dell'istanza __e__ di tutti i probe. | Le connessioni TCP restano attive in caso di inattività dei probe dell'istanza. Tutte le connessioni TCP terminano in tutti i probe non attivi. |
| Zone di disponibilità | Nello SKU Standard, front-end con ridondanza della zona e front-end di zona per connessioni in entrata e in uscita, mapping di flussi in uscita che superano l'errore di zona, bilanciamento del carico tra più zone. | Non disponibile. |
| Diagnostica | Monitoraggio di Azure, metriche multidimensionali, inclusi contatori di byte e pacchetti, stato del probe di integrità, tentativi di connessione (TCP SYN), integrità della connessione in uscita (flussi SNAT riusciti e non), misurazioni del piano dati attivo | Log Analytics di Azure solo per il bilanciamento del carico pubblico, avviso di esaurimento SNAT, conteggio integrità del pool back-end. |
| Porte a disponibilità elevata | Servizio di bilanciamento del carico interno | Non disponibile. |
| Protezione per impostazione predefinita | L'IP pubblico, gli endpoint del servizio di bilanciamento del carico pubblico e gli endpoint del servizio di bilanciamento del carico interno sono chiusi per i flussi in ingresso a meno che non vengano inseriti in un elenco elementi consentiti da un gruppo di sicurezza di rete. | Aperto per impostazione predefinita, gruppo di sicurezza di rete facoltativo. |
| [Connessioni in uscita](../articles/load-balancer/load-balancer-outbound-connections.md) | È possibile definire in modo esplicito un processo NAT in uscita basato su pool con [regole in uscita](../articles/load-balancer/load-balancer-outbound-rules-overview.md). È possibile usare più front-end con rifiuto esplicito in base alla regola di bilanciamento del carico. È _necessario_ creare in modo esplicito uno scenario in uscita affinché la macchina virtuale, il set di disponibilità e il set di scalabilità di macchine virtuali possano usare la connettività in uscita.  Gli endpoint servizio di rete virtuale possono essere raggiunti senza definire la connettività in uscita e non vengono considerati relativamente ai dati elaborati.  Tutti gli indirizzi IP pubblici, inclusi i servizi PaaS di Azure non disponibili come endpoint del servizio di rete virtuale, devono essere raggiunti tramite connettività in uscita e vengono considerati relativamente ai dati elaborati. Quando una macchina virtuale, un set di disponibilità o un set di scalabilità di macchine virtuali vengono serviti solo da un servizio di bilanciamento del carico interno, le connessioni in uscita tramite SNAT predefinito non sono disponibili. Usare in alternativa le [regole in uscita](../articles/load-balancer/load-balancer-outbound-rules-overview.md). La programmazione SNAT in uscita è il protocollo di trasporto specifico basato sul protocollo della regola di bilanciamento del carico in ingresso. | Front-end singolo selezionato in modo casuale quando sono presenti più front-end.  Quando una macchina virtuale, un set di disponibilità o un set di scalabilità di macchine virtuali vengono serviti solo da un servizio di bilanciamento del carico interno, viene usato lo SNAT predefinito. |
| [Regole in uscita](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Configurazione dichiarativa NAT in uscita, che usa gli indirizzi IP pubblici o i prefissi IP pubblici oppure entrambi, il timeout di inattività in uscita configurabile (4-120 minuti), l'allocazione della porta SNAT personalizzata | Non disponibile. |
|  [TCP Reset per inattività](../articles/load-balancer/load-balancer-tcp-reset.md) | Abilitazione di TCP Reset (RST TCP) in caso di timeout per inattività per qualsiasi regola | Non disponibile |
| [Più front-end](../articles/load-balancer/load-balancer-multivip-overview.md) | In ingresso e [in uscita](../articles/load-balancer/load-balancer-outbound-connections.md) | Solo in ingresso |
| Operazioni di gestione | La maggior parte delle operazioni < 30 secondi | In genere 60-90+ secondi |
| Contratto di servizio | 99,99% per il percorso dei dati con due macchine virtuali integre. | Non applicabile | 
| Prezzi | Addebito in base al numero di regole e dati elaborati in ingresso e in uscita associati alla risorsa.  | Nessun addebito. |
|  |  |  |
