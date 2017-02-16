---
title: Limitazione in Cluster Resource Manager di Service Fabric | Microsoft Docs
description: Informazioni sulla configurazione delle limitazioni messe a disposizione da Cluster Resource Manager di Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 8a8419497bda3f1df523d6aff28028548abc155a


---

# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>Limitazione del comportamento di Cluster Resource Manager di Service Fabric
Anche se Cluster Resource Manager è stato configurato correttamente, il cluster può essere interrotto. Ad esempio potrebbero verificarsi errori simultanei di nodi o di domini di errore. Che cosa succederebbe se ciò si verificasse durante un aggiornamento? Cluster Resource Manager tenta di correggere tutti gli elementi, ma questa operazione può introdurre una varianza nel cluster. Le limitazioni contribuiscono a creare una barriera in modo che il cluster possa impiegare risorse per stabilizzarsi: vengono ripristinati i nodi, vengono riparate le partizioni di rete e vengono distribuiti i bit corretti.

Per risolvere questi tipi di situazioni, Cluster Resource Manager di Service Fabric prevede diverse limitazioni. Queste limitazioni intervengono in modo piuttosto pesante. Per queste impostazioni non devono essere modificati i valori predefiniti a meno che non sia stato calcolato con precisione il carico di lavoro che il cluster può eseguire in parallelo.

Le limitazioni hanno valori predefiniti che il team responsabile di Service Fabric ha individuato come corretti sulla base dell'esperienza. Se è necessario modificarli è consigliabile ottimizzarli sulla base del carico effettivo previsto. Potrebbe essere necessario introdurre alcune limitazioni, anche se ciò significa che il cluster impiega più tempo per stabilizzarsi in situazioni di mainline.

## <a name="configuring-the-throttles"></a>Configurazione delle limitazioni
Le limitazioni incluse per impostazione predefinita sono:

* GlobalMovementThrottleThreshold: consente di controllare il numero totale di spostamenti nel cluster in un determinato intervallo di tempo (definito come GlobalMovementThrottleCountingInterval, valore in secondi)
* MovementPerPartitionThrottleThreshold: consente di controllare il numero totale di spostamenti per qualsiasi partizione di servizio in un determinato intervallo di tempo (MovementPerPartitionThrottleCountingInterval, valore in secondi)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThreshold",
          "value": "1000"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      },
      {
          "name": "MovementPerPartitionThrottleThreshold",
          "value": "50"
      },
      {
          "name": "MovementPerPartitionThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

Nella maggior parte dei casi è stato notato che i clienti usano queste limitazioni perché si trovavano già in un ambiente dalle risorse limitate. Alcuni esempi di questo ambiente sono una limitata larghezza di banda in singoli nodi o dischi non in grado di compilare molte repliche in parallelo a causa di limiti di velocità effettiva. Questi tipi di restrizioni significavano che le operazioni attivate in risposta agli errori non avrebbero avuto esito positivo o sarebbero state lente, anche in assenza di limitazioni. In queste situazioni i clienti sapevano che il tempo richiesto al cluster per raggiungere uno stato stabile sarebbe stato maggiore. I clienti inoltre hanno compreso anche che il livello complessivo di affidabilità era inferiore in presenza di limitazioni.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
* Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)



<!--HONumber=Jan17_HO1-->


