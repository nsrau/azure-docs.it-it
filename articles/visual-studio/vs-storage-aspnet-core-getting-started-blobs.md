---
title: Introduzione a Azure Blob storage e Visual Studio connessi (ASP.NET Core) | Documenti Microsoft
description: Informazioni generali sull'utilizzo dell'archiviazione Blob di Azure in un progetto ASP.NET Core in Visual Studio dopo la connessione a un account di archiviazione usando Visual Studio servizi connessi
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introduzione all'archiviazione BLOB di Azure e ai relativi servizi di Visual Studio (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Archiviazione Blob di Azure è un servizio che archivia i dati non strutturati nel cloud come oggetti o BLOB. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.

In questa esercitazione viene illustrato come scrivere il codice ASP.NET Core per alcuni scenari comuni che utilizzano l'archiviazione Blob. Gli scenari includono la creazione di un contenitore BLOB e il caricamento, la creazione di elenchi, il download e l'eliminazione di BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

In questa sezione viene illustrato come configurare l'ambiente di sviluppo. Ciò include la creazione di un'applicazione ASP.NET Model-View-Controller (MVC), aggiunta di una connessione di servizi connessi, aggiunta di un controller e che specifica le direttive dello spazio dei nomi necessari.

### <a name="create-an-aspnet-mvc-app-project"></a>Creare un progetto di app ASP.NET MVC

1. Aprire Visual Studio.

1. Nel menu principale, selezionare **File** > **New** > **progetto**.

