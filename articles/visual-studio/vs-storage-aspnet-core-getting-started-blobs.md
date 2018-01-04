---
title: Introduzione all'archiviazione blob di Azure e Visual Studio servizi connessi (ASP.NET Core) | Documenti Microsoft
description: Come iniziare a usare l'archiviazione blob di Azure in un progetto ASP.NET Core in Visual Studio dopo la connessione a un account di archiviazione tramite servizi connessi di Visual Studio
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
ms.openlocfilehash: 889afa471eeb556662cddf8eb383a905f08b1f01
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introduzione all'archiviazione blob di Azure e Visual Studio servizi connessi (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

L'Archiviazione BLOB di Azure è un servizio che archivia i dati non strutturati nel cloud come oggetti/BLOB. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.

In questa esercitazione viene illustrato come scrivere il codice ASP.NET Core per alcuni scenari comuni di utilizzo dell'archiviazione blob di Azure. Gli scenari includono la creazione di un contenitore BLOB e il caricamento, la creazione di elenchi, il download e l'eliminazione di BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Questa sezione vengono illustrate l'impostazione dell'ambiente di sviluppo, tra cui la creazione di un'applicazione MVC ASP.NET, aggiunta di una connessione di servizi connessi, aggiunta di un controller e che specifica le direttive dello spazio dei nomi obbligatorio.

### <a name="create-an-aspnet-mvc-app-project"></a>Creare un progetto di app ASP.NET MVC

1. Aprire Visual Studio.

1. Scegliere **File->Nuovo->Progetto** dal menu principale.

