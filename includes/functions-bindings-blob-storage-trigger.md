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
ms.openlocfilehash: 557e34e8a6bddd36a92b0e212bda3609baa14407
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642162"
---
È possibile usare i tipi di parametro seguenti per il BLOB di attivazione:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Un oggetto POCO serializzabile come JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> richiede l'associazione "inout" `direction` in *function.json* o `FileAccess.ReadWrite` in una libreria di classi C#.

Se si prova a eseguire l'associazione a uno dei tipi di Storage SDK e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

L'associazione a `string`, `Byte[]` o POCO è consigliabile solo se le dimensioni del BLOB sono ridotte, in quanto l'intero contenuto del BLOB viene caricato in memoria. In genere, è preferibile usare un tipo `Stream` o `CloudBlockBlob`. Per altre informazioni, vedere [Concorrenza e utilizzo della memoria](#trigger---concurrency-and-memory-usage) più avanti in questo articolo.