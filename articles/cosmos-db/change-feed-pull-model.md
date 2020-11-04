---
title: Modello di pull del feed di modifiche
description: Informazioni su come usare il modello di pull del feed di modifiche di Azure Cosmos DB per leggere il feed di modifiche e le differenze tra il modello di pull e il processore dei feed di modifiche
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/27/2020
ms.reviewer: sngun
ms.openlocfilehash: b3f7a8fbe2afcf9b5603f288fe6e3bc429b14532
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340193"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Modello di pull del feed di modifiche in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Il modello di pull del feed di modifiche consente di utilizzare il feed di modifiche di Azure Cosmos DB in modo personalizzato. Come è già possibile fare con il [processore di feed di modifiche](change-feed-processor.md), è possibile usare il modello di pull del feed di modifiche per impostare l'elaborazione parallela delle modifiche tra più consumer di feed di modifiche.

> [!NOTE]
> Il modello di pull del feed di modifiche è attualmente disponibile solo in [anteprima in Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview). L'anteprima non è ancora disponibile per altre versioni dell'SDK.

## <a name="comparing-with-change-feed-processor"></a>Confronto con il processore dei feed di modifiche

In molti scenari è possibile elaborare il feed di modifiche usando il [processore del feed di modifiche](change-feed-processor.md) o il modello di pull. I token di continuazione del modello di pull e il contenitore di lease del processore del feed di modifiche sono entrambi "segnalibri" per l'ultimo elemento o batch di elementi elaborato nel feed di modifiche.

Non è però possibile convertire i token di continuazione in un contenitore di lease (o viceversa).

> [!NOTE]
> Nella maggior parte dei casi, quando è necessario leggere dal feed delle modifiche, l'opzione più semplice consiste nell'usare il [processore del feed delle modifiche](change-feed-processor.md).

È opportuno usare il modello di pull negli scenari seguenti:

- Leggere le modifiche da una particolare chiave di partizione
- Controllare la velocità di ricezione delle modifiche per l'elaborazione da parte del client
- Eseguire una sola volta la lettura dei dati esistenti nel feed delle modifiche, ad esempio per eseguire una migrazione dei dati.

Ecco alcune delle differenze principali tra il processore del feed di modifiche e il modello di pull:

|Funzionalità  | Processore dei feed di modifiche| Modello di pull |
| --- | --- | --- |
| Traccia del punto corrente nell'elaborazione del feed di modifiche | Lease (archiviato in un contenitore Azure Cosmos DB) | Token di continuazione (archiviato in memoria o reso persistente manualmente) |
| Possibilità di riprodurre modifiche precedenti | Sì, con modello di push | Sì, con modello di pull|
| Polling per le modifiche future | Verifica automaticamente della presenza di modifiche in base al parametro `WithPollInterval` specificato dall'utente | Manuale |
| Comportamento in cui non sono presenti nuove modifiche | Attendi `WithPollInterval` e ricontrolla automaticamente | È necessario rilevare l'eccezione e controllare manualmente |
| Elaborazione delle modifiche di un intero contenitore | Sì, con elaborazione parallela automatica tra più thread/computer che utilizzano lo stesso contenitore| Sì, con elaborazione parallela manuale tramite FeedToken |
| Elaborazione delle modifiche da una sola chiave di partizione | Non supportate | Sì|
| Livello di supporto | Disponibile a livello generale | Anteprima |

> [!NOTE]
> Diversamente da quando si esegue la lettura utilizzando il processore del feed delle modifiche, è necessario gestire in modo esplicito i casi in cui non sono presenti nuove modifiche. 

## <a name="consuming-an-entire-containers-changes"></a>Utilizzo delle modifiche di un intero contenitore

È possibile creare un oggetto `FeedIterator` per elaborare il feed di modifiche usando il modello di pull. Quando si crea inizialmente un oggetto `FeedIterator` , è necessario specificare un `ChangeFeedStartFrom` valore obbligatorio costituito sia dalla posizione iniziale per la lettura delle modifiche che da quella desiderata `FeedRange` . `FeedRange`È un intervallo di valori di chiave di partizione e specifica gli elementi che verranno letti dal feed delle modifiche usando tale specifica `FeedIterator` .

Facoltativamente, è possibile specificare `ChangeFeedRequestOptions` per impostare un `PageSizeHint` . `PageSizeHint`È il numero massimo di elementi che verranno restituiti in una singola pagina.

