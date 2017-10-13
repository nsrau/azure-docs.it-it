---
title: Introduzione all'archiviazione code e ai Servizi connessi di Visual Studio (servizi cloud) | Documentazione Microsoft
description: Informazioni su come iniziare a usare il servizio di archiviazione di coda in un progetto di servizio di cloud in Visual Studio dopo aver eseguito la connessione a un account di archiviazione con i servizi connessi di Visual Studio.
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 7a6e58a62b4cfbf99641559363dd0c860cdf8af2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introduzione all’archiviazione di accodamento di Azure e ai servizi connessi di Visual Studio (progetti servizi cloud)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica
Questo articolo descrive come iniziare a usare l'archiviazione code di Azure in Visual Studio dopo aver creato o fatto riferimento a un account di archiviazione di Azure in un progetto servizio cloud usando la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio.

Verrà mostrato come creare una coda nel codice. Infine verrà mostrato come eseguire operazioni relative alle code di base, come l'aggiunta, la modifica, la lettura e la rimozione di messaggi delle code. Negli esempi, scritti in codice C#, viene usata la [libreria client di Archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

L'operazione **Aggiungi servizi connessi** consente di installare i pacchetti NuGet appropriati per accedere all'archiviazione di Azure nel progetto e di aggiungere la stringa di connessione per l'account di archiviazione ai file di configurazione del progetto.

* Per altre informazioni sulla modifica delle code a livello di codice, vedere [Introduzione all'archiviazione code di Azure con .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) .
* Vedere [documentazione di archiviazione](https://azure.microsoft.com/documentation/services/storage/) per informazioni generali sull'archiviazione di Azure.
* Vedere la [documentazione dei servizi Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) per informazioni generali sui servizi cloud di Azure.
* Vedere [ASP.NET](http://www.asp.net) per ulteriori informazioni sulle applicazioni di programmazione di ASP.NET.

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione.

## <a name="access-queues-in-code"></a>Code di accesso nel codice
Per accedere alle code nei progetti Servizi cloud di Visual Studio, è necessario aggiungere i seguenti elementi a un file di origine C# che accede all’archiviazione di accodamento di Azure.

1. Assicurarsi che le dichiarazioni dello spazio dei nomi all'inizio del file C# includano queste istruzioni **using** .
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Ottenere un oggetto **CloudQueueClient** per fare riferimento agli oggetti delle code nell'account di archiviazione.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Ottenere un oggetto **CloudQueue** per fare riferimento a una coda specifica.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**NOTA:** utilizzare tutto il codice riportato in precedenza prima del codice indicato negli esempi seguenti.

## <a name="create-a-queue-in-code"></a>Creare una coda in codice
Per creare la coda nel codice, è sufficiente aggiungere una chiamata a **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda
Per inserire un messaggio in una coda esistente, creare un nuovo oggetto **CloudQueueMessage**, quindi chiamare il metodo **AddMessage**.

È possibile creare un oggetto **CloudQueueMessage** da una stringa in formato UTF-8 o da una matrice di byte.

Di seguito è riportato un esempio che inserisce il messaggio "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Leggere un messaggio in una coda
È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **PeekMessage** .

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Leggere e rimuovere un messaggio in una coda
Il codice può rimuovere un messaggio da una coda in due passaggi.

1. Chiamare **GetMessage** per ottenere il messaggio successivo in una coda. Un messaggio restituito da **GetMessage** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi.
2. Per completare la rimozione del messaggio dalla coda, chiamare **DeleteMessage**.

Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice seguente chiama **DeleteMessage** immediatamente dopo l'elaborazione del messaggio.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Utilizzare opzioni aggiuntive per elaborare e rimuovere i messaggi in coda
È possibile personalizzare il recupero di messaggi da una coda in due modi.

* È possibile recuperare un batch di messaggi (massimo 32).
* È possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo **GetMessages** per recuperare 20 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo **foreach** . Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti. Si noti che i cinque minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi cinque minuti dalla chiamata a **GetMessages**, tutti i messaggi che non sono stati eliminati diventano nuovamente visibili.

Ad esempio:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda
È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **FetchAttributes** chiede al servizio di accodamento di recuperare gli attributi della coda, incluso il numero di messaggi. La proprietà **ApproximateMethodCount** restituisce l'ultimo valore recuperato dal metodo **FetchAttributes**, senza chiamare il servizio di accodamento.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Utilizzare il modello Async-Await con le API comuni di accodamento di Azure.
In questo esempio viene illustrato come utilizzare il modello Async-Await con API di accodamento di Azure comuni. Nell'esempio viene chiamata la versione asincrona di ogni metodo specificato. Ciò può essere osservato dal suffisso **Async** di ogni metodo. Quando un metodo asincrono viene utilizzato, il modello async-await sospende l'esecuzione locale fino al completamento della chiamata. Questo comportamento consente al thread corrente di eseguire altre attività per evitare colli di bottiglia delle prestazioni e migliora la velocità di risposta complessiva dell'applicazione. Per ulteriori informazioni sull'utilizzo del modello Async-Await in .NET, vedere [Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Eliminare una coda
Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **Elimina** sull'oggetto coda.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

