---
title: Ottenere il tipo di account di archiviazione e il nome dello SKU con .NET
titleSuffix: Azure Storage
description: Informazioni su come ottenere il tipo di account di archiviazione di Azure e il nome dello SKU usando la libreria client .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1cff950f2c420651ffa09ffe76a0d1389da84c10
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457480"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Ottenere il tipo di account di archiviazione e il nome dello SKU con .NET

Questo articolo illustra come ottenere il tipo di account di archiviazione di Azure e il nome dello SKU per un BLOB usando la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage/client).

Le informazioni sull'account sono disponibili nelle versioni del servizio a partire dalla versione 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Informazioni sul tipo di account e il nome dello SKU

**Tipo di account**: i tipi di conto validi includono `BlobStorage`, `BlockBlobStorage`, `FileStorage`, `Storage`e `StorageV2`. [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md) contiene altre informazioni, incluse le descrizioni dei vari account di archiviazione.

**Nome SKU**: i nomi di SKU validi includono `Premium_LRS`, `Premium_ZRS`, `Standard_GRS`, `Standard_GZRS`, `Standard_LRS`, `Standard_RAGRS`, `Standard_RAGZRS`e `Standard_ZRS`. I nomi degli SKU fanno distinzione tra maiuscole e minuscole e sono campi stringa nella [classe SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Recuperare le informazioni sull'account

Per ottenere il tipo di account di archiviazione e il nome dello SKU associato a un BLOB, chiamare il metodo [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) o [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) .

Nell'esempio di codice seguente vengono recuperate e visualizzate le proprietà dell'account di sola lettura.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre operazioni che è possibile eseguire in un account di archiviazione tramite il [portale di Azure](https://portal.azure.com) e l'API REST di Azure.

- [Operazione Get account information (REST)](/rest/api/storageservices/get-account-information)
