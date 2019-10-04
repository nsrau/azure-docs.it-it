---
title: Evento di avvio eliminazione pool di Azure Batch | Microsoft Docs
description: Riferimento per l’evento di avvio eliminazione del pool di batch.
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
ms.openlocfilehash: 76f3a15d88166160f28461c34ee03ff7c8880a59
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258424"
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
|`id`|String|ID del pool.|
