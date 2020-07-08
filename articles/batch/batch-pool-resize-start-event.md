---
title: Evento di avvio ridimensionamento pool di Azure Batch
description: Riferimento per l’evento di avvio ridimensionamento del pool di batch. L'esempio mostra il corpo di un evento di avvio ridimensionamento pool per un pool ridimensionato da 0 a 2 nodi in modo manuale.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 63576b04b06aad024211d0a50225907c88c138ce
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723732"
---
# <a name="pool-resize-start-event"></a>Evento di avvio ridimensionamento pool

 Questo evento viene generato quando un ridimensionamento pool è stato avviato. Poiché il ridimensionamento pool è un evento asincrono, è possibile prevedere l'emissione di un evento di completamento ridimensionamento pool una volta completata l'operazione di ridimensionamento.

 L'esempio seguente mostra il corpo di un evento di avvio ridimensionamento pool per un pool che ridimensiona da 0 a 2 nodi con un ridimensionamento manuale.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elemento|Type|Note|
|-------------|----------|-----------|
|`id`|string|ID del pool.|
|`nodeDeallocationOption`|string|Specifica quando è possibile rimuovere nodi dal pool, in caso di riduzione delle dimensioni del pool.<br /><br /> I valori possibili sono:<br /><br /> **requeue**: termina le attività in esecuzione e le reinserisce nella coda. Le attività verranno eseguite di nuovo quando il processo viene abilitato. I nodi vengono rimossi non appena le attività sono state terminate.<br /><br /> **terminate**: termina le attività in esecuzione. Le attività non verranno più eseguite. I nodi vengono rimossi non appena le attività sono state terminate.<br /><br /> **taskcompletion**: consente il completamento delle attività attualmente in esecuzione. Non viene pianificata alcuna nuova attività durante l'attesa. I nodi vengono rimossi al completamento di tutte le attività.<br /><br /> **Retaineddata**: consente il completamento delle attività attualmente in esecuzione e quindi attende che scadano tutti i periodi di conservazione dei dati delle attività. Non viene pianificata alcuna nuova attività durante l'attesa. I nodi vengono rimossi alla scadenza di tutti i periodi di conservazione dati delle attività.<br /><br /> Il valore predefinito è requeue.<br /><br /> In caso di aumento delle dimensioni del pool, il valore è impostato su **invalid**.|
|`currentDedicatedNodes`|Int32|Numero di nodi di calcolo attualmente assegnati al pool.|
|`targetDedicatedNodes`|Int32|Numero di nodi di calcolo richiesti per il pool.|
|`currentLowPriorityNodes`|Int32|Numero di nodi di calcolo attualmente assegnati al pool.|
|`targetLowPriorityNodes`|Int32|Numero di nodi di calcolo richiesti per il pool.|
|`enableAutoScale`|Bool|Specifica se le dimensioni del pool vengono regolate automaticamente nel tempo.|
|`isAutoPool`|Bool|Specifica se il pool è stato creato tramite il meccanismo di pool automatico di un processo.|
