---
title: Introduzione all'archiviazione code e ai servizi connessi di Visual Studio (ASP.NET Core) | Microsoft Docs
description: Informazioni su come iniziare a usare l'archiviazione code di Azure in un progetto ASP.NET Core in Visual Studio
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: d83ad26e4f96e1a7670c5212b7e9ca8182b7cec4
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Introduzione all'archiviazione code e ai servizi connessi di Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Questo articolo descrive come iniziare a usare Archiviazione code di Azure in Visual Studio dopo aver creato o fatto riferimento a un account di archiviazione di Azure in un progetto ASP.NET Core usando la funzionalità **Servizi connessi** di Visual Studio. **Servizi connessi** consente di installare i pacchetti NuGet appropriati per accedere all'archiviazione di Azure nel progetto e di aggiungere la stringa di connessione per l'account di archiviazione ai file di configurazione del progetto. Per informazioni generali su Archiviazione di Azure, vedere la relativa [documentazione](https://azure.microsoft.com/documentation/services/storage/).

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione. Per i dettagli sulla modifica delle code a livello di codice, vedere anche [Introduzione all'archiviazione code di Azure con .NET](../storage/queues/storage-dotnet-how-to-use-queues.md).

Per iniziare, creare una coda di Azure nell'account di archiviazione. Questo articolo illustrerà come creare una coda in C# e come eseguire le operazioni di base relative alle code, ad esempio l'aggiunta, la modifica, la lettura e la rimozione di messaggi nelle code.  Il codice usa la Libreria client di archiviazione di Azure per .NET. Per ulteriori informazioni su ASP.NET, vedere [ASP.NET](http://www.asp.net).

Alcune API di Archiviazione di Azure sono asincrone e il codice riportato in questo articolo presuppone che vengano usati metodi asincroni. Per altre informazioni, vedere [Programmazione asincrona](https://docs.microsoft.com/dotnet/csharp/async).

## <a name="access-queues-in-code"></a>Code di accesso nel codice

Per accedere alle code nei progetti ASP.NET Core, includere gli elementi seguenti in tutti i file di origine C# che accedono all'archiviazione code di Azure. Usare tutti questi elementi prima del codice nelle sezioni seguenti.

1. Aggiungere le necessarie istruzioni `using`:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Ottenere un oggetto `CloudStorageAccount` che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Ottenere un oggetto `CloudQueueClient` per fare riferimento agli oggetti delle code nell'account di archiviazione:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Ottenere un oggetto `CloudQueue` per fare riferimento a una coda specifica:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Creare una coda in codice

Per creare la coda di Azure nel codice, chiamare ``CreateIfNotExistsAsync`:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda

Per inserire un messaggio in una coda esistente, creare un nuovo oggetto `CloudQueueMessage`, quindi chiamare il metodo `AddMessageAsync`. È possibile creare un oggetto `CloudQueueMessage` da una stringa, in formato UTF-8, o da una matrice di byte.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Leggere un messaggio in una coda

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo `PeekMessageAsync`:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Leggere e rimuovere un messaggio in una coda

Il codice può rimuovere un messaggio da una coda in due passaggi.

1. Chiamare `GetMessageAsync` per ottenere il messaggio successivo di una coda. Un messaggio restituito da `GetMessageAsync` diventa invisibile a qualsiasi altro elemento di codice che legge i messaggi di questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi.
1. Per completare la rimozione del messaggio dalla coda, chiamare `DeleteMessageAsync`.

Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice seguente chiama `DeleteMessageAsync` subito dopo che il messaggio è stato elaborato:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Opzioni aggiuntive per rimuovere i messaggi dalla coda

Esistono due modi per personalizzare il recupero di messaggi da una coda. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene usato il metodo `GetMessages` per recuperare 20 messaggi con una sola chiamata. Ogni messaggio viene poi elaborato con un ciclo `foreach`. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti. Si noti che i cinque minuti del timer iniziano per tutti i messaggi contemporaneamente. Dopo che questo intervallo di tempo è trascorso, tutti i messaggi che non sono stati eliminati diventano nuovamente visibili.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo `FetchAttributes` chiede al servizio di accodamento di recuperare gli attributi della coda, incluso il numero di messaggi. La proprietà `ApproximateMethodCount` restituisce l'ultimo valore recuperato dal metodo `FetchAttributes` senza chiamare il servizio di accodamento.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Utilizzare il modello Async-Await con API delle code comuni

Questo esempio illustra come usare il modello async-await con API delle code comuni che terminano con `Async`. Quando si usa un metodo asincrono, il modello async-await sospende l'esecuzione locale fino al completamento della chiamata. Questo comportamento consente al thread corrente di eseguire altre attività per evitare colli di bottiglia delle prestazioni e migliora la velocità di risposta complessiva dell'applicazione.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo `Delete` sull'oggetto coda:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
