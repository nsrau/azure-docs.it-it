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
ms.openlocfilehash: 9d6b54027adcf2b12c6ca4081a11208a31f620e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464828"
---
Nella tabella seguente sono elencate le quote e i limiti specifici di [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Per informazioni sui prezzi di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | `Scope` | Note | Value |
| --- | --- | --- | --- |
| Numero di spazi dei nomi di Hub eventi per sottoscrizione |Sottoscrizione |- |100 |
| Numero di hub eventi per ogni spazio dei nomi |Spazio dei nomi |Le successive richieste per la creazione di un nuovo hub eventi vengono rifiutate. |10 |
| Numero di partizioni per hub eventi |Entità |- |32 |
| Numero di gruppi consumer per hub eventi |Entità |- |20 |
| Numero di connessioni AMQP per spazio dei nomi |Spazio dei nomi |Le successive richieste di connessioni aggiuntive vengono rifiutate e il codice chiamante riceve un'eccezione. |5.000 |
| Dimensione massima degli eventi di Hub eventi|Entità |- |1 MB |
| Dimensione massima del nome di un hub eventi |Entità |- |50 caratteri |
| Numero di ricevitori non epoch per gruppo consumer |Entità |- |5 |
| Periodo di conservazione massimo dei dati dell'evento |Entità |- |1-7 giorni |
| Numero massimo di unità di velocità effettiva |Spazio dei nomi |Che superano il limite di unità elaborate provoca il limitazione dei dati e genera una [eccezioni di server occupato](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Per richiedere un numero maggiore di unità elaborate per un livello Standard, file di un [richiesta di supporto](/azure/azure-supportability/how-to-create-azure-support-request). Le [unità elaborate aggiuntive](../articles/event-hubs/event-hubs-auto-inflate.md) sono disponibili in blocchi da 20 in base a un acquisto con impegno. |20 |
| Numero di regole di autorizzazione per spazio dei nomi |Spazio dei nomi|Le successive richieste di creazione della regola di autorizzazione vengono rifiutate.|12 |
