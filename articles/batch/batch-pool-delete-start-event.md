---
title: Evento di avvio eliminazione pool di Azure Batch | Microsoft Docs
description: "Riferimento per l’evento di avvio eliminazione del pool di batch."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: f8a5241dce422e5c826ab428da6d7bc93284a1cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="pool-delete-start-event"></a>Evento di avvio eliminazione pool

 Questo evento viene generato quando un'operazione di eliminazione pool è stata avviata. Poiché l'eliminazione pool è un evento asincrono, è possibile prevedere l'emissione di un evento di completamento eliminazione pool una volta completata l'operazione di eliminazione.

 L'esempio seguente illustra il corpo di un evento di avvio eliminazione pool.

```
{
    "id": "myPool1"
}
```

|Elemento|Tipo|Note|
|-------------|----------|-----------|
|id|String|ID del pool.|