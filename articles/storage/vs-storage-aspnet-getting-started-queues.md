---
title: Introduzione all&quot;archiviazione code di Azure e ai servizi connessi di Visual Studio (ASP.NET) | Documentazione Microsoft
description: Informazioni su come iniziare a usare l&quot;archiviazione code di Azure in un progetto ASP.NET in Visual Studio dopo aver eseguito la connessione a un account di archiviazione con i servizi connessi di Visual Studio
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/23/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 5840ec74f6af2e373d9ebb34b0f6e13094c33f19
ms.openlocfilehash: 4e5638938c8e9fa0de12aa273d03f3eead35a383


---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Introduzione all'archiviazione code di Azure e ai servizi connessi di Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

L'archivio code di Azure fornisce la messaggistica cloud tra i componenti dell'applicazione. Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, per poter essere scalati in modo indipendente. L'archivio code fornisce la messaggistica asincrona per la comunicazione tra i componenti dell'applicazione, che possono essere eseguiti nel cloud, in un desktop, in un server locale o in un dispositivo mobile. Archiviazione code supporta anche la gestione di attività asincrone e la creazione di flussi di lavoro dei processi.

Questa esercitazione illustra come scrivere codice ASP.NET per alcuni scenari comuni usando le entità di archiviazione code di Azure. Tali scenari includono attività comuni, ad esempio la creazione di una coda di Azure e l'aggiunta, modifica, lettura e rimozione dei messaggi in coda.

##<a name="prerequisites"></a>Prerequisiti

