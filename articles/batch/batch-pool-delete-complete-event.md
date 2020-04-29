---
title: Evento di completamento eliminazione pool di Azure Batch
description: Riferimento per l’evento di completamento eliminazione del pool di batch. Questo evento viene generato quando un'operazione di eliminazione pool è stata completata.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d28223c79f96f35a6ee11f98e0f09f21d7db4451
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115942"
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
|`id`|Stringa|ID del pool.|
|`startTime`|Datetime|Data e ora in cui è stata avviata l'eliminazione del pool.|
|`endTime`|Datetime|Data e ora in cui è stata completata l'eliminazione del pool.|

## <a name="remarks"></a>Osservazioni
Per altre informazioni sugli stati e sui codici di errore per l'operazione di ridimensionamento pool, vedere [Delete a pool from an account](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account) (Eliminare un pool da un account).
