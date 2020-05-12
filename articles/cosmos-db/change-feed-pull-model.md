---
title: Modello di pull del feed di modifiche
description: Informazioni su come usare il modello pull del feed di modifiche Azure Cosmos DB per leggere il feed delle modifiche e le differenze tra il modello pull e il processore dei feed delle modifiche
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e6e243ceb73ca2a1180e59ba6c6b4095ed6069a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116714"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Modello di pull del feed di modifiche in Azure Cosmos DB

Con il modello pull del feed di modifiche, è possibile utilizzare il Azure Cosmos DB feed di modifiche in modo personalizzato. Come si può già fare con il [processore del feed delle modifiche](change-feed-processor.md), è possibile usare il modello pull del feed delle modifiche per parallelizzare l'elaborazione delle modifiche in più consumer di feed di modifiche.

> [!NOTE]
> Il modello pull del feed delle modifiche è attualmente disponibile in [anteprima solo in Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . L'anteprima non è ancora disponibile per altre versioni di SDK.

## <a name="consuming-an-entire-containers-changes"></a>Utilizzo delle modifiche di un intero contenitore

È possibile creare un oggetto `FeedIterator` per elaborare il feed di modifiche utilizzando il modello pull. Quando si crea inizialmente un oggetto `FeedIterator` , è possibile specificare un facoltativo `StartTime` all'interno di `ChangeFeedRequestOptions` . Se non è specificato, `StartTime` sarà l'ora corrente.

Il `FeedIterator` è incluso in due versioni. Oltre agli esempi seguenti che restituiscono oggetti entità, è anche possibile ottenere la risposta con `Stream` supporto. I flussi consentono di leggere i dati senza prima deserializzarli, salvando le risorse del client.

Di seguito è riportato un esempio per ottenere un `FeedIterator` oggetto che restituisce oggetti entità, in questo caso un `User` oggetto:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Di seguito è riportato un esempio per ottenere un oggetto `FeedIterator` che restituisce un oggetto `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

Usando un `FeedIterator` , è possibile elaborare facilmente il feed di modifiche di un intero contenitore con il proprio ritmo. Ad esempio:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Utilizzo delle modifiche della chiave di partizione

In alcuni casi, potrebbe essere necessario elaborare solo le modifiche di una chiave di partizione specifica. È possibile ottenere un oggetto `FeedIterator` per una chiave di partizione specifica ed elaborare le modifiche nello stesso modo in cui è possibile per un intero contenitore:

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

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

Nel [processore del feed delle modifiche](change-feed-processor.md), il lavoro viene distribuito automaticamente tra più consumer. Nel modello pull del feed di modifiche è possibile usare `FeedRange` per parallelizzare l'elaborazione del feed di modifiche. Un oggetto `FeedRange` rappresenta un intervallo di valori di chiave di partizione.

Di seguito è riportato un esempio che illustra come ottenere un elenco di intervalli per il contenitore:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Quando si ottiene un elenco di FeedRanges per il contenitore, è possibile ottenerne uno `FeedRange` per ogni [partizione fisica](partition-data.md#physical-partitions).

Utilizzando un `FeedRange` è possibile creare un oggetto `FeedIterator` per parallelizzare l'elaborazione del feed di modifiche tra più computer o thread. A differenza dell'esempio precedente che ha illustrato come ottenere un singolo `FeedIterator` per l'intero contenitore, è possibile usare `FeedRange` per ottenere più FeedIterators che possono elaborare il feed di modifiche in parallelo.

Nel caso in cui si desideri usare FeedRanges, è necessario disporre di un processo dell'agente di orchestrazione che ottenga FeedRanges e li distribuisca a tali computer. Questa distribuzione può essere:

* Utilizzo `FeedRange.ToJsonString` e distribuzione del valore stringa. I consumer possono usare questo valore con`FeedRange.FromJsonString`
* Se la distribuzione è in-process, passando il `FeedRange` riferimento all'oggetto.

Ecco un esempio che illustra come leggere dall'inizio del feed di modifiche del contenitore usando due computer distinti ipotetici che eseguono la lettura in parallelo:

Computer 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
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

## <a name="saving-continuation-tokens"></a>Salvataggio di token di continuazione

È possibile salvare la posizione di `FeedIterator` creando un token di continuazione. Un token di continuazione è un valore stringa che tiene traccia delle ultime modifiche elaborate di FeedIterator. In questo modo è possibile riprendere l'operazione `FeedIterator` in questo momento. Il codice seguente leggerà il feed delle modifiche dopo la creazione del contenitore. Quando non sono più disponibili modifiche, il token di continuazione verrà salvato in modo permanente in modo da poter riprendere il consumo del feed di modifiche.

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

## <a name="comparing-with-change-feed-processor"></a>Confronto con il processore dei feed delle modifiche

In molti scenari è possibile elaborare il feed di modifiche utilizzando il [processore del feed delle modifiche](change-feed-processor.md) o il modello pull. I token di continuazione del modello pull e il contenitore di lease del processore del feed delle modifiche sono entrambi "segnalibri" per l'ultimo elemento elaborato (o batch di elementi) nel feed delle modifiche.
Tuttavia, non è possibile convertire i token di continuazione in un contenitore lease (o viceversa).

Si consiglia di utilizzare il modello pull in questi scenari:

- Si desidera eseguire una sola lettura dei dati esistenti nel feed delle modifiche
- Si desidera leggere solo le modifiche da una determinata chiave di partizione
- Non si vuole usare un modello push e si vuole usare il feed delle modifiche in modo personalizzato

Di seguito sono riportate alcune differenze principali tra il processore del feed delle modifiche e il modello pull:

|  | Processore dei feed di modifiche| Modello pull |
| --- | --- | --- |
| Tenere traccia del punto corrente nell'elaborazione del feed delle modifiche | Lease (archiviato in un contenitore Azure Cosmos DB) | Token di continuazione (archiviato in memoria o persistente manualmente) |
| Possibilità di riprodurre modifiche precedenti | Sì, con modello push | Sì, con il modello pull|
| Polling per le modifiche future | Verifica automaticamente la presenza di modifiche in base a quanto specificato dall'utente`WithPollInterval` | Manuale |
| Elabora modifiche da un intero contenitore | Sì, ed è automaticamente in parallelo tra più thread/computer che utilizzano lo stesso contenitore| Sì, e con parallelismo manuale usando FeedTokens |
| Elabora le modifiche da una sola chiave di partizione | Non supportato | Sì|
| Livello di supporto | Disponibile a livello generale | Anteprima |

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica del feed di modifiche](change-feed.md)
* [Uso del processore del feed delle modifiche](change-feed-processor.md)
* [Attivare Funzioni di Azure](change-feed-functions.md)