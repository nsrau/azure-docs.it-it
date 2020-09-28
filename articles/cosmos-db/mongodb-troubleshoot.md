---
title: Risolvere gli errori comuni nell'API di Azure Cosmos DB per il database Mongo
description: Questo documento illustra i modi per risolvere i problemi comuni riscontrati nell'API di Azure Cosmos DB per MongoDB.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: 27a9c7eb48c4a0148401c0d146a50a5197593806
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409630"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Risolvere i problemi comuni nell'API Azure Cosmos DB per MongoDB

L'articolo seguente descrive gli errori comuni e le soluzioni per i database che usano l'API Azure Cosmos DB per MongoDB.

>[!Note]
> Azure Cosmos DB non ospita il motore MongoDB. Fornisce un'implementazione della [versione del protocollo wire](mongodb-feature-support-36.md) di MongoDB 3,6 e del supporto legacy per il [protocollo wire versione 3,2](mongodb-feature-support.md), quindi alcuni di questi errori sono disponibili solo nell'API Azure Cosmos DB per MongoDB. 

## <a name="common-errors-and-solutions"></a>Errori comuni e soluzioni

| Errore               | Codice  | Descrizione  | Soluzione  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | L'esecuzione della richiesta ha superato il timeout di 60 secondi. | Questo errore può essere dovuto a numerose cause. Una delle cause è che la capacità attualmente allocata per le unità richiesta non è sufficiente per completare la richiesta. Questo problema può essere risolto aumentando le unità richiesta di tale raccolta o database. In altri casi, questo errore può essere risolto suddividendo una richiesta di grandi dimensioni in quelle più piccole. |
| TooManyRequests     | 16500 | Il numero totale di unità richiesta utilizzate è maggiore del livello di unità richiesta di cui è stato effettuato il provisioning per la raccolta ed è stata applicata la limitazione. | Valutare la possibilità di dimensionare la velocità effettiva assegnata a un contenitore o a un set di contenitori dal portale di Azure oppure riprovare. |
| ExceededMemoryLimit | 16501 | In quanto servizio multi-tenant, l'operazione ha superato il limite di allocazione di memoria del client. | Ridurre l'ambito dell'operazione tramite criteri di query più restrittivi oppure contattare il supporto tecnico dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Esempio: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Il percorso dell'indice corrispondente all'elemento Ordina per specificato è escluso/La query Ordina per non dispone di un indice composto corrispondente da cui può essere servita. | 2 | La query richiede un ordinamento in un campo non indicizzato. | Creare un indice o un indice composto corrispondente per la query di ordinamento da tentare. |
| Problemi con la versione protocollo di collegamento di MongoDB | - | Le versioni precedenti dei driver MongoDB non sono in grado di rilevare il nome dell'account Azure Cosmos nelle stringhe di connessione. | Aggiungere *appname = @**AccountName** @ * alla fine della stringa di connessione dell'API Cosmos DB per MongoDB, dove ***AccountName*** è il nome dell'account Cosmos DB. |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.