L'oggetto `FeedIterator` è disponibile in due versioni. Oltre agli esempi seguenti che restituiscono oggetti entità, è anche possibile ottenere la risposta con il supporto per `Stream`. Gli oggetti Stream consentono di leggere i dati senza prima deserializzarli e quindi di risparmiare risorse del client.

Ecco un esempio per ottenere un oggetto `FeedIterator` che restituisce oggetti entità, in questo caso un oggetto `User`:

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());
```

Ecco un esempio per ottenere un oggetto `FeedIterator` che restituisce un oggetto `Stream`:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning());
```

Se non si fornisce un oggetto `FeedRange` a un `FeedIterator` , è possibile elaborare il feed di modifiche di un intero contenitore con il proprio ritmo. Di seguito è riportato un esempio che avvia la lettura di tutte le modifiche a partire dall'ora corrente:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now());

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Poiché il feed delle modifiche è effettivamente un elenco infinito di elementi che comprende tutte le Scritture e gli aggiornamenti futuri, il valore di `HasMoreResults` è sempre true. Quando si tenta di leggere il feed delle modifiche e non sono disponibili nuove modifiche, si riceverà un'eccezione. Nell'esempio precedente, l'eccezione viene gestita attendendo 5 secondi prima di ricontrollare le modifiche.

## <a name="consuming-a-partition-keys-changes"></a>Utilizzo delle modifiche di una chiave di partizione

In alcuni casi può essere necessario elaborare solo le modifiche di una chiave di partizione specifica. È possibile ottenere un oggetto `FeedIterator` per una chiave di partizione specifica ed elaborare le modifiche allo stesso modo di un intero contenitore.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue"))));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Uso di FeedRange per la parallelizzazione

Nel [processore del feed di modifiche](change-feed-processor.md) il lavoro viene distribuito automaticamente tra più consumer. Nel modello di pull del feed di modifiche è possibile usare `FeedRange` per impostare l'elaborazione parallela del feed di modifiche. Un elemento `FeedRange` rappresenta un intervallo di valori di chiave di partizione.

Ecco un esempio che illustra come ottenere un elenco di intervalli per il contenitore:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Quando si ottiene un elenco di elementi FeedRange per il contenitore, si otterrà un elemento `FeedRange` per ogni [partizione fisica](partitioning-overview.md#physical-partitions).

Con `FeedRange` è quindi possibile creare un oggetto `FeedIterator` per impostare l'elaborazione parallela del feed di modifiche tra più computer o thread. A differenza dell'esempio precedente che ha illustrato come ottenere un oggetto `FeedIterator` per l'intero contenitore o una singola chiave di partizione, è possibile usare FeedRanges per ottenere più FeedIterators che possono elaborare il feed di modifiche in parallelo.

Nel caso in cui si intenda usare oggetti FeedRange, è necessario disporre di un processo dell'agente di orchestrazione che ottenga oggetti FeedRange e li distribuisca a tali computer. Questa distribuzione può essere simile a quella descritta di seguito:

* Uso di `FeedRange.ToJsonString` e distribuzione di questo valore stringa. I consumer possono usare questo valore con `FeedRange.FromJsonString`
* Se la distribuzione è in-process, passaggio del riferimento all'oggetto `FeedRange`.

Ecco un esempio che illustra come leggere dall'inizio del feed di modifiche del contenitore usando due computer ipotetici distinti che eseguono la lettura in parallelo:

Computer 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]));
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Computer 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]));
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="saving-continuation-tokens"></a>Salvataggio dei token di continuazione

Per salvare la posizione dell'oggetto `FeedIterator`, è possibile creare un token di continuazione. Un token di continuazione è un valore stringa che tiene traccia delle ultime modifiche elaborate di FeedIterator. In questo modo `FeedIterator` può riprendere l'elaborazione da questo punto in un secondo momento. Il codice seguente leggerà il feed delle modifiche dopo la creazione del contenitore. Quando non sono più disponibili modifiche, verrà salvato in modo permanente un token di continuazione che consentirà di riprendere il consumo del feed di modifiche in un secondo momento.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation));
```

Il token di continuazione di FeedIterator non scade fino a quando esiste il contenitore Cosmos.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica del feed di modifiche](change-feed.md)
* [Uso del processore dei feed di modifiche](change-feed-processor.md)
* [Attivare Funzioni di Azure](change-feed-functions.md)