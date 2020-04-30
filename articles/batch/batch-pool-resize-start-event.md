---
title: Evento di avvio ridimensionamento pool Azure Batch
description: Riferimento per l’evento di avvio ridimensionamento del pool di batch. Esempio mostra il corpo di un evento di avvio ridimensionamento pool per un pool che ridimensiona da 0 a 2 nodi con ridimensionamento manuale.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: b4aa503c5dfbe00a77216277bdaf7e4c0dc3b1bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115874"
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
|`id`|Stringa|ID del pool.|
|`nodeDeallocationOption`|Stringa|Specifica quando è possibile rimuovere nodi dal pool, in caso di riduzione delle dimensioni del pool.<br /><br /> I valori possibili sono:<br /><br /> **requeue**: termina le attività in esecuzione e le reinserisce nella coda. Le attività verranno eseguite di nuovo quando il processo viene abilitato. I nodi vengono rimossi non appena le attività sono state terminate.<br /><br /> **terminate**: termina le attività in esecuzione. Le attività non verranno più eseguite. I nodi vengono rimossi non appena le attività sono state terminate.<br /><br /> **taskcompletion**: consente il completamento delle attività attualmente in esecuzione. Non viene pianificata alcuna nuova attività durante l'attesa. I nodi vengono rimossi al completamento di tutte le attività.<br /><br /> **Retaineddata** : consente il completamento delle attività attualmente in esecuzione e quindi attende che scadano tutti i periodi di conservazione dei dati dell'attività. Non viene pianificata alcuna nuova attività durante l'attesa. I nodi vengono rimossi alla scadenza di tutti i periodi di conservazione dati delle attività.<br /><br /> Il valore predefinito è requeue.<br /><br /> In caso di aumento delle dimensioni del pool, il valore è impostato su **invalid**.|
|`currentDedicatedNodes`|Int32|Numero di nodi di calcolo attualmente assegnati al pool.|
|`targetDedicatedNodes`|Int32|Numero di nodi di calcolo richiesti per il pool.|
|`currentLowPriorityNodes`|Int32|Numero di nodi di calcolo attualmente assegnati al pool.|
|`targetLowPriorityNodes`|Int32|Numero di nodi di calcolo richiesti per il pool.|
|`enableAutoScale`|Bool|Specifica se le dimensioni del pool vengono regolate automaticamente nel tempo.|
|`isAutoPool`|Bool|Specifica se il pool è stato creato tramite il meccanismo di pool automatico di un processo.|
