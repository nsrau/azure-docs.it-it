---
title: includere file
description: includere file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1b5905046fdde4d5574fd4d39f40dce1bbf11e32
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027645"
---
Le tabelle seguenti forniscono le quote e i limiti specifici di [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Per informazioni sui prezzi di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="common-limits-for-all-tiers"></a>Limiti comuni per tutti i livelli
I limiti seguenti sono comuni tra tutti i livelli. 

| Limite |  Note | valore |
| --- |  --- | --- |
| Numero di spazi dei nomi di Hub eventi per sottoscrizione |- |100 |
| Numero di hub eventi per ogni spazio dei nomi | Le successive richieste per la creazione di un nuovo hub eventi vengono rifiutate. |10 |
| Numero di partizioni per hub eventi |- |32 |
| Dimensioni del nome di un hub eventi |- | 256 caratteri |
| Dimensioni del nome di un gruppo di consumer |- | 256 caratteri |
| Numero di ricevitori non epoch per gruppo consumer |- |5 |
| Numero di regole di autorizzazione per spazio dei nomi | Le richieste successive di creazione di regole di autorizzazione vengono rifiutate.|12 |
| Numero di chiamate al metodo GetRuntimeInformation |  - | 50 al secondo | 
| Numero di regole di rete virtuale e di configurazione IP | - | 128 | 


## <a name="basic-vs-standard-tiers"></a>Livelli Basic e Standard
La tabella seguente illustra i limiti che possono essere diversi per i livelli Basic e Standard. 

| Limite | Note | Basic | Standard |
|---|---|--|---|
| Dimensione massima degli eventi di Hub eventi| &nbsp; | 256 KB | 1 MB |
| Numero di gruppi consumer per hub eventi | &nbsp; |1 |20 |
| Numero di connessioni AMQP per spazio dei nomi | Le richieste successive di connessioni aggiuntive vengono rifiutate e il codice chiamante riceverà un'eccezione. |100 |5\.000|
| Periodo di conservazione massimo dei dati dell'evento | &nbsp; |1 giorno |1-7 giorni |
| Numero massimo di unità di velocità effettiva |Il superamento di questo limite causa la limitazione dei dati e la generazione di un'[eccezione di server occupato](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Per richiedere un numero maggiore di unità elaborate per un livello Standard, inviare una [richiesta di supporto](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). Le [unità elaborate aggiuntive](../articles/event-hubs/event-hubs-auto-inflate.md) sono disponibili in blocchi da 20 in base a un acquisto con impegno. |20 | 20 | 

## <a name="dedicated-tier-vs-standard-tier"></a>Livello Dedicato e Standard
L'offerta Hub eventi Dedicato viene fatturata a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello Dedicato offre tutte le funzionalità del piano Standard, ma con capacità e limiti su scala aziendale per i clienti con carichi di lavoro intensi. 

Fare riferimento a questo [documento](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) per informazioni su come creare un cluster di Hub eventi dedicato usando il portale di Azure.

| Funzionalità | Standard | Dedicato |
| --- |:---|:---|
| Larghezza di banda | 20 unità elaborate (fino a 40 unità elaborate) | 20 unità di capacità |
| Spazi dei nomi |  1 | 50 per unità di capacità |
| Hub eventi |  10 per spazio dei nomi | 1\.000 per spazio dei nomi |
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Dimensioni del messaggio | 1 milione di byte | 1 milione di byte |
| Partizioni | 32 per hub eventi | 1024 per hub eventi<br/>2\.000 per unità di capacità |
| Gruppi di consumer | 20 per hub eventi | Nessun limite per unità di capacità, 1.000 per hub eventi |
| Connessioni negoziate | 1\.000 incluse, massimo 5.000 | 100.000 incluse massimo |
| Conservazione dei messaggi | 7 giorni, 84 TB inclusi per unità elaborata | 90 giorni, 10 TB inclusi per unità di capacità |
| Acquisizione | Pagamento per ogni ora | Incluso |


## <a name="schema-registry-limitations"></a>Limitazioni del registro schemi

### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Limiti uguali per i livelli **standard** e **dedicato** 
| Funzionalità | Limite | 
|---|---|--|
| Lunghezza massima del nome di un gruppo di schemi | 50 |  
| Lunghezza massima del nome di uno schema | 100 |    
| Dimensioni in byte per schema | 1 MB |   
| Numero di proprietà per gruppo di schemi | 1024 |
| Dimensioni in byte per la chiave del gruppo di proprietà | 256 | 
| Dimensioni in byte per valore della proprietà gruppo | 1024 | 


### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Limiti diversi per i livelli **standard** e **dedicato** 

| Limite | Standard | Dedicato | 
|---|---|--|---|
| Dimensioni del registro schemi (spazio dei nomi) in megabyte | 25 |  1024 |
| Numero di gruppi di schemi in un registro schemi o spazio dei nomi | 1 - escluso il gruppo predefinito | 1000 |
| Numero di versioni dello schema in tutti i gruppi di schemi | 25 | 10000 |