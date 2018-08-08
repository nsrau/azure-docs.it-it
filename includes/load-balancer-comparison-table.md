---
title: File di inclusione
description: File di inclusione
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 7/26/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: fc98206b5940ce75ddf26cfd905b1e0a89e2d85c
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326245"
---
| | SKU Standard | SKU Basic |
| --- | --- | --- |
| Dimensioni del pool back-end | Supporta fino a 1000 istanze | Supporta fino a 100 istanze |
| Endpoint di pool back-end | Qualsiasi macchina virtuale in una sola rete virtuale, tra cui un insieme di macchine virtuali, set di disponibilità, set di scalabilità di macchine virtuali. | Macchine virtuali in un singolo set di disponibilità o in un set di scalabilità di macchine virtuali. |
| Zone di disponibilità | Nello SKU Basic, front-end con ridondanza della zona e front-end di zona per connessioni in entrata e in uscita, mapping di flussi in uscita che superano l'errore di zona, bilanciamento del carico tra più zone. | n/d|
| Diagnostica | Monitoraggio di Azure, metriche multidimensionali, inclusi contatori di byte e pacchetti, stato del probe di integrità, tentativi di connessione (TCP SYN), integrità della connessione in uscita (flussi SNAT riusciti e non), misurazioni del piano dati attivo | Log Analytics di Azure solo per il bilanciamento del carico pubblico, avviso di esaurimento SNAT, conteggio integrità del pool back-end. |
| Porte a disponibilità elevata | Servizio di bilanciamento del carico interno | n/d |
| Protezione per impostazione predefinita | È necessario usare endpoint predefiniti chiusi per Load Balancer e indirizzi IP pubblici e un gruppo di sicurezza di rete da inserire nell'elenco elementi consentiti in modo esplicito e consentire il traffico. | Aperto per impostazione predefinita, gruppo di sicurezza di rete facoltativo. |
| [Connessioni in uscita](../articles/load-balancer/load-balancer-outbound-connections.md) | Front-end multipli con rifiuto esplicito in base alla regola di bilanciamento del carico. È _necessario_ creare in modo esplicito uno scenario in uscita affinché la macchina virtuale sia in grado di usare la connettività in uscita.  Gli endpoint del servizio di rete virtuale possono essere raggiunti senza connettività in uscita e non vengono considerati relativamente ai dati elaborati.  Tutti gli indirizzi IP pubblici, inclusi i servizi PaaS di Azure non disponibili come endpoint del servizio di rete virtuale, devono essere raggiunti tramite connettività in uscita e vengono considerati relativamente ai dati elaborati. Quando una macchina virtuale viene servita solo da un Load Balancer interno, le connessioni in uscita tramite SNAT predefinito non sono disponibili. La programmazione SNAT in uscita è il protocollo di trasporto specifico basato sul protocollo della regola di bilanciamento del carico in ingresso. | Front-end singolo selezionato in modo casuale quando sono presenti più front-end.  Quando una macchina virtuale viene servita solo da un Load Balancer interno, viene usato lo SNAT predefinito., viene usato lo SNAT predefinito. |
| [Più front-end](../articles/load-balancer/load-balancer-multivip-overview.md) | In ingresso e [in uscita](../articles/load-balancer/load-balancer-outbound-connections.md) | Solo in ingresso |
| [Comportamento in caso di inattività dei probe](../articles/load-balancer/load-balancer-custom-probe-overview.md) | Le connessioni TCP restano attive in caso di inattività dei probe dell'istanza __e__ di tutti i probe. | Le connessioni TCP restano attive in caso di inattività dei probe dell'istanza. Tutte le connessioni TCP vengono terminate in caso di inattività di tutti i probe. |
| Operazioni di gestione | La maggior parte delle operazioni < 30 secondi | In genere 60-90+ secondi |
| Contratto di servizio | 99,99% per il percorso dei dati con due macchine virtuali integre. | Implicito nel contratto di servizio della macchina virtuale. | 
| Prezzi | Addebito in base al numero di regole e dati elaborati in ingresso o in uscita associati alla risorsa.  | Nessun addebito |
|  |  |  |