---
title: Evento di avvio ridimensionamento pool di Azure Batch | Microsoft Docs
description: "Riferimento per l’evento di avvio ridimensionamento del pool di batch."
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
ms.openlocfilehash: 826cd984d26b923ba38562e05a2e75c399be9121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="pool-resize-start-event"></a>Evento di avvio ridimensionamento pool

 Questo evento viene generato quando un ridimensionamento pool è stato avviato. Poiché il ridimensionamento pool è un evento asincrono, è possibile prevedere l'emissione di un evento di completamento ridimensionamento pool una volta completata l'operazione di ridimensionamento.

 L'esempio seguente mostra il corpo di un evento di avvio ridimensionamento pool per un pool che ridimensiona da 0 a 2 nodi con un ridimensionamento manuale.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Note|
|-------------|----------|-----------|
|poolId|string|ID del pool.|
|nodeDeallocationOption|string|Specifica quando è possibile rimuovere nodi dal pool, in caso di riduzione delle dimensioni del pool.<br /><br /> I valori possibili sono:<br /><br /> **requeue**: termina le attività in esecuzione e le reinserisce nella coda. Le attività verranno eseguite di nuovo quando il processo viene abilitato. I nodi vengono rimossi non appena le attività sono state terminate.<br /><br /> **terminate**: termina le attività in esecuzione. Le attività non verranno più eseguite. I nodi vengono rimossi non appena le attività sono state terminate.<br /><br /> **taskcompletion**: consente il completamento delle attività attualmente in esecuzione. Non viene pianificata alcuna nuova attività durante l'attesa. I nodi vengono rimossi al completamento di tutte le attività.<br /><br /> **Retaineddata**: consente il completamento delle attività attualmente in esecuzione e quindi attende che scadano tutti i periodi di conservazione dei dati delle attività. Non viene pianificata alcuna nuova attività durante l'attesa. I nodi vengono rimossi alla scadenza di tutti i periodi di conservazione dati delle attività.<br /><br /> Il valore predefinito è requeue.<br /><br /> In caso di aumento delle dimensioni del pool, il valore è impostato su **invalid**.|
|currentDedicated|Int32|Numero di nodi di calcolo attualmente assegnati al pool.|
|targetDedicated|Int32|Numero di nodi di calcolo richiesti per il pool.|
|enableAutoScale|Booleano|Specifica se le dimensioni del pool vengono regolate automaticamente nel tempo.|
|isAutoPool|Booleano|Specifica se il pool è stato creato tramite il meccanismo di pool automatico di un processo.|
