---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80151222"
---
## <a name="what-is-queue-storage"></a>Che cos'è l'archiviazione delle code?

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione. L'archiviazione delle code viene spesso utilizzata per creare un backlog del lavoro da elaborare in modo asincrono.

## <a name="queue-service-concepts"></a>Concetti del servizio di accodamento

Il servizio coda di Azure contiene i componenti seguenti:The Azure Queue service contains the following components:

![Componenti del servizio di coda di AzureAzure Queue service components](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Formato URL:** Le code sono indirizzabili utilizzando il`<storage account>`seguente formato URL: http:// .queue.core.windows.net/`<queue>`
  
    L'URL seguente fa riferimento a una delle code nel diagramma:   
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Account di archiviazione:** L'accesso ad Archiviazione di Azure viene eseguito tramite un account di archiviazione. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione](../articles/storage/common/storage-account-overview.md).
* **Coda:** una coda contiene un set di messaggi. Tutti i messaggi devono essere inclusi in una coda. Si noti che il nome della coda deve essere in lettere minuscole. Per altre informazioni, vedere [Denominazione di code e metadati](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Messaggio:** un messaggio, in qualsiasi formato, con dimensioni massime di 64 KB. Il tempo massimo che un messaggio può rimanere nella coda è di 7 giorni. Per la versione 2017-07-29 o successive, la durata massima consentita può essere un numero positivo qualsiasi o -1, a indicare che il messaggio non scade. Se questo parametro viene omesso, la durata predefinita è di sette giorni.

