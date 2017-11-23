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
ms.date: 08/29/2017
ms.author: sethm;babanisa
ms.openlocfilehash: 613ea691e38b6f0bcd8873fc2ec6bcafb3cc6c78
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>Panoramica di Hub eventi dedicato

La capacità di *Hub eventi dedicato* offre distribuzioni a tenant singolo per i clienti con i requisiti più rigorosi. Al massimo delle sue capacità, Hub eventi di Azure può inserire più di due milioni di eventi al secondo o fino a 2 GB al secondo di dati di telemetria con archiviazione completamente durevole e latenza inferiore al secondo. Ciò consente di usare anche le soluzioni integrate, mediante l'elaborazione in tempo reale e di batch nello stesso sistema. La funzionalità [Acquisizione di Hub eventi](event-hubs-capture-overview.md), inclusa nell'offerta, consente di ridurre la complessità della soluzione grazie a un unico flusso che supporta le pipeline in tempo reale e basate su batch.

La tabella seguente confronta i livelli di servizio disponibili per Hub eventi. L'offerta di Hub eventi dedicato prevede un prezzo mensile fisso, rispetto ai prezzi in base all'utilizzo previsti per la maggior parte delle funzionalità del livello Standard. Il livello Dedicato offre le funzionalità del piano Standard, ma con capacità su scala aziendale per i clienti con carichi di lavoro intensi. 

| Funzionalità | Standard | Dedicato |
| --- |:---:|:---:|:---:|
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Unità di velocità effettiva (in entrata 1 MB/secondo, in uscita di 2 MB/secondo) | Pagamento per ogni ora | Incluso |
| Dimensioni del messaggio | 256 KB | 1 MB |
| Criteri per i server di pubblicazione | Sì | Sì |   
| Gruppi di utenti | 20 | 20 |
| Risposta a messaggi | Sì | Sì |
| Numero massimo di unità di velocità effettiva | 20 (con flessibilità fino a 100)   | 1 CU≈50 |
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
* Include la funzionalità [Acquisizione](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) degli hub eventi di Azure per fornire l'integrazione con micro batch e conservazione a lungo termine
* Manutenzione ridotta al minimo: il bilanciamento del carico, gli aggiornamenti del sistema operativo, le patch di protezione e il partizionamento vengono gestiti automaticamente.
* Prezzo orario fisso.
* Conservazione dei messaggi fino a 7 giorni senza costi aggiuntivi

Hub eventi dedicato rimuove anche alcune limitazioni relative alla velocità effettiva presenti nel livello Standard. Le unità elaborate nel livello Standard danno diritto a 1000 eventi al secondo oppure a 1 MB al secondo in ingresso per unità elaborata e al doppio in uscita. Il livello Dedicato non prevede restrizioni sul numero di eventi in ingresso e in uscita. Questi limiti vengono gestiti solo dalla capacità di elaborazione degli hub eventi acquistati.

Questo ambiente riservato e prenotato fornisce altre funzionalità specifiche di questo livello, ad esempio:

* Controllare il numero di spazi dei nomi nel cluster
* Specificare il limite della velocità effettiva in ogni spazio dei nomi
* Configurare il numero di hub eventi in ogni spazio dei nomi
* Stabilire il numero massimo di partizioni

Il servizio è destinato agli utenti che usano quantità elevate di dati di telemetria ed è disponibile ai clienti con contratto Enterprise.

## <a name="how-to-onboard"></a>Modalità di esecuzione dell'onboarding

È possibile aumentare o ridurre la capacità nel corso del mese in base alle esigenze specifiche, aggiungendo o rimuovendo le unità di capacità. Il piano Dedicato è unico nel suo genere perché consente di usufruire di più onboarding pratico dal team di produzione di Hub eventi per ottenere la distribuzione flessibile desiderata. Per eseguire l'onboarding di questo SKU, contattare il (supporto per la fatturazione) [https://ms.portal.azure.com/#create/Microsoft.Support] o il rappresentante Microsoft.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive sul livello Dedicato di Hub eventi, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft. Per altre informazioni su Hub eventi visitare i collegamenti seguenti:

Per informazioni dettagliate sui prezzi visitare i collegamenti seguenti:

- [Tariffe di Hub eventi dedicato](https://azure.microsoft.com/pricing/details/event-hubs/). Per informazioni aggiuntive sulla capacità di Hub eventi dedicato, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft.
- Le [Domande frequenti si Hub eventi](event-hubs-faq.md) contengono informazioni sui prezzi e risposte ad alcune domande frequenti sugli Hub eventi. 
