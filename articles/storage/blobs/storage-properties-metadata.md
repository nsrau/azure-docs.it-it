---
title: Impostare e recuperare proprietà e metadati degli oggetti in Archiviazione di Azure | Microsoft Docs
description: Archiviare i metadati personalizzati per oggetti di archiviazione di Azure, impostare e recuperare le proprietà di sistema.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.openlocfilehash: 86bb7e736754cbc6a93bba5fff5d8d1877b1e3b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916581"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Impostare e recuperare proprietà e metadati

Oggetti nelle proprietà di sistema di supporto di Archiviazione di Azure e metadati definiti dall'utente, oltre ai dati che contengono. Questo articolo illustra la gestione delle proprietà di sistema e i metadati definiti dall'utente con la [Libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Le proprietà di sistema**:  Proprietà di sistema su ogni risorsa di archiviazione. Alcune di esse possono essere lette o impostate, mentre altre sono di sola lettura. Anche se in modo non esplicito, alcune proprietà di sistema corrispondono a specifiche intestazioni HTTP standard. Le librerie client di Archiviazione di Azure gestiscono queste proprietà per l'utente.

* **Metadati definiti dall'utente**: Metadati definiti dall'utente è costituito da una o più coppie nome / valore specificato per una risorsa di archiviazione di Azure. È possibile usare i metadati per archiviare valori aggiuntivi con una risorsa. I valori di metadati sono solo per le proprie esigenze e non influiscono sul comportamento della risorsa.

Il recupero dei valori di proprietà e dei metadati per una risorsa è un processo in due fasi. Prima di leggere questi valori, è necessario recuperarli in modo esplicito chiamando il metodo **FetchAttributes** o **FetchAttributesAsync**. L'eccezione è se si chiama il metodo **Exists** oppure **ExistsAsync** su una risorsa. Quando si chiama uno di questi metodi, Archiviazione di Azure chiama il metodo **FetchAttributes** appropriato come parte della chiamata al metodo **Exists**.

> [!IMPORTANT]
> Se si ritiene che la proprietà o i valori dei metadati per una risorsa di archiviazione non siano stati popolati, verificare che il codice chiami il metodo **FetchAttributes** o **FetchAttributesAsync**.
>
> Coppie nome/valore dei metadati sono intestazioni HTTP valide e pertanto devono essere conforme alle restrizioni imposte sulle intestazioni HTTP. I nomi dei metadati devono essere nomi di intestazioni HTTP validi, possono contenere solo caratteri ASCII e devono essere trattati come maiuscole e minuscole. I valori dei metadati che contiene caratteri non ASCII devono essere con codifica Base64 o con codifica URL.

## <a name="setting-and-retrieving-properties"></a>Impostazione e recupero di proprietà
Per recuperare i valori della proprietà, chiamare il metodo **FetchAttributesAsync** sul BLOB o sul contenitore per popolare le proprietà, quindi leggere i valori.

Per impostare le proprietà di un oggetto, specificare il valore della proprietà, quindi chiamare il metodo **SetProperties** .

L'esempio di codice seguente crea un contenitore e scrive alcuni dei valori delle proprietà in una finestra della console.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Impostazione e recupero dei metadati
È possibile specificare i metadati come uno o più coppie nome-valore in una risorsa BLOB o contenitore. Per impostare i metadati, aggiungere coppie nome-valore alla raccolta **Metadati** nella risorsa, quindi chiamare il metodo **SetMetadata** o **SetMetadataAsync** per salvare i valori nel servizio.

> [!NOTE]
> Il nome dei metadati deve essere conforme alle convenzioni di denominazione degli identificatori C#.
>
>

Il seguente codice di esempio imposta i metadati in un contenitore. Un valore è impostato mediante l'utilizzo del metodo di raccolta **Aggiungi** . L'altro valore è impostato utilizzando la sintassi implicita chiave/valore. Entrambi sono validi.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Per recuperare i metadati, chiamare il metodo **FetchAttributes** o **FetchAttributesAsyn** nel BLOB o nel contenitore per popolare la raccolta **Metadati**, quindi leggere i valori, come nell'esempio mostrato di seguito.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni di riferimento sulla libreria client di archiviazione di Azure per .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Pacchetto NuGet per la libreria client di Archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
