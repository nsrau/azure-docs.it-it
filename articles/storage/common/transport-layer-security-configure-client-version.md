---
title: Configurare Transport Layer Security (TLS) per un'applicazione client
titleSuffix: Azure Storage
description: Configurare un'applicazione client per comunicare con archiviazione di Azure usando una versione minima di Transport Layer Security (TLS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001808"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>Configurare Transport Layer Security (TLS) per un'applicazione client

Per motivi di sicurezza, un account di archiviazione di Azure può richiedere che i client usino una versione minima di Transport Layer Security (TLS) per inviare richieste. Le chiamate ad archiviazione di Azure avranno esito negativo se il client usa una versione di TLS inferiore alla versione minima richiesta. Se ad esempio un account di archiviazione richiede TLS 1,2, una richiesta inviata da un client che usa TLS 1,1 avrà esito negativo.

Questo articolo descrive come configurare un'applicazione client per l'uso di una particolare versione di TLS. Per informazioni su come configurare una versione minima richiesta di TLS per un account di archiviazione di Azure, vedere [configurare la versione minima richiesta di Transport Layer Security (TLS) per un account di archiviazione](transport-layer-security-configure-minimum-version.md).

## <a name="configure-the-client-tls-version"></a>Configurare la versione TLS del client

Per consentire a un client di inviare una richiesta con una particolare versione di TLS, il sistema operativo deve supportare tale versione.

Gli esempi seguenti illustrano come impostare la versione TLS del client su 1,2 da PowerShell o .NET. Il .NET Framework usato dal client deve supportare TLS 1,2. Per ulteriori informazioni, vedere [supporto per TLS 1,2](/dotnet/framework/network-programming/tls#support-for-tls-12).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

L'esempio seguente illustra come abilitare TLS 1,2 in un client di PowerShell:

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

L'esempio seguente illustra come abilitare TLS 1,2 in un client .NET usando la versione 12 della libreria client di archiviazione di Azure:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

L'esempio seguente illustra come abilitare TLS 1,2 in un client .NET usando la versione 11 della libreria client di archiviazione di Azure:

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>Verificare la versione di TLS utilizzata da un client

Per verificare che la versione specificata di TLS sia stata usata dal client per inviare una richiesta, è possibile usare [Fiddler](https://www.telerik.com/fiddler) o uno strumento simile. Aprire Fiddler per avviare l'acquisizione del traffico di rete del client, quindi eseguire uno degli esempi descritti nella sezione precedente. Esaminare la traccia di Fiddler per verificare che sia stata usata la versione corretta di TLS per inviare la richiesta, come illustrato nella figura seguente.

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="Screenshot che mostra la traccia Fiddler che indica la versione di TLS usata su richiesta":::

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la versione minima richiesta di Transport Layer Security (TLS) per un account di archiviazione](transport-layer-security-configure-minimum-version.md)
- [Raccomandazioni sulla sicurezza per archiviazione BLOB](../blobs/security-recommendations.md)
