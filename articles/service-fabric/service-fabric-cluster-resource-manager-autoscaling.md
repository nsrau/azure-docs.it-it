---
title: Servizi e contenitori di scalabilità automatica in Azure Service Fabric | Documentazione Microsoft
description: Azure Service Fabric consente di impostare i criteri di scalabilità automatica per i servizi e i contenitori.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic,nipuzovi
ms.openlocfilehash: 741d77998e9f3405ca6d3863e80cb9125705eefe
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="introduction-to-auto-scaling"></a>Introduzione alla scalabilità automatica
La scalabilità automatica è una funzionalità aggiuntiva di Service Fabric che consente di applicare in modo dinamico la scalabilità ai servizi in base al carico che i servizi segnalano o in base all'utilizzo delle risorse. La scalabilità automatica offre grande elasticità e consente di eseguire il provisioning di istanze o partizioni aggiuntive del servizio su richiesta. L'intero processo di scalabilità è automatico e trasparente e, dopo aver configurato i criteri in un servizio, non è necessario eseguire manualmente le operazioni di scalabilità a livello di servizio. La funzione di scalabilità automatica può essere attivata al momento della creazione del servizio o in qualsiasi momento tramite l'aggiornamento del servizio.

Uno scenario comune in cui la scalabilità automatica è utile è quando il carico su un particolare servizio varia nel tempo. Ad esempio, un servizio quale un gateway può essere ridimensionato in base all'ammontare delle risorse necessarie per gestire le richieste in ingresso. Esaminiamo una possibile regola di scalabilità:
* Se tutte le istanze del gateway personale utilizzano in media più di due core, ridimensionare il servizio gateway mediante l'aggiunta di un'altra istanza. Eseguire questa operazione ogni ora, ma non tenere più di sette istanze in totale.
* Se tutte le istanze del gateway utilizzano in media meno di 0,5 core, ridimensionare il servizio tramite la rimozione di un'istanza. Eseguire questa operazione ogni ora, ma non tenere meno di tre istanze in totale.

La scalabilità automatica è supportata sia per i contenitori sia per i normali servizi di Service Fabric. Il resto di questo articolo descrive i criteri di scalabilità e le modalità per abilitare o disabilitare la scalabilità automatica; inoltre, fornisce alcuni esempi su come usare questa funzionalità.

## <a name="describing-auto-scaling"></a>Descrizione della scalabilità automatica
È possibile definire i criteri di scalabilità automatica per ogni servizio in un cluster di Service Fabric. Ogni criterio di scalabilità automatica è costituito da due parti:
* Il **trigger della scalabilità** descrive quando verrà eseguito il ridimensionamento del servizio. Le condizioni definite nel trigger vengono controllate periodicamente per determinare se un servizio deve essere ridimensionato.

* Il **meccanismo di scalabilità** descrive come verrà eseguito il ridimensionamento una volta attivato. Il meccanismo viene applicato solo quando vengono soddisfatte le condizioni stabilite dal trigger.

Tutti i trigger che sono attualmente supportati funzionano con le [metriche di carico logiche](service-fabric-cluster-resource-manager-metrics.md) o con le metriche fisiche, quali l’utilizzo della CPU o della memoria. In entrambi i casi, Service Fabric monitorerà il carico segnalato per la metrica e controllerà periodicamente il trigger per determinare se è necessario eseguire il ridimensionamento.

Sono disponibili due meccanismi che sono attualmente supportati per la scalabilità automatica. Il primo è progettato per i servizi senza stato o per i contenitori in cui la scalabilità automatica viene eseguita aggiungendo o rimuovendo [istanze](service-fabric-concepts-replica-lifecycle.md). Per i servizi con stato e senza stato, la scalabilità automatica può essere eseguita anche tramite l'aggiunta o la rimozione di [partizioni](service-fabric-concepts-partitioning.md) denominate del servizio.

