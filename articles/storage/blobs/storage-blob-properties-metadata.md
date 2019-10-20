---
title: Gestire proprietà e metadati per un BLOB con .NET-archiviazione di Azure
description: Informazioni su come impostare e recuperare le proprietà di sistema e archiviare metadati personalizzati nei BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 03171e395bb23da05ee5420f60abca9a16ffb774
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600293"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Gestire le proprietà e i metadati dei BLOB con .NET

Oltre ai dati che contengono, i BLOB supportano le proprietà di sistema e i metadati definiti dall'utente. Questo articolo illustra come gestire le proprietà di sistema e i metadati definiti dall'utente con la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Informazioni su proprietà e metadati

- **Proprietà di sistema**: sono presenti proprietà di sistema in ogni risorsa di archiviazione BLOB. Alcune di esse possono essere lette o impostate, mentre altre sono di sola lettura. Anche se in modo non esplicito, alcune proprietà di sistema corrispondono a specifiche intestazioni HTTP standard. La libreria client di archiviazione di Azure per .NET gestisce queste proprietà.

- **Metadati definiti dall'utente**: i metadati definiti dall'utente sono costituiti da una o più coppie nome/valore specificate per una risorsa di archiviazione BLOB. È possibile usare i metadati per archiviare valori aggiuntivi con la risorsa. I valori dei metadati sono solo per i propri scopi e non influiscono sul comportamento della risorsa.

Il recupero dei metadati e dei valori delle proprietà per una risorsa di archiviazione BLOB è un processo in due passaggi. Prima di poter leggere questi valori, è necessario recuperarli in modo esplicito chiamando il metodo `FetchAttributes` o `FetchAttributesAsync`. L'eccezione a questa regola è che i metodi `Exists` e `ExistsAsync` chiamano il metodo `FetchAttributes` appropriato dietro le quinte. Quando si chiama uno di questi metodi, non è necessario chiamare anche `FetchAttributes`.

> [!IMPORTANT]
> Se si ritiene che i valori di proprietà o metadati per una risorsa di archiviazione non siano stati popolati, verificare che il codice chiami il metodo `FetchAttributes` o `FetchAttributesAsync`.

## <a name="set-and-retrieve-properties"></a>Impostare e recuperare le proprietà

Nell'esempio di codice seguente vengono impostate le proprietà di sistema `ContentType` e `ContentLanguage` in un BLOB.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
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

Per recuperare le proprietà del BLOB, chiamare il metodo `FetchAttributes` o `FetchAttributesAsync` sul BLOB per popolare la proprietà `Properties`. Nell'esempio di codice seguente vengono ottenute le proprietà di sistema di un BLOB e vengono visualizzati alcuni valori:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
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

È possibile specificare i metadati come uno o più coppie nome-valore in una risorsa BLOB o contenitore. Per impostare i metadati, aggiungere coppie nome-valore alla raccolta `Metadata` sulla risorsa. Quindi, chiamare uno dei metodi seguenti per scrivere i valori:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Le coppie nome/valore dei metadati sono intestazioni HTTP valide e devono essere conformi a tutte le restrizioni che regolano le intestazioni HTTP. I nomi dei metadati devono essere nomi di intestazioni HTTP C# validi e identificatori validi, possono contenere solo caratteri ASCII e devono essere considerati senza distinzione tra maiuscole e minuscole. Valori di metadati con [codifica Base64](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) o con [codifica URL](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) che contengono caratteri non ASCII.

Il nome dei metadati deve essere conforme alle convenzioni di denominazione degli identificatori C#. I nomi dei metadati mantengono il caso usato al momento della creazione, ma non fanno distinzione tra maiuscole e minuscole durante l'impostazione o la lettura. Se vengono inviate due o più intestazioni di metadati con lo stesso nome per una risorsa, archiviazione BLOB di Azure restituisce il codice di errore HTTP 400 (richiesta non valida).

Nell'esempio di codice seguente vengono impostati i metadati su un BLOB. Un valore viene impostato utilizzando il metodo `Add` della raccolta. L'altro valore è impostato utilizzando la sintassi implicita chiave/valore.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
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

Per recuperare i metadati, chiamare il metodo `FetchAttributes` o `FetchAttributesAsync` sul BLOB o sul contenitore per popolare la raccolta `Metadata`, quindi leggere i valori, come illustrato nell'esempio riportato di seguito.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Vedi anche

- [Operazione Set Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Operazione Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Impostazione operazione metadati BLOB](/rest/api/storageservices/set-blob-metadata)
- [Operazione Get Blob Metadata](/rest/api/storageservices/set-blob-metadata)
