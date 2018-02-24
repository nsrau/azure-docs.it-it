---
title: Introduzione all'archiviazione BLOB di Azure e a Servizi connessi di Visual Studio (ASP.NET Core) | Microsoft Docs
description: Informazioni su come iniziare a usare l'archiviazione BLOB di Azure in un progetto ASP.NET Core in Visual Studio dopo avere eseguito la connessione a un account di archiviazione con i Servizi connessi di Visual Studio
services: storage
documentationcenter: 
author: camsoper
manager: wpickett
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 42390effd6a2d2a8afe9350e0a77d3c0a17b6129
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introduzione all'archiviazione BLOB di Azure e ai relativi servizi di Visual Studio (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Archiviazione BLOB di Azure è un servizio che archivia i dati non strutturati nel cloud come oggetti o BLOB. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.

Questa esercitazione illustra come scrivere codice ASP.NET Core per alcuni scenari comuni che usano l'archiviazione BLOB. Gli scenari includono la creazione di un contenitore BLOB e il caricamento, la creazione di elenchi, il download e l'eliminazione di BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>prerequisiti

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Questa sezione illustra dettagliatamente come configurare l'ambiente di sviluppo. Include la creazione di un'app ASP.NET MVC, con l'aggiunta di una connessione di servizi connessi, l'aggiunta di un controller e la specifica delle direttive sugli spazi dei nomi necessarie.

### <a name="create-an-aspnet-mvc-app-project"></a>Creare un progetto di app ASP.NET MVC

1. Aprire Visual Studio.

1. Nel menu principale selezionare **File** > **Nuovo** > **Progetto**.

