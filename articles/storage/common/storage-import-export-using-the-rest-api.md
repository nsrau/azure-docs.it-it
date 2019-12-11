---
title: Uso dell'API REST del servizio Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su dove trovare le risorse per usare l'API REST del servizio Importazione/Esportazione di Azure, compresi i materiali di riferimento e i materiali relativi alle procedure.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978867"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Uso dell'API REST del servizio Importazione/Esportazione di Azure

Il servizio Importazione/Esportazione di Microsoft Azure espone un'API REST per abilitare il controllo a livello di programmazione dei processi di importazione/esportazione. È possibile utilizzare l'API REST per eseguire tutte le operazioni di importazione/esportazione che è possibile effettuare con il [portale di Azure](https://portal.azure.com/). È possibile usare l'API REST anche per eseguire determinate operazioni dettagliate attualmente non disponibili nel portale di Azure, ad esempio eseguire una query sulla percentuale di completamento di un processo.

Per una panoramica del servizio di Importazione/Esportazione e un'esercitazione che illustra come usare il portale per creare e gestire i processi di importazione ed esportazione, vedere [Usare il servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archivio BLOB](../storage-import-export-service.md).

## <a name="service-endpoints"></a>Endpoint di servizio

Il servizio Importazione/Esportazione di Azure è un provider di risorse per Azure Resource Manager e fornisce un set di API REST ai seguenti endpoint HTTPS per la gestione dei processi di importazione/esportazione:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Controllo delle versioni

Le richieste al servizio Importazione/Esportazione devono specificare il parametro `api-version` e impostarne il valore su `2016-11-01`.

## <a name="importexport-service-operations"></a>Operazioni del servizio Importazione/Esportazione

[Creazione di un processo di importazione](../storage-import-export-creating-an-import-job.md)

[Creating an export job](../storage-import-export-creating-an-export-job.md) (Creazione di un processo di esportazione)

[Retrieving state information for a job](storage-import-export-retrieving-state-info-for-a-job.md) (Recupero delle informazioni sullo stato per un processo)

[Enumerating jobs](../storage-import-export-enumerating-jobs.md) (Enumerazione dei processi)

[Cancelling and deleting jobs](storage-import-export-cancelling-and-deleting-jobs.md) (Annullamento ed eliminazione dei processi)

[Backup dei manifesti delle unità](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostica e ripristino dagli errori per i processi di Importazione/Esportazione](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Passaggi successivi

* [REST di importazione/esportazione dell'archiviazione](/rest/api/storageimportexport)
