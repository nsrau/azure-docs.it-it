---
title: Accesso al feed di modifiche in Azure Cosmos DB
description: Questo articolo descrive le diverse opzioni disponibili per eseguire operazioni di lettura e accesso sul feed di modifiche in Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 266a13b7702f567e69129c0b4e92c4bd7c0f29ef
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982497"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lettura del feed di modifiche in Azure Cosmos DB

È possibile usare il feed di modifiche di Azure Cosmos DB con una delle opzioni seguenti:

* Uso di Funzioni di Azure
* Uso del processore dei feed di modifiche
* Uso di Azure Cosmos DB SQL API SDK
* Uso del modello di pull del feed di modifiche (anteprima)

## <a name="using-azure-functions"></a>Uso di Funzioni di Azure

Funzioni di Azure è l'opzione più semplice e consigliata. Quando si crea un trigger di Funzioni di Azure per Cosmos DB, basta selezionare il contenitore da connettere per attivare la funzione di Funzioni di Azure quando viene apportata una modifica al contenitore. I trigger possono essere creati usando il portale di Funzioni di Azure, il portale di Azure Cosmos DB o a livello di codice con gli SDK. Visual Studio e VS Code forniscono il supporto per scrivere le funzioni di Azure ed è anche possibile usare l'interfaccia della riga di comando di Funzioni di Azure per lo sviluppo multipiattaforma. È possibile scrivere il codice ed eseguire il debug dal desktop e quindi distribuire la funzione con un solo clic. Per altre informazioni, vedere gli articoli [Elaborazione di database serverless con Funzioni di Azure](serverless-computing-database.md) e [Using change feed with Azure Functions](change-feed-functions.md) (Uso del feed di modifiche con Funzioni di Azure).

## <a name="using-the-change-feed-processor"></a>Uso del processore dei feed di modifiche

Il processore dei feed delle modifiche rende tutto più semplice, pur offrendo un controllo completo del feed di modifiche. La libreria segue il criterio osservatore in cui la funzione di elaborazione viene chiamata dalla libreria. Se il feed di modifiche è caratterizzato da una velocità effettiva elevata, è possibile creare più istanze client per leggere il feed di modifiche. Poiché si usa la libreria del processore dei feed delle modifiche, il carico verrà diviso automaticamente tra i diversi client senza dover implementare questa logica. Tutte le complessità vengono gestite dalla libreria. Per altre informazioni, vedere [Uso del processore dei feed di modifiche](change-feed-processor.md). Il processore del feed di modifiche è incluso in [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Uso di Azure Cosmos DB SQL API SDK

Con l'SDK, si ottiene un controllo di basso livello del feed delle modifiche. È possibile gestire il checkpoint, accedere a una chiave di partizione logica specifica e così via. Se si hanno più lettori, è possibile usare `ChangeFeedOptions` per distribuire il carico delle letture in thread diversi o in client diversi.

## <a name="using-the-change-feed-pull-model"></a>Uso del modello di pull del feed di modifiche

Il [modello di pull del feed di modifiche](change-feed-pull-model.md) consente di utilizzare il feed di modifiche in modo personalizzato e attivare l'elaborazione parallela delle modifiche con elementi FeedRange. Un elemento FeedRange è costituito da un intervallo di valori di chiave di partizione. Grazie al modello di pull del feed di modifiche, è anche possibile elaborare facilmente le modifiche per una chiave di partizione specifica.

> [!NOTE]
> Il modello di pull del feed di modifiche è attualmente disponibile solo in [anteprima in Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview). L'anteprima non è ancora disponibile per altre versioni dell'SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed di modifiche nelle API per Cassandra e MongoDB

La funzionalità del feed di modifiche è disponibile come flusso di modifiche nell'API MongoDB e come query con predicato nell'API Cassandra. Per altre informazioni sui dettagli di implementazione per l'API MongoDB, vedere [Flussi di modifiche nell'API Azure Cosmos DB per MongoDB](mongodb-change-streams.md).

La versione nativa di Apache Cassandra include Change Data Capture (CDC), un meccanismo per contrassegnare tabelle specifiche per l'archiviazione e rifiutare operazioni di scrittura in tali tabelle quando viene raggiunta una dimensione su disco configurabile per il log CDC. La funzionalità del feed di modifiche nell'API Azure Cosmos DB per Cassandra consente di eseguire query relative alle modifiche con predicato tramite CQL. Per altre informazioni sui dettagli di implementazione, vedere [Feed di modifiche nell'API Azure Cosmos DB per Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere altre informazioni sul feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso della libreria del processore dei feed di modifiche](change-feed-processor.md)
