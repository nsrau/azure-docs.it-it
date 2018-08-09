---
title: Elencare tutti i processi di Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come elencare tutti i processi del servizio Importazione/Esportazione di Azure in una sottoscrizione.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 69daac71b69969a7ad9acfeb7095053f8138bf53
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520881"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Enumerazione dei processi nel servizio Importazione/Esportazione di Azure
Per enumerare tutti i processi in una sottoscrizione, chiamare l'operazione [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` restituisce un elenco dei processi nonché gli attributi seguenti:

-   Tipo di processo (importazione o esportazione)

-   Stato corrente del processo

-   Account di archiviazione associato al processo

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)
