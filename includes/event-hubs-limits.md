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
ms.openlocfilehash: 928b0bcfb32d1a319c811d00fc84a37f85702895
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857414"
---
Nella tabella seguente sono elencate le quote e i limiti specifici di [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Per informazioni sui prezzi di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Ambito | Note | Value |
| --- | --- | --- | --- |
| Numero di spazi dei nomi di Hub eventi per sottoscrizione |Sottoscrizione |- |100 |
| Numero di hub eventi per ogni spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di un nuovo hub eventi vengono rifiutate. |10 |
| Numero di partizioni per hub eventi |Entità |- |32 |
| Dimensione massima degli eventi di Hub eventi|Entità |- |1 MB |
| Dimensione massima del nome di un hub eventi |Entità |- |50 caratteri |
| Numero di ricevitori non epoch per gruppo consumer |Entità |- |5 |
| Numero massimo di unità di velocità effettiva |Spazio dei nomi |Il superamento del limite di unità elaborate causa la limitazione della velocità dei dati e la generazione di un' [eccezione server occupato](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Per richiedere un numero maggiore di unità di velocità effettiva per un livello standard, archiviare una [richiesta di supporto](/azure/azure-supportability/how-to-create-azure-support-request). Le [unità elaborate aggiuntive](../articles/event-hubs/event-hubs-auto-inflate.md) sono disponibili in blocchi da 20 in base a un acquisto con impegno. |20 |
| Numero di regole di autorizzazione per spazio dei nomi |Spazio dei nomi|Le successive richieste di creazione della regola di autorizzazione vengono rifiutate.|12 |
| Numero di chiamate al Metodo GetRuntimeInformation | Entità | - | 50 al secondo | 
| Numero di regole di rete virtuale (VNet) e di configurazione IP | Entità | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Hub eventi Basic e standard-quote e limiti
| Limite | Ambito | Note | Basic | Standard |
| --- | --- | --- | -- | --- |
| Numero di gruppi consumer per hub eventi |Entità | - |1 |20 |
| Numero di connessioni AMQP per spazio dei nomi |Spazio dei nomi |Le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. |100 |5\.000|
| Periodo di conservazione massimo dei dati dell'evento |Entità | - |1 giorno |1-7 giorni |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Hub eventi Dedicato-quote e limiti
L'offerta Hub eventi Dedicato viene fatturata a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello dedicato offre tutte le funzionalità del piano standard, ma con capacità e limiti di scalabilità aziendale per i clienti con carichi di lavoro complessi. 

| Funzionalità | Limiti |
| --- | ---|
| Larghezza di banda |  20 CUs |
| Spazi dei nomi | 50 per CU |
| Hub eventi |  1000 per spazio dei nomi |
| Eventi in ingresso | Incluso |
| Dimensioni del messaggio | 1 milione byte |
| Partizioni | 2000 per CU |
| Gruppi di consumer | Nessun limite per CU, 1000 per hub eventi |
| Connessioni negoziate | 100 K incluse |
| Conservazione messaggi | Fino a 7 giorni (la conservazione di 90 giorni sarà presto disponibile), 10 TB inclusi per CU |
| Acquisizione | Incluso |
