---
title: Elencare tutti i processi di Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come elencare tutti i processi del servizio Importazione/Esportazione di Azure in una sottoscrizione.
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
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.lasthandoff: 03/30/2017


---

# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Enumerazione dei processi nel servizio Importazione/Esportazione di Azure
Per enumerare tutti i processi in una sottoscrizione, chiamare l'operazione [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` restituisce un elenco dei processi nonché gli attributi seguenti:

-   Tipo di processo (importazione o esportazione)

-   Stato corrente del processo

-   Account di archiviazione associato al processo

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)

