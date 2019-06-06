---
title: Risolvere gli errori comuni nell'API di Azure Cosmos DB per Mongodb
description: Questo documento illustra i modi per risolvere i problemi comuni riscontrati nell'API di Azure Cosmos DB per MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735707"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Risolvere i problemi comuni nell'API di Azure Cosmos DB per MongoDB

Azure Cosmos DB implementa i protocolli di rete di database NoSQL comuni, tra cui MongoDB. A causa dell'implementazione del protocollo di rete, è possibile in modo trasparente interagire con Azure Cosmos DB usando il SDK del client esistente, i driver e gli strumenti che funzionano con i database NoSQL. Azure Cosmos DB non utilizzato tutto il codice sorgente dei database per fornire API transito compatibile per i database NoSQL. Qualsiasi driver client MongoDB che riconosce le versioni del protocollo wire può connettersi ad Azure Cosmos DB.

API di Azure Cosmos DB per MongoDB è compatibile con la versione 3.2 del protocollo di trasmissione di MongoDB (gli operatori di query e le funzionalità aggiunte nella versione 3.4 sono attualmente disponibili in anteprima), esistono alcuni codici di errore personalizzato che corrispondono ad Azure Cosmos DB errori specifici. Questo articolo illustra diversi errori, i codici di errore e i passaggi per risolvere gli errori.

## <a name="common-errors-and-solutions"></a>Errori comuni e soluzioni

| Tipi di errore               | Codice  | Descrizione  | Soluzione  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Il numero totale di unità richiesta utilizzate è maggiore del livello di unità richiesta di cui è stato effettuato il provisioning per la raccolta ed è stata applicata la limitazione. | Provare a ridimensionare la velocità effettiva assegnata a un contenitore o un set di contenitori dal portale di Azure oppure è possibile ripetere l'operazione. |
| ExceededMemoryLimit | 16501 | In quanto servizio multi-tenant, l'operazione ha superato il limite di allocazione di memoria del client. | Ridurre l'ambito dell'operazione tramite criteri di query più restrittivi oppure contattare il supporto tecnico dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Esempio: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Problemi di versione wire MongoDB | - | Le versioni precedenti dei driver di MongoDB sono in grado di rilevare il nome dell'account Azure Cosmos nelle stringhe di connessione. | Accodare *appName = @**accountName** @*  alla fine dell'API di Cosmos DB per la stringa di connessione di MongoDB, dove ***accountName*** è il nome dell'account Cosmos DB . |


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.

