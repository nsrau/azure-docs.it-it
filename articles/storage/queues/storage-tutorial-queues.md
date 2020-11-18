---
title: Esercitazione - Usare le code di archiviazione di Azure in .NET
description: Esercitazione su come usare il servizio di accodamento di Azure per creare code e per inserire, visualizzare ed eliminare messaggi tramite codice .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/09/2020
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d661800c53cc0795efde1f411675d17661fb968
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345534"
---
# <a name="tutorial-work-with-azure-storage-queues-in-net"></a>Esercitazione: Usare le code di archiviazione di Azure in .NET

L'archiviazione code di Azure implementa le code basate sul cloud per consentire la comunicazione tra i componenti di un'applicazione distribuita. Ogni coda gestisce un elenco di messaggi che possono essere aggiunti da un componente mittente ed elaborati da un componente destinatario. Con una coda, l'applicazione può essere ridimensionata immediatamente per soddisfare la richiesta. Questo articolo illustra i passaggi di base per usare una coda di archiviazione di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> - Creare un account di archiviazione di Azure
> - Creare l'app
> - Aggiungere le librerie client di Azure
> - Aggiungere il supporto per il codice asincrono
> - Creare una coda
> - Inserire i messaggi in una coda
> - Rimuovere dalla coda i messaggi
> - Eliminare una coda vuota
> - Cercare gli argomenti della riga di comando
> - Compilare ed eseguire l'app

## <a name="prerequisites"></a>Prerequisiti

