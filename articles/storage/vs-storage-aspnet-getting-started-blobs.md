---
title: Introduzione all&quot;Archiviazione BLOB di Azure e ai Servizi connessi di Visual Studio (ASP.NET) | Documentazione Microsoft
description: Informazioni su come iniziare a usare l&quot;Archiviazione BLOB di Azure in un progetto ASP.NET in Visual Studio dopo la connessione a un account di archiviazione con Servizi connessi di Visual Studio
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 5840ec74f6af2e373d9ebb34b0f6e13094c33f19
ms.openlocfilehash: 003ca36c80274c4cad430ac67cf8cf3551d4137a


---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Introduzione all'Archiviazione BLOB di Azure e ai Servizi connessi di Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

L'Archiviazione BLOB di Azure è un servizio che archivia i dati non strutturati nel cloud come oggetti/BLOB. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.

Questa esercitazione illustra come scrivere codice ASP.NET per alcuni scenari comuni usando l'Archiviazione BLOB di Azure. Gli scenari includono la creazione di un contenitore BLOB e il caricamento, la creazione di elenchi, il download e l'eliminazione di BLOB.

##<a name="prerequisites"></a>Prerequisiti

* [Microsoft Visual Studio](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)
* [Account di archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Creare un controller MVC 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Controller** e selezionare **Aggiungi > Controller** dal menu di scelta rapida.

    ![Aggiungere un controller a un'app MVC ASP.NET](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. Nella finestra di dialogo **Aggiungi scaffolding** fare clic su **Controller MVC 5 - Vuoto** e selezionare **Aggiungi**.

    ![Specificare il tipo di controller MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. Nella finestra di dialogo **Aggiungi controller** assegnare un nome al controller *BlobsController*e selezionare **Aggiungi**.

    ![Assegnare un nome al controller MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Aggiungere le direttive *using* seguenti al file `BlobsController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>Creare un contenitore BLOB

Un contenitore BLOB è una gerarchia nidificata di BLOB e cartelle. I passaggi seguenti illustrano come creare un contenitore BLOB:

> [!NOTE]
> 
> Il codice in questa sezione presuppone che siano stati completati i passaggi descritti nella sezione [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato **CreateBlobContainer** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Nel metodo **CreateBlobContainer** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Cambiare *&lt;storage-account-name>* con il nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Recuperare un oggetto **CloudBlobClient** che rappresenta un client del servizio BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Recuperare un oggetto **CloudBlobContainer** che rappresenta un riferimento al nome del contenitore BLOB desiderato. Il metodo **CloudBlobClient.GetContainerReference** non esegue una richiesta all'archiviazione BLOB. Il riferimento viene restituito indipendentemente dall'esistenza del contenitore BLOB. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Chiamare il metodo **CloudBlobContainer.CreateIfNotExists** per creare il contenitore, se non esiste già. Il metodo **CloudBlobContainer.CreateIfNotExists** restituisce **true** se il contenitore non esiste e il contenitore viene creato. In caso contrario, viene restituito **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Aggiornare **ViewBag** con il nome del contenitore BLOB.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

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

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **Crea contenitore BLOB** per visualizzare risultati simili allo screenshot seguente:
  
    ![Creare un contenitore BLOB](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Come accennato in precedenza, il metodo **CloudBlobContainer.CreateIfNotExists** restituisce **true** solo quando il contenitore non esiste e viene creato. Se quindi si esegue l'app quando il contenitore esiste, il metodo restituisce **false**. Per eseguire l'app più volte, è necessario eliminare il contenitore prima di eseguire di nuovo l'app. È possibile eliminare il contenitore usando il metodo **CloudBlobContainer.Delete**. È possibile anche eliminare il contenitore usando il [portale Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) o [Esplora archivi di Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="upload-a-blob-into-a-blob-container"></a>Caricare un BLOB in un contenitore BLOB

Dopo avere [creato un contenitore BLOB](#create-a-blob-container), è possibile caricare file in questo contenitore. Questa sezione illustra come caricare un file locale in un contenitore BLOB. Nei passaggi si presuppone che sia stato creato un contenitore BLOB denominato *test-blob-container*. 

> [!NOTE]
> 
> Il codice in questa sezione presuppone che siano stati completati i passaggi descritti nella sezione [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato **UploadBlob** che restituisce un **EmptyResult**.

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Nel metodo **UploadBlob** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Recuperare un oggetto **CloudBlobClient** che rappresenta un client del servizio BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Recuperare un oggetto **CloudBlobContainer** che rappresenta un riferimento al nome del contenitore BLOB. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Come illustrato in precedenza, l'Archiviazione di Azure supporta tipi di BLOB diversi. Per recuperare un riferimento a un BLOB di pagine, chiamare il metodo **CloudBlobContainer.GetPageBlobReference**. Per recuperare un riferimento a un BLOB in blocchi, chiamare il metodo **CloudBlobContainer.GetBlockBlobReference**. Nella maggior parte dei casi è consigliabile usare il tipo di BLOB in blocchi. Cambiare <blob-name>* nel nome da assegnare al BLOB una volta caricato.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Dopo avere ottenuto un riferimento al BLOB, è possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStream** dell'oggetto di riferimento al BLOB. Il metodo **UploadFromStream** crea il BLOB, se non esiste, o lo sovrascrive, se esiste. Cambiare *&lt;file-to-upload>* in un percorso completo del file da caricare.

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **BLOB** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **Carica BLOB**.  
  
La sezione [Elencare i BLOB in un contenitore BLOB](#list-the-blobs-in-a-blob-container) illustra come elencare i BLOB in un contenitore BLOB.    

## <a name="list-the-blobs-in-a-blob-container"></a>Elencare i BLOB in un contenitore BLOB

Questa sezione illustra come elencare i BLOB in un contenitore BLOB. Il codice di esempio fa riferimento al *test-blob-container* creato nella sezione [Creare un contenitore BLOB](#create-a-blob-container).

> [!NOTE]
> 
> Il codice in questa sezione presuppone che siano stati completati i passaggi descritti nella sezione [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato **ListBlobs** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Nel metodo **ListBlobs** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Recuperare un oggetto **CloudBlobClient** che rappresenta un client del servizio BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Recuperare un oggetto **CloudBlobContainer** che rappresenta un riferimento al nome del contenitore BLOB. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Per elencare i BLOB in un contenitore BLOB, usare il metodo **CloudBlobContainer.ListBlobs**. Il metodo **CloudBlobContainer.ListBlobs** restituisce un oggetto **IListBlobItem** che viene trasmesso a un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Il seguente frammento di codice enumera tutti i BLOB in un contenitore BLOB. Ogni BLOB trasmesso all'oggetto appropriato in base al tipo e il suo nome (o URI nel caso di una **CloudBlobDirectory**) viene aggiunto a un elenco.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
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

    I contenitori BLOB possono contenere directory, oltre ai BLOB. Se ad esempio si esamina un contenitore BLOB denominato *test-blob-container* con la gerarchia seguente:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Con l'esempio di codice precedente, l'elenco di stringhe **blobs** contiene valori simili ai seguenti:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Come può notare, l'elenco include solo le entità di livello superiore, non quelle annidate (*bar.png* e *baz.png*). Per elencare tutte le entità all'interno di un contenitore BLOB, è necessario chiamare il metodo **CloudBlobContainer.ListBlobs** e passare **true** per il parametro **useFlatBlobListing**.    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    L'impostazione del parametro **useFlatBlobListing** su **true** restituisce un elenco semplice di tutte le entità nel contenitore BLOB e genera i risultati seguenti:

        foo.png
        dir1/bar.png
        dir2/baz.png

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **BLOB** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **ListBlobs** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `ListBlobs.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

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

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **List blobs** (Elenca BLOB) per visualizzare risultati simili allo screenshot seguente:
  
    ![Elenco di BLOB](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Scaricare BLOB

Questa sezione illustra come scaricare un BLOB e salvarlo in modo permanente nell'archiviazione locale o leggere il contenuto in una stringa. Il codice di esempio fa riferimento al *test-blob-container* creato nella sezione [Creare un contenitore BLOB](#create-a-blob-container).

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato **DownloadBlob** che restituisce un **ActionResult**.

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Nel metodo **DownloadBlob** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Recuperare un oggetto **CloudBlobClient** che rappresenta un client del servizio BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Recuperare un oggetto **CloudBlobContainer** che rappresenta un riferimento al nome del contenitore BLOB. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Recuperare un oggetto di riferimento al BLOB chiamando il metodo **CloudBlobContainer.GetBlockBlobReference** o **CloudBlobContainer.GetPageBlobReference**. Cambiare *&lt;blob-name>* nel nome del BLOB che si sta scaricando.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Per scaricare un BLOB, usare il metodo **CloudBlockBlob.DownloadToStream** o **CloudPageBlob.DownloadToStream**, a seconda del tipo di BLOB. Il frammento di codice seguente usa il metodo **CloudBlockBlob.DownloadToStream** per trasferire il contenuto di un BLOB a un oggetto flusso che viene salvato in modo permanente in un file locale: modificare *&lt;local-file-name>* nel nome del file completo che rappresenta il percorso in cui scaricare il BLOB. 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **Scarica BLOB** per scaricare il BLOB. Il BLOB specificato nella chiamata al metodo **CloudBlobContainer.GetBlockBlobReference** viene scaricato nel percorso specificato nella chiamata al metodo **File.OpenWrite**. 

## <a name="delete-blobs"></a>Eliminare BLOB

I passaggi seguenti illustrano come eliminare un BLOB:

> [!NOTE]
> 
> Il codice in questa sezione presuppone che siano stati completati i passaggi descritti nella sezione [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `BlobsController.cs` .

1. Aggiungere un metodo denominato **DeleteBlob** che restituisce un **ActionResult**.

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;storage-account-name>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Recuperare un oggetto **CloudBlobClient** che rappresenta un client del servizio BLOB.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Recuperare un oggetto **CloudBlobContainer** che rappresenta un riferimento al nome del contenitore BLOB. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Recuperare un oggetto di riferimento al BLOB chiamando il metodo **CloudBlobContainer.GetBlockBlobReference** o **CloudBlobContainer.GetPageBlobReference**. Cambiare *&lt;blob-name>* nel nome del BLOB che si sta eliminando.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink**, aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Eseguire l'applicazione e selezionare **Elimina BLOB** per eliminare il BLOB specificato nella chiamata al metodo **CloudBlobContainer.GetBlockBlobReference**. 

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.

  * [Introduzione all'archiviazione tabelle di Azure e ai Servizi connessi di Visual Studio (ASP.NET)](./vs-storage-aspnet-getting-started-tables.md)
  * [Introduzione all'archiviazione code di Azure e ai Servizi connessi di Visual Studio (ASP.NET)](./vs-storage-aspnet-getting-started-queues.md)


<!--HONumber=Jan17_HO1-->


