---
title: Ottenere il tipo di account di archiviazione e il nome dello SKU con .NET
titleSuffix: Azure Storage
description: Informazioni su come ottenere il tipo di account di archiviazione di Azure e il nome dello SKU usando la libreria client .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fd693573858df095b62a7a7917563141ac19c5b
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579335"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Ottenere il tipo di account di archiviazione e il nome dello SKU con .NET

Questo articolo illustra come ottenere il tipo di account di archiviazione di Azure e il nome dello SKU per un BLOB usando la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage).

## <a name="about-account-type-and-sku-name"></a>Informazioni sul tipo di account e il nome dello SKU

**Tipo di account** : i tipi di conto validi sono `BlobStorage` ,, `BlockBlobStorage` `FileStorage` , `Storage` e `StorageV2` . [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md) contiene altre informazioni, incluse le descrizioni dei vari account di archiviazione.

**Nome SKU** : i nomi di SKU validi includono,,, `Premium_LRS` `Premium_ZRS` `Standard_GRS` `Standard_GZRS` , `Standard_LRS` , `Standard_RAGRS` , `Standard_RAGZRS` e `Standard_ZRS` . I nomi degli SKU fanno distinzione tra maiuscole e minuscole e sono campi stringa nella [classe SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname).

## <a name="retrieve-account-information"></a>Recuperare le informazioni sull'account

Nell'esempio di codice seguente vengono recuperate e visualizzate le proprietà dell'account di sola lettura.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Per ottenere il tipo di account di archiviazione e il nome dello SKU associato a un BLOB, chiamare il metodo [GetAccountInfo](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) o [GetAccountInfoAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Account.cs" id="Snippet_GetAccountInfo":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Per ottenere il tipo di account di archiviazione e il nome dello SKU associato a un BLOB, chiamare il metodo [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) o [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) .

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
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

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre operazioni che è possibile eseguire in un account di archiviazione tramite il [portale di Azure](https://portal.azure.com) e l'API REST di Azure.

- [Operazione Get account information (REST)](/rest/api/storageservices/get-account-information)
