---
title: Gestire proprietà e metadati per un BLOB con .NET - Archiviazione di AzureManage properties and metadata for a blob with .NET - Azure Storage
description: Informazioni su come impostare e recuperare le proprietà di sistema e archiviare i metadati personalizzati nei BLOB nell'account di Archiviazione di Azure usando la libreria client .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137663"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Gestire le proprietà e i metadati BLOB con .NETManage blob properties and metadata with .NET

Oltre ai dati che contengono, i BLOB supportano le proprietà di sistema e i metadati definiti dall'utente. Questo articolo illustra come gestire le proprietà di sistema e i metadati definiti dall'utente con la [libreria client di Archiviazione di Azure per .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-properties-and-metadata"></a>Informazioni su proprietà e metadati

- **Proprietà di sistema:** proprietà di sistema in ogni risorsa di archiviazione BLOB. Alcune di esse possono essere lette o impostate, mentre altre sono di sola lettura. Anche se in modo non esplicito, alcune proprietà di sistema corrispondono a specifiche intestazioni HTTP standard. La libreria client di Archiviazione di Azure per .NET gestisce automaticamente queste proprietà.

- **Metadati definiti dall'utente:** i metadati definiti dall'utente sono costituiti da una o più coppie nome-valore specificate per una risorsa di archiviazione BLOB. È possibile usare i metadati per archiviare valori aggiuntivi con la risorsa. I valori dei metadati sono solo per scopi personalizzati e non influiscono sul funzionamento della risorsa.

Il recupero dei metadati e dei valori delle proprietà per una risorsa di archiviazione BLOB è un processo in due passaggi. Prima di poter leggere questi valori, è `FetchAttributes` necessario `FetchAttributesAsync` recuperarli in modo esplicito chiamando il metodo o . L'eccezione a questa `Exists` `ExistsAsync` regola è `FetchAttributes` che i metodi e chiamano sotto copertura il metodo appropriato. Quando si chiama uno di questi metodi, non `FetchAttributes`è necessario chiamare anche .

> [!IMPORTANT]
> Se i valori di proprietà o metadati per una risorsa di `FetchAttributes` archiviazione `FetchAttributesAsync` non sono stati popolati, verificare che il codice chiami il metodo or .

## <a name="set-and-retrieve-properties"></a>Impostare e recuperare le proprietà

Esempio di codice `ContentType` seguente `ContentLanguage` imposta le proprietà di sistema e in un BLOB.

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

Per recuperare le proprietà `FetchAttributes` `FetchAttributesAsync` del BLOB, chiamare `Properties` il metodo o nel BLOB per popolare la proprietà. L'esempio di codice seguente ottiene le proprietà di sistema di un BLOB e visualizza alcuni dei valori:The following code example gets a blob's system properties and displays some of the values:

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

È possibile specificare i metadati come uno o più coppie nome-valore in una risorsa BLOB o contenitore. Per impostare i metadati, `Metadata` aggiungere coppie nome-valore alla raccolta nella risorsa. Chiamare quindi uno dei seguenti metodi per scrivere i valori:

- [SetMetadata (Metadati)](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Le coppie nome/valore dei metadati sono intestazioni HTTP valide e devono rispettare tutte le restrizioni che regolano le intestazioni HTTP. I nomi dei metadati devono essere nomi di intestazione HTTP validi e identificatori validi di C, possono contenere solo caratteri ASCII e devono essere considerati senza distinzione tra maiuscole e minuscole. Valori dei metadati basati [su base 64](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) o [codifica URL](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) contenenti caratteri non ASCII.

Il nome dei metadati deve essere conforme alle convenzioni di denominazione degli identificatori C#. I nomi dei metadati mantengono la distinzione tra maiuscole e minuscole al momento della creazione, ma non fanno distinzione tra maiuscole e minuscole quando vengono impostati o letti. Se due o più intestazioni di metadati che usano lo stesso nome vengono inviate per una risorsa, Archiviazione BLOB di Azure restituisce il codice di errore HTTP 400 (richiesta non valida).

Esempio di codice seguente imposta i metadati in un BLOB. Un valore viene impostato utilizzando `Add` il metodo della raccolta. L'altro valore è impostato utilizzando la sintassi implicita chiave/valore.

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

Per recuperare i `FetchAttributes` metadati, chiamare il metodo `FetchAttributesAsync` `Metadata` o nel BLOB o nel contenitore per popolare la raccolta, quindi leggere i valori, come illustrato nell'esempio seguente.

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

## <a name="see-also"></a>Vedere anche

- [Operazione Imposta proprietà BLOB](/rest/api/storageservices/set-blob-properties)
- [Operazione Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Impostare l'operazione dei metadati BLOBSet Blob Metadata operation](/rest/api/storageservices/set-blob-metadata)
- [Ottenere l'operazione di metadati BLOBGet Blob Metadata operation](/rest/api/storageservices/get-blob-metadata)
