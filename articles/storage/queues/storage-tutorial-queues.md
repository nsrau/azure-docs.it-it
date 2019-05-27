---
title: Esercitazione - Usare le code di archiviazione di Azure - Archiviazione di Azure
description: Esercitazione su come usare il Servizio di accodamento di Azure per creare code e per inserire, visualizzare ed eliminare messaggi.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 8d108e1683be03a79e87990b983f2eda3eadba90
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65797528"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Esercitazione: Usare le code di archiviazione di Azure

L'archiviazione code di Azure implementa le code basate sul cloud per consentire la comunicazione tra i componenti di un'applicazione distribuita. Ogni coda gestisce un elenco di messaggi che possono essere aggiunti da un componente mittente ed elaborati da un componente destinatario. Con una coda, l'applicazione può essere ridimensionata immediatamente per soddisfare la richiesta. Questo articolo illustra i passaggi di base per usare una coda di archiviazione di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Creare un account di archiviazione di Azure
> - Creare l'app
> - Aggiungere il supporto per il codice asincrono
> - Creare una coda
> - Inserire i messaggi in una coda
> - Rimuovere dalla coda i messaggi
> - Eliminare una coda vuota
> - Cercare gli argomenti della riga di comando
> - Compilare ed eseguire l'app

## <a name="prerequisites"></a>Prerequisiti

