---
title: Formato di file dei metadati e delle proprietà del servizio Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come specificare i metadati e le proprietà per uno o più BLOB nell'ambito di un processo di importazione o esportazione.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 5a886244b43ad006a95e9be0350d9c69fd987ad9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526233"
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
  
|Elemento XML|type|DESCRIZIONE|  
|-----------------|----------|-----------------|  
|`Metadata`|Elemento radice|L'elemento radice del file di metadati.|  
|`metadata-name`|string|facoltativo. L'elemento XML specifica il nome dei metadati per il BLOB e il relativo valore specifica il valore dell'impostazione dei metadati.|  
  
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
  
|Elemento XML|type|DESCRIZIONE|  
|-----------------|----------|-----------------|  
|`Properties`|Elemento radice|L'elemento radice del file delle proprietà.|  
|`Last-Modified`|string|facoltativo. L'ora dell'ultima modifica per il BLOB. Solo per i processi di esportazione.|  
|`Etag`|string|facoltativo. Il valore ETag del BLOB. Solo per i processi di esportazione.|  
|`Content-Length`|string|facoltativo. La dimensione del BLOB in byte. Solo per i processi di esportazione.|  
|`Content-Type`|string|facoltativo. Il tipo di contenuto del BLOB.|  
|`Content-MD5`|string|facoltativo. L'hash MD5 del BLOB.|  
|`Content-Encoding`|string|facoltativo. La codifica del contenuto del BLOB.|  
|`Content-Language`|string|facoltativo. La lingua del contenuto del BLOB.|  
|`Cache-Control`|string|facoltativo. La stringa di controllo della cache per il BLOB.|  

## <a name="next-steps"></a>Passaggi successivi

Per le regole dettagliate sull'impostazione dei metadati e delle proprietà di un BLOB, vedere [Set blob properties](/rest/api/storageservices/set-blob-properties) (Impostare le proprietà di un BLOB), [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) (Impostare i metadati di un BLOB) e [Setting and retrieving properties and metadata for blob resources](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) (Impostazione e recupero di proprietà e metadati per le risorse BLOB).
