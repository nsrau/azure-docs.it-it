---
title: Evento di completamento eliminazione pool di Azure Batch | Microsoft Docs
description: Riferimento per l’evento di completamento eliminazione del pool di batch.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 3ad48fdba298a10d94a32e31e432c0c88b35b658
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094830"
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
|id|String|ID del pool.|
|startTime|DateTime|Data e ora in cui è stata avviata l'eliminazione del pool.|
|endTime|DateTime|Data e ora in cui è stata completata l'eliminazione del pool.|

## <a name="remarks"></a>Note
Per altre informazioni sugli stati e sui codici di errore per l'operazione di ridimensionamento pool, vedere [Delete a pool from an account](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account) (Eliminare un pool da un account).