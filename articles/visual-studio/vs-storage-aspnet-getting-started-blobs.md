---
title: Introduzione ad Archiviazione BLOB di Azure e a Servizi connessi di Visual Studio (ASP.NET) | Microsoft Docs
description: Informazioni su come iniziare a usare Archiviazione BLOB di Azure in un progetto ASP.NET in Visual Studio dopo la connessione a un account di archiviazione con Servizi connessi di Visual Studio
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: kraig
ms.openlocfilehash: cb406e528568dafd1e142943f5273ad58e550609
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Introduzione ad Archiviazione BLOB di Azure e a Servizi connessi di Visual Studio (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Archiviazione BLOB di Azure è un servizio che archivia i dati non strutturati nel cloud come oggetti o BLOB. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.

Questa esercitazione illustra come scrivere codice ASP.NET per alcuni scenari comuni che usano l'archiviazione BLOB. Gli scenari includono la creazione di un contenitore BLOB e il caricamento, la creazione di elenchi, il download e l'eliminazione di BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Creare un controller MVC 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Controller**.

2. Nel menu di scelta rapida selezionare **Aggiungi** > **Controller**.

    ![Screenshot di Esplora soluzioni con le opzioni Aggiungi e Controller evidenziate](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. Nella finestra di dialogo **Aggiungi scaffolding** fare clic su **Controller MVC 5 - Vuoto** e selezionare **Aggiungi**.

    ![Screenshot della finestra di dialogo Aggiungi scaffolding](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. Nella finestra di dialogo **Aggiungi controller** assegnare un nome al controller *BlobsController* e selezionare **Aggiungi**.

    ![Screenshot della finestra di dialogo Aggiungi controller](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Aggiungere le direttive `using` seguenti al file `BlobsController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Connettersi a un account di archiviazione e ottenere un riferimento a un contenitore

Un contenitore BLOB è una gerarchia nidificata di BLOB e cartelle. Il resto della procedura in questo documento richiede che venga specificato un riferimento a un contenitore BLOB. È quindi consigliabile inserire il codice nel metodo perché sia riutilizzabile.

La procedura seguente crea un metodo per eseguire la connessione all'account di archiviazione usando la stringa di connessione in **Web.config**. La procedura crea anche un riferimento a un contenitore.  L'impostazione della stringa di connessione in **Web.config** viene espressa con il formato `<storageaccountname>_AzureStorageConnectionString`. 

1. Aprire il file `BlobsController.cs`.

1. Aggiungere un metodo denominato **GetCloudBlobContainer** che restituisce un elemento **CloudBlobContainer**.  Assicurarsi di sostituire `<storageaccountname>_AzureStorageConnectionString` con il nome effettivo della chiave in **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Anche se *test-blob-container* non esiste ancora, il codice crea un riferimento al contenitore. Questo è quindi il contenitore che può essere creato con il metodo `CreateIfNotExists` nel passaggio successivo.

## <a name="create-a-blob-container"></a>Creare un contenitore BLOB

I passaggi seguenti illustrano come creare un contenitore BLOB:

1. Aggiungere un metodo denominato `CreateBlobContainer` che restituisce un elemento `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Recuperare un oggetto `CloudBlobContainer` che rappresenta un riferimento al nome del contenitore BLOB desiderato. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Chiamare il metodo `CloudBlobContainer.CreateIfNotExists` per creare il contenitore, se non esiste ancora. Il metodo `CloudBlobContainer.CreateIfNotExists` restituisce **true** se il contenitore non esiste, ma viene creato correttamente. In caso contrario, il metodo restituisce **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Aggiornare `ViewBag` con il nome del contenitore BLOB.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Di seguito viene illustrato il metodo `CreateBlobContainer` completato:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Visualizzazioni**.

2. Nel menu di scelta rapida selezionare **Aggiungi** > **Nuova cartella**. Assegnare alla nuova cartella il nome *Blobs*. 
 
1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** e fare clic con il pulsante destro del mouse su **Blobs**.

4. Nel menu di scelta rapida selezionare **Aggiungi** > **Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **CreateBlobContainer** come nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `CreateBlobContainer.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** > **Condiviso** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **Crea contenitore BLOB** per visualizzare risultati simili allo screenshot seguente:
  
    ![Screenshot di Crea contenitore BLOB](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Come accennato in precedenza, il metodo `CloudBlobContainer.CreateIfNotExists` restituisce **true** solo quando il contenitore non esiste e viene creato. Se quindi l'applicazione viene eseguita in presenza del contenitore, il metodo restituisce **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Caricare un BLOB in un contenitore BLOB

Dopo aver [creato il contenitore BLOB](#create-a-blob-container), caricarvi i file. Questa sezione illustra dettagliatamente come caricare un file locale in un contenitore BLOB. Nella procedura si presume che sia stato creato un contenitore BLOB denominato *test-blob-container*. 

1. Aprire il file `BlobsController.cs`.

1. Aggiungere un metodo denominato `UploadBlob` che restituisce una stringa.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. All'interno del metodo `UploadBlob`, recuperare un oggetto `CloudBlobContainer` che rappresenta un riferimento al nome del contenitore BLOB desiderato. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Archiviazione di Azure supporta diversi tipi di BLOB. In questa esercitazione vengono utilizzati BLOB in blocchi. Per recuperare un riferimento a un BLOB in blocchi, eseguire una chiamata al metodo `CloudBlobContainer.GetBlockBlobReference`.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Il nome del BLOB è incluso nell'URL usato per recuperare un BLOB e può essere qualsiasi stringa, incluso il nome del file.

1. Dopo avere ottenuto un riferimento al BLOB, è possibile caricarvi qualsiasi flusso di dati chiamando il metodo `UploadFromStream` dell'oggetto di riferimento al BLOB. Il metodo `UploadFromStream` crea il BLOB se non esiste o lo sovrascrive se esiste. Impostare *&lt;file-to-upload>* su un percorso assoluto a un file da caricare.

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Di seguito viene illustrato il metodo `UploadBlob` completato (con un percorso assoluto al file da caricare):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** > **Condiviso** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **Carica BLOB**.  Dovrebbe venire visualizzato il termine *success!* .
    
    ![Screenshot della verifica dell'esito positivo](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Elencare i BLOB in un contenitore BLOB

Questa sezione illustra come elencare i BLOB in un contenitore BLOB. Il codice di esempio fa riferimento al *test-blob-container* creato nella sezione [Creare un contenitore BLOB](#create-a-blob-container).

1. Aprire il file `BlobsController.cs`.

1. Aggiungere un metodo denominato `ListBlobs` che restituisce un elemento `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. All'interno del metodo `ListBlobs`, recuperare un oggetto `CloudBlobContainer` che rappresenta un riferimento al contenitore BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Per elencare i BLOB presenti in un contenitore BLOB, usare il metodo `CloudBlobContainer.ListBlobs`. Il metodo `CloudBlobContainer.ListBlobs` restituisce un oggetto `IListBlobItem` di cui è possibile eseguire il cast in un oggetto `CloudBlockBlob`, `CloudPageBlob` o `CloudBlobDirectory`. Il seguente frammento di codice enumera tutti i BLOB in un contenitore BLOB. Per ogni BLOB viene eseguito il cast all'oggetto appropriato, in base al tipo. Il relativo nome (o URI nel caso di un elemento **CloudBlobDirectory**) viene aggiunto a un elenco.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    I contenitori BLOB possono contenere directory, oltre ai BLOB. Si supponga di disporre di un contenitore BLOB denominato *test-blob-container* con la gerarchia seguente:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Con l'esempio di codice precedente, l'elenco di stringhe **blobs** contiene valori simili ai seguenti:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Come si può notare, l'elenco include solo le entità di livello superiore, non quelle annidate (*bar.png* e *baz.png*). Per elencare tutte le entità all'interno di un contenitore BLOB, modificare il codice in modo che il metodo **CloudBlobContainer.ListBlobs** passi a **true** il parametro **useFlatBlobListing**.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    L'impostazione del parametro **useFlatBlobListing** su **true** restituisce un elenco semplice di tutte le entità nel contenitore BLOB. L'operazione genera il risultato seguente:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Di seguito viene illustrato il metodo **ListBlobs** completato:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** e fare clic con il pulsante destro del mouse su **Blobs**.

2. Nel menu di scelta rapida selezionare **Aggiungi** > **Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere `ListBlobs` come nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `ListBlobs.cshtml` e sostituire il contenuto con il codice seguente:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** > **Condiviso** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **Elenca BLOB** per visualizzare risultati simili allo screenshot seguente:
  
    ![Screenshot di Elenca BLOB](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Scaricare BLOB

Questa sezione illustra come scaricare un BLOB. È possibile renderlo persistente nell'archivio locale o leggere il contenuto in una stringa. Il codice di esempio fa riferimento al *test-blob-container* creato nella sezione [Creare un contenitore BLOB](#create-a-blob-container).

1. Aprire il file `BlobsController.cs`.

1. Aggiungere un metodo denominato `DownloadBlob` che restituisce una stringa.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. All'interno del metodo `DownloadBlob`, recuperare un oggetto `CloudBlobContainer` che rappresenta un riferimento al contenitore BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Ottenere un oggetto di riferimento del BLOB chiamando il metodo `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Per scaricare un BLOB, usare il metodo `CloudBlockBlob.DownloadToStream`. Il codice seguente trasferisce il contenuto di un BLOB a un oggetto flusso. Tale oggetto viene quindi reso persistente in un file locale. Modificare *&lt;local-file-name>* nel nome file completo che rappresenta la posizione in cui viene scaricato il BLOB. 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Di seguito viene illustrato il metodo `ListBlobs` completato (con un percorso assoluto al file locale da creare):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** > **Condiviso** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **Scarica BLOB** per scaricare il BLOB. Il BLOB specificato nel metodo `CloudBlobContainer.GetBlockBlobReference` chiama i download nella posizione specificata nella chiamata al metodo `File.OpenWrite`.  Dovrebbe venire visualizzato il testo *success!* nel browser. 

## <a name="delete-blobs"></a>Eliminare BLOB

I passaggi seguenti illustrano come eliminare un BLOB:

1. Aprire il file `BlobsController.cs`.

1. Aggiungere un metodo denominato `DeleteBlob` che restituisce una stringa.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. All'interno del metodo `DeleteBlob`, recuperare un oggetto `CloudBlobContainer` che rappresenta un riferimento al contenitore BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Ottenere un oggetto di riferimento del BLOB chiamando il metodo `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Per eliminare un BLOB, usare il metodo `Delete`.

    ```csharp
    blob.Delete();
    ```
    
    Il metodo `DeleteBlob` completato dovrebbe apparire come illustrato di seguito:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** > **Condiviso** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **Elimina BLOB** per eliminare il BLOB specificato nella chiamata al metodo `CloudBlobContainer.GetBlockBlobReference`. Dovrebbe venire visualizzato il testo *success!* nel browser. Selezionare il pulsante **Indietro** del browser e quindi selezionare **Elenca BLOB** per verificare che il BLOB non sia più presente nel contenitore.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come archiviare, elencare e recuperare i BLOB in Archiviazione di Azure usando ASP.NET. Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.

  * [Introduzione all'archiviazione tabelle di Azure e a Servizi connessi di Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introduzione all'archiviazione code di Azure e a Servizi connessi di Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