> [!NOTE]
> Attualmente è supportato un solo criterio di scalabilità per ogni servizio.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Trigger di carico medio della partizione con scalabilità basata sull’istanza
Il primo tipo di trigger è basato sul carico delle istanze in una partizione del servizio senza stato. I carichi della metrica vengono innanzitutto livellati per ottenere il carico di ogni istanza di una partizione, quindi viene calcolata una media di questi valori per tutte le istanze della partizione. Esistono tre fattori che determinano quando il servizio verrà ridimensionato:

* La _soglia di carico inferiore_ è un valore che determina quando il servizio sarà **ridotto**. Se il carico medio di tutte le istanze delle partizioni è inferiore a questo valore, il servizio verrà ridotto.
* La _soglia di carico superiore_ è un valore che determina quando il servizio sarà **aumentato**. Se il carico medio di tutte le istanze delle partizioni è inferiore a questo valore, il servizio verrà aumentato.
* L’_intervallo di scalabilità_ determina ogni quanto verrà eseguito un controllo del trigger. Una volta controllato il trigger, se è necessario eseguire un ridimensionamento verrà applicato il meccanismo. Se il ridimensionamento non è necessario, non verrà eseguita alcuna azione. In entrambi i casi, il trigger non verrà controllato nuovamente prima dello scadere dell'intervallo di scalabilità.

Questo trigger può essere utilizzato solo con i servizi senza stato (contenitori senza stato o servizi di Service Fabric). Nel caso in cui un servizio dispone di più partizioni, il trigger viene valutato separatamente per ogni partizione e per ognuna di esse verrà applicato il meccanismo specificato in modo indipendente. Di conseguenza, in questo caso, è possibile che, in base al loro carico, alcune delle partizioni del servizio verranno aumentate, alcune verranno ridotte e altre non subiranno nessun ridimensionamento.

L'unico meccanismo che può essere utilizzato con questo trigger è PartitionInstanceCountScaleMechanism. Esistono tre fattori che determinano il modo in cui viene applicato questo meccanismo:
* L’_incremento di scalabilità_ determina il numero di istanze che verranno aggiunte o rimosse quando viene attivato meccanismo.
* Il _numero massimo di istanze_ definisce il limite superiore per la scalabilità. Se il numero di istanze della partizione raggiunge questo limite, non sarà possibile aumentare il servizio, indipendentemente dal carico. È possibile omettere questo limite specificando il valore -1: in tal caso il servizio verrà aumentato quanto più possibile (il limite corrisponde al numero di nodi che sono disponibili nel cluster).
* Il _numero minimo di istanze_ definisce il limite inferiore per la scalabilità. Se il numero di istanze della partizione raggiunge questo limite, non sarà possibile ridurre il servizio, indipendentemente dal carico.

## <a name="setting-auto-scaling-policy"></a>Impostazione dei criteri di scalabilità automatica

### <a name="using-application-manifest"></a>Tramite il manifesto dell'applicazione
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Tramite le API C#
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Tramite PowerShell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Trigger di carico medio del servizio con scalabilità basata sulla partizione
Il secondo tipo di trigger è basato sul carico di tutte le partizioni di un servizio. I carichi della metrica vengono innanzitutto livellati per ottenere il carico per ogni replica o istanza di una partizione. Per i servizi con stato, il carico della partizione è considerato il carico della replica primaria, mentre per i servizi senza stato il carico della partizione è il carico medio di tutte le istanze della partizione. Questi valori sono calcolati in media tra tutte le partizioni del servizio e questo valore viene utilizzato per attivare la scalabilità automatica. Come per il meccanismo precedente, esistono tre fattori che determinano quando il servizio verrà ridimensionato:

* La _soglia di carico inferiore_ è un valore che determina quando il servizio sarà **ridotto**. Se il carico medio di tutte le partizioni del servizio è inferiore a questo valore, il servizio verrà ridotto.
* La _soglia di carico superiore_ è un valore che determina quando il servizio sarà **aumentato**. Se il carico medio di tutte le partizioni del servizio è inferiore a questo valore, il servizio verrà ridotto.
* L’_intervallo di scalabilità_ determina ogni quanto verrà eseguito un controllo del trigger. Una volta controllato il trigger, se è necessario eseguire un ridimensionamento verrà applicato il meccanismo. Se il ridimensionamento non è necessario, non verrà eseguita alcuna azione. In entrambi i casi, il trigger non verrà controllato nuovamente prima dello scadere dell'intervallo di scalabilità.

