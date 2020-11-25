---
title: includere file
description: includere file
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: f4e6d5fb41769544b7be0f689447364988d0380d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95998838"
---
Il trigger del BLOB contiene diverse proprietà di metadati. Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Questi valori hanno la stessa semantica del tipo [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet).

|Proprietà  |Type  |Descrizione  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Percorso del BLOB trigger.|
|`Uri`|`System.Uri`|L'URI del BLOB per la posizione primaria.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Le proprietà di sistema del BLOB. |
|`Metadata` |`IDictionary<string,string>`|I metadati definiti dall'utente per il BLOB.|

Ad esempio, gli esempi seguenti di script C# e JavaScript registrano il percorso nel BLOB di attivazione, incluso il contenitore:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```