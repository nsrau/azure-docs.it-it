---
title: Ottenere il tipo di account di archiviazione e il nome dello SKU con .NETGet storage account type and SKU name with .NET
titleSuffix: Azure Storage
description: Informazioni su come ottenere il tipo di account di Archiviazione di Azure e il nome SKU usando la libreria client .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137059"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Ottenere il tipo di account di archiviazione e il nome dello SKU con .NETGet storage account type and SKU name with .NET

Questo articolo illustra come ottenere il tipo di account di Archiviazione di Azure e il nome SKU per un BLOB usando la libreria client di [Archiviazione di Azure per .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

Le informazioni sull'account sono disponibili nelle versioni del servizio a partire dalla versione 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Informazioni sul tipo di account e sul nome SKU

**Tipo di account**: `BlobStorage` `BlockBlobStorage`I `FileStorage` `Storage`tipi `StorageV2`di conto validi includono , , , e . [Panoramica dell'account di archiviazione](storage-account-overview.md) di Azure include altre informazioni, incluse le descrizioni dei vari account di archiviazione.

**Nome SKU**: i `Premium_LRS`nomi `Premium_ZRS` `Standard_GRS`di `Standard_GZRS` `Standard_LRS`SKU `Standard_RAGZRS`validi `Standard_ZRS`includono , , , , , `Standard_RAGRS`, e . I nomi degli SKU fanno distinzione tra maiuscole e minuscole e sono campi stringa nella [classe SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Recuperare le informazioni sull'account

Per ottenere il tipo di account di archiviazione e il nome dello SKU associati a un BLOB, chiamare il [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) o [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) metodo.

Nell'esempio di codice riportato di seguito vengono recuperate e visualizzate le proprietà dell'account di sola lettura.

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

Informazioni sulle altre operazioni che è possibile eseguire in un account di archiviazione tramite il portale di Azure e l'API REST di Azure.Learn about other operations you can perform on a storage account through the [Azure portal](https://portal.azure.com) and the Azure REST API.

- [Operazione Get Account Information (REST)](/rest/api/storageservices/get-account-information)
