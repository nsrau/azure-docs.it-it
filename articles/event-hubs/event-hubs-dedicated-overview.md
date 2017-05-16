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
ms.date: 02/21/2017
ms.author: sethm;babanisa
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: fa0d49498db74ec81c98f3df9317d42cdd31ab46
ms.lasthandoff: 04/18/2017


---

# <a name="overview-of-event-hubs-dedicated"></a>Panoramica di Hub eventi dedicato

La capacità di *Hub eventi dedicato* offre distribuzioni a tenant singolo per i clienti con i requisiti più rigorosi. Al massimo delle sue capacità, Hub eventi di Azure può inserire più di due milioni di eventi al secondo o fino a 2 GB al secondo di dati di telemetria con archiviazione completamente durevole e latenza inferiore al secondo. Ciò consente di usare anche le soluzioni integrate, mediante l'elaborazione in tempo reale e di batch nello stesso sistema. La funzionalità Archivio di Hub eventi, inclusa nell'offerta, consente di ridurre la complessità della soluzione grazie all'unico flusso che supporta le pipeline in tempo reale e basate su batch.

La tabella seguente confronta i livelli di servizio disponibili per Hub eventi. L'offerta di Hub eventi dedicato comporta un prezzo mensile fisso, rispetto ai prezzi di utilizzo per la maggior parte delle funzionalità dei livelli Standard e Basic. Il livello Dedicato offre le funzionalità del piano Standard, ma con capacità su scala aziendale per i clienti con carichi di lavoro intensi. 

| Funzionalità | Basic | Standard | Dedicato |
| --- |:---:|:---:|:---:|
| Eventi in ingresso | Pagamento per ogni milione di eventi | Pagamento per ogni milione di eventi | Incluso |
| Unità di velocità effettiva (in entrata 1 MB/secondo, in uscita di 2 MB/secondo) | Pagamento per ogni ora | Pagamento per ogni ora | Incluso |
| Dimensioni del messaggio | 256 KB | 256 KB | 1 MB |
| Criteri per i server di pubblicazione | N/D | Sì | Sì |     
| Gruppi di utenti | 1 - Impostazione predefinita | 20 | 20 |
| Risposta a messaggi | Sì | Sì | Sì |
| Numero massimo di unità di velocità effettiva | 20 | 20 (con flessibilità fino a 100)    | 1 UC≈200 |
| Connessioni negoziate | 100 incluse | 1.000 incluse | 100 K incluse |
| Connessioni negoziate aggiuntive | N/D | Sì | Sì |
| Conservazione dei messaggi | 1 giorno incluso | 1 giorno incluso | Fino a 7 giorni inclusi |
| Archivio (anteprima) | N/D    | Pagamento per ogni ora | Incluso |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Vantaggi della capacità di Hub eventi dedicato

Quando si usa Hub eventi dedicato sono disponibili i vantaggi seguenti:

* Hosting a tenant singolo senza interferenze da altri tenant.
* Incremento delle dimensioni dei messaggi fino a 1 MB rispetto a 256 KB per i piani Standard e Basic.
* Prestazioni sempre ripetibili.
* Capacità garantita per soddisfare le esigenze a livello di burst.
* Scalabilità compresa tra 1 e 8 unità di capacità per offrire fino a 2 milioni di eventi in ingresso al secondo.
  * Le unità di capacità gestiscono la scalabilità per Hub eventi dedicato e ogni unità di capacità può offrire circa l'equivalente di 200 unità elaborate.
* Manutenzione ridotta al minimo: il bilanciamento del carico, gli aggiornamenti del sistema operativo, le patch di protezione e il partizionamento vengono gestiti automaticamente.
* Prezzo mensile fisso.

Hub eventi dedicato rimuove anche alcune limitazioni relative alla velocità effettiva presenti nel livello Standard. Le unità elaborate dei livelli Basic e Standard consentono di usare fino a 1000 eventi al secondo o 1 MB al secondo in ingresso per le unità elaborare e il doppio in uscita. Il livello Dedicato non prevede restrizioni sul numero di eventi in ingresso e in uscita. Questi limiti vengono gestiti solo dalla capacità di elaborazione degli hub eventi acquistati.

Il servizio è destinato agli utenti che usano quantità elevate di dati di telemetria ed è disponibile ai clienti con contratto Enterprise.

## <a name="how-to-onboard"></a>Modalità di esecuzione dell'onboarding

La piattaforma Hub eventi dedicata viene offerta al pubblico tramite un contratto Enterprise con unità di capacità di dimensioni diverse. Ogni unità di capacità fornisce circa l'equivalente di 200 unità elaborate. È possibile aumentare o ridurre la capacità nel corso del mese in base alle esigenze specifiche, aggiungendo o rimuovendo le unità di capacità. Il piano dedicato è caratterizzato da un'esperienza di onboarding più efficiente da parte del team di Hub eventi, che consente di ottenere una distribuzione flessibile ottimale. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive sul livello Dedicato di Hub eventi, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft. Per altre informazioni su Hub eventi visitare i collegamenti seguenti:

Per informazioni dettagliate sui prezzi visitare i collegamenti seguenti:

- [Tariffe di Hub eventi dedicato](https://azure.microsoft.com/pricing/details/event-hubs/). Per informazioni aggiuntive sulla capacità di Hub eventi dedicato, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft.
- Le [Domande frequenti si Hub eventi](event-hubs-faq.md) contengono informazioni sui prezzi e risposte ad alcune domande frequenti sugli Hub eventi. 

