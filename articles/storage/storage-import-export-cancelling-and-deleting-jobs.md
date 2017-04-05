---
title: Annullare ed eliminare un processo di Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come annullare ed eliminare i processi per il servizio Importazione/Esportazione di Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e0a7ff391e5a03ed563912dea54c7cfe73111bcf
ms.lasthandoff: 03/30/2017


---

# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Annullamento ed eliminazione dei processi di Importazione/Esportazione di Azure

È possibile richiedere che un processo venga annullato prima che entri nello stato `Packaging` chiamando l'operazione [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) e impostando l'elemento `CancelRequested` su `true`. Il processo verrà annullato nel modo più efficiente possibile. Se nelle unità è in corso il trasferimento dei dati, dati possono continuare a essere trasferiti anche dopo che è stato richiesto l'annullamento.

 Un processo annullato passa nello stato `Completed` e vi rimane per 90 giorni, dopo i quali verrà eliminato.

 Per eliminare un processo, chiamare l'operazione [Elimina processo](/rest/api/storageimportexport/jobs#Jobs_Delete) prima che questo venga inviato (*ad esempio*, mentre il processo si trova nello stato `Creating`). È possibile eliminare un processo anche quando è nello stato `Completed`. Dopo che un processo è stato eliminato, il relativo stato e le relative informazioni non saranno più accessibili tramite l'API REST o il portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)

