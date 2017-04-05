---
title: Uso dell&quot;API REST del servizio Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su dove trovare le risorse per usare l&quot;API REST del servizio Importazione/Esportazione di Azure, compresi i materiali di riferimento e i materiali relativi alle procedure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e4f5ca289f4bd87574e448d37a1154b222f221f5
ms.lasthandoff: 03/30/2017


---
# <a name="using-the-azure-importexport-service-rest-api"></a>Uso dell'API REST del servizio Importazione/Esportazione di Azure

Il servizio Importazione/Esportazione di Microsoft Azure espone un'API REST per abilitare il controllo a livello di programmazione dei processi di importazione/esportazione. È possibile utilizzare l'API REST per eseguire tutte le operazioni di importazione/esportazione che è possibile effettuare con il [portale di Azure](https://portal.azure.com/). Inoltre, è possibile usare l'API REST per eseguire determinate operazioni granulari, ad esempio l'interrogazione sulla percentuale di completamento di un processo, che non sono attualmente disponibili nel portale classico.

Vedere [Uso del servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati ad Archiviazione BLOB](storage-import-export-service.md) per una panoramica del servizio Importazione/Esportazione e un'esercitazione che illustra come usare il portale di Azure per creare e gestire i processi di importazione ed esportazione.

## <a name="service-endpoints"></a>Endpoint di servizio

Il servizio Importazione/Esportazione di Azure è un provider di risorse per Azure Resource Manager e fornisce un set di API REST ai seguenti endpoint HTTPS per la gestione dei processi di importazione/esportazione:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Controllo delle versioni

Le richieste al servizio Importazione/Esportazione devono specificare il parametro `api-version` e impostarne il valore su `2016-11-01`.

## <a name="importexport-service-operations"></a>Operazioni del servizio Importazione/Esportazione

[Creating an import job](storage-import-export-creating-an-import-job.md) (Creazione di un processo di importazione)

[Creating an export job](storage-import-export-creating-an-export-job.md) (Creazione di un processo di esportazione)

[Retrieving state information for a job](storage-import-export-retrieving-state-info-for-a-job.md) (Recupero delle informazioni sullo stato per un processo)

[Enumerating jobs](storage-import-export-enumerating-jobs.md) (Enumerazione dei processi)

[Cancelling and deleting jobs](storage-import-export-cancelling-and-deleting-jobs.md) (Annullamento ed eliminazione dei processi)

[Backup dei manifesti delle unità](storage-import-export-backing-up-drive-manifests.md)

[Diagnostica e ripristino dagli errori per i processi di Importazione/Esportazione](storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Passaggi successivi

* [REST di importazione/esportazione dell'archiviazione](/rest/api/storageimportexport)

