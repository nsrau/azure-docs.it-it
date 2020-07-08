---
title: Modello di pull del feed di modifiche
description: Informazioni su come usare il modello di pull del feed di modifiche di Azure Cosmos DB per leggere il feed di modifiche e le differenze tra il modello di pull e il processore dei feed di modifiche
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/19/2020
ms.reviewer: sngun
ms.openlocfilehash: 8916f4b9824f88361fdeb9d866f84adb71e8138e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563803"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Modello di pull del feed di modifiche in Azure Cosmos DB

Il modello di pull del feed di modifiche consente di utilizzare il feed di modifiche di Azure Cosmos DB in modo personalizzato. Come è già possibile fare con il [processore di feed di modifiche](change-feed-processor.md), è possibile usare il modello di pull del feed di modifiche per impostare l'elaborazione parallela delle modifiche tra più consumer di feed di modifiche.

> [!NOTE]
> Il modello di pull del feed di modifiche è attualmente disponibile solo in [anteprima in Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview). L'anteprima non è ancora disponibile per altre versioni dell'SDK.

## <a name="consuming-an-entire-containers-changes"></a>Utilizzo delle modifiche di un intero contenitore

È possibile creare un oggetto `FeedIterator` per elaborare il feed di modifiche usando il modello di pull. Quando si crea inizialmente un oggetto `FeedIterator`, è possibile specificare un parametro `StartTime` facoltativa in `ChangeFeedRequestOptions`. Se non viene specificato, il valore del parametro `StartTime` sarà l'ora corrente.

L'oggetto `FeedIterator` è disponibile in due versioni. Oltre agli esempi seguenti che restituiscono oggetti entità, è anche possibile ottenere la risposta con il supporto per `Stream`. Gli oggetti Stream consentono di leggere i dati senza prima deserializzarli e quindi di risparmiare risorse del client.

Ecco un esempio per ottenere un oggetto `FeedIterator` che restituisce oggetti entità, in questo caso un oggetto `User`:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Ecco un esempio per ottenere un oggetto `FeedIterator` che restituisce un oggetto `Stream`:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

Con `FeedIterator` è possibile elaborare facilmente il feed di modifiche di un intero contenitore in modo personalizzato. Ad esempio:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator<User>();

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Utilizzo delle modifiche di una chiave di partizione

In alcuni casi può essere necessario elaborare solo le modifiche di una chiave di partizione specifica. È possibile ottenere un oggetto `FeedIterator` per una chiave di partizione specifica ed elaborare le modifiche allo stesso modo di un intero contenitore.

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator<User>(new PartitionKey("myPartitionKeyValueToRead"));

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Uso di FeedRange per la parallelizzazione

Nel [processore del feed di modifiche](change-feed-processor.md) il lavoro viene distribuito automaticamente tra più consumer. Nel modello di pull del feed di modifiche è possibile usare `FeedRange` per impostare l'elaborazione parallela del feed di modifiche. Un elemento `FeedRange` rappresenta un intervallo di valori di chiave di partizione.

Ecco un esempio che illustra come ottenere un elenco di intervalli per il contenitore:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Quando si ottiene un elenco di elementi FeedRange per il contenitore, si otterrà un elemento `FeedRange` per ogni [partizione fisica](partition-data.md#physical-partitions).

Con `FeedRange` è quindi possibile creare un oggetto `FeedIterator` per impostare l'elaborazione parallela del feed di modifiche tra più computer o thread. A differenza dell'esempio precedente in cui è stato illustrato come ottenere un singolo oggetto `FeedIterator` per l'intero contenitore, è possibile usare l'elemento `FeedRange` per ottenere più oggetti FeedIterator per elaborare il feed di modifiche in parallelo.

Nel caso in cui si intenda usare oggetti FeedRange, è necessario disporre di un processo dell'agente di orchestrazione che ottenga oggetti FeedRange e li distribuisca a tali computer. Questa distribuzione può essere simile a quella descritta di seguito:

* Uso di `FeedRange.ToJsonString` e distribuzione di questo valore stringa. I consumer possono usare questo valore con `FeedRange.FromJsonString`
* Se la distribuzione è in-process, passaggio del riferimento all'oggetto `FeedRange`.

Ecco un esempio che illustra come leggere dall'inizio del feed di modifiche del contenitore usando due computer ipotetici distinti che eseguono la lettura in parallelo:

Computer 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

Computer 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>Salvataggio dei token di continuazione

Per salvare la posizione dell'oggetto `FeedIterator`, è possibile creare un token di continuazione. Un token di continuazione è un valore stringa che tiene traccia delle ultime modifiche elaborate di FeedIterator. In questo modo `FeedIterator` può riprendere l'elaborazione da questo punto in un secondo momento. Il codice seguente leggerà il feed delle modifiche dopo la creazione del contenitore. Quando non sono più disponibili modifiche, verrà salvato in modo permanente un token di continuazione che consentirà di riprendere il consumo del feed di modifiche in un secondo momento.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

Il token di continuazione di FeedIterator non scade fino a quando esiste il contenitore Cosmos.

## <a name="comparing-with-change-feed-processor"></a>Confronto con il processore dei feed di modifiche

In molti scenari è possibile elaborare il feed di modifiche usando il [processore del feed di modifiche](change-feed-processor.md) o il modello di pull. I token di continuazione del modello di pull e il contenitore di lease del processore del feed di modifiche sono entrambi "segnalibri" per l'ultimo elemento o batch di elementi elaborato nel feed di modifiche.
Non è però possibile convertire i token di continuazione in un contenitore di lease (o viceversa).

È opportuno usare il modello di pull negli scenari seguenti:

- Lettura delle modifiche di una chiave di partizione specifica
- Controllo della velocità di ricezione delle modifiche per l'elaborazione da parte del client
- Esecuzione di una sola lettura dei dati esistenti nel feed di modifiche (ad esempio per eseguire una migrazione dei dati)

Ecco alcune delle differenze principali tra il processore del feed di modifiche e il modello di pull:

|Funzionalità  | Processore dei feed di modifiche| Modello di pull |
| --- | --- | --- |
| Traccia del punto corrente nell'elaborazione del feed di modifiche | Lease (archiviato in un contenitore Azure Cosmos DB) | Token di continuazione (archiviato in memoria o reso persistente manualmente) |
| Possibilità di riprodurre modifiche precedenti | Sì, con modello di push | Sì, con modello di pull|
| Polling per le modifiche future | Verifica automaticamente della presenza di modifiche in base al parametro `WithPollInterval` specificato dall'utente | Manuale |
| Elaborazione delle modifiche di un intero contenitore | Sì, con elaborazione parallela automatica tra più thread/computer che utilizzano lo stesso contenitore| Sì, con elaborazione parallela manuale tramite FeedToken |
| Elaborazione delle modifiche da una sola chiave di partizione | Non supportate | Sì|
| Livello di supporto | Disponibile a livello generale | Anteprima |

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica del feed di modifiche](change-feed.md)
* [Uso del processore dei feed di modifiche](change-feed-processor.md)
* [Attivare Funzioni di Azure](change-feed-functions.md)