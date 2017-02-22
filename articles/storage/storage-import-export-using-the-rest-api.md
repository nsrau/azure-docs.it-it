---
title: Uso dell&quot;API REST del servizio Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come usare l&quot;API REST del servizio Importazione/Esportazione di Azure
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
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 675aef8ec0f9344d2b3073021e83856c2fb34b7c


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

## <a name="in-this-section"></a>Contenuto della sezione

[Creazione di un processo di importazione](storage-import-export-creating-an-import-job.md)

[Creazione di un processo di esportazione](storage-import-export-creating-an-export-job.md)

[Recupero delle informazioni sullo stato per un processo](storage-import-export-retrieving-state-info-for-a-job.md)

[Enumerazione dei processi](storage-import-export-enumerating-jobs.md)

[Annullamento ed eliminazione dei processi](storage-import-export-cancelling-and-deleting-jobs.md)

[Backup dei manifesti delle unità](storage-import-export-backing-up-drive-manifests.md)

[Diagnostica e ripristino dagli errori per i processi di Importazione/Esportazione](storage-import-export-diagnostics-and-error-recovery.md)

## <a name="see-also"></a>Vedere anche
 [REST di importazione/esportazione dell'archiviazione](/rest/api/storageimportexport)



<!--HONumber=Dec16_HO3-->