- Ottenere la copia gratuita dell'editor [Visual Studio Code](https://code.visualstudio.com/download) multipiattaforma.
- Scaricare e installare [.NET Core SDK](https://dotnet.microsoft.com/download) versione 3.1 o successiva.
- Se non si ha una sottoscrizione di Azure corrente, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

Prima di tutto, creare un account di archiviazione di Azure. Per una guida dettagliata alla creazione di un account di archiviazione, vedere l'argomento di avvio rapido [Creare un account di archiviazione](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json). Si tratta di un passaggio che viene eseguito separatamente dopo la creazione di un account Azure gratuito nei prerequisiti.

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

   Verrà visualizzata un output simile al seguente:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

<!-- markdownlint-disable MD023 -->

## <a name="add-the-azure-client-libraries"></a>Aggiungere le librerie client di Azure

1. Aggiungere le librerie client di Archiviazione di Azure al progetto usando il comando `dotnet add package`.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   Eseguire il comando seguente dalla cartella del progetto nella finestra della console.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Eseguire i comandi seguenti dalla cartella del progetto nella finestra della console.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>Aggiungere istruzioni using

1. Nella riga di comando nella directory del progetto digitare `code .` per aprire Visual Studio Code nella directory corrente. Tenere aperta la finestra della riga di comando. Più avanti sarà necessario eseguire altri comandi. Se viene chiesto di aggiungere asset C# necessari per la compilazione e il debug, fare clic sul pulsante **Sì**.

1. Aprire il file di origine **Program.cs** e aggiungere gli spazi dei nomi seguenti dopo l'istruzione `using System;`. Questa app usa i tipi di questi spazi dei nomi per connettersi ad Archiviazione di Azure e servirsi delle code.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. Salvare il file **Program.cs**.

## <a name="add-support-for-asynchronous-code"></a>Aggiungere il supporto per il codice asincrono

Poiché l'app usa le risorse cloud, il codice viene eseguito in modo asincrono.

1. Aggiornare il metodo **Main** per eseguire il codice in modo asincrono. Sostituire **void** con un valore restituito **async Task**.

   ```csharp
   static async Task Main(string[] args)
   ```

1. Salvare il file **Program.cs**.

## <a name="create-a-queue"></a>Creare una coda

Prima di effettuare chiamate alle API di Azure, è necessario ottenere le credenziali dal portale di Azure.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>Aggiungere la stringa di connessione all'app

Aggiungere la stringa di connessione nell'app in modo che possa accedere all'account di archiviazione.

1. Tornare a Visual Studio Code.

1. Nel metodo **Main** sostituire il codice `Console.WriteLine("Hello World!");` con la riga seguente che ottiene la stringa di connessione dalla variabile di ambiente.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. Aggiungere il codice seguente a **Main** per creare un oggetto coda, che in seguito viene passato ai metodi send e receive.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. Salvare il file.

## <a name="insert-messages-into-the-queue"></a>Inserire i messaggi nella coda

Creare un nuovo metodo per inviare un messaggio nella coda.

1. Aggiungere il metodo **InsertMessageAsync** seguente alla classe **Program**.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   A questo metodo viene passato un riferimento alla coda. Se non esiste già, viene creata una nuova coda chiamando [CreateIfNotExistsAsync](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync). *newMessage* viene quindi aggiunto alla coda chiamando [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   A questo metodo viene passato un riferimento alla coda. Se non esiste già, viene creata una nuova coda chiamando [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). *newMessage* viene quindi aggiunto alla coda chiamando [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **Facoltativo** Per impostazione predefinita, la durata massima (TTL) di un messaggio è impostata su sette giorni. È possibile specificare qualsiasi numero positivo per la durata massima del messaggio. Il frammento di codice seguente aggiunge un messaggio che non scade *mai*.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

    Per aggiungere un messaggio che non scade, usare `Timespan.FromSeconds(-1)` nella chiamata a **SendMessageAsync**.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

    Per aggiungere un messaggio che non scade, usare `Timespan.FromSeconds(-1)` nella chiamata a **AddMessageAsync**.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. Salvare il file.

Un messaggio della coda deve essere in un formato compatibile con una richiesta XML con codifica UTF-8. Un messaggio può avere una dimensione massima di 64 kB. Se un messaggio contiene dati binari, usare la [codifica Base64](/dotnet/api/system.convert.tobase64string).

## <a name="dequeue-messages"></a>Rimuovere dalla coda i messaggi

Creare un nuovo metodo per recuperare un messaggio dalla coda. Dopo che il messaggio è stato ricevuto correttamente, è importante eliminarlo dalla coda in modo che non venga elaborato più volte.

1. Aggiungere un nuovo metodo denominato **RetrieveNextMessageAsync** alla classe **Program**.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   Questo metodo riceve un messaggio dalla coda chiamando [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync), passando 1 nel primo parametro per recuperare solo il messaggio successivo nella coda. Dopo la ricezione del messaggio, eliminarlo dalla coda chiamando [DeleteMessageAsync](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync).

   I messaggi inviati alla coda con una versione dell'SDK precedente a v12 vengono automaticamente codificati in Base64. A partire da v12 la funzionalità è stata rimossa. I messaggi recuperati con l'SDK v12 non vengono automaticamente decodificati in Base64. È necessario [decodificare esplicitamente in Base64](/dotnet/api/system.convert.frombase64string) il contenuto.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Questo metodo riceve un messaggio dalla coda chiamando [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Dopo la ricezione del messaggio, eliminarlo dalla coda chiamando [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. Salvare il file.

## <a name="delete-an-empty-queue"></a>Eliminare una coda vuota

È buona norma, alla fine di un progetto, verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. Se la coda esiste, ma è vuota, chiedere all'utente se vuole eliminarla.

1. Espandere il metodo **RetrieveNextMessageAsync** per includere un prompt per eliminare la coda vuota.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. Salvare il file.

## <a name="check-for-command-line-arguments"></a>Cercare gli argomenti della riga di comando

Se sono presenti argomenti della riga di comando passati all'app, presupporre che si tratti di un messaggio da aggiungere alla coda. Unire gli argomenti per creare una stringa. Aggiungere questa stringa alla coda di messaggi chiamando il metodo **InsertMessageAsync** aggiunto prima.

Se non sono presenti argomenti della riga di comando, provare a eseguire un'operazione di recupero. Chiamare il metodo **RetrieveNextMessageAsync** per recuperare il messaggio successivo della coda.

Attendere infine l'input dell'utente prima di uscire chiamando **Console.ReadLine**.

1. Espandere il metodo **Main** per cercare gli argomenti della riga di comando e attendere l'input dell'utente.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. Salvare il file.

## <a name="complete-code"></a>Codice completo

Ecco il listato di codice completo per questo progetto.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Dalla riga di comando nella directory del progetto eseguire il comando dotnet seguente per compilare il progetto.

   ```console
   dotnet build
   ```

1. Dopo aver compilato il progetto, eseguire il comando seguente per aggiungere il primo messaggio alla coda.

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

1. Eseguire l'app senza argomenti della riga di comando per ricevere e rimuovere il primo messaggio nella coda.

   ```console
   dotnet run
   ```

1. Continuare a eseguire l'app fino alla rimozione di tutti i messaggi. Se la si esegue ancora una volta, verranno visualizzati un messaggio che informa che la coda è vuota e un prompt per eliminare la coda.

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

In questa esercitazione sono state illustrate le procedure per:

1. Creare una coda
1. Aggiungere e rimuovere messaggi da una coda
1. Eliminare una coda di archiviazione di Azure

Per altre informazioni, vedere gli argomenti di avvio rapido sulle code di Azure.

> [!div class="nextstepaction"]
> [Guida di avvio rapido sulle code per il portale](storage-quickstart-queues-portal.md)

- [Guida di avvio rapido sulle code per .NET](storage-quickstart-queues-dotnet.md)
- [Guida di avvio rapido sulle code per Java](storage-quickstart-queues-java.md)
- [Guida di avvio rapido sulle code per Python](storage-quickstart-queues-python.md)
- [Guida di avvio rapido sulle code per JavaScript](storage-quickstart-queues-nodejs.md)
