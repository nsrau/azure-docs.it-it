---
title: Come configurare l'ora di avvio del processore dei feed di modifiche - Azure Cosmos DB
description: Informazioni su come configurare l'avvio della lettura del processore dei feed di modifiche da una data e ora specifiche
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093653"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Come configurare l'ora di avvio del processore dei feed di modifiche

Questo articolo descrive come è possibile configurare l'avvio della lettura del [processore dei feed di modifiche](./change-feed-processor.md) da una data e ora specifiche.

## <a name="default-behavior"></a>Comportamento predefinito

Quando un processore dei feed di modifiche viene avviato per la prima volta, inizializza il contenitore per i lease e avvia la [durata dell'elaborazione](./change-feed-processor.md#processing-life-cycle). Eventuali modifiche apportate nel contenitore precedentemente alla prima inizializzazione del processore dei feed di modifiche non verranno rilevate.

## <a name="reading-from-a-previous-date-and-time"></a>Lettura da una data e un'ora precedenti

È possibile inizializzare il processore dei feed di modifiche in modo da leggere le modifiche a partire da una **data e ora specifiche**, passando un'istanza di `DateTime` all'estensione del generatore `WithStartTime`:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Il processore dei feed di modifiche verrà inizializzato per la data e l'ora specifiche e inizierà a leggere le modifiche che si sono verificate dopo.

## <a name="reading-from-the-beginning"></a>Lettura dall'inizio

In altri scenari, ad esempio la migrazione dei dati o l'analisi dell'intera cronologia di un contenitore, è necessario leggere il feed di modifiche dall'**inizio della durata del contenitore**. A tale scopo, è possibile usare `WithStartTime` sull'estensione del generatore, ma passando `DateTime.MinValue.ToUniversalTime()`, che genererebbe la rappresentazione UTC del valore minimo `DateTime`, come segue:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Il processore dei feed di modifiche verrà inizializzato e inizierà a leggere le modifiche dall'inizio della durata del contenitore.

> [!NOTE]
> Queste opzioni di personalizzazione funzionano solo per impostare il punto di partenza nel tempo del processore dei feed di modifiche. Una volta inizializzato il contenitore dei lease per la prima volta, modificarle non ha alcun effetto.

> [!NOTE]
> Quando si specifica un punto nel tempo, verranno lette solo le modifiche per gli elementi attualmente presenti nel contenitore. Se un elemento è stato eliminato, viene rimossa anche la relativa cronologia nel feed di modifiche.

## <a name="additional-resources"></a>Risorse aggiuntive

* [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Esempi di utilizzo su GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Altri esempi in GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile procedere ad acquisire altre informazioni sul processore di feed di modifiche negli articoli seguenti:

* [Panoramica del processore di feed di modifiche](change-feed-processor.md)
* [Uso dello strumento di stima di feed di modifiche](how-to-use-change-feed-estimator.md)
