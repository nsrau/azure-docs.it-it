---
title: Evento di avvio ridimensionamento pool - Azure | Microsoft Docs
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 8bd33e8b-6390-4a34-95dc-2e63d8bce941
caps.latest.revision: 6
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 5064d86063251aeed22688d8fac133c4be1a88f8
ms.lasthandoff: 04/13/2017

---
# <a name="pool-resize-start-event"></a>Evento di avvio ridimensionamento pool
Corpo del log di un evento di avvio ridimensionamento pool

## <a name="remarks"></a>Osservazioni
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
|poolId|String|ID del pool.|
|nodeDeallocationOption|String|Specifica quando è possibile rimuovere nodi dal pool, in caso di riduzione delle dimensioni del pool.<br /><br /> I valori possibili sono:<br /><br /> **requeue**: termina le attività in esecuzione e le reinserisce nella coda. Le attività verranno eseguite di nuovo quando il processo viene abilitato. I nodi vengono rimossi non appena le attività sono state terminate.<br /><br /> **terminate**: termina le attività in esecuzione. Le attività non verranno più eseguite. I nodi vengono rimossi non appena le attività sono state terminate.<br /><br /> **taskcompletion**: consente il completamento delle attività attualmente in esecuzione. Non viene pianificata alcuna nuova attività durante l'attesa. I nodi vengono rimossi al completamento di tutte le attività.<br /><br /> **Retaineddata**: consente il completamento delle attività attualmente in esecuzione e quindi attende che scadano tutti i periodi di conservazione dei dati delle attività. Non viene pianificata alcuna nuova attività durante l'attesa. I nodi vengono rimossi alla scadenza di tutti i periodi di conservazione dati delle attività.<br /><br /> Il valore predefinito è requeue.<br /><br /> In caso di aumento delle dimensioni del pool, il valore è impostato su **invalid**.|
|currentDedicated|Int32|Numero di nodi di calcolo attualmente assegnati al pool.|
|targetDedicated|Int32|Numero di nodi di calcolo richiesti per il pool.|
|enableAutoScale|Booleano|Specifica se le dimensioni del pool vengono regolate automaticamente nel tempo.|
|isAutoPool|Booleano|Specifica se il pool è stato creato tramite il meccanismo di pool automatico di un processo.|

