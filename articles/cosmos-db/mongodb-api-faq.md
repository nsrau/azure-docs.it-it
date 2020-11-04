---
title: Domande frequenti sull'API Azure Cosmos DB per MongoDB
description: Risposte alle domande frequenti sull'API Azure Cosmos DB per MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 4c93aea5a37fb286b49dabfb97522bf40c8f5429
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333172"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Domande frequenti sull'API Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

L'API Azure Cosmos DB per MongoDB è un livello di compatibilità del protocollo wire che consente alle applicazioni di comunicare in modo semplice e trasparente con il motore di database nativo di Azure Cosmos usando gli SDK e i driver supportati dalla community esistenti per MongoDB. Gli sviluppatori possono ora usare toolchain e competenze di MongoDB esistenti per creare applicazioni che sfruttano i vantaggi offerti da Azure Cosmos DB. Gli sviluppatori traggono vantaggio dalle funzionalità esclusive di Azure Cosmos DB, tra cui la distribuzione globale con replica di scrittura in più aree, l'indicizzazione automatica, la manutenzione dei backup, i contratti di servizio con supporto finanziario e così via.

## <a name="how-do-i-connect-to-my-database"></a>Come ci si connette al database?

Il modo più veloce per connettersi a un database Cosmos con l'API di Azure Cosmos DB per MongoDB è quello di passare al [portale di Azure](https://portal.azure.com). Accedere al proprio account e quindi fare clic su **Avvio rapido** nel menu di spostamento a sinistra. La Guida introduttiva è il modo migliore per ottenere i frammenti di codice per la connessione al database.

Azure Cosmos DB applica standard e requisiti di sicurezza rigidi. Gli account Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite TLS. Assicurarsi quindi di usare TLSv 1.2.

Per altre informazioni, vedere [Connettersi al database Cosmos con l'API di Azure Cosmos DB per MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Codici di errore durante l'uso dell'API di Azure Cosmos DB per MongoDB?

Oltre ai codici di errore comuni di MongoDB, l'API di Azure Cosmos DB per MongoDB può restituire codici di errore specifici:

| Errore               | Codice  | Descrizione  | Soluzione  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Il numero totale di unità richiesta utilizzate è superiore al tasso di unità richiesta di cui è stato effettuato il provisioning per il contenitore ed è stato limitato. | Valutare la possibilità di ridimensionare la velocità effettiva assegnata a un contenitore o a un set di contenitori dal portale di Azure oppure riprovare. |
| ExceededMemoryLimit | 16501 | In quanto servizio multi-tenant, l'operazione ha superato il limite di allocazione di memoria del client. | Ridurre l'ambito dell'operazione tramite criteri di query più restrittivi oppure contattare il supporto tecnico dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br> Esempio: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |

## <a name="supported-drivers"></a>Driver supportati

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Il driver Simba per MongoDB è supportato per l'uso con l'API di Azure Cosmos DB per MongoDB?

Sì, è possibile usare il driver ODBC Mongo di Simba con l'API di Azure Cosmos DB per MongoDB

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app Web .NET usando l'API Azure Cosmos DB per MongoDB](create-mongodb-dotnet.md)
* [Creare un'app console con Java e l'API MongoDB in Azure Cosmos DB](create-mongodb-java.md)
