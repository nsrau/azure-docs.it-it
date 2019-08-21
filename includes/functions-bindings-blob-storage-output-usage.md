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
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642097"
---
Per scrivere i BLOB, è possibile associare i tipi seguenti:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> richiede l'associazione "in" `direction` in *function.json* o `FileAccess.Read` in una libreria di classi C#. Tuttavia, è possibile usare l'oggetto contenitore che il runtime fornisce per scrivere operazioni, come ad esempio il caricamento di BLOB nel contenitore.

<sup>2</sup> richiede l'associazione "inout" `direction` in *function.json* o `FileAccess.ReadWrite` in una libreria di classi C#.

Se si prova a eseguire l'associazione a uno dei tipi di Storage SDK e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](#azure-storage-sdk-version-in-functions-1x).

Nelle funzioni asincrone usare il valore restituito o `IAsyncCollector` anziché un parametro `out`.

L'associazione a `string` o `Byte[]` è consigliabile solo se le dimensioni del BLOB sono ridotte, in quanto l'intero contenuto del BLOB viene caricato in memoria. In genere, è preferibile usare un tipo `Stream` o `CloudBlockBlob`. Per altre informazioni, vedere [Concorrenza e utilizzo della memoria](#trigger---concurrency-and-memory-usage) più indietro in questo articolo.