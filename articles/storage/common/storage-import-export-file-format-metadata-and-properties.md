---
title: "Formato di file dei metadati e delle proprietà del servizio Importazione/Esportazione di Azure | Documentazione Microsoft"
description: "Informazioni su come specificare i metadati e le proprietà per uno o più BLOB nell'ambito di un processo di importazione o esportazione."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3f728ad94cdcbd32092b677f11a737ae91376720
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Formato di file dei metadati e delle proprietà di Importazione/Esportazione di Azure
È possibile specificare i metadati e le proprietà per uno o più BLOB nell'ambito di un processo di importazione o esportazione. Per impostare i metadati o le proprietà per i BLOB che vengono creati nell'ambito di un processo di importazione, occorre fornire un file di metadati o delle proprietà nel disco rigido contenente i dati da importare. In un processo di esportazione, i metadati e le proprietà vengono scritti in un file di metadati o delle proprietà che viene incluso nel disco rigido restituito all'utente.  
  
## <a name="metadata-file-format"></a>Formato del file di metadati  
Il formato del file di metadati è indicato di seguito:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|Elemento XML|Tipo|Descrizione|  
|-----------------|----------|-----------------|  
|`Metadata`|Elemento radice|L'elemento radice del file di metadati.|  
|`metadata-name`|string|Facoltativa. L'elemento XML specifica il nome dei metadati per il BLOB e il relativo valore specifica il valore dell'impostazione dei metadati.|  
  
## <a name="properties-file-format"></a>Formato del file delle proprietà  
Il formato di un file delle proprietà è indicato di seguito:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|Elemento XML|Tipo|Descrizione|  
|-----------------|----------|-----------------|  
|`Properties`|Elemento radice|L'elemento radice del file delle proprietà.|  
|`Last-Modified`|String|Facoltativa. L'ora dell'ultima modifica per il BLOB. Solo per i processi di esportazione.|  
|`Etag`|String|Facoltativa. Il valore ETag del BLOB. Solo per i processi di esportazione.|  
|`Content-Length`|String|Facoltativa. La dimensione del BLOB in byte. Solo per i processi di esportazione.|  
|`Content-Type`|String|Facoltativa. Il tipo di contenuto del BLOB.|  
|`Content-MD5`|String|Facoltativa. L'hash MD5 del BLOB.|  
|`Content-Encoding`|String|Facoltativa. La codifica del contenuto del BLOB.|  
|`Content-Language`|String|Facoltativa. La lingua del contenuto del BLOB.|  
|`Cache-Control`|String|Facoltativa. La stringa di controllo della cache per il BLOB.|  

## <a name="next-steps"></a>Passaggi successivi

Per le regole dettagliate sull'impostazione dei metadati e delle proprietà di un BLOB, vedere [Set blob properties](/rest/api/storageservices/set-blob-properties) (Impostare le proprietà di un BLOB), [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) (Impostare i metadati di un BLOB) e [Setting and retrieving properties and metadata for blob resources](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) (Impostazione e recupero di proprietà e metadati per le risorse BLOB).
