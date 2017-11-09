---
title: "Impostare e recuperare proprietà e metadati degli oggetti in Archiviazione di Azure | Microsoft Docs"
description: "Archiviare i metadati personalizzati per oggetti di archiviazione di Azure, impostare e recuperare le proprietà di sistema."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: a3eb598b2dabd4986c72b8814926eb0944707050
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Impostare e recuperare proprietà e metadati

Oggetti nelle proprietà di sistema di supporto di Archiviazione di Azure e metadati definiti dall'utente, oltre ai dati che contengono. Questo articolo illustra la gestione delle proprietà di sistema e i metadati definiti dall'utente con la [Libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Proprietà di sistema**: proprietà di sistema su ogni risorsa di archiviazione. Alcune di esse possono essere lette o impostate, mentre altre sono di sola lettura. Anche se in modo non esplicito, alcune proprietà di sistema corrispondono a specifiche intestazioni HTTP standard. La libreria client di archiviazione di Azure gestisce tale funzionalità.

* **Metadati definiti dall'utente**: i metadati definiti dall'utente sono metadati specificati in una determinata risorsa, sotto forma di coppia nome-valore. È possibile usare i metadati per archiviare valori aggiuntivi con una risorsa di archiviazione. Questi valori di metadati aggiuntivi sono solo per le proprie esigenze e non influiscono sul comportamento della risorsa.

Il recupero dei valori di proprietà e dei metadati per una risorsa è un processo in due fasi. Prima di leggere questi valori, è necessario recuperarli in modo esplicito chiamando il metodo **FetchAttributesAsync**.

> [!IMPORTANT]
> I valori di proprietà e i metadati per una risorsa di archiviazione non vengono popolati a meno che non si chiami uno dei metodi **FetchAttributesAsync**.
>
> Verrà visualizzato un messaggio `400 Bad Request`, se una qualsiasi coppia nome/valore contiene caratteri non ASCII. Le coppie nome/valore di metadati sono intestazioni HTTP valide e devono essere conformi alle restrizioni imposte sulle intestazioni HTTP. È quindi consigliabile usare la codifica URL o la codifica Base64 per i nomi e i valori contenenti caratteri non ASCII.
>

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
È possibile specificare i metadati come uno o più coppie nome-valore in una risorsa BLOB o contenitore. Per impostare i metadati, aggiungere coppie nome-valore alla raccolta **Metadati** nella risorsa, quindi chiamare il metodo **SetMetadata** per salvare i valori nel servizio.

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

Per recuperare i metadati, chiamare il metodo **FetchAttributes** nel BLOB o nel contenitore per popolare la raccolta **Metadati**, quindi leggere i valori, come nell'esempio mostrato di seguito.

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
