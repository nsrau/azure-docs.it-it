---
title: Informazioni di riferimento rapido sui comandi per i processi di importazione dello strumento di importazione/esportazione di Azure | Documentazione Microsoft
description: Informazioni di riferimento sui comandi dello strumento di importazione/esportazione di Azure di uso frequente per i processi di importazione
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 41bc5585f3d9dea2a08dc8a6bc1e4fdf9f0c8fc4
ms.openlocfilehash: 79b1d3f92671638ba43bac2f5428a1f903d11080


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Informazioni di riferimento rapido sui comandi di uso frequente per i processi di importazione

Questo articolo contiene informazioni di riferimento rapido su alcuni comandi di uso frequente. Per informazioni dettagliate sull'utilizzo, vedere l'articolo relativo alla [preparazione dei dischi rigidi per un processo di importazione](storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="import-job-quick-reference"></a>Informazioni di riferimento rapido sul processo di importazione

Per la prima sessione:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Seconda sessione:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

Interrompere l'ultima sessione:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

Riprendere l'ultima sessione interrotta:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

Aggiungere unità all'ultima sessione:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Passaggi successivi

[Sample workflow to prepare hard drives for an import job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) (Flusso di lavoro campione per preparare i dischi rigidi per un processo di importazione)



<!--HONumber=Dec16_HO3-->


