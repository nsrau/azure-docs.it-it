---
title: Cluster Resource Manager di Service Fabric - Costo dello spostamento | Microsoft Docs
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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 3063647671fea94da3ce635b887f6f0f7de89f70
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---
# <a name="service-movement-cost"></a>Costo di spostamento dei servizi
Un fattore preso in considerazione da Cluster Resource Manager di Service Fabric nel tentativo di determinare le modifiche da apportare a un cluster è il costo di tali modifiche. Il concetto di "costo" viene compensato sulla base di quanto il cluster può essere migliorato. Il factoring del costo avviene durante lo spostamento di servizi di bilanciamento del carico, la deframmentazione e altri requisiti. L'obiettivo è soddisfare i requisiti nel modo meno problematico o costoso. 

Lo spostamento dei servizi comporta costi, come minimo in termini di tempo della CPU e ampiezza di banda della rete. Per i servizi con stato, è necessario copiare lo stato di tali servizi, consumando disco e memoria aggiuntivi. La riduzione del costo delle soluzioni offerta da Cluster Resource Manager di Azure Service Fabric contribuisce a garantire che le risorse del cluster non vengono usate inutilmente. Ma non bisogna neppure ignorare le soluzioni in grado di migliorare significativamente l'allocazione delle risorse del cluster.

Cluster Resource Manager prevede due modalità di calcolo dei costi e del relativo contenimento quando tenta di gestire il cluster. Il primo meccanismo è semplicemente il conteggio ogni spostamento che verrebbe eseguito. Se due soluzioni vengono generate in modo bilanciato, ovvero con un punteggio analogo, Cluster Resource Manager sceglie quella con il costo minore (numero totale degli spostamenti).

Questa strategia funziona bene. Ma come con i carichi predefiniti o statici, è improbabile che in qualsiasi sistema complesso tutti gli spostamenti siano uguali. Alcuni possono risultare molto più costosi.

## <a name="setting-move-costs"></a>Impostazione dei costi di spostamento 
È possibile specificare il costo predefinito degli spostamenti di un servizio al momento della creazione:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

È anche possibile specificare o aggiornare in modo dinamico MoveCost per un servizio dopo averlo creato: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Specificare in modo dinamico il costo di spostamento su base di replica

I precedenti frammenti di codice sono tutti per la specifica di MoveCost contemporaneamente per un intero servizio all'esterno del servizio stesso. Tuttavia, il costo di spostamento è maggiormente utile quando questo cambia per un servizio specifico durante la sua durata. Poiché i servizi stessi sono a conoscenza del costo del loro spostamento in un determinato momento, è disponibile un'API per i servizi che notifica i singoli costi di spostamento in fase di runtime. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impatto del costo di spostamento
MoveCost ha quattro livelli: Zero, Low, Medium e High. Ad eccezione del livello Zero, tali livelli sono correlati. Se un costo di spostamento è di livello Zero, significa che lo spostamento è gratuito e non deve influire sul punteggio della soluzione. L'impostazione del costo di spostamento su High *non* garantisce che la replica rimanga in un'unica posizione.

<center>
![Costo di spostamento come fattore da considerare per la selezione delle repliche da spostare][Image1]
</center>

MoveCost consente di trovare le soluzioni che causano un'interruzione complessivamente minima e che sono più facili da realizzare garantendo allo stesso tempo un bilanciamento equivalente. Il concetto di costo di un servizio può essere correlato a molti aspetti. I fattori più comuni per il calcolo del costo di spostamento sono:

- La quantità di stato o dati che il servizio deve spostare.
- Il costo di disconnessione dei client. Lo spostamento di una replica primaria è solitamente più costoso dello spostamento di una replica secondaria.
- Il costo di interruzione di un'operazione in corso. Alcune operazioni a livello di archivio dati o operazioni eseguite in risposta a una chiamata del client sono costose. Dopo un certo punto è preferibile non arrestarle se non è indispensabile. Durante l'esecuzione dell'operazione, aumentare il costo di spostamento di questo oggetto del servizio per ridurre la probabilità che si sposti. Al termine dell'operazione, reimpostare il costo sul valore normale.

## <a name="enabling-move-cost-in-your-cluster"></a>Abilitazione del costo di spostamento del cluster
Per considerare il MoveCosts più granulare, è necessario abilitare MoveCost nel cluster. Senza questa impostazione, viene usata la modalità predefinita per conteggio degli spostamenti per calcolare MoveCost e i report di MoveCost vengono ignorati.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Passaggi successivi
- Cluster Resource Manger di Service Fabric usa le repliche per gestire il consumo e la capacità del cluster. Per altre informazioni sulle metriche e sulla relativa configurazione, consultare [Gestione dell'utilizzo delle risorse e del carico in Service Fabric con le metriche](service-fabric-cluster-resource-manager-metrics.md).
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere [Bilanciamento del carico nel cluster di Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