- Ottenere la copia gratuita dell'editor [Visual Studio Code](https://code.visualstudio.com/download) multipiattaforma.
- Scaricare e installare [.NET Core SDK](https://dotnet.microsoft.com/download).
- Se non si ha una sottoscrizione di Azure corrente, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

Prima di tutto, creare un account di archiviazione di Azure. Per una guida dettagliata alla creazione di un account di archiviazione, vedere l'argomento di avvio rapido [Creare un account di archiviazione](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json).

## <a name="create-the-app"></a>Creare l'app

Creare un'applicazione .NET Core denominata **QueueApp**. Per semplicità, questa app invierà e riceverà messaggi tramite la coda.

1. Nella finestra di una console (ad esempio CMD, PowerShell o l'interfaccia della riga di comando di Azure) usare il comando `dotnet new` per creare una nuova app console con il nome **QueueApp**. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Passare alla cartella **QueueApp** appena creata e compilare l'app per verificare che tutto funzioni correttamente.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Verranno visualizzati risultati simili ai seguenti:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Aggiungere il supporto per il codice asincrono

Poiché l'app usa le risorse cloud, il codice viene eseguito in modo asincrono. Le parole chiave **async** e **await** di C# non erano tuttavia valide nei metodi **Main** prima di C# 7.1. È possibile passare facilmente a tale compilatore tramite un flag nel file **csproj**.

1. Nella riga di comando nella directory del progetto digitare `code .` per aprire Visual Studio Code nella directory corrente. Tenere aperta la finestra della riga di comando. Più avanti sarà necessario eseguire altri comandi. Se viene chiesto di aggiungere asset C# necessari per la compilazione e il debug, fare clic sul pulsante **Sì**.

2. Aprire il file **QueueApp.csproj** nell'editor.

3. Aggiungere `<LangVersion>7.1</LangVersion>` nel primo **PropertyGroup** nel file di compilazione. Assicurarsi di aggiungere solo il tag **LangVersion** perché **TargetFramework** potrebbe variare a seconda della versione di .NET installata.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Salvare il file **QueueApp.csproj**.

5. Aprire il file di origine **Program.cs** e aggiornare il metodo **Main** per eseguirlo in modo asincrono. Sostituire **void** con un valore restituito **async Task**.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Salvare il file **Program.cs**.

## <a name="create-a-queue"></a>Creare una coda

1. Installare i pacchetti **Microsoft.Azure.Storage.Common** e **Microsoft.Azure.Storage.Queue** nel progetto con il comando `dotnet add package`. Eseguire i comandi dotnet seguenti dalla cartella del progetto nella finestra della console.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. Nella parte superiore del file **Program.cs** aggiungere gli spazi dei nomi seguenti dopo l'istruzione `using System;`. Questa app usa i tipi di questi spazi dei nomi per connettersi ad Archiviazione di Azure e servirsi delle code.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Salvare il file **Program.cs**.

### <a name="get-your-connection-string"></a>Ottenere una stringa di connessione

La libreria client usa una stringa di connessione per stabilire la connessione. La stringa di connessione è disponibile nella sezione **Impostazioni** dell'account di archiviazione nel portale di Azure.

1. Nel Web browser accedere al [portale di Azure](https://portal.azure.com/).

2. Passare all'account di archiviazione nel portale di Azure.

3. Selezionare **Chiavi di accesso**.

4. Fare clic sul pulsante **Copia** a destra del campo **Stringa di connessione**.

![Stringa di connessione](media/storage-tutorial-queues/get-connection-string.png)

La stringa di connessione è nel formato seguente:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Aggiungere la stringa di connessione all'app

Aggiungere la stringa di connessione nell'app in modo che possa accedere all'account di archiviazione.

1. Tornare a Visual Studio Code.

2. Nella classe **Program** aggiungere un membro `private const string connectionString =` per memorizzare la stringa di connessione.

3. Dopo il segno di uguale incollare il valore della stringa copiato in precedenza nel portale di Azure. Il valore **connectionString** sarà univoco nell'account.

4. Rimuovere il codice "Hello World" da **Main**. Il codice dovrebbe essere simile al seguente, ma con il valore univoco della stringa di connessione.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Aggiornare **Main** per creare un oggetto **CloudQueue**, che in seguito viene passato nei metodi send e receive.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Salvare il file.

## <a name="insert-messages-into-the-queue"></a>Inserire i messaggi nella coda

Creare un nuovo metodo per inviare un messaggio nella coda. Aggiungere il metodo seguente alla classe **Program**. Questo metodo ottiene un riferimento alla coda, quindi crea una nuova coda, se non esiste già, chiamando [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Aggiunge quindi il messaggio alla coda chiamando [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Aggiungere il metodo **SendMessageAsync** seguente alla classe **Program**.

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Salvare il file.

## <a name="dequeue-messages"></a>Rimuovere dalla coda i messaggi

Creare un nuovo metodo denominato **ReceiveMessageAsync**. Questo metodo riceve un messaggio dalla coda chiamando [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Dopo che il messaggio è stato ricevuto correttamente, è importante eliminarlo dalla coda in modo che non venga elaborato più volte. Dopo la ricezione del messaggio, eliminarlo dalla coda chiamando [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Aggiungere il metodo **ReceiveMessageAsync** seguente alla classe **Program**.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Salvare il file.

## <a name="delete-an-empty-queue"></a>Eliminare una coda vuota

È buona norma, alla fine di un progetto, verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. Se la coda esiste, ma è vuota, chiedere all'utente se vuole eliminarla.

1. Espandere il metodo **ReceiveMessageAsync** per includere un prompt per eliminare la coda vuota.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Salvare il file.

## <a name="check-for-command-line-arguments"></a>Cercare gli argomenti della riga di comando

Se sono presenti argomenti della riga di comando passati all'app, presupporre che si tratti di un messaggio da aggiungere alla coda. Unire gli argomenti per creare una stringa. Aggiungere questa stringa alla coda di messaggi chiamando il metodo **SendMessageAsync** aggiunto prima.

Se non sono presenti argomenti della riga di comando, eseguire un'operazione di recupero. Chiamare il metodo **ReceiveMessageAsync** per recuperare il primo messaggio della coda.

Attendere infine l'input dell'utente prima di uscire chiamando **Console.ReadLine**.

1. Espandere il metodo **Main** per cercare gli argomenti della riga di comando e attendere l'input dell'utente.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Salvare il file.

## <a name="complete-code"></a>Codice completo

Ecco il listato di codice completo per questo progetto.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Dalla riga di comando nella directory del progetto eseguire il comando dotnet seguente per compilare il progetto.

   ```console
   dotnet build
   ```

2. Dopo aver compilato il progetto, eseguire il comando seguente per aggiungere il primo messaggio alla coda.

   ```console
   dotnet run First queue message
   ```

Dovrebbe venire visualizzato questo output:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Eseguire l'app senza argomenti della riga di comando per ricevere e rimuovere il primo messaggio nella coda.

   ```console
   dotnet run
   ```

4. Continuare a eseguire l'app fino alla rimozione di tutti i messaggi. Se la si esegue ancora una volta, verranno visualizzati un messaggio che informa che la coda è vuota e un prompt per eliminare la coda.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

1. Creare una coda
2. Aggiungere e rimuovere messaggi da una coda
3. Eliminare una coda di archiviazione di Azure

Per altre informazioni, vedere l'argomento di avvio rapido sulle code di Azure.

> [!div class="nextstepaction"]
> [Avvio rapido delle code](storage-quickstart-queues-portal.md)
