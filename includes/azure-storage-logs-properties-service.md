---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553411"
---
| Proprietà | Descrizione |
|:--- |:---|
|**accountName** | Nome dell'account di archiviazione. Ad esempio: `mystorageaccount`.  |
|**requestUrl** | URL richiesto. |
|**userAgentHeader** | Valore dell'**intestazione User-Agent** tra virgolette. Ad esempio: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Valore dell'intestazione **Referrer**. Ad esempio: `http://contoso.com/about.html`.|
|**clientRequestId** | Valore dell'intestazione **x-ms-client-request-id** della richiesta. Ad esempio: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**etag** | Identificatore ETag per l'oggetto restituito, tra virgolette. Ad esempio: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Tempo totale, espresso in millisecondi, per eseguire l'operazione richiesta. Questo valore non include la latenza di rete (il tempo per leggere la richiesta in arrivo e inviare la risposta al richiedente). Ad esempio: `22`. |
|**serviceType** | Servizio associato alla richiesta. Ad esempio: `blob`, `table`, `files` o `queue`. |
|**operationCount** | Numero di ogni operazione registrate coinvolta nella richiesta. Questo conteggio inizia con un indice di `0`. Alcune richieste richiedono più di un'operazione. La maggior parte delle richieste esegue una sola operazione. Ad esempio: `1`. |
|**requestHeaderSize** | Dimensioni dell'intestazione della richiesta espresse in byte. Ad esempio: `578`. <br>Se una richiesta ha esito negativo, questo valore potrebbe essere vuoto. |
|**requestBodySize** | Dimensioni dei pacchetti della richiesta, espressi in byte, che vengono letti dal servizio di archiviazione. <br> Ad esempio: `0`. <br>Se una richiesta ha esito negativo, questo valore potrebbe essere vuoto.  |
|**responseHeaderSize** | Dimensioni dell'intestazione della risposta espresse in byte. Ad esempio: `216`. <br>Se una richiesta ha esito negativo, questo valore potrebbe essere vuoto.  |
|**responseBodySize** | Dimensioni dei pacchetti della risposta scritti dal servizio di archiviazione, in byte. Se una richiesta ha esito negativo, questo valore può essere vuoto. Ad esempio: `216`.  |
|**requestMd5** | Valore dell'intestazione **Content-MD5** o **x-ms-content-md5** nella richiesta. Il valore hash MD5 specificato in questo campo rappresenta il contenuto nella richiesta. Ad esempio: `788815fd0198be0d275ad329cafd1830`. <br>Questo campo può essere vuoto.  |
|**serverMd5** | Valore hash MD5 calcolato dal servizio di archiviazione. Ad esempio: `3228b3cf1069a5489b298446321f8521`. <br>Questo campo può essere vuoto.  |
|**lastModifiedTime** | Ora dell'ultima modifica per l'oggetto restituito.  Ad esempio: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Questo campo è vuoto per le operazioni che possono restituire più oggetti. |
|**conditionsUsed** | Elenco di coppie chiave-valore separate da punto e virgola che rappresentano una condizione. Le condizioni possono essere uno dei valori seguenti: <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> Ad esempio: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Valore dell'intestazione Content-Length per la richiesta inviata al servizio di archiviazione. Se la richiesta ha avuto esito positivo, questo valore è uguale a requestBodySize. Se una richiesta ha avuto esito negativo, questo valore potrebbe essere diverso da requestBodySize oppure vuoto. |
|**tlsVersion** | Versione di TLS usata nella connessione della richiesta. Ad esempio: `TLS 1.2`. |
|**smbTreeConnectID** | SMB (Server Message Block) **treeConnectId** stabilito al momento della connessione ad albero. Ad esempio: `0x3` |
|**smbPersistentHandleID** | ID handle permanente da una richiesta SMB2 CREATE che sopravvive alle riconnessioni di rete,  a cui viene fatto riferimento in [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 come **SMB2_FILEID.Persistent**. Ad esempio: `0x6003f` |
|**smbVolatileHandleID** | ID handle volatile da una richiesta SMB2 CREATE che viene riciclato nelle riconnessioni di rete,  a cui viene fatto riferimento in [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 come **SMB2_FILEID.Volatile**. Ad esempio: `0xFFFFFFFF00000065` |
|**smbMessageID** | **MessageId** relativo alla connessione. Ad esempio: `0x3b165` |
|**smbCreditsConsumed** | Ingresso o uscita utilizzati dalla richiesta, in unità di 64 K. Ad esempio: `0x3` |
|**smbCommandDetail** | Altre informazioni su questa richiesta specifica anziché sul tipo generale di richiesta. Ad esempio: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | **FileId** associato al file o alla directory.  Approssimativamente analogo a NTFS FileId. Ad esempio: `0x9223442405598953` |
|**smbSessionID** | **SessionId** SMB2 stabilito al momento della configurazione della sessione. Ad esempio: `0x8530280128000049` |
|**smbCommandMajor  uint32** | Valore in **SMB2_HEADER.Command**. Attualmente, si tratta di un numero compreso tra 0 e 18 inclusi. Ad esempio: `0x6` |
|**smbCommandMinor** | Sottoclasse di **SmbCommandMajor**, laddove appropriato. Ad esempio: `DirectoryCloseAndDelete` |