1. Nel **nuovo progetto** nella finestra di dialogo **Web** > **applicazione Web di ASP.NET Core** > **AspNetCoreStorage**. Selezionare **OK**.

    ![Schermata di Visual Studio nella finestra Nuovo progetto](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Nel **nuova applicazione Web di ASP.NET Core** nella finestra di dialogo **.NET Core** > **ASP.NET Core 2.0** > **(applicazione Web Model-View-Controller)**. Selezionare **OK**.

    ![La finestra di dialogo di schermata della nuova Core applicazione Web ASP.NET](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utilizzare i servizi connessi per connettersi a un account di archiviazione di Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto.

2. Dal menu di scelta rapida, selezionare **Aggiungi** > **servizio connesso**.

1. Nel **servizi connessi** nella finestra di dialogo **archiviazione Cloud con l'archiviazione di Azure**, quindi selezionare **configura**.

    ![La finestra di dialogo schermata di servizi connessi](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Nel **di archiviazione di Azure** nella finestra di dialogo account di archiviazione di Azure da utilizzare per questa esercitazione. Per creare un nuovo account di archiviazione di Azure, selezionare **creare un nuovo Account di archiviazione**e completare il modulo. Dopo aver selezionato un account archiviazione esistente o crearne uno nuovo, selezionare **Aggiungi**. Visual Studio installa il pacchetto NuGet per archiviazione di Azure e una stringa di connessione di archiviazione per **appSettings. JSON**.

> [!TIP]
> Per informazioni su come creare un account di archiviazione con il [portale di Azure](https://portal.azure.com), vedere [creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> È anche possibile creare un account di archiviazione tramite [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [CLI di Azure](../storage/common/storage-azure-cli.md), o [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Creare un controller MVC 

1. In **Esplora**, fare doppio clic su **controller**.

2. Dal menu di scelta rapida, selezionare **Aggiungi** > **Controller**.

    ![Schermata di Esplora soluzioni](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Nel **aggiungere lo scaffolding** nella finestra di dialogo **Controller MVC - vuoto**e selezionare **Aggiungi**.

    ![La finestra di dialogo schermata di aggiungere lo scaffolding](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Nel **aggiungere Controller MVC vuoto** nella finestra di dialogo Nome controller *BlobsController*e selezionare **Aggiungi**.

    ![La finestra di dialogo schermata di aggiungere Controller MVC vuoto](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Aggiungere il seguente `using` direttive per il `BlobsController.cs` file:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Connettersi a un account di archiviazione e ottenere un riferimento a un contenitore

Un contenitore BLOB è una gerarchia nidificata di BLOB e cartelle. Il resto dei passaggi in questo documento richiedono un riferimento a un contenitore blob, in modo che il codice deve essere inserito nel proprio metodo per riutilizzare.

La seguente procedura crea un metodo per la connessione all'account di archiviazione usando la stringa di connessione in **appSettings. JSON**. I passaggi crea anche un riferimento a un contenitore. L'impostazione della stringa di connessione in **appSettings. JSON** denominato con il formato `<storageaccountname>_AzureStorageConnectionString`. 

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato **GetCloudBlobContainer** che restituisce un **CloudBlobContainer**. Assicurarsi di sostituire `<storageaccountname>_AzureStorageConnectionString` con il nome effettivo della chiave in **Web. config**.
    
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
> Anche se *contenitore di blob test* non esiste ancora, questo codice crea un riferimento a esso. Si tratta pertanto il contenitore può essere creato con il `CreateIfNotExists` metodo illustrato nel passaggio successivo.

## <a name="create-a-blob-container"></a>Creare un contenitore BLOB

I passaggi seguenti illustrano come creare un contenitore BLOB:

1. Aggiungere un metodo denominato `CreateBlobContainer` che restituisce un `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Ottenere un `CloudBlobContainer` oggetto che rappresenta un riferimento al nome del contenitore blob desiderato. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Chiamare il `CloudBlobContainer.CreateIfNotExists` metodo per creare il contenitore, se non esiste ancora. Il `CloudBlobContainer.CreateIfNotExists` restituisce **true** se il contenitore non esiste ed è stato creato correttamente. In caso contrario, il metodo restituisce **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Aggiornamento `ViewBag` con il nome del contenitore blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Nell'esempio seguente viene completato `CreateBlobContainer` metodo:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. In **Esplora**, fare doppio clic su di **viste** cartella.

2. Dal menu di scelta rapida, selezionare **Aggiungi** > **nuova cartella**. Denominare la nuova cartella *BLOB*. 

1. In **Esplora**, espandere il **viste** cartella e il pulsante destro del mouse, **BLOB**.

4. Dal menu di scelta rapida, selezionare **Aggiungi** > **vista**.

1. Nel **Aggiungi visualizzazione** finestra di dialogo immettere **CreateBlobContainer** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `CreateBlobContainer.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. In **Esplora**, espandere il **viste** > **Shared** cartella e aprire `_Layout.cshtml`.

1. Cercare l'elenco non ordinato simile al seguente: `<ul class="nav navbar-nav">`.  Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Eseguire l'applicazione e selezionare **crea contenitore Blob** per visualizzare i risultati simili a nella schermata seguente:
  
    ![Schermata di creazione del contenitore blob](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Come accennato in precedenza, il `CloudBlobContainer.CreateIfNotExists` restituisce **true** solo quando il contenitore non esiste e viene creato. Pertanto, se l'app viene eseguita quando il contenitore esiste, il metodo restituisce **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Caricare un BLOB in un contenitore BLOB

Quando il [contenitore blob viene creato](#create-a-blob-container), caricare file in tale contenitore. In questa sezione vengono illustrati il caricamento di un file locale a un contenitore blob. I passaggi, non vi è un contenitore blob denominato *contenitore di blob test*. 

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato `UploadBlob` che restituisce una stringa.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. All'interno di `UploadBlob` (metodo), ottenere un `CloudBlobContainer` oggetto che rappresenta un riferimento al nome del contenitore blob desiderato. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Archiviazione di Azure supporta i tipi di blob diversi. In questa esercitazione vengono utilizzati BLOB in blocchi. Per recuperare un riferimento a un blob in blocchi, chiamare il `CloudBlobContainer.GetBlockBlobReference` metodo.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Il nome del blob è parte dell'URL utilizzato per recuperare un blob e può essere qualsiasi stringa, incluso il nome del file.

1. Dopo che è un riferimento di blob, è possibile caricare qualsiasi flusso di dati a esso chiamando l'oggetto di riferimento blob `UploadFromStream` metodo. Il `UploadFromStream` metodo crea il blob se non esiste o lo sovrascrive se esiste. (Modifica  *&lt;al caricamento di file >* a un percorso completo in un file da caricare.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Nell'esempio seguente viene completato `UploadBlob` metodo (con il percorso completo per il file da caricare):

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

1. In **Esplora**, espandere il **viste** > **Shared** cartella e aprire `_Layout.cshtml`.

1. Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Carica BLOB**. La parola *esito positivo.* dovrebbe essere visualizzato.
    
    ![Schermata di verifica di esito positivo](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Elencare i BLOB in un contenitore BLOB

Questa sezione illustra come elencare i BLOB in un contenitore BLOB. Il codice di esempio fa riferimento al *test-blob-container* creato nella sezione [Creare un contenitore BLOB](#create-a-blob-container).

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato `ListBlobs` che restituisce un `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. All'interno di `ListBlobs` (metodo), ottenere un `CloudBlobContainer` oggetto che rappresenta un riferimento al contenitore di blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Per elencare i BLOB in un contenitore blob, utilizzare il `CloudBlobContainer.ListBlobsSegmentedAsync` metodo. Il `CloudBlobContainer.ListBlobsSegmentedAsync` metodo restituisce un `BlobResultSegment`. Questo file contiene `IListBlobItem` gli oggetti che possono essere convertiti in `CloudBlockBlob`, `CloudPageBlob`, o `CloudBlobDirectory` oggetti. Il seguente frammento di codice enumera tutti i BLOB in un contenitore BLOB. Ogni blob viene eseguito il cast all'oggetto appropriato, in base al tipo. Il nome (o l'URI nel caso di un `CloudBlobDirectory`) viene aggiunto a un elenco.

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
    Nell'esempio seguente viene completato `ListBlobs` metodo:

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

1. In **Esplora**, espandere il **viste** cartella e il pulsante destro del mouse, **BLOB**.

2. Dal menu di scelta rapida, selezionare **Aggiungi** > **vista**.

1. Nel **Aggiungi visualizzazione** finestra di dialogo immettere `ListBlobs` per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `ListBlobs.cshtml`e sostituire il contenuto con il codice seguente:

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

1. In **Esplora**, espandere il **viste** > **Shared** cartella e aprire `_Layout.cshtml`.

1. Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Eseguire l'applicazione e selezionare **elencare i BLOB** per visualizzare i risultati simili a nella schermata seguente:
  
    ![Schermata di elenco BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Scaricare BLOB

In questa sezione viene illustrato come scaricare un blob. È possibile renderlo persistente nell'archivio locale o leggere il contenuto in una stringa. Il codice di esempio fa riferimento al *test-blob-container* creato nella sezione [Creare un contenitore BLOB](#create-a-blob-container).

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato `DownloadBlob` che restituisce una stringa.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. All'interno di `DownloadBlob` (metodo), ottenere un `CloudBlobContainer` oggetto che rappresenta un riferimento al contenitore di blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Ottenere un oggetto di riferimento di blob chiamando la `CloudBlobContainer.GetBlockBlobReference` metodo. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Per scaricare un blob, utilizzare il `CloudBlockBlob.DownloadToStream` metodo. Il codice seguente trasferisce il contenuto di un blob a un oggetto flusso. Tale oggetto viene quindi inserito in un file locale. (Modifica  *&lt;locale-file-name >* e il nome completo del file che rappresenta in cui il blob è da scaricare.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Nell'esempio seguente viene completato `ListBlobs` metodo (con il percorso completo per il file locale creato):
    
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

1. In **Esplora**, espandere il **viste** > **Shared** cartella e aprire `_Layout.cshtml`.

1. Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Scarica BLOB** per scaricare il BLOB. Il blob specificato nella `CloudBlobContainer.GetBlockBlobReference` chiamata al metodo vengono scaricati nel percorso specificato nel `File.OpenWrite` chiamata al metodo. Il testo *esito positivo.* dovrebbe essere visualizzato nel browser. 

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

1. All'interno di `DeleteBlob` (metodo), ottenere un `CloudBlobContainer` oggetto che rappresenta un riferimento al contenitore di blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Ottenere un oggetto di riferimento di blob chiamando la `CloudBlobContainer.GetBlockBlobReference` metodo. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Per eliminare un blob, utilizzare il `Delete` metodo.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    Completato `DeleteBlob` metodo compariranno come indicato di seguito:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. In **Esplora**, espandere il **viste** > **Shared** cartella e aprire `_Layout.cshtml`.

1. Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Delete blob** per eliminare il blob specificato nella `CloudBlobContainer.GetBlockBlobReference` chiamata al metodo. Il testo *esito positivo.* dovrebbe essere visualizzato nel browser. Selezionare il browser **nuovamente** e quindi selezionare **elencare i BLOB** per verificare che il blob non è più nel contenitore.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come archiviare, elenco e recuperare i BLOB in archiviazione di Azure tramite ASP.NET Core. Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.

  * [Introduzione a tabelle di Azure Visual Studio e archiviazione connessi (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introduzione a Azure coda Visual Studio e archiviazione connessi (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
