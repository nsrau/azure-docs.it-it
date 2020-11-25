---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011184"
---
| Proprietà | Descrizione |
|:--- |:---|
|**time** | Ora UTC (Universal Time Coordinated) in cui la richiesta è stata ricevuta dalla risorsa di archiviazione. Ad esempio: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | ID risorsa dell'account di archiviazione. Ad esempio: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | Categoria dell'operazione richiesta. Ad esempio: `StorageRead`, `StorageWrite` o `StorageDelete`.|
|**operationName** | Tipo di operazione REST eseguita. <br> Per un elenco completo delle operazioni, vedere l'argomento [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Versione del servizio di archiviazione specificata quando è stata effettuata la richiesta. Equivale al valore dell'intestazione **x-ms-version**. Ad esempio: `2017-04-17`.|
|**schemaVersion** | Versione dello schema del log. Ad esempio: `1.0`.|
|**statusCode** | Codice di stato HTTL della richiesta. Se la richiesta viene interrotta, questo valore potrebbe essere impostato su `Unknown`. <br> Ad esempio: `206` |
|**statusText** | Stato dell'operazione richiesta.  Per un elenco completo dei messaggi di stato, vedere l'argomento [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Nella versione 2017-04-17 e successive il messaggio di stato `ClientOtherError` non viene usato. Questo campo contiene invece un codice di errore. Ad esempio: `SASSuccess`  |
|**durationMs** | Tempo totale, espresso in millisecondi, per eseguire l'operazione richiesta. Questo valore include il tempo per leggere la richiesta in arrivo e inviare la risposta al richiedente. Ad esempio: `12`.|
|**callerIpAddress** | Indirizzo IP del richiedente, incluso il numero di porta. Ad esempio: `192.100.0.102:4362`. |
|**correlationId** | ID usato per correlare i log tra le risorse. Ad esempio: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**location** | Posizione dell'account di archiviazione. Ad esempio: `North Europe`. |
|**protocol**|Protocollo usato nell'operazione. Ad esempio: `HTTP`, `HTTPS`, `SMB` o `NFS`|
| **Uri** | URI (Uniform Resource Identifier) richiesto. |