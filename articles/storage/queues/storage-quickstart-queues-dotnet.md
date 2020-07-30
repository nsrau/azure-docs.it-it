---
title: 'Guida introduttiva: Libreria di Archiviazione code di Azure v12 - .NET'
description: In questo argomento di avvio rapido si apprenderà come usare la libreria di Archiviazione code di Azure v12 per .NET per creare una coda e aggiungervi messaggi, come leggere ed eliminare i messaggi dalla coda e come eliminare una coda.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 5931311368a6389386adb689a505feeeed03b29c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289176"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Guida introduttiva: Libreria client di Archiviazione code di Azure v12 per .NET

Iniziare a usare la libreria client di Archiviazione code di Azure versione 12 per .NET. Archiviazione code di Azure è un servizio che consente di archiviare un numero elevato di messaggi per recuperali ed elaborarli successivamente. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

Usare la libreria client di Archiviazione code di Azure v12 per .NET per:

* Creare una coda
* Aggiungere messaggi a una coda
* Visualizzare in anteprima i messaggi in una coda
* Aggiornare un messaggio in una coda
* Ricevere messaggi da una coda
* Eliminare messaggi da una coda
* Eliminare una coda

Risorse aggiuntive:

* [Documentazione di riferimento delle API](/dotnet/api/azure.storage.queues)
* [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
* [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
* [Esempi](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Account di archiviazione di Azure: [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Versione aggiornata di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) per il sistema operativo in uso. Assicurarsi di ottenere l'SDK e non il runtime.

## <a name="setting-up"></a>Configurazione

Questa sezione illustra come preparare un progetto da usare con la libreria client di Archiviazione code di Azure v12 per .NET.

### <a name="create-the-project"></a>Creare il progetto

Creare un'applicazione .NET Core con il nome *QueuesQuickstartV12*.

1. Nella finestra di una console (ad esempio cmd, PowerShell o Bash) usare il comando `dotnet new` per creare una nuova app console con il nome *QueuesQuickstartV12*. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Passare alla directory *QueuesQuickstartV12* appena creata.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Installare il pacchetto

Sempre nella directory dell'applicazione, installare la libreria client di Archiviazione code di Azure per il pacchetto .NET usando il comando `dotnet add package`.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire il file *Program.cs* in un editor di testo
1. Rimuovere l'istruzione `Console.WriteLine("Hello World!");`
1. Aggiungere le direttive `using`
1. Aggiornare la dichiarazione del metodo `Main` per [supportare codice asincrono](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



Ecco il codice:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modello a oggetti

Archiviazione code di Azure è un servizio per l'archiviazione di un numero elevato di messaggi. Un messaggio in coda avere dimensioni fino a 64 KB. Una coda può contenere milioni di messaggi, fino al limite di capacità totale dell'account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono. Archiviazione code offre tre tipi di risorse:

* L'account di archiviazione
* Una coda nell'account di archiviazione
* Messaggi all'interno della coda

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura di Archiviazione code](./media/storage-queues-introduction/queue1.png)

Per interagire con queste risorse, usare le classi .NET seguenti:

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): la classe `QueueServiceClient` consente di gestire tutte le code nell'account di archiviazione.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): la classe `QueueClient` consente di gestire e modificare una singola coda e i relativi messaggi.
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): la classe `QueueMessage` rappresenta i singoli oggetti restituiti quando si chiama [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) su una coda.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire le azioni seguenti con la libreria client di Archiviazione code di Azure per .NET:

* [Ottenere la stringa di connessione](#get-the-connection-string)
* [Creare una coda](#create-a-queue)
* [Aggiungere messaggi a una coda](#add-messages-to-a-queue)
* [Visualizzare in anteprima i messaggi in una coda](#peek-at-messages-in-a-queue)
* [Aggiornare un messaggio in una coda](#update-a-message-in-a-queue)
* [Ricevere messaggi da una coda](#receive-messages-from-a-queue)
* [Eliminare messaggi da una coda](#delete-messages-from-a-queue)
* [Eliminare una coda](#delete-a-queue)

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione per l'account di archiviazione. La stringa di connessione è archiviata nella variabile di ambiente creata nella sezione [Configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice all'interno del metodo `Main`:

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Creare una coda

Decidere un nome per la nuova coda. Il codice seguente aggiunge un valore GUID al nome della coda per garantirne l'univocità.

> [!IMPORTANT]
> I nomi di coda possono contenere solo lettere minuscole, numeri e segni meno e devono iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino. Il nome deve inoltre avere una lunghezza compresa fra 3 e 63 caratteri. Per altre informazioni sull'assegnazione di nomi alle code, vedere [Denominazione di code e metadati](/rest/api/storageservices/naming-queues-and-metadata).


Creare un'istanza della classe [QueueClient](/dotnet/api/azure.storage.queues.queueclient). Chiamare quindi il metodo [CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) per creare la coda nell'account di archiviazione.

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Aggiungere messaggi a una coda

Il frammento di codice seguente aggiunge messaggi alla coda in modo asincrono chiamando il metodo [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync). Salva anche un [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) restituito da una chiamata a `SendMessageAsync`. L'elemento restituito viene usato per aggiornare il messaggio in un secondo momento nel programma.

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Visualizzare in anteprima i messaggi in una coda

Per visualizzare in anteprima i messaggi nella coda, chiamare il metodo [PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync). Il metodo `PeekMessagesAsync` recupera uno o più messaggi dall'inizio della coda, senza modificare la visibilità del messaggio.

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Aggiornare un messaggio in una coda

Per aggiornare il contenuto di un messaggio, chiamare il metodo [UpdateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync). Il metodo `UpdateMessageAsync` può modificare il timeout di visibilità e il contenuto di un messaggio. Il contenuto del messaggio deve essere una stringa con codifica UTF-8 di dimensioni non superiori a 64 KB. Insieme al nuovo contenuto del messaggio, passare i valori del `SendReceipt` salvati in precedenza nel codice. I valori del `SendReceipt` identificano il messaggio da aggiornare.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda

Scaricare i messaggi aggiunti precedentemente chiamando il metodo [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync).

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Eliminare messaggi da una coda

Eliminare i messaggi dalla coda dopo che sono stati elaborati. In questo caso, l'elaborazione consiste semplicemente nella visualizzazione del messaggio nella console.

L'app viene sospesa per l'input dell'utente chiamando `Console.ReadLine` prima dell'elaborazione ed eliminazione dei messaggi. Verificare nel [portale di Azure](https://portal.azure.com) che le risorse siano state create correttamente, prima di eliminarle. Gli eventuali messaggi che non vengono eliminati in modo esplicito diventeranno nuovamente visibili nella coda per poter essere elaborati di nuovo.

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Eliminare una coda

Il codice seguente pulisce le risorse create dall'app eliminando la coda tramite il metodo [DeleteAsync](/dotnet/api/azure.storage.queues.queueclient.deleteasync).

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea e aggiunge tre messaggi a una coda di Azure. Il codice elenca i messaggi nella coda, quindi li recupera e li elimina, prima di eliminare definitivamente la coda.

Nella finestra della console passare alla directory dell'applicazione, quindi compilarla ed eseguirla.

```console
dotnet build
```

```console
dotnet run
```

L'output dell'app è simile all'esempio seguente:

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Quando l'app viene sospesa prima della ricezione dei messaggi, controllare l'account di archiviazione nel [portale di Azure](https://portal.azure.com). Verificare che i messaggi siano nella coda.

Premere **INVIO** per ricevere ed eliminare i messaggi. Quando richiesto, premere di nuovo **INVIO** per eliminare la coda e terminare la demo.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare una coda e aggiungervi messaggi usando codice .NET asincrono. Si è quindi appreso come visualizzare in anteprima, recuperare ed eliminare i messaggi. Infine, si è appreso come eliminare una coda di messaggi.

Per esercitazioni, esempi, guide di avvio rapido e altra documentazione, vedere:

> [!div class="nextstepaction"]
> [Azure per sviluppatori .NET e .NET Core](https://docs.microsoft.com/dotnet/azure/)

* Per altre informazioni, vedere le [librerie di Archiviazione di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage).
* Per altre app di esempio su Archiviazione code di Azure, continuare con gli [esempi della libreria client di Archiviazione code di Azure v12 per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
* Per altre informazioni su .NET Core, vedere [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Introduzione a .NET in 10 minuti).
