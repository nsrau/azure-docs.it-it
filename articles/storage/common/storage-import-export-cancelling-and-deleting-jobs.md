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
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 1e989c72fc03697bf6d2e515ff53003703665d1a
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---

# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Annullamento ed eliminazione dei processi di Importazione/Esportazione di Azure

 Per richiedere che un processo venga annullato prima che entri nello stato `Packaging`, chiamare l'operazione [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) (Aggiorna proprietà processo) e impostare l'elemento `CancelRequested` su `true`. Il processo viene annullato nel modo più efficiente possibile. Se nelle unità è in corso il trasferimento dei dati, dati possono continuare a essere trasferiti anche dopo che è stato richiesto l'annullamento.

 Un processo annullato passa nello stato `Completed` e vi rimane per 90 giorni, al termine dei quali verrà eliminato.

 Per eliminare un processo, chiamare l'operazione [Elimina processo](/rest/api/storageimportexport/jobs#Jobs_Delete) prima che questo venga inviato (ad esempio, mentre il processo si trova nello stato `Creating`). È possibile eliminare un processo anche quando è nello stato `Completed`. Dopo che un processo è stato eliminato, il relativo stato e le relative informazioni non saranno più accessibili tramite l'API REST o il portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)