1. Nella finestra di dialogo **Nuovo progetto** specificare le opzioni evidenziate nella figura seguente:

    ![Creare il progetto ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Selezionare **OK**.

1. Nella finestra di dialogo **Nuovo progetto ASP.NET** specificare le opzioni evidenziate nella figura seguente:

    ![Specificare MVC](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

1. Selezionare **OK**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Usare Servizi connessi per connettersi a un account di archiviazione di Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi > Servizi connessi** dal menu di scelta rapida.

1. Nel **Aggiungi servizio connesso** finestra di dialogo Seleziona **archiviazione Cloud con l'archiviazione di Azure**, quindi selezionare **configura**.

    ![Finestra di dialogo Servizi connessi](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Nel **di archiviazione di Azure** finestra di dialogo, Seleziona account di archiviazione di Azure da utilizzare per questa esercitazione.  Per creare un nuovo account di archiviazione di Azure, fare clic su **creare un nuovo Account di archiviazione** e completare il modulo.  Dopo aver selezionato un account archiviazione esistente o crearne uno nuovo, fare clic su **Aggiungi**.  Visual Studio verrà installato il pacchetto NuGet per l'archiviazione di Azure e una stringa di connessione di archiviazione per **appSettings. JSON**.

> [!TIP]
> Per informazioni su come creare un account di archiviazione con il [portale di Azure](https://portal.azure.com), vedere [creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Un account di archiviazione di Azure può essere creato anche usando [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [CLI di Azure](../storage/common/storage-azure-cli.md), o [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Creare un controller MVC 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Controller** e selezionare **Aggiungi > Controller** dal menu di scelta rapida.

    ![Aggiungere un controller a un'applicazione ASP.NET MVC di base](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Nel **aggiungere lo scaffolding** finestra di dialogo Seleziona **Controller MVC - vuoto**e selezionare **Aggiungi**.

    ![Specificare il tipo di controller MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Nella finestra di dialogo **Aggiungi controller** assegnare un nome al controller *BlobsController*e selezionare **Aggiungi**.

    ![Assegnare un nome al controller MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Aggiungere le direttive *using* seguenti al file `BlobsController.cs`:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Connettersi a un account di archiviazione e ottenere un riferimento a un contenitore

Un contenitore BLOB è una gerarchia nidificata di BLOB e cartelle.  Il resto dei passaggi in questo documento richiedono un riferimento a un contenitore blob, in modo che il codice deve essere inserito il proprio metodo per riutilizzare.

La seguente procedura crea un metodo per la connessione all'account di archiviazione usando la stringa di connessione in **appSettings. JSON** e creare un riferimento a un contenitore.  L'impostazione della stringa di connessione in **appSettings. JSON** saranno denominate con il formato `<storageaccountname>_AzureStorageConnectionString`. 

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato **GetCloudBlobContainer** che restituisce un **CloudBlobContainer**.  Assicurarsi di sostituire `<storageaccountname>_AzureStorageConnectionString` con il nome effettivo della chiave in **Web. config**.
    
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
> Anche se *contenitore di blob test* non esiste ancora, questo codice crea un riferimento a esso, quindi il contenitore può essere creato con il `CreateIfNotExists` metodo illustrato nel passaggio successivo.

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

1. Chiamare il `CloudBlobContainer.CreateIfNotExists` metodo per creare il contenitore se non esiste ancora. Il `CloudBlobContainer.CreateIfNotExists` restituisce **true** se il contenitore non esiste ed è stato creato correttamente. In caso contrario, viene restituito **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Aggiornamento di `ViewBag` con il nome del contenitore blob.

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

1. Nel **Esplora**, fare doppio clic sul **viste** cartella, scegliere dal menu di scelta rapida, **Aggiungi -> nuova cartella**. Denominare la nuova cartella *BLOB*. 

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **BLOB** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **CreateBlobContainer** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `CreateBlobContainer.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Cercare l'elenco non ordinato simile al seguente: `<ul class="nav navbar-nav">`.  Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Crea contenitore BLOB** per visualizzare risultati simili allo screenshot seguente:
  
    ![Creare un contenitore BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Come accennato in precedenza, il `CloudBlobContainer.CreateIfNotExists` restituisce **true** solo quando il contenitore non esiste e viene creato. Pertanto, se l'app viene eseguita quando il contenitore esiste, il metodo restituisce **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Caricare un BLOB in un contenitore BLOB

Una volta il [contenitore blob viene creato](#create-a-blob-container), caricare file in tale contenitore. In questa sezione vengono illustrati il caricamento di un file locale a un contenitore blob. I passaggi, non vi è un contenitore blob denominato *contenitore di blob test*. 

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

1. Come illustrato in precedenza, l'Archiviazione di Azure supporta tipi di BLOB diversi. In questa esercitazione vengono utilizzati BLOB in blocchi.  Per recuperare un riferimento a un blob in blocchi, chiamare il `CloudBlobContainer.GetBlockBlobReference` metodo.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Il nome del blob è parte dell'URL utilizzato per recuperare un blob e può essere qualsiasi stringa, incluso il nome del file.

1. Quando è presente un riferimento di blob, di caricare qualsiasi flusso di dati a esso la chiamata dell'oggetto di riferimento blob `UploadFromStream` metodo. Il `UploadFromStream` metodo crea il blob se non esiste o lo sovrascrive se esiste. (Modifica  *&lt;al caricamento di file >* a un percorso completo in un file da caricare.)

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

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Carica BLOB**.  La parola "success!" deve essere visualizzato.
    
    ![Verifica di esito positivo](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
La sezione [Elencare i BLOB in un contenitore BLOB](#list-the-blobs-in-a-blob-container) illustra come elencare i BLOB in un contenitore BLOB.    

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
   
1. Per elencare i BLOB in un contenitore blob, utilizzare il `CloudBlobContainer.ListBlobsSegmentedAsync` metodo. Il `CloudBlobContainer.ListBlobsSegmentedAsync` metodo restituisce un `BlobResultSegment` contenente `IListBlobItem` gli oggetti che possono essere convertiti in `CloudBlockBlob`, `CloudPageBlob`, o `CloudBlobDirectory` oggetti. Il seguente frammento di codice enumera tutti i BLOB in un contenitore BLOB. Ogni blob viene eseguito il cast all'oggetto appropriato in base a tipo e il relativo nome (o l'URI nel caso di un `CloudBlobDirectory`) viene aggiunto a un elenco.

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

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **BLOB** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

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

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Eseguire l'applicazione e selezionare **List blobs** (Elenca BLOB) per visualizzare risultati simili allo screenshot seguente:
  
    ![Elenco di BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Scaricare BLOB

Questa sezione illustra come scaricare un BLOB e salvarlo in modo permanente nell'archiviazione locale o leggere il contenuto in una stringa. Il codice di esempio fa riferimento al *test-blob-container* creato nella sezione [Creare un contenitore BLOB](#create-a-blob-container).

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

1. Ottenere un oggetto di riferimento di blob chiamando `CloudBlobContainer.GetBlockBlobReference` metodo. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Per scaricare un blob, utilizzare il `CloudBlockBlob.DownloadToStream` metodo. Il codice seguente trasferisce il contenuto di un blob a un oggetto flusso che viene quindi inserito in un file locale (modifica  *&lt;locale-file-name >* e il nome completo del file che rappresenta in cui il blob è da scaricare.): 

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

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Scarica BLOB** per scaricare il BLOB. Il blob specificato nella `CloudBlobContainer.GetBlockBlobReference` chiamata al metodo vengono scaricati nel percorso specificato nel `File.OpenWrite` chiamata al metodo.  Il testo "success!" deve essere visualizzato nel browser. 

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

1. Ottenere un oggetto di riferimento di blob chiamando `CloudBlobContainer.GetBlockBlobReference` metodo. 

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

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo `<li>` elemento nell'elenco, aggiungere il codice HTML seguente per aggiungere un'altra voce di menu di navigazione:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Eseguire l'applicazione e selezionare **Delete blob** per eliminare il blob specificato nella `CloudBlobContainer.GetBlockBlobReference` chiamata al metodo.  Il testo "success!" dovrebbe essere visualizzato nel browser.  Selezionare il browser **nuovamente** pulsante, quindi selezionare **elencare i BLOB** per verificare il blob non è più nel contenitore.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come archiviare, elenco e recuperare i BLOB in archiviazione di Azure tramite ASP.NET Core.  Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.

  * [Introduzione all'archiviazione tabelle di Azure e ai Servizi connessi di Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introduzione all'archiviazione code di Azure e ai Servizi connessi di Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
