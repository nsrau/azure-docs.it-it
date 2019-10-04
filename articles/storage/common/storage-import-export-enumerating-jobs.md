---
title: Elencare tutti i processi di Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come elencare tutti i processi del servizio Importazione/Esportazione di Azure in una sottoscrizione.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478692"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Enumerazione dei processi nel servizio Importazione/Esportazione di Azure
Per enumerare tutti i processi in una sottoscrizione, chiamare l'operazione [List Jobs](/rest/api/storageimportexport/jobs). `List Jobs` restituisce un elenco dei processi nonché gli attributi seguenti:

-   Tipo di processo (importazione o esportazione)

-   Stato corrente del processo

-   Account di archiviazione associato al processo

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)
