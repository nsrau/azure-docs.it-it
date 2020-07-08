---
title: Evento di completamento eliminazione del pool di Azure Batch
description: Riferimento per l’evento di completamento eliminazione del pool di batch. Questo evento viene generato quando un'operazione di eliminazione pool è stata completata.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: f00d32fd5e4eb49fddf4975a9b64ce792d0226dd
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962459"
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

|Elemento|Type|Note|
|-------------|----------|-----------|
|`id`|string|ID del pool.|
|`startTime`|Datetime|Data e ora in cui è stata avviata l'eliminazione del pool.|
|`endTime`|Datetime|Data e ora in cui è stata completata l'eliminazione del pool.|

## <a name="remarks"></a>Osservazioni
Per altre informazioni sugli stati e sui codici di errore per l'operazione di ridimensionamento pool, vedere [Delete a pool from an account](/rest/api/batchservice/delete-a-pool-from-an-account) (Eliminare un pool da un account).
