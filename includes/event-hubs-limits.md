---
title: File di inclusione
description: File di inclusione
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75901975"
---
Le tabelle seguenti forniscono quote e limiti specifici per gli hub eventi di [Azure.](https://azure.microsoft.com/services/event-hubs/) Per informazioni sui prezzi degli hub eventi, consultate [Prezzi degli hub eventi.](https://azure.microsoft.com/pricing/details/event-hubs/)

I limiti seguenti sono comuni a livelli di base, standard e dedicati. 

| Limite | Scope | Note | valore |
| --- | --- | --- | --- |
| Numero di spazi dei nomi di Hub eventi per sottoscrizione |Subscription |- |100 |
| Numero di hub eventi per ogni spazio dei nomi |Spazio dei nomi |Le richieste successive per la creazione di un nuovo hub eventi vengono rifiutate. |10 |
| Numero di partizioni per hub eventi |Entità |- |32 |
| Dimensione massima del nome di un hub eventi |Entità |- |50 caratteri |
| Numero di ricevitori non epoch per gruppo consumer |Entità |- |5 |
| Numero massimo di unità di velocità effettiva |Spazio dei nomi |Il superamento del limite di unità di velocità effettiva determina la limitazione dei dati e genera [un'eccezione occupata del server.](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) Per richiedere un numero maggiore di unità di velocità effettiva per un livello Standard, presentare una richiesta di [supporto.](/azure/azure-portal/supportability/how-to-create-azure-support-request) Le [unità elaborate aggiuntive](../articles/event-hubs/event-hubs-auto-inflate.md) sono disponibili in blocchi da 20 in base a un acquisto con impegno. |20 |
| Numero di regole di autorizzazione per spazio dei nomi |Spazio dei nomi|Le richieste successive per la creazione di regole di autorizzazione vengono rifiutate.|12 |
| Numero di chiamate al metodo GetRuntimeInformation | Entità | - | 50 al secondo | 
| Numero di regole di rete virtuale (VNet) e configurazione IP | Entità | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Hub eventi Basic e Standard - quote e limiti
| Limite | Scope | Note | Basic | Standard |
| --- | --- | --- | -- | --- |
| Dimensione massima degli eventi di Hub eventi|Entità | &nbsp; | 256 KB | 1 MB |
| Numero di gruppi consumer per hub eventi |Entità | &nbsp; |1 |20 |
| Numero di connessioni AMQP per spazio dei nomi |Spazio dei nomi |Le richieste successive di connessioni aggiuntive vengono rifiutate e il codice chiamante riceve un'eccezione. |100 |5.000|
| Periodo di conservazione massimo dei dati dell'evento |Entità | &nbsp; |1 giorno |1-7 giorni |
|Spazio dei nomi abilitato Apache KafkaApache enabled namespace|Spazio dei nomi |Lo spazio dei nomi Event Hubs trasmette le applicazioni utilizzando il protocollo KafkaEvent Hubs namespace streams applications using Kafka protocol |No | Sì |
|Acquisizione |Entità | Se abilitato, i micro-batch sullo stesso flusso |No |Sì |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Hub eventi dedicati - quote e limiti
L'offerta Event Hubs Dedicated viene fatturata a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello Dedicato offre tutte le funzionalità del piano Standard, ma con capacità di scalabilità aziendale e limiti per i clienti con carichi di lavoro più impegnativi. 

| Funzionalità | Limiti |
| --- | ---|
| Larghezza di banda |  20 CU |
| Spazi dei nomi | 50 per CU |
| Hub eventi |  1000 per spazio dei nomi |
| Eventi in ingresso | Incluso |
| Dimensioni del messaggio | 1 MB |
| Partizioni | 2000 per CU |
| Gruppi di consumer | Nessun limite per CU, 1000 per hub eventi |
| Connessioni negoziate | 100 K incluse |
| Conservazione dei messaggi | 90 giorni, 10 TB inclusi per CU |
| Acquisizione | Incluso |
