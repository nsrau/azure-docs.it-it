---
title: "Come visualizzare l'integrità aggregata delle entità di Azure Service Fabric | Microsoft Docs"
description: "Descrive come eseguire una query dell'integrità aggregata delle entità di Azure Service Fabric, come visualizzarla e come valutarla con query di integrità e query generali."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: oanapl
ms.openlocfilehash: acd3168adc6624e172099c8d62124f7b5ae4839a
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="view-service-fabric-health-reports"></a>Come visualizzare i report sull'integrità di Service Fabric
Azure Service Fabric introduce un [modello di integrità](service-fabric-health-introduction.md) con entità di integrità per le quali i componenti di sistema e i watchdog possono creare report sulle condizioni locali sottoposte a monitoraggio. L' [archivio integrità](service-fabric-health-introduction.md#health-store) aggrega tutti i dati di integrità per determinare se le entità sono integre.

Il cluster viene popolato automaticamente con report sull'integrità inviati dai componenti di sistema. Per altre informazioni, vedere [Usare i report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric offre diversi modi per ottenere l'integrità aggregata delle entità:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) o altri strumenti di visualizzazione
* Query di integrità (tramite PowerShell, API o REST)
* Query generali che restituiscono un elenco di entità per le quali l'integrità costituisce una proprietà (tramite PowerShell, API o REST)

Per illustrare queste opzioni, si userà un cluster locale con cinque nodi e l'[applicazione fabric:/WordCount](http://aka.ms/servicefabric-wordcountapp). L'applicazione **fabric:/WordCount** contiene due servizi predefiniti, un servizio con stato di tipo `WordCountServiceType` e un servizio senza stato di tipo `WordCountWebServiceType`. L'oggetto `ApplicationManifest.xml` è stato modificato per richiedere sette repliche di destinazione per il servizio con stato e una partizione. Poiché ci sono solo cinque nodi nel cluster, i componenti di sistema segnalano un avviso nella partizione del servizio perché è al di sotto del numero previsto.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Integrità in Esplora Infrastruttura di servizi
Esplora Infrastruttura di servizi fornisce una panoramica visiva del cluster. Nell'immagine seguente è possibile osservare quanto segue:

* L'applicazione **fabric:/WordCount** è di colore rosso (condizione di errore), perché per questa applicazione è stato segnalato un evento di errore da **MyWatchdog** per la proprietà **Availability**.
* Uno dei servizi di questa applicazione, **fabric:/WordCount/WordCountService** è di colore giallo (condizione di avviso). Il servizio è configurato con sette repliche e il cluster ha cinque nodi, quindi due repliche non possono essere posizionate. Anche se qui non è illustrata, la partizione del servizio è di colore giallo a causa di un report di sistema di `System.FM` che indica `Partition is below target replica or instance count`. La partizione gialla avvia il servizio giallo.
* Il cluster è di colore rosso perché è rossa anche l'applicazione.

La valutazione usa criteri predefiniti del manifesto del cluster dal manifesto dell'applicazione. Questi sono criteri rigorosi e non tollerano errori.

Visualizzazione del cluster con Service Fabric Explorer:

![Visualizzazione del cluster con Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Ulteriori informazioni su [Esplora Infrastruttura di servizi](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Query relative all’integrità
Infrastruttura di servizi espone le query relative all’integrità per ognuno dei [tipi di entità](service-fabric-health-introduction.md#health-entities-and-hierarchy)supportati. È possibile accedervi tramite l'API, usando i metodi in [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), i cmdlet di PowerShell e REST. Queste query restituiscono informazioni complete sull'integrità per l'entità, come lo stato aggregato dell'integrità, gli eventi di integrità dell'entità, gli stati di integrità degli elementi figlio, se applicabili, le valutazioni di non integrità (quando l'entità non è integra) e le statistiche sull'integrità degli elementi figlio, quando applicabili.

> [!NOTE]
> Un'entità integra viene restituita quando è popolata completamente nell'archivio integrità. L'entità deve essere attiva (non eliminata) e avere un report di sistema. Anche le entità padre nella catena della gerarchia devono avere report di sistema. Se una di queste condizioni non viene soddisfatta, le query relative all'integrità restituiscono un oggetto [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) con [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` che illustra il motivo per cui l'entità non viene restituita.
>
>

Le query di integrità richiedono il passaggio nell'identificatore dell'entità, che dipende dal tipo di entità. Le query accettano parametri dei criteri di integrità facoltativi. Se non sono specificati, per la valutazione vengono usati i [criteri di integrità](service-fabric-health-introduction.md#health-policies) dal manifesto del cluster o dell'applicazione. Se i manifesti non contengono una definizione per i criteri di integrità, per la valutazione vengono usati i criteri di integrità predefiniti. I criteri di integrità predefiniti non tollerano errori. Le query accettano anche filtri per restituire solo elementi figlio o eventi parziali, quelli che rispettano i filtri specificati. Un altro filtro consente di escludere le statistiche relative agli elementi figlio.

> [!NOTE]
> Sul lato server vengono applicati i filtri di output, in modo che la dimensione della risposta al messaggio venga ridotta. È consigliabile usare i filtri di output per limitare i dati restituiti, invece di applicare filtri sul lato client.
>
>

L'integrità di un'entità contiene quanto segue:

* Lo stato di integrità aggregato dell'entità. Viene calcolato dall'archivio integrità in base ai report sull'integrità dell'entità, gli stati di integrità degli elementi figlio, se applicabili, e i criteri di integrità. Per altre informazioni, vedere [valutazione dell'integrità dell'entità](service-fabric-health-introduction.md#health-evaluation).  
* Gli eventi di integrità dell'entità.
* La raccolta degli stati di integrità di tutti gli elementi figlio per le entità che possono avere elementi figlio. Gli stati di integrità contengono l'identificatore dell'entità e lo stato di integrità aggregato. Per ottenere l'integrità completa per un elemento figlio, chiamare l'integrità di query per il tipo di entità figlio e passare l'identificatore dell'elemento figlio.
* Le valutazioni non integre che puntano al report che ha attivato lo stato dell'entità, se l'entità non è integra. Le valutazioni sono ricorsive e contengono le valutazioni di integrità degli elementi figlio che hanno attivato lo stato di integrità corrente. Un watchdog ha segnalato ad esempio un errore in una replica. L'integrità dell'applicazione presenta una valutazione di non integrità a causa di un servizio non integro. Il servizio non è integro a causa di un errore di una partizione. La partizione non è integra a causa di un errore di una replica. La replica non è integra a causa del report di integrità di errore del watchdog.
* Le statistiche di integrità per tutti i tipi figlio delle entità che hanno elementi figlio. L'integrità del cluster mostra ad esempio il numero totale di applicazioni, servizi, partizioni, repliche ed entità distribuite nel cluster. L'integrità del servizio mostra il numero totale di partizioni e repliche nel servizio specificato.

## <a name="get-cluster-health"></a>Get cluster health
Restituisce l'integrità dell'entità cluster e contiene gli stati di integrità di applicazioni e nodi, elementi figlio del cluster. Input:

* [Facoltativo] Criteri di integrità del cluster usati per valutare i nodi e gli eventi del cluster.
* [Facoltativo] Mappa dei criteri di integrità dell'applicazione con criteri di integrità usati per sostituire i criteri del manifesto dell'applicazione.
* [Facoltativo] Filtri per eventi, nodi e applicazioni che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi, i nodi e le applicazioni, indipendentemente dal filtro.
* [Facoltativo] Filtrare per escludere le statistiche di integrità.
* [Facoltativo] Filtrare per includere le statistiche di integrità di fabric:/System. Applicabile solo quando le statistiche di integrità non sono escluse. Per impostazione predefinita, le statistiche di integrità includono solo le statistiche per le applicazioni utente e non per l'applicazione System.

### <a name="api"></a>API
Per ottenere l'integrità del cluster, creare un oggetto `FabricClient` e chiamare il metodo [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) sul relativo **HealthManager**.

La chiamata seguente permette di ottenere l'integrità del cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Il codice seguente permette di ottenere l'integrità del cluster usando criteri di integrità del cluster personalizzati e filtri per nodi e applicazioni. Specifica che le statistiche di integrità includono le statistiche di fabric:/System. Crea un oggetto [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), che contiene le informazioni di input.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Il cmdlet per ottenere l'integrità del cluster è [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Lo stato del cluster è costituito da cinque nodi, dall'applicazione di sistema e da fabric:/WordCount, configurati come descritto.

I cmdlet seguenti ottengono l'integrità del cluster con criteri di integrità predefiniti. Lo stato di integrità aggregato è di tipo avviso, perché per l'applicazione fabric:/WordCount è stato generato un avviso. Notare come le valutazioni non integre forniscano dettagli sulle condizioni che hanno attivato lo stato di integrità aggregato.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                          
                          
NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_0
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Il cmdlet PowerShell seguente ottiene lo stato di integrità del cluster con i criteri dell'applicazione personalizzati. Filtra i risultati per ottenere solo le applicazioni e i nodi con stato di errore o avviso. Di conseguenza, non vengono restituiti nodi, perché sono tutti integri. Solo l'applicazione fabric:/WordCount rispetta il filtro delle applicazioni. Poiché i criteri personalizzati specificano di considerare gli avvisi come errori per l'applicazione fabric:/WordCount, questa viene valutata in stato di errore e lo stesso accade per il cluster.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                          
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="rest"></a>REST
Per ottenere l'integrità di un cluster è possibile usare una [richiesta GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) o una [richiesta POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) che nel corpo include la descrizione di criteri di integrità.

## <a name="get-node-health"></a>Get node health
Restituisce l'integrità di un'entità nodo e contiene gli eventi di integrità segnalati sul nodo. Input:

* [Obbligatorio] Nome del nodo che identifica il nodo.
* [Facoltativo] Impostazioni dei criteri di integrità usate per valutare l'integrità.
* [Facoltativo] Filtri per gli eventi che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere l'integrità del nodo tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) sul relativo HealthManager.

Il codice seguente permette di ottenere l'integrità del nodo per il nome del nodo specificato:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Il codice seguente permette di ottenere l'integrità del nodo per il nome del nodo specificato e passa un filtro eventi e criteri personalizzati tramite [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Il cmdlet per ottenere l'integrità del nodo è [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .
I cmdlet seguenti ottengono l'integrità del nodo con criteri di integrità predefiniti:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Il cmdlet seguente ottiene lo stato di tutti i nodi del cluster:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Per ottenere l'integrità di un nodo è possibile usare una [richiesta GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) o una [richiesta POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) che nel corpo include la descrizione di criteri di integrità.

## <a name="get-application-health"></a>Ottieni lo stato dell'integrità dell'applicazione
Restituisce lo stato di un'entità applicazione. Contiene gli stati di integrità dell'applicazione distribuita e gli elementi figlio del servizio. Input:

* [Obbligatorio] Nome dell'applicazione (URI) che identifica l'applicazione.
* [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.
* [Facoltativo] Filtri per eventi, servizi e applicazioni distribuite che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi, i servizi e le applicazioni distribuite, indipendentemente dal filtro.
* [Facoltativo] Filtrare per escludere le statistiche di integrità. Se non specificato, le statistiche di integrità includono il numero di stati ok, di avviso e di errore per tutti gli elementi figlio dell'applicazione: servizi, partizioni, repliche, applicazioni distribuite e pacchetti del servizio distribuiti.

### <a name="api"></a>API
Per ottenere l'integrità dell'applicazione, creare un oggetto `FabricClient` e chiamare il metodo [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) sul relativo HealthManager.

Il codice seguente permette di ottenere l'integrità dell'applicazione per il nome dell'applicazione (URI) specificato:

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Il codice seguente permette di ottenere l'integrità dell'applicazione per il nome dell'applicazione (URI) specificato, con filtri e criteri personalizzati specificati tramite [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Il cmdlet per ottenere l'integrità dell'applicazione è [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Il cmdlet seguente restituisce l'integrità dell'applicazione **fabric:/WordCount** :

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok
                                  
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning
                                  
DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok
                                  
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
                                  
HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

Il cmdlet PowerShell seguente passa i criteri personalizzati. Filtra anche gli elementi figlio e gli eventi.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error
                                  
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Per ottenere l'integrità di un'applicazione è possibile usare una [richiesta GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) o una [richiesta POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) che nel corpo include la descrizione di criteri di integrità.

## <a name="get-service-health"></a>Get service health
Restituisce lo stato di un'entità di servizio. Contiene gli stati di integrità della partizione. Input:

* [Obbligatorio] Nome del servizio (URI) che identifica il servizio.
* [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.
* [Facoltativo] Filtri per eventi e partizioni che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi e tutte le partizioni, indipendentemente dal filtro.
* [Facoltativo] Filtrare per escludere le statistiche di integrità. Se non specificato, le statistiche di integrità mostrano il numero di stati ok, di avviso e di errore per tutte le partizioni e le repliche del servizio.

### <a name="api"></a>API
Per ottenere l'integrità del servizio tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) sul relativo HealthManager.

L'esempio seguente ottiene l'integrità di un servizio con il nome di servizio (URI) specificato:

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Il codice seguente permette di ottenere l'integrità del servizio per il nome del servizio (URI) specificato, con filtri e criteri personalizzati specificati tramite [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Il cmdlet per ottenere l'integrità del servizio è [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Il cmdlet seguente ottiene l'integrità del servizio usando i criteri di integrità predefiniti:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                        
                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                        
                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Per ottenere l'integrità di un servizio è possibile usare una [richiesta GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) o una [richiesta POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) che nel corpo include la descrizione di criteri di integrità.

## <a name="get-partition-health"></a>Get partition health
Restituisce lo stato di un'entità partizione. Contiene gli stati di integrità della replica. Input:

* [Obbligatorio] ID partizione (GUID) che identifica la partizione.
* [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.
* [Facoltativo] Filtri per eventi e repliche che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi e tutte le repliche, indipendentemente dal filtro.
* [Facoltativo] Filtrare per escludere le statistiche di integrità. Se non specificato, le statistiche di integrità mostrano il numero di repliche con stati ok, di avviso e di errore.

### <a name="api"></a>API
Per ottenere l'integrità della partizione tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) sul relativo HealthManager. Per specificare parametri facoltativi, creare [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Il cmdlet per ottenere l'integrità della partizione è [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Il cmdlet seguente ottiene l'integrità di tutte le partizioni del servizio **fabric:/WordCount/WordCountService** ed esclude tramite filtro gli stati di integrità delle repliche:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Per ottenere l'integrità di una partizione è possibile usare una [richiesta GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition) o una [richiesta POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) che nel corpo include la descrizione di criteri di integrità.

## <a name="get-replica-health"></a>Get replica health
Restituisce l'integrità di una replica di un servizio con stato o di un'istanza di un servizio senza stato. Input:

* [Obbligatorio] ID partizione (GUID) e ID replica che identifica la replica.
* [Facoltativo] Parametri dei criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.
* [Facoltativo] Filtri per gli eventi che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere l'integrità della replica tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) sul relativo HealthManager. Per specificare parametri avanzati, usare [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Il cmdlet per ottenere l'integrità della replica è [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Il cmdlet seguente ottiene l'integrità della replica primaria per tutte le partizioni del servizio.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Per ottenere l'integrità di una replica è possibile usare una [richiesta GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) o una [richiesta POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) che nel corpo include la descrizione di criteri di integrità.

## <a name="get-deployed-application-health"></a>Ottieni lo stato dell'integrità delle applicazioni distribuite.
Restituisce l’integrità di un’applicazione distribuita in un’entità nodo. Contiene gli stati di integrità del pacchetto di servizi distribuito. Input:

* [Obbligatorio] Nome dell'applicazione (URI) e nome del nodo (stringa) che identificano l'applicazione distribuita
* [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.
* [Facoltativo] Filtri per eventi e pacchetti di servizi distribuiti che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi e i pacchetti di servizi distribuiti, indipendentemente dal filtro.
* [Facoltativo] Filtrare per escludere le statistiche di integrità. Se non specificato, le statistiche di integrità mostrano il numero di pacchetti del servizio distribuiti con stati di integrità ok, di avviso e di errore.

### <a name="api"></a>API
Per ottenere l'integrità di un'applicazione distribuita in un nodo tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) sul relativo HealthManager. Per specificare parametri facoltativi, usare [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Il cmdlet per ottenere l'integrità dell'applicazione distribuita è [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) . Per sapere dove viene distribuita un'applicazione, eseguire [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e osservare gli elementi figlio dell'applicazione distribuita.

Il cmdlet seguente ottiene l'integrità dell'applicazione **fabric:/WordCount** distribuita nel nodo **_Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM
                                     
HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Per ottenere l'integrità di un'applicazione distribuita è possibile usare una [richiesta GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) o una [richiesta POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) che nel corpo include la descrizione di criteri di integrità.

## <a name="get-deployed-service-package-health"></a>Get deployed service package health
Restituisce lo stato di un'entità di pacchetto di servizi distribuito. Input:

* [Obbligatorio] Nome dell'applicazione (URI), nome del nodo (stringa) e nome del manifesto del servizio (stringa) che identificano il pacchetto di servizi distribuito.
* [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.
* [Facoltativo] Filtri per gli eventi che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere l'integrità di un pacchetto del servizio distribuito tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) sul relativo HealthManager. Per specificare parametri facoltativi, usare [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Il cmdlet per ottenere l'integrità del pacchetto del servizio distribuito è [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) . Per verificare dove viene distribuita un'applicazione, eseguire [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) e osservare le applicazioni distribuite. Per verificare quali pacchetti di servizi sono contenuti in un'applicazione, esaminare gli elementi figlio del pacchetto del servizio distribuito nell'output di [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) .

Il cmdlet seguente permette di ottenere l'integrità del pacchetto del servizio **WordCountServicePkg** dell'applicazione **fabric:/WordCount** distribuita in **Node2**. L'entità include report **System.Hosting** per l'attivazione corretta del pacchetto del servizio e del punto di ingresso, nonché per la registrazione corretta del tipo di servizio.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Per ottenere l'integrità di un pacchetto del servizio distribuito è possibile usare una [richiesta GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) o una [richiesta POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) che nel corpo include la descrizione di criteri di integrità.

## <a name="health-chunk-queries"></a>Query sul blocco di integrità
Le query sul blocco di integrità possono restituire gli elementi figlio di un cluster a più livelli (in modo ricorsivo) per ogni filtro di input. Supporta i filtri avanzati che consentono una notevole flessibilità nella scelta degli elementi figlio da restituire. I filtri consentono di specificare gli elementi figlio in base a un identificatore univoco o ad altri identificatori di gruppo e/o stati di integrità. Per impostazione predefinita, non sono inclusi elementi figlio, a differenza dei comandi relativi all'integrità che includono sempre gli elementi figlio di primo livello.

Le [query sull'integrità](service-fabric-view-entities-aggregated-health.md#health-queries) restituiscono solo gli elementi figlio di primo livello dell'entità specificata per ogni filtro necessario. Per ottenere gli elementi figlio di un elemento figlio, è necessario chiamare API di integrità aggiuntive per ogni entità di interesse. Analogamente, per ottenere l'integrità di entità specifiche, è necessario chiamare un'API di integrità per ogni entità di interesse. I filtri avanzati per le query sui blocchi consentono di richiedere più elementi di interesse in una sola query, riducendo al minimo le dimensioni del messaggio e il numero di messaggi.

Il vantaggio delle query sui blocchi sta nella possibilità di ottenere lo stato dell'integrità per più entità cluster, potenzialmente tutte le entità cluster a partire dalla radice richiesta, in una sola chiamata. È possibile esprimere query sull'integrità complesse, ad esempio:

* Restituzione solo delle applicazioni con errore e inclusione di tutti i servizi con avviso o errore per queste applicazioni. Per i servizi restituiti, inclusione di tutte le partizioni.
* Restituzione solo dell'integrità di quattro applicazioni, specificate in base ai rispettivi nomi.
* Restituzione solo dell'integrità delle applicazioni con un tipo di applicazione desiderato.
* Restituzione di tutte le entità distribuite su un nodo. Restituisce tutte le applicazioni, tutte le applicazioni distribuite nel nodo specificato e tutti i pacchetti di servizio distribuiti nel nodo.
* Restituzione di tutte le repliche con errore. Vengono restituiti tutti i servizi, le applicazioni e le partizioni e le sole repliche con errore.
* Restituzione di tutte le applicazioni. Per un servizio specificato, inclusione di tutte le partizioni.

La query sul blocco di integrità è attualmente esposta solo per l'entità del cluster. Restituisce un blocco di integrità del cluster che contiene:

* Lo stato di integrità aggregato del cluster.
* L'elenco del blocco dello stato di integrità dei nodi che rispettano i filtri di input.
* L'elenco del blocco dello stato di integrità delle applicazioni che rispettano i filtri di input. Ogni blocco dello stato di integrità dell'applicazione contiene un elenco di blocchi con tutti i servizi che rispettano i filtri di input e un elenco di blocchi con tutte le applicazioni distribuite che rispettano i filtri. Lo stesso vale per gli elementi figlio dei servizi e delle applicazioni distribuite. In questo modo, tutte le entità nel cluster possono essere potenzialmente restituite se richiesto, in modo gerarchico.

### <a name="cluster-health-chunk-query"></a>Query sul blocco di integrità del cluster
Restituisce l'integrità dell'entità cluster e contiene blocchi di stato dell'integrità gerarchici degli elementi figlio necessari. Input:

* [Facoltativo] Criteri di integrità del cluster usati per valutare i nodi e gli eventi del cluster.
* [Facoltativo] Mappa dei criteri di integrità dell'applicazione con criteri di integrità usati per sostituire i criteri del manifesto dell'applicazione.
* [Facoltativo] Filtri per i nodi e per le applicazioni che specificano le voci di interesse e da restituire nel risultato. I filtri sono specifici per un'entità/un gruppo di entità o sono applicabili a tutte le entità a tale livello. L'elenco di filtri può contenere un filtro generale e/o filtri per identificatori specifici per entità dettagliate restituite dalla query. Se l'elenco è vuoto, gli elementi figlio non vengono restituiti per impostazione predefinita.
  Per altre informazioni sui filtri, vedere [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) e [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). I filtri dell'applicazione possono specificare in modo ricorsivo filtri avanzati per gli elementi figlio.

I risultati del blocco includono gli elementi figlio che rispettano i filtri.

Analogamente, la query sul blocco non restituisce valutazioni non integre o eventi dell'entità. Tali informazioni aggiuntive possono essere ottenute usando la query sull'integrità del cluster esistente.

### <a name="api"></a>API
Per ottenere il blocco di integrità del cluster, creare un oggetto `FabricClient` e chiamare il metodo [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) sul relativo **HealthManager**. È possibile passare [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) per descrivere i criteri di integrità e i filtri avanzati.

Il codice seguente permette di ottenere il blocco di integrità del cluster con filtri avanzati.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Il cmdlet per ottenere l'integrità del cluster è [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) .

Il codice seguente permette di ottenere solo i nodi in stato di errore, eccetto un nodo specifico che deve essere restituito sempre.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1
                               
                               NodeName              : _Node_1
                               HealthState           : Ok
                               
ApplicationHealthStateChunks : None
```

Il cmdlet seguente permette di ottenere il blocco di cluster con filtri dell'applicazione.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1
                               
                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1
                               
                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5
                               
                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Il cmdlet seguente restituisce tutte le entità distribuite su un nodo.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2
                               
                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
                               
                               
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Per ottenere il blocco di integrità di un cluster è possibile usare una [richiesta GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) o una [richiesta POST](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) che nel corpo include la descrizione di criteri di integrità.

## <a name="general-queries"></a>Query generali
Le query generali restituiscono l'elenco delle entità di Service Fabric di un tipo specificato. Le query vengono esposte tramite l'API con i metodi in **FabricClient.QueryManager**, tramite i cmdlet di PowerShell e REST. Queste query aggregano sottoquery da più componenti. Uno di questi è l' [archivio integrità](service-fabric-health-introduction.md#health-store), che popola lo stato di integrità aggregato per ogni risultato della query.  

> [!NOTE]
> Le query generali restituiscono lo stato di integrità aggregato dell'entità e non contengono i dati di integrità complessi. Se un'entità non è integra, è possibile procedere con query di integrità per ottenere tutte le informazioni di integrità, come gli eventi, gli stati di integrità degli elementi figlio e le valutazioni non integre.
>
>

Se le query generali restituiscono uno stato di integrità sconosciuto per un'entità, è possibile che l'archivio integrità non abbia dati completi sull'entità. È anche possibile che una sottoquery nell'archivio integrità non sia riuscita, ad esempio, si è verificato un errore di comunicazione o l'archivio integrità è stato limitato. Procedere con una query di integrità per l'entità. Se la sottoquery ha rilevato errori temporanei, ad esempio problemi di rete, questa query di completamento può riuscire. Può anche fornire altri dettagli dall'archivio integrità sui motivi che impediscono l'esposizione dell'entità.

Di seguito sono elencate le query che contengono **HealthState** per le entità:

* Elenco dei nodi: restituisce i nodi elencati nel cluster (di paging).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Elenco delle applicazioni: restituisce l'elenco di applicazioni nel cluster (di paging).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Elenco dei servizi: restituisce l'elenco dei servizi in un'applicazione (di paging).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Elenco delle partizioni: restituisce l'elenco delle partizioni in un servizio (di paging).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Elenco delle repliche: restituisce l'elenco delle repliche in una partizione (di paging).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Elenco delle applicazioni distribuite: restituisce l'elenco delle applicazioni distribuite in un nodo.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Elenco dei pacchetti di servizi distribuiti: restituisce l'elenco dei pacchetti di servizi in un'applicazione distribuita.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Alcune query restituiscono risultati di paging. Queste query restituiscono un elenco derivato da [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Se questi risultati non corrispondono a un messaggio, viene restituita solo una pagina e un ContinuationToken che tiene traccia del punto in cui l'enumerazione è stata arrestata. Continuare a chiamare la stessa query e passare il token di continuazione dalla query precedente per ottenere i risultati successivi.
>
>

### <a name="examples"></a>Esempi
Il codice seguente permette di ottenere le applicazioni non integre nel cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Il cmdlet seguente ottiene i dettagli dell'applicazione per l'applicazione fabric:/WordCount. Lo stato di integrità è di tipo avviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Il cmdlet seguente ottiene i servizi con stato di integrità di errore:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Aggiornamenti del cluster e dell'applicazione
Durante un aggiornamento monitorato del cluster e dell'applicazione, Service Fabric controlla l'integrità per garantire che lo stato sia integro e che rimanga tale. Se un'entità non è integra ed è stata valutata con i criteri di integrità configurati, l'aggiornamento applica criteri specifici dell'aggiornamento per determinare l'azione successiva. L'aggiornamento può essere sospeso per consentire l'interazione dell'utente, ad esempio per correggere le condizioni di errore o modificare i criteri, oppure può eseguire automaticamente il ripristino dello stato precedente di una versione funzionante.

Durante un aggiornamento del *cluster* è possibile ottenerne lo stato di aggiornamento. Quest'ultimo include valutazioni di non integrità, che puntano agli elementi non integri nel cluster. Se viene eseguito il rollback dell'aggiornamento a causa di problemi di integrità, lo stato di aggiornamento memorizza le cause di non integrità più recenti. Queste informazioni consentono agli amministratori di analizzare la causa dell'errore dopo il rollback o l'arresto dell'aggiornamento.

Analogamente, durante l'aggiornamento di un' *applicazione* , lo stato di aggiornamento dell'applicazione stessa include le eventuali valutazioni di non integrità.

Di seguito viene illustrato lo stato di aggiornamento dell’applicazione per un’applicazione fabric:/WordCount modificata. Un watchdog ha segnalato un errore in una delle repliche. Viene eseguito il rollback dell’aggiornamento poiché le verifiche dell’integrità non sono rispettate.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Per altre informazioni, vedere [Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Usare le valutazioni dell'integrità per risolvere i problemi
Ogni volta che si verifica un problema in cluster o in un'applicazione, osservare l'integrità del cluster o dell'applicazione per individuare il problema riscontrato. Le valutazioni di non integrità includono dettagli sulle cause che hanno attivato lo stato di non integrità corrente. È possibile eseguire il drill-down delle entità figlio non integre per identificare la causa radice.

Si consideri, ad esempio, un'applicazione non integra a causa di un errore segnalato in una delle relative repliche. Il cmdlet di Powershell seguente mostra le valutazioni di non integrità:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.
                                  
                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.
                                  
                                            Error event: SourceId='MyWatchdog', Property='Memory'.
                                  
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

È possibile esaminare la replica per ottenere altre informazioni:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> Le valutazioni non integre mostrano il primo motivo per cui l'entità restituisce lo stato di integrità corrente. Lo stato potrebbe essere attivato da vari altri eventi, che però non devono riflettersi nelle valutazioni. Per ottenere altre informazioni, è necessario eseguire il drill-down nelle entità di integrità per trovare tutti i report non integri nel cluster.
>
>

## <a name="next-steps"></a>Passaggi successivi
[Usare i report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Aggiungere report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)

[Creare report e verificare l'integrità dei servizi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorare e diagnosticare servizi in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento di un'applicazione di infrastruttura di servizi](service-fabric-application-upgrade.md)
