---
title: Ottenere il tipo di account di archiviazione e il nome dello SKU con .NET-archiviazione di Azure
description: Informazioni su come ottenere il tipo di account di archiviazione di Azure e il nome dello SKU usando la libreria client .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 21ecaae679ad4a5f21107ef53d3899cf03593c9d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829203"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Ottenere il tipo di account di archiviazione e il nome dello SKU con .NET

Questo articolo illustra come ottenere il tipo di account di archiviazione di Azure e il nome dello SKU per un BLOB usando la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage/client).

Le informazioni sull'account sono disponibili nelle versioni del servizio a partire dalla versione 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Informazioni sul tipo di account e il nome dello SKU

**Tipo di account**: I tipi di conto `BlobStorage`validi `BlockBlobStorage`sono `FileStorage`, `Storage`,, `StorageV2`e. [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md) contiene altre informazioni, incluse le descrizioni dei vari account di archiviazione.

**Nome SKU**: I nomi di SKU `Premium_LRS`validi `Premium_ZRS`includono `Standard_GRS`, `Standard_GZRS`, `Standard_LRS`, `Standard_RAGRS`,,, `Standard_RAGZRS`e .`Standard_ZRS` I nomi degli SKU fanno distinzione tra maiuscole e minuscole e sono campi stringa nella [classe SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

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

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre operazioni che è possibile eseguire in un account di archiviazione tramite il [portale di Azure](https://portal.azure.com) e l'API REST di Azure.

- [Gestire un account di archiviazione](storage-account-manage.md)
- [Aggiornare un account di archiviazione](storage-account-upgrade.md)
- [Operazione Get account information (REST)](/rest/api/storageservices/get-account-information)
