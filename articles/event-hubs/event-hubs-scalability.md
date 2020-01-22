---
title: Scalabilità-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come ridimensionare Hub eventi di Azure usando partizioni e unità di velocità effettiva.
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
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309524"
---
# <a name="scaling-with-event-hubs"></a>Ridimensionamento con hub eventi

Esistono due fattori che influenzano la scalabilità con hub eventi.
*   Unità elaborate
*   Partizioni

## <a name="throughput-units"></a>Unità elaborate

La capacità di velocità effettiva di Hub eventi è controllata dalle *unità elaborate*. Le unità elaborate sono unità di capacità pre-acquistate. Una singola velocità effettiva consente di:

* Dati in ingresso: fino a 1 MB al secondo o 1000 eventi al secondo, qualunque valore venga raggiunto per primo.
* Dati in uscita: fino a 2 MB al secondo o 4096 eventi al secondo.

Oltre la capacità delle unità elaborate acquistate, i dati in ingresso vengono limitati e viene restituito un valore [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). I dati in uscita non producono eccezioni di limitazione, ma sono ancora limitati alla capacità delle unità elaborate acquistate. Se si ricevono eccezioni di velocità di pubblicazione o sono previste uscite maggiori, controllare il numero di unità elaborate acquistate per lo spazio dei nomi. È possibile gestire le unità elaborate nel pannello **Ridimensionamento** dello spazio dei nomi nel [portale di Azure](https://portal.azure.com). È anche possibile gestire le unità elaborate a livello di programmazione usando le [API degli hub eventi](event-hubs-api-overview.md).

Le unità sono pre-acquistate e vengono fatturate su base oraria. Una volta acquistate, le unità elaborate vengono fatturate per un minimo di un'ora. È possibile acquistare fino a 20 unità elaborate per uno spazio dei nomi di Hub eventi, che vengono condivise in tutti gli hub eventi nello spazio dei nomi.

La funzionalità **Aumento automatico** di Hub eventi aumenta automaticamente le prestazioni aumentando il numero di unità elaborate per soddisfare le esigenze di utilizzo. L'aumento delle unità elaborate previene scenari di limitazione in cui:

- Le velocità di ingresso dei dati superano le unità elaborate impostate.
- Le velocità di richiesta dei dati in uscita superano le unità elaborate impostate.

Il servizio Hub eventi aumenta la velocità effettiva quando il carico supera la soglia minima, senza che le richieste abbiano esito negativo con errori ServerBusy. 

Per altre informazioni sulla funzionalità di aumento automatico, vedere [ridimensionare automaticamente le unità di velocità effettiva](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partizioni
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Chiave di partizione

È possibile usare una [chiave di partizione](event-hubs-programming-guide.md#partition-key) per mappare i dati dell'evento in ingresso in partizioni specifiche ai fini dell'organizzazione dei dati. La chiave di partizione è un valore fornito dal mittente che viene passato a un hub eventi. Viene elaborato tramite una funzione di hashing statica, che crea l'assegnazione di partizione. Se non si specifica una chiave di partizione quando si pubblica un evento, viene usata un'assegnazione round robin.

L'autore di eventi è a conoscenza solo della chiave di partizione, non la partizione in cui gli eventi vengono pubblicati. Questa separazione tra chiave e partizione evita che il mittente debba conoscere troppe informazioni sull'elaborazione downstream. Un’identità univoca per dispositivo o utente crea una chiave di partizione efficace, ma è possibile utilizzare anche altri attributi, ad esempio l’area geografica, per raggruppare gli eventi correlati in un'unica partizione.


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

- [Ridimensionare automaticamente le unità elaborate](event-hubs-auto-inflate.md)
- [Panoramica del servizio Hub eventi](event-hubs-what-is-event-hubs.md)