Questo trigger può essere utilizzato sia con i servizi con stato sia con i servizi senza stato. L'unico meccanismo che può essere utilizzato con questo trigger è AddRemoveIncrementalNamedParitionScalingMechanism. Quando il servizio viene aumentato, viene aggiunta una nuova partizione; quando il servizio viene ridotto, viene rimossa una delle partizioni esistenti. Vi sono restrizioni che verranno controllate quando il servizio viene creato o aggiornato; la creazione o l’aggiornamento del servizio avrà esito negativo se non vengono soddisfatte le condizioni seguenti:
* Lo schema di partizione denominata deve essere utilizzato per il servizio.
* I nomi delle partizioni devono essere numeri interi consecutivi, ad esempio "0", "1", ecc.
* Il nome della prima partizione deve essere "0".

Ad esempio, se un servizio viene creato inizialmente con tre partizioni, gli unici possibili nomi validi per le partizioni sono "0", "1" e "2".

Anche l'effettiva operazione di scalabilità automatica che viene eseguita rispetterà questo schema di denominazione:
* Se le partizioni correnti del servizio sono denominate "0", "1" e "2", la partizione che verrà aggiunta per eseguire l’aumento del servizio verrà denominata "3".
* Se le partizioni correnti del servizio sono denominate "0", "1" e "2", la partizione che verrà rimossa per eseguire la riduzione del servizio sarà la partizione denominata "2".

Come per il meccanismo che usa la scalabilità per aggiungere o rimuovere istanze, esistono tre parametri che determinano il modo in cui viene applicato tale meccanismo:
* L’_incremento di scalabilità_ determina il numero di partizioni che verranno aggiunte o rimosse quando viene attivato il meccanismo.
* Il _numero massimo di partizioni_ definisce il limite superiore per la scalabilità. Se il numero di partizioni del servizio raggiunge questo limite, non sarà possibile aumentare il servizio, indipendentemente dal carico. È possibile omettere questo limite specificando il valore -1: in tal caso il servizio verrà aumentato quanto più possibile (il limite corrisponde alla capacità corrente del cluster).
* Il _numero minimo di istanze_ definisce il limite inferiore per la scalabilità. Se il numero di partizioni del servizio raggiunge questo limite, non sarà possibile ridurre il servizio, indipendentemente dal carico.

## <a name="setting-auto-scaling-policy"></a>Impostazione dei criteri di scalabilità automatica

### <a name="using-application-manifest"></a>Tramite il manifesto dell'applicazione
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedParitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Tramite le API C#
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedParitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Tramite PowerShell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedParitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Scalabilità automatica basata sulle risorse

Per abilitare il servizio di monitoraggio delle risorse al fine di applicare la scalabilità basata sulle risorse effettive

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Esistono due metriche che rappresentano le risorse fisiche effettive. Una di esse è servicefabric: / _CpuCores, che rappresenta l'effettivo utilizzo della CPU (dove 0,5 corrisponde a metà di un core), e l'altra è servicefabric: / _MemoryInMB, che rappresenta l'utilizzo della memoria in MB.
ResourceMonitorService è responsabile del monitoraggio dell'utilizzo della CPU e della memoria dei servizi utente. Questo servizio applicherà una media mobile ponderata per tenere conto di potenziali picchi di breve durata. Il monitoraggio delle risorse è supportato per le applicazioni in contenitori e fuori contenitori su Windows e per quelle in contenitori su Linux. La scalabilità automatica basata sulle risorse è abilitata solo per i servizi attivati nel [modello di processo esclusivo](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla [scalabilità delle applicazioni](service-fabric-concepts-scalability.md).