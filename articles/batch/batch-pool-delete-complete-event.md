---
title: Evento di completamento eliminazione pool di Azure Batch | Microsoft Docs
description: Riferimento per l’evento di completamento eliminazione del pool di batch.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: bcbfe009e2880c0155a067305cc28317678cbfa6
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026700"
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
|`id`|string|ID del pool.|
|`startTime`|Data e ora|Data e ora in cui è stata avviata l'eliminazione del pool.|
|`endTime`|Data e ora|Data e ora in cui è stata completata l'eliminazione del pool.|

## <a name="remarks"></a>Osservazioni
Per altre informazioni sugli stati e sui codici di errore per l'operazione di ridimensionamento pool, vedere [Delete a pool from an account](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account) (Eliminare un pool da un account).
