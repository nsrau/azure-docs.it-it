---
title: Annullare ed eliminare un processo di Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come annullare ed eliminare i processi per il servizio Importazione/Esportazione di Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6eb56413319208feef1b4ab51296fe12a1e0bcf2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700129"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Annullamento ed eliminazione dei processi di Importazione/Esportazione di Azure

 Per richiedere che un processo venga annullato prima che entri nello stato `Packaging`, chiamare l'operazione [Update Job Properties](/rest/api/storageimportexport/jobs) (Aggiorna proprietà processo) e impostare l'elemento `CancelRequested` su `true`. Il processo viene annullato nel modo più efficiente possibile. Se nelle unità è in corso il trasferimento dei dati, dati possono continuare a essere trasferiti anche dopo che è stato richiesto l'annullamento.

 Un processo annullato passa nello stato `Completed` e vi rimane per 90 giorni, al termine dei quali verrà eliminato.

 Per eliminare un processo, chiamare l'operazione [Elimina processo](/rest/api/storageimportexport/jobs) prima che questo venga inviato (ad esempio, mentre il processo si trova nello stato `Creating`). È possibile eliminare un processo anche quando è nello stato `Completed`. Dopo che un processo è stato eliminato, il relativo stato e le relative informazioni non saranno più accessibili tramite l'API REST o il portale di Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)
