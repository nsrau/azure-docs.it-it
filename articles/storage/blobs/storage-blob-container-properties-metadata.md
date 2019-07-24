---
title: Gestire proprietà e metadati per un contenitore BLOB con .NET-archiviazione di Azure
description: Informazioni su come impostare e recuperare le proprietà di sistema e archiviare metadati personalizzati nei contenitori BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.openlocfilehash: bd745c54fb659729b1e31f9975b2e4e0b6a97a83
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235249"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Gestire le proprietà e i metadati del contenitore con .NET

I contenitori BLOB supportano le proprietà di sistema e i metadati definiti dall'utente, oltre ai dati che contengono. Questo articolo illustra come gestire le proprietà di sistema e i metadati definiti dall'utente con la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Informazioni su proprietà e metadati

- **Proprietà di sistema**: Sono presenti proprietà di sistema in ogni risorsa di archiviazione BLOB. Alcune di esse possono essere lette o impostate, mentre altre sono di sola lettura. Anche se in modo non esplicito, alcune proprietà di sistema corrispondono a specifiche intestazioni HTTP standard. La libreria client di archiviazione di Azure per .NET gestisce queste proprietà.

- **Metadati definiti dall'utente**: I metadati definiti dall'utente sono costituiti da una o più coppie nome/valore specificate per una risorsa di archiviazione BLOB. È possibile usare i metadati per archiviare valori aggiuntivi con la risorsa. I valori di metadati sono solo per le proprie esigenze e non influiscono sul comportamento della risorsa.

Il recupero dei valori di proprietà e metadati per una risorsa di archiviazione BLOB è un processo in due passaggi. Prima di leggere questi valori, è necessario recuperarli in modo esplicito chiamando il metodo **FetchAttributes** o **FetchAttributesAsync**. L'eccezione a questa regola è che i  metodi Exists e **ExistsAsync** chiamano il metodo **FetchAttributes** appropriato dietro le quinte. Quando si chiama uno di questi metodi, non è necessario chiamare anche **FetchAttributes**.

> [!IMPORTANT]
> Se si ritiene che la proprietà o i valori dei metadati per una risorsa di archiviazione non siano stati popolati, verificare che il codice chiami il metodo **FetchAttributes** o **FetchAttributesAsync**.

Le coppie nome/valore dei metadati sono intestazioni HTTP valide, pertanto devono essere conformi a tutte le restrizioni che regolano le intestazioni HTTP. I nomi dei metadati devono essere nomi di intestazioni HTTP C# validi e identificatori validi, possono contenere solo caratteri ASCII e devono essere considerati senza distinzione tra maiuscole e minuscole. I valori dei metadati che contengono caratteri non ASCII devono essere codificati in base 64 o con codifica URL.

## <a name="retrieve-container-properties"></a>Recuperare le proprietà del contenitore

Per recuperare le proprietà del contenitore, chiamare uno dei metodi seguenti:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Nell'esempio di codice seguente vengono recuperate le proprietà di sistema di un contenitore e vengono scritti alcuni valori di proprietà in una finestra della console:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Impostare e recuperare i metadati

È possibile specificare i metadati come uno o più coppie nome-valore in una risorsa BLOB o contenitore. Per impostare i metadati, aggiungere coppie nome-valore alla raccolta di **metadati** nella risorsa, quindi chiamare uno dei metodi seguenti per scrivere i valori:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Il nome dei metadati deve essere conforme alle convenzioni di denominazione degli identificatori C#. I nomi dei metadati mantengono il caso in cui sono stati creati, ma non fanno distinzione tra maiuscole e minuscole durante l'impostazione o la lettura. Se vengono inviate due o più intestazioni di metadati con lo stesso nome per una risorsa, l'archivio BLOB restituisce il codice di errore HTTP 400 (richiesta non valida).

Il seguente codice di esempio imposta i metadati in un contenitore. Un valore è impostato mediante l'utilizzo del metodo di raccolta **Aggiungi** . L'altro valore è impostato utilizzando la sintassi implicita chiave/valore. Entrambi sono validi.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Per recuperare i metadati, chiamare il metodo **FetchAttributes** o **FetchAttributesAsyn** nel BLOB o nel contenitore per popolare la raccolta **Metadati**, quindi leggere i valori, come nell'esempio mostrato di seguito.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
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
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="see-also"></a>Vedere anche

- [Operazione Get Container Properties](/rest/api/storageservices/get-container-properties)
- [Operazione di impostazione dei metadati del contenitore](/rest/api/storageservices/set-container-metadata)
- [Operazione Get Container Metadata](/rest/api/storageservices/set-container-metadata)
