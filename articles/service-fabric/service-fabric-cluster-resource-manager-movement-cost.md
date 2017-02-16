---
title: Cluster Resource Manager di Service Fabric - Costo dello spostamento | Microsoft Azure
description: Panoramica del costo di spostamento per i servizi di Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: f85365a36aea39b4179805e728c7ddafa140f08b


---
# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Costo dello spostamento del servizio per influenzare le scelte di Cluster Resource Manager
Un fattore importante preso in considerazione da Cluster Resource Manager di Service Fabric nel tentativo di determinare le modifiche da apportare a un cluster è il costo complessivo necessario per realizzare tale soluzione. La nozione di "costo" viene compensata dal bilanciamento che può essere ottenuto.

Lo spostamento delle istanze dei servizi o delle repliche comporta costi, come minimo in termini di tempo della CPU e ampiezza di banda della rete. Per i servizi con stato, è necessario considerare anche il costo della quantità di spazio su disco e in memoria necessaria per creare una copia dello stato prima dell'arresto delle repliche precedenti. Chiaramente è preferibile ridurre al minimo il costo delle soluzioni di Azure Service Fabric Cluster Resource Manager. Ma non bisogna neppure ignorare le soluzioni in grado di migliorare significativamente l'allocazione delle risorse del cluster.

Cluster Resource Manager prevede due modalità di calcolo dei costi e di relativo contenimento, anche quando tenta di gestire il cluster in base agli altri obiettivi. Nella prima, durante la pianificazione di un nuovo layout per il cluster, Cluster Resource Manager conta ogni potenziale spostamento. Se due soluzioni vengono generate in modo bilanciato, ovvero con un punteggio analogo, è preferibile scegliere quella con il costo minore (numero totale degli spostamenti).

Questa strategia funziona bene. Ma come con i carichi predefiniti o statici, è improbabile che in qualsiasi sistema complesso tutti gli spostamenti siano uguali. Alcuni possono risultare molto più costosi.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Modifica dei costi di spostamento di una replica e fattori da considerare
In modo analogo ai report di carico (altra funzionalità disponibile in Cluster Resource Manager), i servizi possono segnalare in modo autonomo il costo di uno spostamento in qualsiasi momento.

Codice:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

Un costo di spostamento predefinito può essere specificato anche quando viene creato un servizio.

MoveCost ha quattro livelli: Zero, Low, Medium e High. Ad eccezione del livello Zero, tali livelli sono correlati. Se un costo di spostamento è di livello Zero, significa che lo spostamento è gratuito e non deve influire sul punteggio della soluzione. L'impostazione del costo di spostamento su High *non* garantisce che la replica rimanga in un'unica posizione.

<center>
![Costo di spostamento come fattore da considerare per la selezione delle repliche da spostare][Image1]
</center>

MoveCost consente di trovare le soluzioni che causano un'interruzione complessivamente minima e che sono più facili da realizzare garantendo allo stesso tempo un bilanciamento equivalente. Il concetto di costo di un servizio può essere correlato a molti aspetti. I fattori più comuni per il calcolo del costo di spostamento sono:

* La quantità di stato o dati che il servizio deve spostare.
* Il costo di disconnessione dei client. Il costo di spostamento di una replica primaria è solitamente maggiore del costo di spostamento di una replica secondaria.
* Il costo di interruzione di un'operazione in corso. Alcune operazioni a livello di archivio dati o operazioni eseguite in risposta a una chiamata del client sono costose. Dopo un certo punto è preferibile non arrestarle se non è indispensabile. Durante l'esecuzione dell'operazione, aumentare il costo di spostamento di questo oggetto del servizio per ridurre la probabilità che si sposti. Al termine dell'operazione, reimpostare il costo sul valore normale.

## <a name="next-steps"></a>Passaggi successivi
* Cluster Resource Manger di Service Fabric usa le repliche per gestire il consumo e la capacità del cluster. Per altre informazioni sulle metriche e sulla relativa configurazione, consultare [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md).
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere [Bilanciamento del carico nel cluster di Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png



<!--HONumber=Jan17_HO1-->


