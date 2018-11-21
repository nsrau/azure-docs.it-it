---
title: Accesso al feed di modifiche in Azure Cosmos DB
description: Questo articolo descrive diverse opzioni disponibili per leggere e accedere al feed di modifiche in Azure Cosmos DB.
author: rafats
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rafats
ms.openlocfilehash: 3168593e30a7f5acf49960c8479da75529860a65
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684546"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lettura del feed di modifiche in Azure Cosmos DB

È possibile usare il feed di modifiche di Azure Cosmos DB con una delle opzioni seguenti:

* Uso di Funzioni di Azure
* Uso della libreria del processore dei feed di modifiche
* Uso di Azure Cosmos DB SQL API SDK

## <a name="using-azure-functions"></a>Uso di Funzioni di Azure

Funzioni di Azure è l'opzione più semplice e consigliata. Quando si crea un trigger di Azure Cosmos DB in un'applicazione di Funzioni di Azure, è possibile selezionare il contenitore da connettere e la funzione di Azure viene attivata quando viene apportata una modifica al contenitore. I trigger possono essere creati usando il portale di Funzioni di Azure, il portale di Azure Cosmos DB o a livello di codice con gli SDK. Visual Studio e VS Code forniscono il supporto per scrivere le funzioni di Azure ed è anche possibile usare l'interfaccia della riga di comando di Funzioni di Azure per lo sviluppo multipiattaforma. È possibile scrivere il codice ed eseguire il debug dal desktop e quindi distribuire la funzione con un solo clic. Per altre informazioni, vedere gli articoli [Elaborazione di database serverless con Funzioni di Azure](serverless-computing-database.md) e [Using change feed with Azure Functions](change-feed-functions.md) (Uso del feed di modifiche con Funzioni di Azure).

## <a name="using-the-change-feed-processor-library"></a>Uso della libreria del processore dei feed di modifiche

La libreria del processore dei feed delle modifiche rende tutto più semplice, pur offrendo un controllo completo del feed di modifiche. La libreria segue il criterio osservatore in cui la funzione di elaborazione viene chiamata dalla libreria. Se il feed di modifiche è caratterizzato da una velocità effettiva elevata, è possibile creare più istanze client per leggere il feed di modifiche. Poiché si usa la libreria del processore dei feed delle modifiche, il carico verrà diviso automaticamente tra i diversi client senza dover implementare questa logica. Tutte le complessità vengono gestite dalla libreria. Se si vuole avere un servizio di bilanciamento del carico proprio, è possibile implementare `IPartitionLoadBalancingStrategy` per elaborare il feed di modifiche con una strategia di partizionamento personalizzata. Per altre informazioni, vedere [Uso della libreria del processore dei feed di modifiche](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Uso di Azure Cosmos DB SQL API SDK

Con l'SDK, si ottiene un controllo di basso livello del feed delle modifiche. È possibile gestire il checkpoint, accedere a una chiave di partizione logica specifica e così via. Se si hanno più lettori, è possibile usare `ChangeFeedOptions` per distribuire il carico delle letture in thread diversi o in client diversi. 

## <a name="next-steps"></a>Passaggi successivi

È ora possibile procedere per altre informazioni sul feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso della libreria del processore dei feed di modifiche](change-feed-processor.md)