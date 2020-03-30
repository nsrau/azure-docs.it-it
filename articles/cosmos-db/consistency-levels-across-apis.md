---
title: Livelli di coerenza e API di Azure Cosmos DB
description: Comprendere il mapping del livello di coerenza tra le diverse API in Azure Cosmos DB e Apache Cassandra, MongoDBUnderstanding the consistency level mapping between different APIs in Azure Cosmos DB and Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131464"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Livelli di coerenza e API di Azure Cosmos DB

Azure Cosmos DB fornisce supporto nativo per le API compatibili con il protocollo wire per i database più diffusi. Questi includono MongoDB, Apache Cassandra, Gremlin e Archiviazione tabelle di Azure.These include MongoDB, Apache Cassandra, Gremlin, and Azure Table storage. Questi database non offrono modelli di coerenza definiti con precisione o garanzie supportate dal servizio SLA per i livelli di coerenza. In genere forniscono solo un subset dei cinque modelli di coerenza offerti da Cosmos DB. 

Quando si usano l'API SQL, l'API Gremlin e l'API delle tabelle, viene usato il livello di coerenza predefinito configurato nell'account Cosmos di Azure.When using SQL API, Gremlin API, and Table API, the default consistency level configured on the Azure Cosmos account is used. 

Quando si usa l'API Cassandra o l'API di Azure Cosmos DB per MongoDB, le applicazioni ottengono un set completo di livelli di coerenza offerti da Apache Cassandra e MongoDB, rispettivamente, con garanzie di coerenza e durabilità ancora più avanzate. Questo documento mostra i livelli di coerenza di Azure Cosmos DB corrispondenti per i livelli di coerenza Apache Cassandra e MongoDB.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Eseguire il mapping tra i livelli di coerenza di Azure Cosmos DB e Apache Cassandra

A differenza di Azure Cosmos DB, Apache Cassandra non fornisce in modo nativo garanzie di coerenza definite con precisione.  Al contrario, Apache Cassandra fornisce un livello di coerenza di scrittura e un livello di coerenza di lettura, per consentire i compromessi di disponibilità elevata, coerenza e latenza. Quando si usa l'API Cassandra di Azure Cosmos DB:When using Azure Cosmos DB's Cassandra API: 

* Il livello di coerenza di scrittura di Apache Cassandra è mappato al livello di coerenza predefinito configurato nell'account Azure Cosmos.The write consistency level of Apache Cassandra is mapped to the default consistency level configured on your Azure Cosmos account. La coerenza per un'operazione di scrittura (CL) non può essere modificata in base alle richieste.

* Azure Cosmos DB esederà dinamicamente il livello di coerenza di lettura specificato dal driver client Cassandra a uno dei livelli di coerenza di Azure Cosmos DB configurati dinamicamente in una richiesta di lettura. 

The following table illustrates how the native Cassandra consistency levels are mapped to the Azure Cosmos DB's consistency levels when using Cassandra API:  

[![Mappatura del modello di coerenza Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mappatura tra I livelli di coerenza di MongoDB e Azure Cosmos DB

A differenza di Azure Cosmos DB, il MongoDB nativo non fornisce garanzie di coerenza definite con precisione. MongoDB nativo consente agli utenti di configurare le seguenti garanzie di coerenza: un problema di scrittura, un problema di lettura e la direttiva isMaster - per indirizzare le operazioni di lettura a repliche primarie o secondarie per ottenere il livello di coerenza desiderato. 

Quando si usa l'API di Azure Cosmos DB per MongoDB, il driver MongoDB considera l'area di scrittura come replica primaria e tutte le altre aree vengono lette. È possibile scegliere l'area associata all'account Cosmos di Azure come replica primaria. 

Durante l'uso dell'API di Azure Cosmos DB per MongoDB:While using Azure Cosmos DB's API for MongoDB:

* Il problema di scrittura viene mappato al livello di coerenza predefinito configurato nell'account Azure Cosmos.The write concern is mapped to the default consistency level configured on your Azure Cosmos account.
 
* Azure Cosmos DB esederà dinamicamente il problema di lettura specificato dal driver client MongoDB a uno dei livelli di coerenza di Azure Cosmos DB configurato dinamicamente in una richiesta di lettura.  

* È possibile annotare un'area specifica associata all'account Cosmos di Azure come "Master" rendendo l'area come prima area scrivibile. 

The following table illustrates how the native MongoDB write/read concerns are mapped to the Azure Cosmos consistency levels when using Azure Cosmos DB's API for MongoDB:

[![Mapping del modello di coerenza MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sui livelli di coerenza e la compatibilità tra le API di Azure Cosmos DB e le API open source. Vedere gli articoli seguenti:

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Funzionalità di MongoDB supportate dall'API Azure Cosmos DB per MongoDB](mongodb-feature-support.md)
* [Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB](cassandra-support.md)
