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
ms.date: 12/02/2016
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 394e2138ed2ba21b949195e473dafeaf6413cccf
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Introduzione all'archiviazione code e ai servizi connessi di Visual Studio (ASP.NET Core)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica
In questo articolo viene descritto come iniziare a usare l'archiviazione code di Azure in Visual Studio dopo aver creato o fatto riferimento a un account di archiviazione di Azure in un progetto ASP.NET Core usando la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio. L'operazione **Aggiungi servizi connessi** consente di installare i pacchetti NuGet appropriati per accedere all'archiviazione di Azure nel progetto e di aggiungere la stringa di connessione per l'account di archiviazione ai file di configurazione del progetto.

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione.

Per iniziare, è innanzitutto necessario creare una coda di Azure nell'account di archiviazione. Verrà mostrato come creare una coda nel codice. Infine verrà mostrato come eseguire operazioni relative alle code di base, come l'aggiunta, la modifica, la lettura e la rimozione di messaggi delle code. Negli esempi, scritti in codice C\# viene usata la libreria client di Archiviazione di Azure per .NET. Per ulteriori informazioni su ASP.NET, vedere [ASP.NET](http://www.asp.net).

**NOTA:** alcune API che eseguono chiamate ad Archiviazione di Azure in ASP.NET Core sono asincrone. Per ulteriori informazioni, vedere [Programmazione asincrona con Async e Await](http://msdn.microsoft.com/library/hh191443.aspx) . Nel codice riportato di seguito si presuppone vengano utilizzati i metodi di programmazione asincrona.

* Per altre informazioni sulla modifica delle code a livello di codice, vedere [Introduzione all'archiviazione code di Azure con .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) .
* Vedere la [documentazione di archiviazione](https://azure.microsoft.com/documentation/services/storage/) per informazioni generali sull'archiviazione di Azure.
* Vedere la [documentazione dei servizi Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) per informazioni generali sui servizi cloud di Azure.
* Vedere [ASP.NET](http://www.asp.net) per ulteriori informazioni sulle applicazioni di programmazione di ASP.NET.

## <a name="access-queues-in-code"></a>Code di accesso nel codice
Per accedere alle code nei progetti ASP.NET Core, è necessario includere gli elementi seguenti ai file di origine C# che consentono di accedere all'archiviazione code di Azure.

1. Assicurarsi che le dichiarazioni dello spazio dei nomi all'inizio del file C# includano queste istruzioni **using** .
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Ottenere un oggetto **CloudQueueClient** per fare riferimento agli oggetti delle code nell'account di archiviazione.  
   
        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Ottenere un oggetto **CloudQueue** per fare riferimento a una coda specifica.
   
        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**NOTA:** utilizzare tutto il codice riportato in precedenza prima del codice indicato negli esempi seguenti.

### <a name="create-a-queue-in-code"></a>Creare una coda in codice
Per creare la coda nel codice, è sufficiente aggiungere una chiamata a **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

## <a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda
Per inserire un messaggio in una coda esistente, creare un nuovo oggetto **CloudQueueMessage**, quindi chiamare il metodo **AddMessageAsync**.

È possibile creare un oggetto **CloudQueueMessage** da una stringa in formato UTF-8 o da una matrice di byte.

Di seguito è riportato un esempio che inserisce il messaggio "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

## <a name="read-a-message-in-a-queue"></a>Leggere un messaggio in una coda
È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **PeekMessageAsync** .

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


## <a name="read-and-remove-a-message-in-a-queue"></a>Leggere e rimuovere un messaggio in una coda
Il codice può rimuovere un messaggio da una coda in due passaggi.

1. Chiamare **GetMessageAsync** per ottenere il messaggio successivo in una coda. Un messaggio restituito da **GetMessageAsync** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi.
2. Per completare la rimozione del messaggio dalla coda, chiamare **DeleteMessageAsync**.

Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice seguente chiama **DeleteMessageAsync** immediatamente dopo l'elaborazione del messaggio.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>Utilizzare opzioni aggiuntive per rimuovere i messaggi dalla coda
È possibile personalizzare il recupero di messaggi da una coda in due modi.
Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo **GetMessages** per recuperare 20 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo **foreach** . Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su 5 minuti. Si noti che i 5 minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi 5 minuti dalla chiamata a **GetMessages**tutti i messaggi che non sono stati eliminati diventano nuovamente visibili.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda
È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **FetchAttributes** chiede al servizio di accodamento di recuperare gli attributi della coda, incluso il numero di messaggi. La proprietà **ApproximateMethodCount** restituisce l'ultimo valore recuperato dal metodo **FetchAttributes**, senza chiamare il servizio di accodamento.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Utilizzare il modello Async-Await con API delle code comuni
In questo esempio viene illustrato come utilizzare il modello Async-Await con API delle code comuni. L'esempio chiama la versione asincrona di ogni metodo specificato. Può essere visto dalla post-correzione di Async di ciascun metodo. Quando un metodo asincrono viene utilizzato, il modello async-await sospende l'esecuzione locale fino al completamento della chiamata. Questo comportamento consente al thread corrente di eseguire altre attività per evitare colli di bottiglia delle prestazioni e migliora la velocità di risposta complessiva dell'applicazione. Per ulteriori informazioni sull'utilizzo del modello Async-Await in .NET, vedere [Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

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
## <a name="delete-a-queue"></a>Eliminare una coda
Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **Elimina** sull'oggetto coda.

    // Delete the queue.
    messageQueue.Delete();


## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]


