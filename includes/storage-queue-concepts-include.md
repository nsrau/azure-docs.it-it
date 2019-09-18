---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 935a6201e24e97deaa0a1a65e499310f74a49e2d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059054"
---
## <a name="what-is-queue-storage"></a>Che cos'è l'archiviazione code?

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione. L'archiviazione code viene spesso utilizzata per creare un backlog di lavoro da elaborare in modo asincrono.

## <a name="queue-service-concepts"></a>Concetti del servizio di accodamento

Il Servizio di accodamento di Azure contiene i componenti seguenti:

![Queue1](./media/storage-queue-concepts-include/queue1.png)

* **Formato URL:** è possibile fare riferimento alle code usando il formato URL seguente:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    L'URL seguente fa riferimento a una delle code nel diagramma:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../articles/storage/common/storage-scalability-targets.md) .
* **Coda:** una coda contiene un set di messaggi. Tutti i messaggi devono essere inclusi in una coda. Si noti che il nome della coda deve essere in lettere minuscole. Per altre informazioni, vedere [Denominazione di code e metadati](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Messaggio:** un messaggio, in qualsiasi formato, con dimensioni massime di 64 kB. Il tempo massimo che un messaggio può rimanere nella coda è di 7 giorni.

