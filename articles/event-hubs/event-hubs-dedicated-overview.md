---
title: Panoramica del livello Dedicato di Hub eventi di Azure | Documentazione Microsoft
description: Hub eventi di Microsoft Azure
services: event-hubs
author: banisadr
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 6aebaeccc502d20c8a80297972b8f3b5e8e363c6
ms.openlocfilehash: 3cd3d3fa7a540f9608ac07172bb78ee719d6b0a6


---


# <a name="dedicated-event-hubs--an-overview"></a>Livello Dedicato di Hub eventi – Panoramica

Il livello Dedicato di Hub eventi offre distribuzioni a tenant singolo per i clienti con i requisiti più rigorosi. Hub eventi di Azure può inserire al massimo più di due milioni di eventi al secondo o 2 GB al secondo di dati di telemetria con archiviazione completamente durevole e latenza inferiore al secondo. Ciò consente di usare anche le soluzioni integrate, mediante l'elaborazione in tempo reale e di batch nello stesso sistema. La funzionalità Archivio di Hub eventi, inclusa nell'offerta, consente di ottenere un flusso singolo che supporta le pipeline in tempo reale e basate su batch, riducendo la complessità della soluzione.

La tabella seguente confronta i livelli di servizio disponibili per Hub eventi. Il livello Dedicato di Hub eventi comporta un prezzo mensile fisso, rispetto ai prezzi di utilizzo per la maggior parte delle funzionalità dei livelli Standard e Basic. Il livello Dedicato offre le funzionalità del piano Standard, ma con capacità su scala aziendale per i clienti con carichi di lavoro intensi.

|  | Basic | Standard | Dedicato |
| --- |:---:|:---:|:---:|
| Eventi in ingresso | Pagamento per ogni milione di eventi | Pagamento per ogni milione di eventi | Incluso |
| Unità elaborate (1 MB/s in ingresso, 2 MB/s in uscita) | Pagamento per ogni ora | Pagamento per ogni ora | Incluso |
| Dimensioni del messaggio | 256 KB | 256 KB | 1 MB |
| Criteri per i server di pubblicazione | N/D | ✓ | ✓ |     
| Gruppi di utenti | 1 - Impostazione predefinita | 20 | 20 |
| Risposta a messaggi | ✓ | ✓  | ✓ |
| Numero massimo di unità di velocità effettiva | 20 | 20 (con flessibilità fino a 100)  | 1 UC≈200 |
| Connessioni negoziate | 100 incluse | 1.000 incluse | 100.000 incluse |
| Connessioni negoziate aggiuntive | N/D | ✓ | ✓ |
| Conservazione dei messaggi | 1 giorno incluso | 1 giorno incluso | Fino a 7 giorni inclusi |
| Archivio <sup>Anteprima</sup> | N/D  | Pagamento per ogni ora | Incluso |

## <a name="benefits-of-event-hubs-at-dedicated-capacity-include"></a>Il livello Dedicato di Hub eventi offre molti vantaggi, inclusi i seguenti:

* Hosting a tenant singolo senza interferenze da altri tenant
* Incremento delle dimensioni dei messaggi fino a 1 MB rispetto a 256 KB per Standard e Basic
* Prestazioni sempre ripetibili
* Capacità garantita per soddisfare le esigenze a livello di burst
* Scalabilità compresa tra 1 e 8 unità di capacità per fornire fino a 2 milioni di eventi in ingresso al secondo
  * Le unità di capacità (UC) gestiscono la scalabilità per il livello Dedicato di Hub eventi e ogni unità di capacità può offrire circa l'equivalente di 200 unità elaborate
* Manutenzione ridotta al minimo: il bilanciamento del carico, gli aggiornamenti del sistema operativo, le patch di protezione e il partizionamento vengono gestiti automaticamente
* Prezzo mensile fisso

Il livello Dedicato di Hub eventi rimuove anche alcune limitazioni relative alla velocità effettiva presenti nel livello Standard. Le unità elaborate dei livelli Basic e Standard consentono al cliente di usare fino a 1000 eventi al secondo o 1 MB al secondo in ingresso per le unità elaborare e il doppio in uscita. Il livello Dedicato non prevede restrizioni sul numero di eventi in ingresso e in uscita. Questi limiti vengono gestiti solo dalla capacità di elaborazione degli Hub eventi dedicati acquistati.

Il servizio è destinato agli utenti che usano quantità elevate di dati di telemetria ed è disponibile ai clienti con Contratto Enterprise.

## <a name="how-to-onboard"></a>Modalità di esecuzione dell'onboarding

La piattaforma Dedicata di Hub eventi viene offerta al pubblico tramite un Contratto Enterprise con unità di capacità di dimensioni diverse. Ogni unità di capacità fornisce circa l'equivalente di 200 unità elaborate e viene fatturata a $31/h. È possibile aumentare o ridurre la capacità nel corso del mese in base alle esigenze specifiche, aggiungendo o rimuovendo le unità di capacità. Il piano Dedicato è caratterizzato da un'esperienza di onboarding più efficiente da parte del team di Hub eventi, che consente di ottenere la distribuzione flessibile ottimale. 


## <a name="next-steps"></a>Passaggi successivi

[Prezzi del livello Dedicato di Hub eventi](https://azure.microsoft.com/en-us/pricing/details/event-hubs/)

Per informazioni aggiuntive sul livello Dedicato di Hub eventi, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft.


<!--HONumber=Jan17_HO4-->


