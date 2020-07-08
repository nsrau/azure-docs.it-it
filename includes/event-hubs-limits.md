---
title: includere file
description: includere file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 333f2317fcc834a10b7336bbda9a43ba16a7ad38
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317784"
---
Le tabelle seguenti forniscono le quote e i limiti specifici di [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Per informazioni sui prezzi di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

I limiti seguenti sono comuni tra i livelli Basic e Standard. 

| Limite | Scope | Note | valore |
| --- | --- | --- | --- |
| Numero di spazi dei nomi di Hub eventi per sottoscrizione |Subscription |- |100 |
| Numero di hub eventi per ogni spazio dei nomi |Spazio dei nomi |Le successive richieste per la creazione di un nuovo hub eventi vengono rifiutate. |10 |
| Numero di partizioni per hub eventi |Entità |- |32 |
| Dimensione massima del nome di un hub eventi |Entità |- | 256 caratteri |
| Dimensioni massime del nome di un gruppo di consumer |Entità |- | 256 caratteri |
| Numero di ricevitori non epoch per gruppo consumer |Entità |- |5 |
| Numero massimo di unità di velocità effettiva |Spazio dei nomi |Il superamento del limite di unità elaborate provoca la limitazione dei dati e la generazione di un'[eccezione server occupato](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Per richiedere un numero maggiore di unità elaborate per un livello Standard, inviare una [richiesta di supporto](/azure/azure-portal/supportability/how-to-create-azure-support-request). Le [unità elaborate aggiuntive](../articles/event-hubs/event-hubs-auto-inflate.md) sono disponibili in blocchi da 20 in base a un acquisto con impegno. |20 |
| Numero di regole di autorizzazione per spazio dei nomi |Spazio dei nomi|Le richieste successive di creazione di regole di autorizzazione vengono rifiutate.|12 |
| Numero di chiamate al metodo GetRuntimeInformation | Entità | - | 50 al secondo | 
| Numero di regole di rete virtuale e di configurazione IP | Entità | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Hub eventi Basic e Standard - quote e limiti
| Limite | Scope | Note | Basic | Standard |
| --- | --- | --- | -- | --- |
| Dimensione massima degli eventi di Hub eventi|Entità | &nbsp; | 256 KB | 1 MB |
| Numero di gruppi consumer per hub eventi |Entità | &nbsp; |1 |20 |
| Numero di connessioni AMQP per spazio dei nomi |Spazio dei nomi |Le richieste successive di connessioni aggiuntive vengono rifiutate e il codice chiamante riceverà un'eccezione. |100 |5\.000|
| Periodo di conservazione massimo dei dati dell'evento |Entità | &nbsp; |1 giorno |1-7 giorni |
|Spazio dei nomi abilitato per Apache Kafka|Spazio dei nomi |Lo spazio dei nomi di Hub eventi trasmette le applicazioni tramite il protocollo Kafka |No | Sì |
|Acquisizione |Entità | Se abilitato, vengono usati micro-batch nello stesso flusso |No |Sì |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Hub eventi Dedicato - quote e limiti
L'offerta Hub eventi Dedicato viene fatturata a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello Dedicato offre tutte le funzionalità del piano Standard, ma con capacità e limiti su scala aziendale per i clienti con carichi di lavoro intensi. 

| Funzionalità | Limiti |
| --- | ---|
| Larghezza di banda |  20 unità di capacità |
| Spazi dei nomi | 50 per unità di capacità |
| Hub eventi |  1\.000 per spazio dei nomi |
| Eventi in ingresso | Incluso |
| Dimensioni del messaggio | 1 MB |
| Partizioni | 2\.000 per unità di capacità |
| Gruppi di consumer | Nessun limite per unità di capacità, 1.000 per hub eventi |
| Connessioni negoziate | 100 K incluse |
| Conservazione dei messaggi | 90 giorni, 10 TB inclusi per unità di capacità |
| Acquisizione | Incluso |
