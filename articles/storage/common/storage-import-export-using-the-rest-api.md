---
title: Uso dell'API REST del servizio Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su dove trovare le risorse per usare l'API REST del servizio Importazione/Esportazione di Azure, compresi i materiali di riferimento e i materiali relativi alle procedure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 3775a77a6dfc590e79e785e1604226c1187952de
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528409"
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

[Creating an import job](../storage-import-export-creating-an-import-job.md) (Creazione di un processo di importazione)

[Creazione di un processo di esportazione](../storage-import-export-creating-an-export-job.md)

[Retrieving state information for a job](storage-import-export-retrieving-state-info-for-a-job.md) (Recupero delle informazioni sullo stato per un processo)

[Enumerazione dei processi](../storage-import-export-enumerating-jobs.md)

[Cancelling and deleting jobs](storage-import-export-cancelling-and-deleting-jobs.md) (Annullamento ed eliminazione dei processi)

[Backup dei manifesti delle unità](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostica e ripristino dagli errori per i processi di Importazione/Esportazione](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Passaggi successivi

* [REST di importazione/esportazione dell'archiviazione](/rest/api/storageimportexport)