1. Nella finestra di dialogo **Nuovo progetto** selezionare **Web** > **Applicazione Web ASP.NET Core** > **AspNetCoreStorage**. Selezionare **OK**.

    ![Screenshot della finestra di dialogo Nuovo progetto in Visual Studio](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Nella finestra di dialogo **Nuova applicazione Web ASP.NET Core** selezionare **.NET Core** > **ASP.NET Core 2.0** > **Applicazione Web (MVC)**. Selezionare **OK**.

    ![Screenshot della finestra di dialogo Nuova applicazione Web ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Usare i servizi connessi per connettersi a un account di archiviazione di Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.

2. Nel menu di scelta rapida selezionare **Aggiungi** > **Servizio connesso**.

1. Nella finestra di dialogo **Servizi connessi** selezionare **Archiviazione cloud con Archiviazione di Azure** e quindi selezionare **Configura**.

    ![Screenshot della finestra di dialogo Servizi connessi](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Nella finestra di dialogo **Archiviazione di Azure** selezionare l'account di archiviazione di Azure da usare per questa esercitazione. Per creare un nuovo account di archiviazione di Azure, selezionare **Crea un nuovo Account di archiviazione** e completare il modulo. Dopo aver selezionato un account di archiviazione esistente o averne creato uno nuovo, selezionare **Aggiungi**. Visual Studio installa il pacchetto NuGet per Archiviazione di Azure e inserisce una stringa di connessione di archiviazione in **appsettings.json**.

> [!TIP]
> Per informazioni su come creare un account di archiviazione con il [portale di Azure](https://portal.azure.com), vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Si può creare un account di archiviazione anche tramite [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), l'[interfaccia della riga di comando di Azure](../storage/common/storage-azure-cli.md) o [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Creare un controller MVC 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Controller**.

2. Nel menu di scelta rapida selezionare **Aggiungi** > **Controller**.

    ![Screenshot di Esplora soluzioni](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Nella finestra di dialogo **Aggiungi scaffolding** fare clic su **Controller MVC - Vuoto** e selezionare **Aggiungi**.

    ![Screenshot della finestra di dialogo Aggiungi scaffolding](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Nella finestra di dialogo **Aggiungi controller MVC vuoto** assegnare al controller il nome *BlobsController* e selezionare **Aggiungi**.

    ![Screenshot della finestra di dialogo Aggiungi controller MVC vuoto](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Aggiungere le direttive `using` seguenti al file `BlobsController.cs`:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Connettersi a un account di archiviazione e ottenere un riferimento a un contenitore

Un contenitore BLOB è una gerarchia nidificata di BLOB e cartelle. Il resto della procedura in questo documento richiede che venga specificato un riferimento a un contenitore BLOB. È quindi consigliabile inserire il codice nel metodo perché sia riutilizzabile.

La procedura seguente crea un metodo per eseguire la connessione all'account di archiviazione usando la stringa di connessione in **appsettings.json**. La procedura crea anche un riferimento a un contenitore. L'impostazione della stringa di connessione in **appsettings.json** viene espressa con il formato `<storageaccountname>_AzureStorageConnectionString`. 

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato **GetCloudBlobContainer** che restituisce un elemento **CloudBlobContainer**. Assicurarsi di sostituire `<storageaccountname>_AzureStorageConnectionString` con il nome effettivo della chiave in **Web. config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
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

1. Chiamare il metodo `CloudBlobContainer.CreateIfNotExists` per creare il contenitore, se non esiste ancora. Il metodo `CloudBlobContainer.CreateIfNotExists` restituisce **true** se il contenitore non esiste ma viene creato correttamente. In caso contrario, il metodo restituisce **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Aggiornare `ViewBag` con il nome del contenitore BLOB.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Di seguito viene mostrato il metodo `CreateBlobContainer` completato:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
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

1. Cercare l'elenco non ordinato simile al seguente: `<ul class="nav navbar-nav">`.  Dopo l'ultimo elemento `<li>` nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce al menu di spostamento:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Crea contenitore BLOB** per visualizzare risultati simili allo screenshot seguente:
  
    ![Screenshot di Crea contenitore BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Come accennato in precedenza, il metodo `CloudBlobContainer.CreateIfNotExists` restituisce **true** solo quando il contenitore non esiste e viene creato. Se quindi l'applicazione viene eseguita in presenza del contenitore, il metodo restituisce **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Caricare un BLOB in un contenitore BLOB

Dopo aver [creato il contenitore BLOB](#create-a-blob-container), caricarvi i file. Questa sezione illustra dettagliatamente come caricare un file locale in un contenitore BLOB. Nella procedura si presume che sia stato creato un contenitore BLOB denominato *test-blob-container*. 

1. Aprire il file `BlobsController.cs` .

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

1. Archiviazione di Azure supporta tipi di BLOB diversi. In questa esercitazione vengono utilizzati BLOB in blocchi. Per recuperare un riferimento a un BLOB in blocchi, eseguire una chiamata al metodo `CloudBlobContainer.GetBlockBlobReference`.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Il nome del BLOB è incluso nell'URL usato per recuperare un BLOB e può essere qualsiasi stringa, incluso il nome del file.

1. Dopo avere ottenuto un riferimento al BLOB, è possibile caricarvi qualsiasi flusso di dati chiamando il metodo `UploadFromStream` dell'oggetto di riferimento al BLOB. Il metodo `UploadFromStream` crea il BLOB se non esiste o lo sovrascrive se esiste. Impostare *&lt;file-to-upload>* su un percorso assoluto a un file da caricare.

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Di seguito viene mostrato il metodo `UploadBlob` completato (con un percorso assoluto al file da caricare):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** > **Condiviso** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo elemento `<li>` nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce al menu di spostamento:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Carica BLOB**. Dovrebbe venire visualizzato il testo *success!* .
    
    ![Screenshot della verifica dell'esito positivo](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Elencare i BLOB in un contenitore BLOB

Questa sezione illustra come elencare i BLOB in un contenitore BLOB. Il codice di esempio fa riferimento al *test-blob-container* creato nella sezione [Creare un contenitore BLOB](#create-a-blob-container).

1. Aprire il file `BlobsController.cs` .

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
   
1. Per elencare i BLOB presenti in un contenitore BLOB, usare il metodo `CloudBlobContainer.ListBlobsSegmentedAsync`. Il metodo `CloudBlobContainer.ListBlobsSegmentedAsync` restituisce un elemento `BlobResultSegment`. Questo elemento contiene oggetti `IListBlobItem` di cui è possibile eseguire il cast a oggetti `CloudBlockBlob`, `CloudPageBlob` o `CloudBlobDirectory`. Il seguente frammento di codice enumera tutti i BLOB in un contenitore BLOB. Per ogni BLOB viene eseguito il cast all'oggetto appropriato, in base al tipo. Il relativo nome (o URI nel caso di un elemento `CloudBlobDirectory`) viene aggiunto a un elenco.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
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
    Di seguito viene mostrato il metodo `ListBlobs` completato:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
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

1. Aprire `ListBlobs.cshtml` e sostituire il contenuto con il codice riportato di seguito:

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

1. Dopo l'ultimo elemento `<li>` nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce al menu di spostamento:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Elenca BLOB** per visualizzare risultati simili allo screenshot seguente:
  
    ![Schermata di Elenca BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Scaricare BLOB

Questa sezione illustra come scaricare un BLOB. È possibile renderlo persistente nell'archivio locale o leggere il contenuto in una stringa. Il codice di esempio fa riferimento al *test-blob-container* creato nella sezione [Creare un contenitore BLOB](#create-a-blob-container).

1. Aprire il file `BlobsController.cs` .

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

1. Per scaricare un BLOB, usare il metodo `CloudBlockBlob.DownloadToStream`. Il codice seguente trasferisce il contenuto di un BLOB a un oggetto flusso. Tale oggetto viene quindi reso persistente in un file locale. Cambiare *&lt;local-file-name>* nel nome file completo che rappresenta la posizione in cui viene scaricato il BLOB. 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Di seguito viene mostrato il metodo `ListBlobs` completato (con un percorso assoluto al file locale da creare):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** > **Condiviso** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo elemento `<li>` nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce al menu di spostamento:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Scarica BLOB** per scaricare il BLOB. Il BLOB specificato nel metodo `CloudBlobContainer.GetBlockBlobReference` chiama i download nella posizione specificata nella chiamata al metodo `File.OpenWrite`. Dovrebbe venire visualizzato il testo *success!* nel browser. 

## <a name="delete-blobs"></a>Eliminare BLOB

I passaggi seguenti illustrano come eliminare un BLOB:

1. Aprire il file `BlobsController.cs` .

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
    blob.DeleteAsync().Wait();
    ```
    
    Il metodo `DeleteBlob` completato dovrebbe apparire come mostrato di seguito:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni** > **Condiviso** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo elemento `<li>` nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce al menu di spostamento:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Elimina BLOB** per eliminare il BLOB specificato nella chiamata al metodo `CloudBlobContainer.GetBlockBlobReference`. Dovrebbe venire visualizzato il testo *success!* nel browser. Selezionare il pulsante **Indietro** del browser e quindi selezionare **Elenca BLOB** per verificare che il BLOB non sia più presente nel contenitore.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come archiviare, elencare e recuperare i BLOB in Archiviazione di Azure usando ASP.NET Core. Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.

  * [Introduzione all'archiviazione tabelle di Azure e a Servizi connessi di Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introduzione all'archiviazione code di Azure e a Servizi connessi di Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
