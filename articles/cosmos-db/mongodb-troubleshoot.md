---
title: Risolvere gli errori comuni nell'API di Azure Cosmos DB per il database Mongo
description: Questo documento illustra i modi per risolvere i problemi comuni riscontrati nell'API di Azure Cosmos DB per MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75941835"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Risolvere i problemi comuni nell'API Azure Cosmos DB per MongoDB

Azure Cosmos DB implementa i protocolli wire dei database NoSQL comuni, incluso MongoDB. A causa dell'implementazione del protocollo wire, è possibile interagire in modo trasparente con Azure Cosmos DB usando gli SDK, i driver e gli strumenti client esistenti che funzionano con i database NoSQL. Azure Cosmos DB non usa codice sorgente dei database per fornire API compatibili con Wire per uno dei database NoSQL. Qualsiasi driver client MongoDB che riconosce le versioni del protocollo Wire può connettersi a Azure Cosmos DB.

Anche se l'API di Azure Cosmos DB per MongoDB è compatibile con la versione 3,2 del protocollo wire di MongoDB (gli operatori di query e le funzionalità aggiunte nella versione 3,4 sono attualmente disponibili come anteprima), esistono alcuni codici di errore personalizzati che corrispondono a Azure Cosmos DB errori specifici. Questo articolo illustra diversi errori, codici di errore e i passaggi per risolvere tali errori.

## <a name="common-errors-and-solutions"></a>Errori e soluzioni comuni

| Errore               | Codice  | Descrizione  | Soluzione  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Il numero totale di unità richiesta utilizzate è maggiore del livello di unità richiesta di cui è stato effettuato il provisioning per la raccolta ed è stata applicata la limitazione. | Provare a ridimensionare la velocità effettiva assegnata a un contenitore o a un set di contenitori dalla portale di Azure oppure è possibile ripetere l'operazione. |
| ExceededMemoryLimit | 16501 | In quanto servizio multi-tenant, l'operazione ha superato il limite di allocazione di memoria del client. | Ridurre l'ambito dell'operazione tramite criteri di query più restrittivi oppure contattare il supporto tecnico dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Esempio: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Il percorso di indice corrispondente all'elemento Order by specificato è escluso/la query Order by non dispone di un indice composto corrispondente da cui può essere servito. | 2 | La query richiede un ordinamento in un campo non indicizzato. | Creare un indice o un indice composto corrispondente per la query di ordinamento da tentare. |
| Problemi con la versione protocollo di collegamento di MongoDB | - | Le versioni precedenti dei driver MongoDB non sono in grado di rilevare il nome dell'account Azure Cosmos nelle stringhe di connessione. | Aggiungere *appname = @**AccountName** @ * alla fine della stringa di connessione dell'API Cosmos DB per MongoDB, dove ***AccountName*** è il nome dell'account Cosmos DB. |


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.

