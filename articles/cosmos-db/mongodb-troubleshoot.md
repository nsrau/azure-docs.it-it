---
title: Risolvere gli errori comuni nell'API di Azure Cosmos DB per Mongo DBTroubleshoot common errors in Azure Cosmos DB's API for Mongo DB
description: In questo documento vengono illustrati i modi per risolvere i problemi comuni riscontrati nell'API di Azure Cosmos DB per MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941835"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Risolvere i problemi comuni nell'API di Azure Cosmos DB per MongoDBTroubleshoot common issues in Azure Cosmos DB's API for MongoDB

Azure Cosmos DB implements the wire protocols of common NoSQL databases, including MongoDB. A causa dell'implementazione del protocollo wire, è possibile interagire in modo trasparente con Azure Cosmos DB usando gli SDK client, i driver e gli strumenti esistenti che funzionano con i database NoSQL. Database Cosmos di Azure non utilizza alcun codice sorgente dei database per fornire API compatibili con wire per uno qualsiasi dei database NoSQL. Qualsiasi driver client MongoDB che comprende le versioni del protocollo wire può connettersi a Azure Cosmos DB.

Mentre l'API di Azure Cosmos DB per MongoDB è compatibile con la versione 3.2 del protocollo wire di MongoDB (gli operatori di query e le funzionalità aggiunte nella versione 3.4 sono attualmente disponibili come anteprima), esistono alcuni codici di errore personalizzati che corrispondono a Azure Cosmos DB errori specifici. In questo articolo vengono illustrati diversi errori, codici di errore e la procedura per risolverli.

## <a name="common-errors-and-solutions"></a>Errori e soluzioni comuni

| Errore               | Codice  | Descrizione  | Soluzione  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Il numero totale di unità richiesta utilizzate è maggiore del livello di unità richiesta di cui è stato effettuato il provisioning per la raccolta ed è stata applicata la limitazione. | Valutare la possibilità di ridimensionare la velocità effettiva assegnata a un contenitore o a un set di contenitori dal portale di Azure oppure è possibile ripetere l'operazione. |
| ExceededMemoryLimit | 16501 | In quanto servizio multi-tenant, l'operazione ha superato il limite di allocazione di memoria del client. | Ridurre l'ambito dell'operazione tramite criteri di query più restrittivi oppure contattare il supporto tecnico dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Esempio: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Il percorso dell'indice corrispondente all'elemento order-by specificato è escluso / L'ordine per query non dispone di un indice composito corrispondente da cui può essere servito. | 2 | La query richiede un ordinamento su un campo non indicizzato. | Creare un indice corrispondente (o indice composito) per la query di ordinamento tentata. |
| Problemi con la versione protocollo di collegamento di MongoDB | - | Le versioni precedenti dei driver MongoDB non sono in grado di rilevare il nome dell'account Azure Cosmos nelle stringhe di connessione. | Accodare *appName,**accountName** @ * alla fine della stringa di connessione Cosmos DB per MongoDB, dove ***accountName*** è il nome dell'account Cosmos DB. |


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.

