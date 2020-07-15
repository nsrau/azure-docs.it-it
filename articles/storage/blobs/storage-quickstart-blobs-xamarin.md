---
title: 'Avvio rapido: Libreria di Archiviazione BLOB di Azure v12 - Xamarin'
description: Questo argomento di avvio rapido illustra come usare la libreria client di Archiviazione BLOB di Azure versione 12 con Xamarin per creare un contenitore e un BLOB nell'archiviazione BLOB (oggetto). Verrà successivamente illustrato come scaricare il BLOB nel dispositivo mobile e come elencare tutti i BLOB di un contenitore.
author: codemillmatt
ms.author: masoucou
ms.date: 05/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b6dc5ba59aa563da6e7c19e6c821f4a9b697832f
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206529"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Avvio rapido: Libreria client di Archiviazione BLOB di Azure v12 con Xamarin

Introduzione alla libreria client di Archiviazione BLOB di Azure v12 con Xamarin. L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. L'archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati.

Usare la libreria client di Archiviazione BLOB di Azure v12 con Xamarin per:

* Creare un contenitore
* Caricare un oggetto BLOB in Archiviazione di Azure
* Elencare tutti i BLOB in un contenitore
* Scaricare il BLOB nel dispositivo
* Eliminare un contenitore

Collegamenti di riferimento:

* [Documentazione di riferimento delle API](/dotnet/api/azure.storage.blobs)
* [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Esempio](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Account di archiviazione di Azure: [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Visual Studio con il [carico di lavoro Sviluppo di applicazioni per dispositivi mobili con .NET](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows) installato o [Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>Configurazione
    
Questa sezione illustra come preparare un progetto da usare con la libreria client di Archiviazione BLOB di Azure v12 con Xamarin.
    
### <a name="create-the-project"></a>Creare il progetto

1. Aprire Visual Studio e creare un'app Forms vuota.
1. Assegnare il nome BlobQuickstartV12

### <a name="install-the-package"></a>Installare il pacchetto

1. Nel riquadro Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Gestisci pacchetti NuGet per la soluzione**.
1. Cercare **Azure.Storage.Blobs** e installare la versione stabile più recente in tutti i progetti nella soluzione.

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory **BlobQuickstartV12**:

1. Aprire il file *MainPage.xaml* nell'editor
1. Rimuovere tutto il contenuto tra gli elementi `<ContentPage></ContentPage>` e sostituirlo con il seguente:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Modello a oggetti

Archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari. L’archiviazione BLOB offre tre tipi di risorse:

* L'account di archiviazione
* Un contenitore nell'account di archiviazione
* Un oggetto BLOB in un contenitore

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB](./media/storage-blobs-introduction/blob1.png)

Per interagire con queste risorse, usare le classi .NET seguenti:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): la classe `BlobServiceClient` consente di modificare le risorse di Archiviazione di Azure e i contenitori BLOB.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): la classe `BlobContainerClient` consente di modificare i contenitori di Archiviazione di Azure e i relativi oggetti BLOB.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): la classe `BlobClient` consente di modificare gli oggetti BLOB di Archiviazione di Azure.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): la classe `BlobDownloadInfo` rappresenta le proprietà e il contenuto restituiti dal download di un BLOB.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire le attività seguenti con la libreria client di Archiviazione BLOB di Azure per .NET in un'app Xamarin.Forms:

* [Creare variabili a livello di classe](#create-class-level-variables)
* [Creare un contenitore](#create-a-container)
* [Caricare BLOB in un contenitore](#upload-blobs-to-a-container)
* [Elencare i BLOB in un contenitore](#list-the-blobs-in-a-container)
* [Scaricare BLOB](#download-blobs)
* [Eliminare un contenitore](#delete-a-container)

### <a name="create-class-level-variables"></a>Creare variabili a livello di classe

Il codice seguente dichiara diverse variabili a livello di classe. Sono necessarie per comunicare con Archiviazione BLOB di Azure nel resto di questo esempio.

Si aggiungono alla stringa di connessione per l'account di archiviazione impostata nella sezione [Configurare la stringa di connessione per l'account di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice come variabili a livello di classe all'interno del file *MainPage.xaml.cs*:

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Creare un contenitore

Decidere un nome per il nuovo contenitore. Il codice seguente aggiunge un valore GUID al nome del contenitore per garantirne l'univocità.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).

Creare un'istanza della classe [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient). Chiamare quindi il metodo [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) per creare il contenitore nell'account di archiviazione.

Aggiungere questo codice al file *MainPage.xaml.cs*:

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Caricare BLOB in un contenitore

Il frammento di codice seguente consente di:

1. Creare un oggetto `MemoryStream` di testo.
1. Caricare il testo in un BLOB chiamando la funzione [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync?view=azure-dotnet#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) della classe [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient), passando sia il nome file definito che l'oggetto `MemoryStream` di testo. Questo metodo crea il BLOB se non esiste o lo sovrascrive se esiste già.

Aggiungere questo codice al file *MainPage.xaml.cs*:

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB nel contenitore chiamando il metodo [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync). In questo caso, al contenitore è stato aggiunto un unico BLOB, quindi l'operazione di recupero dell'elenco restituisce solo tale BLOB.

Aggiungere questo codice al file *MainPage.xaml.cs*:

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare il BLOB creato in precedenza chiamando il metodo [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync). Il codice di esempio copia la rappresentazione `Stream` del BLOB prima in un oggetto `MemoryStream` e poi in un oggetto `StreamReader` in modo che sia possibile visualizzare il testo.

Aggiungere questo codice al file *MainPage.xaml.cs*:

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Eliminare un contenitore

Il codice seguente elimina le risorse create dall'app eliminando l'intero contenitore usando [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync).

L'app chiede la conferma prima di eliminare il BLOB e il contenitore. Si tratta di una valida opportunità per verificare che le risorse siano state create correttamente, prima che vengano eliminate.

Aggiungere questo codice al file *MainPage.xaml.cs*:

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>Eseguire il codice

Quando l'app viene avviata, crea prima di tutto il contenitore. Sarà quindi necessario fare clic sui pulsanti per caricare, elencare, scaricare i BLOB ed eliminare il contenitore.

Per eseguire l'app in Windows premere F5. Per eseguire l'app in Mac, premere CMD+INVIO.

L'app scrive sullo schermo dopo ogni operazione. L'output dell'app è simile all'esempio seguente:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Prima di iniziare il processo di pulizia, verificare che l'output del contenuto del BLOB sullo schermo corrisponda al valore caricato.

Dopo aver verificato i valori, confermare la richiesta di eliminazione del contenitore e terminare la demo.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come caricare, scaricare ed elencare BLOB usando la libreria client di Archiviazione BLOB di Azure v12 con Xamarin.

Per visualizzare le app di esempio di Archiviazione BLOB, procedere con:

> [!div class="nextstepaction"]
> [Esempio dell'SDK di Archiviazione BLOB di Azure v12 per Xamarin](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Per esercitazioni, esempi, guide di avvio rapido e altra documentazione, vedere [Azure per sviluppatori di app per dispositivi mobili](/azure/mobile-apps).
* Per altre informazioni su Xamarin, vedere l'[introduzione a Xamarin](/xamarin/get-started/).