* [Microsoft Visual Studio](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)
* [Account di archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Creare un controller MVC 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Controller** e selezionare **Aggiungi > Controller** dal menu di scelta rapida.

    ![Aggiungere un controller a un'app MVC ASP.NET](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Nella finestra di dialogo **Aggiungi scaffolding** fare clic su **Controller MVC 5 - Vuoto** e selezionare **Aggiungi**.

    ![Specificare il tipo di controller MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Nella finestra di dialogo **Aggiungi controller** assegnare il nome *QueuesController* al controller e selezionare **Aggiungi**.

    ![Assegnare un nome al controller MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Aggiungere le direttive *using* seguenti al file `QueuesController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Creare una coda

I passaggi seguenti illustrano come creare una coda:

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `QueuesController.cs` . 

1. Aggiungere un metodo denominato **CreateQueue** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Nel metodo **CreateQueue** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Ottenere un oggetto **CloudQueue** che rappresenta un riferimento al nome della coda desiderata. Il metodo **CloudQueueClient.GetQueueReference** non esegue una richiesta all'archiviazione code. Il riferimento viene restituito indipendentemente dall'esistenza della coda. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chiamare il metodo **CloudQueue.CreateIfNotExists** per creare la coda se non esiste. Il metodo **CloudQueue.CreateIfNotExists** restituisce **true** se la coda non esiste e viene creata correttamente. In caso contrario, viene restituito **false**.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Aggiornare **ViewBag** con il nome della coda.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Code** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **CreateQueue** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `CreateQueue.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Eseguire l'applicazione e selezionare **Crea coda** per visualizzare risultati simili allo screenshot seguente:
  
    ![Crea coda](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Come accennato in precedenza, il metodo **CloudQueue.CreateIfNotExists** restituisce **true** solo quando la coda non esiste e viene creata. Se quindi si esegue l'app quando la coda esiste, il metodo restituisce **false**. Per eseguire l'app più volte, è necessario eliminare la coda prima di eseguire di nuovo l'app. L'eliminazione della coda può essere eseguita tramite il metodo **CloudQueue.Delete**. È possibile anche eliminare la coda usando il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) o [Esplora archivi di Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda

Dopo aver [creato una coda](#create-a-queue) è possibile aggiungervi dei messaggi. Questa sezione illustra i passaggi per aggiungere un messaggio alla coda *test-queue*. 

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `QueuesController.cs` .

1. Aggiungere un metodo denominato **AddMessage** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Nel metodo **AddMessage** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Ottenere un oggetto **CloudQueueContainer** che rappresenta un riferimento alla coda. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Creare l'oggetto **CloudQueueMessage** che rappresenta il messaggio da aggiungere alla coda. È possibile creare un oggetto **CloudQueueMessage** da una stringa in formato UTF-8 o da una matrice di byte.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Chiamare il metodo **CloudQueue.AddMessage** per aggiungere il messaggio alla coda.

    ```csharp
    queue.AddMessage(message);
    ```

1. Creare e impostare un paio di proprietà **ViewBag** per la visualizzazione.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Code** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **AddMessage** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `AddMessage.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Eseguire l'applicazione e selezionare **Aggiungi messaggio** per visualizzare risultati simili allo screenshot seguente:
  
    ![Aggiungi messaggio](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Le sezioni [Leggere un messaggio da una coda senza rimuoverlo](#read-a-message-from-a-queue-without-removing-it) e [Leggere e rimuovere un messaggio da una coda](#read-and-remove-a-message-from-a-queue) illustrano come leggere i messaggi da una coda.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Leggere un messaggio da una coda senza rimuoverlo

Questa sezione illustra come visualizzare un messaggio in coda (leggere il primo messaggio senza rimuoverlo).  

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `QueuesController.cs` .

1. Aggiungere un metodo denominato **PeekMessage** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Nel metodo **PeekMessage** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Ottenere un oggetto **CloudQueueContainer** che rappresenta un riferimento alla coda. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chiamare il metodo **CloudQueue.PeekMessage** per leggere il primo messaggio di una coda senza rimuoverlo dalla coda. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Aggiornare **ViewBag** con due valori: il nome della coda e il messaggio letto. L'oggetto **CloudQueueMessage** espone due proprietà per ottenerne il valore: **CloudQueueMessage.AsBytes** e **CloudQueueMessage.AsString**. **AsString** (usata in questo esempio) restituisce una stringa, mentre **AsBytes** restituisce una matrice di byte.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Code** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **PeekMessage** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `PeekMessage.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Eseguire l'applicazione e selezionare **Visualizza il messaggio** per visualizzare risultati simili allo screenshot seguente:
  
    ![Visualizza il messaggio](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Leggere e rimuovere un messaggio da una coda

In questa sezione viene illustrato come leggere e rimuovere un messaggio da una coda.   

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `QueuesController.cs` .

1. Aggiungere un metodo denominato **ReadMessage** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Nel metodo **ReadMessage** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Ottenere un oggetto **CloudQueueContainer** che rappresenta un riferimento alla coda. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chiamare il metodo **CloudQueue.GetMessage** per leggere il primo messaggio nella coda. Il metodo **CloudQueue.GetMessage** rende invisibile il messaggio per 30 secondi (impostazione predefinita) per qualsiasi altro codice che legge i messaggi, in modo che nessun altro codice possa modificare o eliminare il messaggio mentre lo si sta elaborando. Per modificare l'intervallo di tempo per cui il messaggio rimane invisibile, modificare il parametro **visibilityTimeout** passato al metodo **CloudQueue.GetMessage**.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Chiamare il metodo **CloudQueueMessage.Delete** per eliminare il messaggio dalla coda.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Aggiornare **ViewBag** con il messaggio eliminato e il nome della coda.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Code** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **ReadMessage** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `ReadMessage.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Eseguire l'applicazione e selezionare **Read/Delete message** (Leggi/Elimina messaggio) per visualizzare risultati simili allo screenshot seguente:
  
    ![Read/Delete message (Leggi/Elimina messaggio)](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda

In questa sezione viene illustrato come ottenere la lunghezza della coda (numero di messaggi). 

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `QueuesController.cs` .

1. Aggiungere un metodo denominato **GetQueueLength** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Nel metodo **ReadMessage** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Ottenere un oggetto **CloudQueueContainer** che rappresenta un riferimento alla coda. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chiamare il metodo **CloudQueue.FetchAttributes** per recuperare gli attributi della coda, che includono anche la lunghezza. 

    ```csharp
    queue.FetchAttributes();
    ```

6. Accedere alla proprietà **CloudQueue.ApproximateMessageCount** per ottenere la lunghezza della coda.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Aggiornare **ViewBag** con il nome e la lunghezza della coda.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Code** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **GetQueueLength** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `GetQueueLengthMessage.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Eseguire l'applicazione e selezionare **Get queue length** (Ottieni lunghezza coda) per visualizzare risultati simili allo screenshot seguente:
  
    ![Recuperare la lunghezza della coda](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Eliminare una coda
Questa sezione illustra come eliminare una coda. 

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `QueuesController.cs` .

1. Aggiungere un metodo denominato **DeleteQueue** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Nel metodo **DeleteQueue** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Ottenere un oggetto **CloudQueueContainer** che rappresenta un riferimento alla coda. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chiamare il metodo **CloudQueue.Delete** per eliminare la coda rappresentata dall'oggetto **CloudQueue**.

    ```csharp
    queue.Delete();
    ```

1. Aggiornare **ViewBag** con il nome e la lunghezza della coda.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Code** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **DeleteQueue** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `DeleteQueue.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Eseguire l'applicazione e selezionare **Get queue length** (Ottieni lunghezza coda) per visualizzare risultati simili allo screenshot seguente:
  
    ![Elimina coda](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.

  * [Introduzione all'Archiviazione BLOB di Azure e ai Servizi connessi di Visual Studio (ASP.NET)](./vs-storage-aspnet-getting-started-blobs.md)
  * [Introduzione all'archiviazione tabelle di Azure e ai Servizi connessi di Visual Studio (ASP.NET)](./vs-storage-aspnet-getting-started-tables.md)


<!--HONumber=Jan17_HO1-->


