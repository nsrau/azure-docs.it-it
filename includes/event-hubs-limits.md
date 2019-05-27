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
ms.openlocfilehash: 38f7dd6eb1c4965eca003e5ba337ec5912a53420
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148230"
---
Nella tabella seguente sono elencate le quote e i limiti specifici di [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Per informazioni sui prezzi di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | `Scope` | Note | Value |
| --- | --- | --- | --- |
| Numero di spazi dei nomi di Hub eventi per sottoscrizione |Sottoscrizione |- |100 |
| Numero di hub eventi per ogni spazio dei nomi |Namespace |Le successive richieste per la creazione di un nuovo hub eventi vengono rifiutate. |10 |
| Numero di partizioni per hub eventi |Entità |- |32 |
| Numero di gruppi consumer per hub eventi |Entità |- |20 |
| Numero di connessioni AMQP per spazio dei nomi |Namespace |Le successive richieste di connessioni aggiuntive vengono rifiutate e il codice chiamante riceve un'eccezione. |5.000 |
| Dimensione massima degli eventi di Hub eventi|Entità |- |1 MB |
| Dimensione massima del nome di un hub eventi |Entità |- |50 caratteri |
| Numero di ricevitori non epoch per gruppo consumer |Entità |- |5 |
| Periodo di conservazione massimo dei dati dell'evento |Entità |- |1-7 giorni |
| Numero massimo di unità di velocità effettiva |Namespace |Che superano il limite di unità elaborate provoca il limitazione dei dati e genera una [eccezioni di server occupato](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Per richiedere un numero maggiore di unità elaborate per un livello Standard, file di un [richiesta di supporto](/azure/azure-supportability/how-to-create-azure-support-request). Le [unità elaborate aggiuntive](../articles/event-hubs/event-hubs-auto-inflate.md) sono disponibili in blocchi da 20 in base a un acquisto con impegno. |20 |
| Numero di regole di autorizzazione per spazio dei nomi |Namespace|Le successive richieste di creazione della regola di autorizzazione vengono rifiutate.|12 |
| Numero di chiamate al metodo GetRuntimeInformation | Entità | - | 50 al secondo | 
