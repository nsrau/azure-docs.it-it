---
title: Scalabilità, hub eventi di Azure | Microsoft Docs
description: Fornisce informazioni su come ridimensionare hub eventi di Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276782"
---
# <a name="scaling-with-event-hubs"></a>Scalabilità con l'hub eventi

Esistono due fattori che influenzano la scalabilità con l'hub eventi.
*   Unità elaborate
*   Partitions

## <a name="throughput-units"></a>Unità elaborate

La capacità di velocità effettiva di Hub eventi è controllata dalle *unità elaborate*. Le unità elaborate sono unità di capacità pre-acquistate. Una velocità effettiva single consente di:

* Dati in ingresso: fino a 1 MB al secondo o 1000 eventi al secondo, in base al valore raggiunto per primo.
* Dati in uscita: fino a 2 MB al secondo o 4096 eventi al secondo.

Oltre la capacità delle unità elaborate acquistate, i dati in ingresso vengono limitati e viene restituito un valore [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). I dati in uscita non producono eccezioni di limitazione, ma sono ancora limitati alla capacità delle unità elaborate acquistate. Se si ricevono eccezioni di velocità di pubblicazione o sono previste uscite maggiori, controllare il numero di unità elaborate acquistate per lo spazio dei nomi. È possibile gestire le unità elaborate nel pannello **Ridimensionamento** dello spazio dei nomi nel [portale di Azure](https://portal.azure.com). È anche possibile gestire le unità elaborate a livello di programmazione usando le [API degli hub eventi](event-hubs-api-overview.md).

Le unità sono pre-acquistate e vengono fatturate su base oraria. Una volta acquistate, le unità elaborate vengono fatturate per un minimo di un'ora. È possibile acquistare fino a 20 unità elaborate per uno spazio dei nomi di Hub eventi, che vengono condivise in tutti gli hub eventi nello spazio dei nomi.

La funzionalità **Aumento automatico** di Hub eventi aumenta automaticamente le prestazioni aumentando il numero di unità elaborate per soddisfare le esigenze di utilizzo. L'aumento delle unità elaborate previene scenari di limitazione in cui:

- Le velocità di ingresso dei dati superano le unità elaborate impostate.
- Le velocità di richiesta dei dati in uscita superano le unità elaborate impostate.

Il servizio Hub eventi aumenta la velocità effettiva quando il carico supera la soglia minima, senza che le richieste abbiano esito negativo con errori ServerBusy. 

Per altre informazioni sulla funzionalità aumento automatico, vedere [ridimensionare automaticamente le unità elaborate](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partitions

Le partizioni consentono scalabilità all'elaborazione downstream. A causa di modello consumer partizionato che offre un hub eventi con le partizioni, è possibile scalare orizzontalmente durante l'elaborazione di eventi simultaneamente. Un Hub eventi può avere un massimo di 32 partizioni.

È consigliabile bilanciare unità elaborate di 1:1 e partizioni per ottenere una scalabilità ottimale. Una singola partizione ha un ingresso e uscita di fino a un'unità di velocità effettiva garantite. Benché sia possibile essere in grado di raggiungere una velocità effettiva in una partizione, le prestazioni non sono garantita. Ecco perché è fortemente consigliabile che il numero di partizioni in un hub eventi sia maggiore o uguale al numero di unità di velocità effettiva.

Data la velocità effettiva totale che si prevede di dover, si conosce il numero di unità di velocità effettiva desiderata e il numero minimo di partizioni, ma il numero di partizioni è opportuno avere? Selezionare numero di partizioni in base al parallelismo downstream che si desidera ottenere, nonché esigenze di velocità effettiva futura. Non sono previsti addebiti per il numero di partizioni all'interno di un Hub eventi.

Per informazioni dettagliate sui prezzi di Hub eventi, vedere [Prezzi di Hub eventi ](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

- [Ridimensionare automaticamente le unità elaborate](event-hubs-auto-inflate.md)
- [Panoramica del servizio Hub eventi](event-hubs-what-is-event-hubs.md)
