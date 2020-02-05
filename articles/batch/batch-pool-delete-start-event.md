---
title: Evento di avvio eliminazione pool di Azure Batch
description: Riferimento per l’evento di avvio eliminazione del pool di batch. Questo evento viene generato quando un'operazione di eliminazione pool è stata avviata.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 24a68c6656bd13f0c353d53870a51cdc940fd141
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022206"
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
|`id`|string|ID del pool.|
