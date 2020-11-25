---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 08/21/2020
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 45df30f4f1444b6148af9f3c7d47b94909ccef3d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027178"
---
## <a name="what-is-queue-storage"></a>Che cos'è l'archiviazione code?

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione. L'archiviazione code viene spesso utilizzata per creare un backlog di lavoro da elaborare in modo asincrono.

## <a name="queue-service-concepts"></a>Concetti del servizio di accodamento

Il Servizio di accodamento di Azure contiene i componenti seguenti:

![Componenti di Azure Servizio di accodamento](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Account di archiviazione:** Tutti gli accessi ad archiviazione di Azure vengono eseguiti tramite un account di archiviazione. Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione](../articles/storage/common/storage-account-overview.md).
* **Coda:** una coda contiene un set di messaggi. Tutti i messaggi devono essere inclusi in una coda. Si noti che il nome della coda deve essere in lettere minuscole. Per altre informazioni, vedere [Denominazione di code e metadati](/rest/api/storageservices/Naming-Queues-and-Metadata).
* **Messaggio:** un messaggio, in qualsiasi formato, con dimensioni massime di 64 KB. Il tempo massimo che un messaggio può rimanere nella coda è di 7 giorni. Per la versione 2017-07-29 o successive, la durata massima consentita può essere un numero positivo qualsiasi o -1, a indicare che il messaggio non scade. Se questo parametro viene omesso, la durata predefinita è di sette giorni.
* **Formato URL:** Le code sono indirizzabili usando il formato di URL seguente: http:// `<storage account>` . Queue.Core.Windows.NET/`<queue>`

    L'URL seguente fa riferimento a una delle code nel diagramma: 

    `http://myaccount.queue.core.windows.net/incoming-orders`