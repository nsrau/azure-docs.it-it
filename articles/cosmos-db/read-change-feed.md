---
title: Accesso al feed di modifiche in Azure Cosmos DB
description: Questo articolo descrive le diverse opzioni disponibili per leggere e accedere al feed delle modifiche in Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 266a13b7702f567e69129c0b4e92c4bd7c0f29ef
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982497"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lettura del feed di modifiche in Azure Cosmos DB

È possibile usare il feed di modifiche di Azure Cosmos DB con una delle opzioni seguenti:

* Uso di Funzioni di Azure
* Uso del processore del feed delle modifiche
* Uso di Azure Cosmos DB SQL API SDK
* Uso del modello pull del feed delle modifiche (anteprima)

## <a name="using-azure-functions"></a>Uso di Funzioni di Azure

Funzioni di Azure è l'opzione più semplice e consigliata. Quando si crea un trigger di funzioni di Azure per Cosmos DB, è possibile selezionare il contenitore per la connessione e la funzione di Azure viene attivata ogni volta che viene apportata una modifica al contenitore. I trigger possono essere creati usando il portale di Funzioni di Azure, il portale di Azure Cosmos DB o a livello di codice con gli SDK. Visual Studio e VS Code forniscono il supporto per scrivere le funzioni di Azure ed è anche possibile usare l'interfaccia della riga di comando di Funzioni di Azure per lo sviluppo multipiattaforma. È possibile scrivere il codice ed eseguire il debug dal desktop e quindi distribuire la funzione con un solo clic. Per altre informazioni, vedere gli articoli [Elaborazione di database serverless con Funzioni di Azure](serverless-computing-database.md) e [Using change feed with Azure Functions](change-feed-functions.md) (Uso del feed di modifiche con Funzioni di Azure).

## <a name="using-the-change-feed-processor"></a>Uso del processore del feed delle modifiche

Il processore del feed delle modifiche nasconde la complessità e fornisce comunque un controllo completo sul feed delle modifiche. La libreria segue il criterio osservatore in cui la funzione di elaborazione viene chiamata dalla libreria. Se il feed di modifiche è caratterizzato da una velocità effettiva elevata, è possibile creare più istanze client per leggere il feed di modifiche. Poiché si usa la libreria del processore dei feed delle modifiche, il carico verrà diviso automaticamente tra i diversi client senza dover implementare questa logica. Tutte le complessità vengono gestite dalla libreria. Per altre informazioni, vedere [uso del processore di feed di modifiche](change-feed-processor.md). Il processore del feed delle modifiche fa parte del [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Uso di Azure Cosmos DB SQL API SDK

Con l'SDK, si ottiene un controllo di basso livello del feed delle modifiche. È possibile gestire il checkpoint, accedere a una particolare chiave di partizione logica e così via. Se si dispone di più lettori, è possibile `ChangeFeedOptions` usare per distribuire il carico di lettura a thread diversi o a client diversi.

## <a name="using-the-change-feed-pull-model"></a>Uso del modello pull del feed delle modifiche

Il [modello pull del feed](change-feed-pull-model.md) delle modifiche consente di usare il feed delle modifiche in modo personalizzato e parallelizzare l'elaborazione delle modifiche con FeedRanges. Un FeedRange si estende su un intervallo di valori di chiave di partizione. Utilizzando il modello pull del feed delle modifiche, è anche facile elaborare le modifiche per una chiave di partizione specifica.

> [!NOTE]
> Il modello pull del feed delle modifiche è attualmente disponibile in [anteprima solo in Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . L'anteprima non è ancora disponibile per altre versioni di SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed delle modifiche nelle API per Cassandra e MongoDB

La funzionalità del feed delle modifiche viene rilevata come flusso di modifica nell'API MongoDB ed esegue una query con predicato in API Cassandra. Per altre informazioni sui dettagli di implementazione per l'API MongoDB, vedere i [flussi di modifiche nell'api Azure Cosmos DB per MongoDB](mongodb-change-streams.md).

Apache Cassandra nativo fornisce Change Data Capture (CDC), un meccanismo per contrassegnare le tabelle specifiche per l'archiviazione e rifiutare le Scritture nelle tabelle quando viene raggiunta una dimensione configurabile su disco per il log CDC. La funzionalità del feed delle modifiche nell'API Azure Cosmos DB per Cassandra consente di eseguire query sulle modifiche con predicato tramite CQL. Per altre informazioni sui dettagli di implementazione, vedere [feed delle modifiche nell'API Azure Cosmos DB per Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere altre informazioni sul feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso della libreria del processore del feed delle modifiche](change-feed-processor.md)
