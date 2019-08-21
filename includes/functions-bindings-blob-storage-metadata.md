---
title: File di inclusione
description: File di inclusione
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642136"
---
Il trigger del BLOB contiene diverse proprietà di metadati. Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Questi valori hanno la stessa semantica del tipo [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet).

|Proprietà  |Type  |Descrizione  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Percorso del BLOB trigger.|
|`Uri`|`System.Uri`|L'URI del BLOB per la posizione primaria.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Le proprietà di sistema del BLOB. |
|`Metadata` |`IDictionary<string,string>`|I metadati definiti dall'utente per il BLOB.|

Ad esempio, gli esempi seguenti di script C# e JavaScript registrano il percorso nel BLOB di attivazione, incluso il contenitore:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
