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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 22223923f6783f11178b44c3c3087f17eb8dc41b
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---

# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Limitazione del Cluster Resource Manager di Service Fabric
Anche se Cluster Resource Manager è stato configurato correttamente, il cluster può essere interrotto. Ad esempio potrebbero verificarsi errori simultanei di nodi e di domini di errore. Che cosa succederebbe se ciò si verificasse durante un aggiornamento? Cluster Resource Manager tenta sempre di correggere tutti gli elementi, usando le risorse del cluster nel tentativo di riorganizzare e correggere il cluster. Le limitazioni contribuiscono a creare una barriera in modo che il cluster possa impiegare risorse per stabilizzarsi: vengono ripristinati i nodi, vengono riparate le partizioni di rete e vengono distribuiti i bit corretti.

Per risolvere questi tipi di situazioni, Cluster Resource Manager di Service Fabric prevede diverse limitazioni. Queste limitazioni intervengono tutte in modo piuttosto pesante. In genere non devono essere modificate senza una pianificazione e un test accurati.

Se si modificano le limitazioni di Cluster Resource Manager, è consigliabile ottimizzarle al carico effettivo previsto. Potrebbe essere necessario introdurre alcune limitazioni, anche se ciò significa che il cluster impiega più tempo per stabilizzarsi in alcune situazioni. Il test è obbligatorio per determinare i valori corretti per le limitazioni. Le limitazioni devono essere sufficientemente alte in modo da consentire al cluster di rispondere alle modifiche in un intervallo di tempo ragionevole, e sufficientemente basse per impedire effettivamente un eccessivo consumo di risorse. 

Nella maggior parte dei casi è stato notato che i clienti usano delle limitazioni perché si trovavano già in un ambiente dalle risorse limitate. Alcuni esempi sono una limitata larghezza di banda per singoli nodi o dischi non in grado di compilare molte repliche con stato in parallelo a causa di limiti di velocità effettiva. Senza limitazioni, le operazioni potrebbero sovraccaricare queste risorse, causando l'esito negativo delle operazione o un loro rallentamento. In queste situazioni i clienti usavano delle limitazioni sapevano che il tempo richiesto al cluster per raggiungere uno stato stabile sarebbe stato maggiore. I clienti inoltre hanno compreso anche che il livello complessivo di affidabilità era inferiore in presenza di limitazioni.


## <a name="configuring-the-throttles"></a>Configurazione delle limitazioni

Service Fabric ha due meccanismi per la limitazione del numero di spostamenti della replica. Il meccanismo predefinito che esisteva prima di Service Fabric 5.7 rappresenta la limitazione come un numero assoluto di spostamenti consentito. Non funziona per i cluster di tutte le dimensioni. In particolare, per i cluster di grandi dimensioni il valore predefinito può essere troppo piccolo, rallentando notevolmente il bilanciamento del carico anche quando è necessario, mentre non ha alcun impatto nei cluster più piccoli. Il meccanismo precedentemente descritto è stato sostituito dalla limitazione basata sulla percentuale, che offre una scalabilità migliore con i cluster dinamici in cui il numero di servizi e i nodi cambia regolarmente.

Le limitazioni sono basate su una percentuale del numero di repliche dei cluster. Le limitazioni basate sulla percentuale abilitano l'espressione della regola: "non spostare più del 10% di repliche in un intervallo di 10 minuti", ad esempio.

Le impostazioni di configurazione per la limitazione basata sulla percentuale sono:

  - GlobalMovementThrottleThresholdPercentage: numero massimo di movimenti consentiti nel cluster in qualsiasi momento, espresso come percentuale del numero totale di repliche del cluster. Impostarlo su 0 per non avere limiti. Il valore predefinito è 0. Se vengono specificati sia l'impostazione sia la GlobalMovementThrottleThreshold, viene usato il limite più conservativo.
  - GlobalMovementThrottleThresholdPercentageForPlacement: numero massimo di movimenti consentiti durante la fase di posizionamento, espresso come percentuale del numero totale di repliche del cluster. Impostarlo su 0 per non avere limiti. Il valore predefinito è 0. Se vengono specificati sia l'impostazione sia la GlobalMovementThrottleThresholdForPlacement, viene usato il limite più conservativo.
  - GlobalMovementThrottleThresholdPercentageForBalancing: numero massimo di movimenti consentiti durante la fase di bilanciamento, espresso come percentuale del numero totale di repliche del cluster. Impostarlo su 0 per non avere limiti. Il valore predefinito è 0. Se vengono specificati sia l'impostazione sia la GlobalMovementThrottleThresholdPercentageForBalancing, viene usato il limite più conservativo.

Quando si specifica la percentuale di limitazione, è necessario specificare 5% come 0,05. L'intervallo in cui vengono gestite queste limitazioni è il GlobalMovementThrottleCountingInterval, che è espresso in secondi.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Limitazioni basate sul conteggio predefinito
Queste informazioni vengono fornite nel caso in cui si disponga di cluster precedenti o si mantengano queste configurazioni in cluster che sono quindi stati aggiornati. In generale, è consigliabile che queste ultime vengano sostituite con le limitazioni basate su percentuale descritte in precedenza. Poiché la limitazione basata su percentuale è disabilitata per impostazione predefinita, queste limitazioni restano le limitazioni predefinite per un cluster finché non vengono disabilitate e sostituite con le limitazioni basate su percentuale. 

  - GlobalMovementThrottleThreshold: consente di controllare il numero totale di spostamenti nel cluster in un determinato intervallo di tempo. La quantità di tempo viene specificata in secondi come GlobalMovementThrottleCountingInterval. Il valore predefinito per la GlobalMovementThrottleThreshold è 1000 e il valore predefinito per il GlobalMovementThrottleCountingInterval è 600.
  - MovementPerPartitionThrottleThreshold: consente di controllare il numero totale di spostamenti per qualsiasi partizione di servizio in un determinato intervallo di tempo. La quantità di tempo viene specificata in secondi come MovementPerPartitionThrottleCountingInterval. Il valore predefinito per la MovementPerPartitionThrottleThreshold è 50 e il valore predefinito per il MovementPerPartitionThrottleCountingInterval è 600.

La configurazione di queste limitazioni segue lo stesso modello delle limitazioni basate su percentuale.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

