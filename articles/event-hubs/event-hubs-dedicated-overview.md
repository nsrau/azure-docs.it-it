---
title: "Panoramica della capacità di Hub eventi dedicato di Azure | Microsoft Docs"
description: "Panoramica della capacità di Hub eventi dedicato di Microsoft Azure."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm;babanisa
ms.openlocfilehash: 563152a019464f3d0342383ff13e6ee1c87a22fe
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>Panoramica di Hub eventi dedicato

La capacità di *Hub eventi dedicato* offre distribuzioni a tenant singolo per i clienti con i requisiti più rigorosi. Al massimo delle sue capacità, Hub eventi di Azure può inserire più di due milioni di eventi al secondo o fino a 2 GB al secondo di dati di telemetria con archiviazione completamente durevole e latenza inferiore al secondo. Ciò consente di usare anche le soluzioni integrate, mediante l'elaborazione in tempo reale e di batch nello stesso sistema. La funzionalità [Acquisizione di Hub eventi](event-hubs-capture-overview.md), inclusa nell'offerta, consente di ridurre la complessità della soluzione grazie a un unico flusso che supporta le pipeline in tempo reale e basate su batch.

La tabella seguente confronta i livelli di servizio disponibili per Hub eventi. L'offerta di Hub eventi dedicato prevede un prezzo mensile fisso, rispetto ai prezzi in base all'utilizzo previsti per la maggior parte delle funzionalità del livello Standard. Il livello dedicato offre tutte le funzionalità del piano Standard, ma con capacità di scala enterprise per i clienti con carichi di lavoro complesse. 

| Funzionalità | Standard | Dedicato |
| --- |:---:|:---:|:---:|
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Unità di velocità effettiva (in entrata 1 MB/secondo, in uscita di 2 MB/secondo) | Pagamento per ogni ora | Incluso |
| Dimensioni del messaggio | 256 KB | 1 MB |
| Criteri per i server di pubblicazione | Sì | Sì |   
| Gruppi di consumer | 20 | 20 |
| Risposta a messaggi | Sì | Sì |
| Numero massimo di unità di velocità effettiva | 20 (con flessibilità fino a 100)   | 1 unità di capacità (CU) cavi 50 |
| Connessioni negoziate | 1.000 incluse | 100 K incluse |
| Connessioni negoziate aggiuntive | Sì | Sì |
| Conservazione dei messaggi | 1 giorno incluso | Fino a 7 giorni inclusi |
| Acquisizione | Pagamento per ogni ora | Incluso |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Vantaggi della capacità di Hub eventi dedicato

Quando si usa Hub eventi dedicato sono disponibili i vantaggi seguenti:

* Hosting a tenant singolo senza interferenze da altri tenant.
* Incremento delle dimensioni dei messaggi fino a 1 MB rispetto a 256 KB per il livello Standard.
* Prestazioni sempre ripetibili.
* Capacità garantita per soddisfare le esigenze a livello di burst.
* Include il [acquisire](event-hubs-capture-overview.md) funzionalità degli hub di eventi, per fornire l'integrazione con la conservazione dei batch di micro e a lungo termine.
* Manutenzione zero: il servizio gestisce il bilanciamento del carico del sistema operativo degli aggiornamenti, patch di sicurezza e partizionamento.
* Prezzo orario fisso.
* Messaggio conservazione dei backup per 7 giorni con senza alcun costo aggiuntivo.

Hub eventi dedicato rimuove anche alcune limitazioni relative alla velocità effettiva presenti nel livello Standard. Le unità elaborate nel livello Standard danno diritto a 1000 eventi al secondo oppure a 1 MB al secondo in ingresso per unità elaborata e al doppio in uscita. Il livello Dedicato non prevede restrizioni sul numero di eventi in ingresso e in uscita. Questi limiti vengono gestiti solo dalla capacità di elaborazione degli hub eventi acquistati.

Questo ambiente riservato e prenotato fornisce altre funzionalità specifiche di questo livello, ad esempio:

* Controlla il numero di spazi dei nomi del cluster.
* Specifica i limiti di velocità effettiva in ognuno degli spazi dei nomi.
* Configura il numero di hub eventi sotto ogni spazio dei nomi.
* Determina il limite al numero di partizioni.

Questo servizio è destinato agli utenti di telemetria più grande ed è disponibile per i clienti con un contratto enterprise agreement.

## <a name="how-to-onboard"></a>Modalità di esecuzione dell'onboarding

È possibile aumentare o ridurre la capacità nel corso del mese in base alle esigenze specifiche, aggiungendo o rimuovendo le unità di capacità. Il piano Dedicato è unico nel suo genere perché consente di usufruire di più onboarding pratico dal team di produzione di Hub eventi per ottenere la distribuzione flessibile desiderata. Per caricare questo SKU, [contattare il supporto per la fatturazione](https://ms.portal.azure.com/#create/Microsoft.Support) o il rappresentante Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive sul livello Dedicato di Hub eventi, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft. È anche possibile ottenere ulteriori informazioni hub eventi, i livelli di prezzo, visitare i collegamenti seguenti:

- [Tariffe di Hub eventi dedicato](https://azure.microsoft.com/pricing/details/event-hubs/). Per informazioni aggiuntive sulla capacità di Hub eventi dedicato, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft.
- Le [Domande frequenti si Hub eventi](event-hubs-faq.md) contengono informazioni sui prezzi e risposte ad alcune domande frequenti sugli Hub eventi. 
