---
title: Enumerazione dei processi nel servizio Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come enumerare tutti i processi del servizio Importazione/Esportazione di Azure in una sottoscrizione.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 22e37e26fec913a7638c71b2547c38f5efacd10b
ms.openlocfilehash: 65d8912c4eced92206ee1262f3e3307b3162cbd6


---

# <a name="enumerating-jobs"></a>Enumerazione dei processi
Per enumerare tutti i processi in una sottoscrizione, chiamare l'operazione [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` restituisce un elenco dei processi nonché gli attributi seguenti:

-   Tipo di processo (importazione o esportazione)

-   Stato corrente del processo

-   Account di archiviazione associato al processo

## <a name="see-also"></a>Vedere anche
 [Using the Import/Export service REST API](storage-import-export-using-the-rest-api.md) (Uso dell'API REST del servizio Importazione/Esportazione)



<!--HONumber=Dec16_HO3-->


