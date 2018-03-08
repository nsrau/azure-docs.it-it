---
title: File di inclusione
description: File di inclusione
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
Nella tabella seguente sono elencate le quote e i limiti specifici di [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Per informazioni sui prezzi di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Scope | Note | Valore |
| --- | --- | --- | --- | --- |
| Numero di hub eventi per ogni spazio dei nomi |Spazio dei nomi |Le successive richieste per la creazione di un nuovo hub eventi verranno rifiutate. |10 |
| Numero di partizioni per hub eventi |Entità |- |32 |
| Numero di gruppi consumer per hub eventi |Entità |- |20 |
| Numero di connessioni AMQP per spazio dei nomi |Spazio dei nomi |Le richieste successive di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. |5.000 |
| Dimensione massima degli eventi di Hub eventi|Entità |- |256 KB |
| Dimensione massima del nome di un hub eventi |Entità |- |50 caratteri |
| Numero di ricevitori non epoch per gruppo consumer |Entità |- |5 |
| Periodo di conservazione massimo dei dati dell'evento |Entità |- |1-7 giorni |
| Numero massimo di unità di velocità effettiva |Spazio dei nomi |Il superamento del limite di unità elaborate provoca la limitazione dei dati e la generazione di un'eccezione **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. È possibile richiedere un numero maggiore di unità elaborate per un livello Standard compilando una [richiesta di supporto](/azure/azure-supportability/how-to-create-azure-support-request). Le [unità elaborate aggiuntive](../articles/event-hubs/event-hubs-auto-inflate.md) sono disponibili in blocchi da 20 in base a un acquisto con impegno. |20 |
| Numero di regole di autorizzazione per spazio dei nomi |Spazio dei nomi|Le richieste successive di creazione di regole di autorizzazione verranno rifiutate.|12 |
