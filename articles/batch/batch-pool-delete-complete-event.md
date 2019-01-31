---
title: Evento di completamento eliminazione pool di Azure Batch | Microsoft Docs
description: Riferimento per l’evento di completamento eliminazione del pool di batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474549"
---
# <a name="pool-delete-complete-event"></a>Evento di completamento eliminazione pool

 Questo evento viene generato quando un'operazione di eliminazione pool è stata completata.

 L'esempio seguente illustra il corpo di un evento di completamento eliminazione pool.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elemento|Tipo|Note|
|-------------|----------|-----------|
|id|string|ID del pool.|
|startTime|DateTime|Data e ora in cui è stata avviata l'eliminazione del pool.|
|endTime|DateTime|Data e ora in cui è stata completata l'eliminazione del pool.|

## <a name="remarks"></a>Osservazioni
Per altre informazioni sugli stati e sui codici di errore per l'operazione di ridimensionamento pool, vedere [Delete a pool from an account](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account) (Eliminare un pool da un account).