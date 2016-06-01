<properties
   pageTitle="Come visualizzare l'integrità aggregata delle entità di Service Fabric | Microsoft Azure"
   description="Descrive come eseguire una query dell'integrità aggregata delle entità di Azure Service Fabric, come visualizzarla e come valutarla con query di integrità e query generali."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/25/2016"
   ms.author="oanapl"/>

# Come visualizzare i report sull'integrità di Service Fabric
Service Fabric introduce un [modello di integrità](service-fabric-health-introduction.md) in cui sono incluse entità di integrità per le quali i componenti di sistema e i watchdog possono segnalare le condizioni locali sottoposte a monitoraggio. L'[archivio integrità](service-fabric-health-introduction.md#health-store) aggrega tutti i dati di integrità per determinare se le entità sono integre.

Per impostazione predefinita, il cluster viene popolato con report sull'integrità inviati dai componenti di sistema. Per altre informazioni, vedere [Usare i report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric offre diversi modi per ottenere l'integrità aggregata delle entità:

- [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) o altri strumenti di visualizzazione

- Query di integrità (tramite PowerShell, l'API o REST)

- Query generali che restituiscono un elenco di entità per le quali l'integrità costituisce una proprietà (tramite PowerShell, l'API o REST)

Per illustrare queste opzioni, si userà un cluster locale con 5 nodi. Dopo l'applicazione **fabric:/System** applicazione, disponibile per impostazione predefinita, vengono distribuite altre applicazioni. Una di queste è **fabric:/WordCount**. Questa applicazione contiene un servizio con stato configurato con 7 repliche. Poiché sono presenti solo cinque nodi, i componenti di sistema visualizzano un avviso per indicare la partizione è inferiore al numero di destinazioni.

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## Integrità in Esplora Infrastruttura di servizi
Esplora Infrastruttura di servizi fornisce una panoramica visiva del cluster. Nell'immagine seguente è possibile osservare quanto segue:

- L'applicazione **fabric:/WordCount** è di colore rosso (condizione di errore), perché per questa applicazione è stato segnalato un evento di errore da **MyWatchdog** per la proprietà **Availability**.

- Uno dei servizi di questa applicazione, **fabric:/WordCount/WordCountService** è di colore giallo (condizione di avviso). Come descritto in precedenza, il servizio è configurato con 7 repliche, che non possono essere tutte posizionate perché sono disponibili solo di 5 nodi. Anche se qui non è illustrata, la partizione del servizio è di colore giallo a causa del report di sistema. La partizione gialla avvia il servizio giallo.

- Il cluster è di colore rosso perché è rossa anche l'applicazione.

La valutazione usa criteri predefiniti del manifesto del cluster dal manifesto dell'applicazione. Questi sono criteri rigorosi e non tollerano errori.

Visualizzazione del cluster con Service Fabric Explorer:

![Visualizzazione del cluster con Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Ulteriori informazioni su [Esplora Infrastruttura di servizi](service-fabric-visualizing-your-cluster.md).

## Query relative all’integrità
Infrastruttura di servizi espone le query relative all’integrità per ognuno dei [tipi di entità](service-fabric-health-introduction.md#health-entities-and-hierarchy) supportati. È possibile accedere alle query tramite l'API (i metodi sono disponibili in **FabricClient.HealthManager**), i cmdlet di PowerShell e REST. Queste query restituiscono informazioni di integrità complete sull'entità, inclusi lo stato aggregato dell'integrità, gli eventi di integrità segnalati nell'entità, gli stati di integrità degli elementi figlio, se applicabili, e valutazioni di non integrità quando l'entità non è integra.

> [AZURE.NOTE] Un'entità integra viene restituita all'utente quando è popolata completamente nell'archivio integrità. L'entità deve essere attiva (non eliminata) e avere un report di sistema. Anche le entità padre nella catena della gerarchia devono avere report di sistema. Se una di queste condizioni non è soddisfatta, le query relative all'integrità restituiscono un'eccezione che illustra il motivo per cui l'entità non viene restituita.

Le query di integrità richiedono il passaggio nell'identificatore dell'entità, che dipende dal tipo di entità. Le query accettano parametri dei criteri di integrità facoltativi. Se non sono specificati, per la valutazione vengono usati i [criteri di integrità](service-fabric-health-introduction.md#health-policies) dal manifesto del cluster o dell'applicazione. Le query accettano anche filtri per restituire solo elementi figlio o eventi parziali, quelli che rispettano i filtri specificati.

> [AZURE.NOTE] Sul lato server vengono applicati i filtri di output, in modo che la dimensione della risposta al messaggio venga ridotta. È consigliabile usare i filtri di output per limitare i dati restituiti, invece di applicare filtri sul lato client.

L'integrità di un'entità include le informazioni seguenti:

- Lo stato di integrità aggregato dell'entità. Viene calcolato dall'archivio integrità in base ai report sull'integrità dell'entità, gli stati di integrità degli elementi figlio, se applicabili, e i criteri di integrità. Per altre informazioni, vedere [valutazione dell'integrità dell'entità](service-fabric-health-introduction.md#entity-health-evaluation).  

- Gli eventi di integrità dell'entità.

- La raccolta degli stati di integrità di tutti gli elementi figlio per le entità che possono avere elementi figlio. Gli stati di integrità contengono l'identificatore dell'entità e lo stato di integrità aggregato. Per ottenere l'integrità completa per un elemento figlio, chiamare l'integrità di query per il tipo di entità figlio e passare l'identificatore dell'elemento figlio.

- Le valutazioni non integre che puntano al report che ha attivato lo stato dell'entità, se l'entità non è integra.

## Get cluster health
Restituisce l'integrità dell'entità cluster e contiene gli stati di integrità di applicazioni e nodi (elementi figlio del cluster). Input:

- [Facoltativo] Criteri di integrità del cluster usati per valutare i nodi e gli eventi del cluster.

- [Facoltativo] Mappa dei criteri di integrità dell'applicazione con criteri di integrità usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per eventi, nodi e applicazioni che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Tutti gli eventi, i nodi e le applicazioni vengono usati per valutare l'integrità aggregata dell'entità, indipendentemente dal filtro.

### API
Per ottenere l'integrità del cluster, creare un oggetto `FabricClient` e chiamare il metodo [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) sul relativo **HealthManager**.

La sintassi seguente consente di ottenere l’integrità del cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

La sintassi seguente consente di ottenere l'integrità del cluster usando criteri di integrità del cluster personalizzati e filtri per nodi e applicazioni. Viene creato [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx), che contiene tutti i dati di input.

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
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### PowerShell
Il cmdlet per ottenere l'integrità del cluster è [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

Lo stato del cluster è 5 nodi, l'applicazione di sistema e fabric:/WordCount sono configurate come descritto in precedenza.

I cmdlet seguenti ottengono l'integrità del cluster con criteri di integrità predefiniti. Lo stato di integrità aggregato è di tipo avviso, come lo è stato dell'applicazione fabric:/WordCount. Notare come le valutazioni non integre forniscano dettagli sulle condizioni che hanno attivato lo stato di integrità aggregato.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

Il cmdlet PowerShell seguente ottiene lo stato di integrità del cluster con i criteri dell'applicazione personalizzati. Il cmdlet filtra i risultati per ottenere solo le applicazioni e i nodi con stato di errore o avviso. Di conseguenza, non verranno restituiti nodi, perché sono tutti integri. Solo l'applicazione fabric:/WordCount rispetta il filtro delle applicazioni. Poiché i criteri personalizzati specificano di considerare gli avvisi come errori per l'applicazione fabric:/WordCount, questa viene valutata in stato di errore e lo stesso accade per il cluster.

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## Get node health
Restituisce lo stato di un'entità nodo e contiene gli eventi di integrità segnalati sul nodo. Input:

- [Obbligatorio] Nome del nodo che identifica il nodo.

- [Facoltativo] Impostazioni dei criteri di integrità usate per valutare l'integrità.

- [Facoltativo] Filtri per gli eventi che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità di entità aggregate, vengono usati tutti gli eventi indipendentemente dal filtro.

### API
Per ottenere l'integrità tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) sul relativo HealthManager.

La sintassi seguente ottiene l'integrità del nodo per il nome del nodo specificato:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

La sintassi seguente ottiene lo stato del nodo per il nome nodo specificato e passa un filtro eventi e i criteri personalizzati tramite [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### PowerShell
Il cmdlet per ottenere l'integrità del nodo è [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx). I cmdlet seguenti ottengono l'integrità del nodo con criteri di integrità predefiniti:

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Il cmdlet seguente ottiene lo stato di tutti i nodi del cluster:

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

## Ottieni lo stato dell'integrità dell'applicazione
Restituisce lo stato di un'entità applicazione. Contiene gli stati di integrità dell'applicazione distribuita e gli elementi figlio del servizio. Input:

- [Obbligatorio] Nome dell'applicazione (URI) che identifica l'applicazione.

- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per eventi, servizi e applicazioni distribuite che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Tutti gli eventi, i servizi e le applicazioni distribuite vengono usati per valutare l'integrità aggregata dell'entità, indipendentemente dal filtro.

### API
Per ottenere l'integrità dell'applicazione, creare un oggetto `FabricClient` e chiamare il metodo [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) sul relativo HealthManager.

La sintassi seguente ottiene l'integrità dell'applicazione per il nome dell'applicazione (URI) specificato.

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

La sintassi seguente ottiene l'integrità dell'applicazione per il nome dell'applicazione (URI) specificato, indicando filtri e criteri personalizzati tramite [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx).

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

### PowerShell
Il cmdlet per ottenere l'integrità dell'applicazione è [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

Il cmdlet seguente restituisce l'integrità dell'applicazione **fabric:/WordCount**:

```powershell
PS c:>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Il cmdlet PowerShell seguente passa i criteri personalizzati. Filtra anche gli elementi figlio e gli eventi.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## Get service health
Restituisce l'integrità di un'entità servizio. Contiene gli stati di integrità della partizione. Input:

- [Obbligatorio] Nome del servizio (URI) che identifica il servizio.

- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per eventi e partizioni che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi e tutte le partizioni, indipendentemente dal filtro.

### API
Per ottenere lo stato del servizio tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) sul relativo HealthManager.

L'esempio seguente ottiene l'integrità di un servizio con il nome di servizio (URI) specificato:

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

L'esempio seguente ottiene l'integrità del servizio per il nome del servizio (URI) specificato, indicando filtri e criteri personalizzati tramite [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### PowerShell
Il cmdlet per ottenere l'integrità del servizio è [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

Il cmdlet seguente ottiene l'integrità del servizio usando i criteri di integrità predefiniti:

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

## Get partition health
Restituisce l'integrità di un'entità partizione. Contiene gli stati di integrità della replica. Input:

- [Obbligatorio] ID partizione (GUID) che identifica la partizione.

- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per eventi e repliche che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi e tutte le repliche, indipendentemente dal filtro.

### API
Per ottenere lo stato della partizione tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) sul relativo HealthManager. Per specificare i parametri facoltativi, creare [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### PowerShell
Il cmdlet per ottenere l'integrità della partizione è [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

Il cmdlet seguente ottiene l'integrità di tutte le partizioni del servizio **fabric:/WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## Get replica health
Restituisce l'integrità di una replica di un servizio con stato o di un'istanza di un servizio senza stato. Input:

- [Obbligatorio] ID partizione (GUID) e ID replica che identificano la replica.

- [Facoltativo] Parametri dei criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per gli eventi che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità di entità aggregate, vengono usati tutti gli eventi indipendentemente dal filtro.

### API
Per ottenere l'integrità della replica tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) sul relativo HealthManager. Per specificare parametri avanzati, usare [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### PowerShell
Il cmdlet per ottenere l'integrità della replica è [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

Il cmdlet seguente ottiene l'integrità della replica primaria per tutte le partizioni del servizio.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## Ottieni lo stato dell'integrità delle applicazioni distribuite.
Restituisce l'integrità di un'applicazione distribuita in un'entità nodo. Contiene gli stati di integrità del pacchetto di servizi distribuito. Input:

- [Obbligatorio] Nome dell'applicazione (URI) e nome del nodo (stringa) che identificano l'applicazione distribuita

- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per eventi e pacchetti di servizi distribuiti che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Tutti gli eventi e i pacchetti di servizi distribuiti vengono usati per valutare l'integrità aggregata dell'entità, indipendentemente dal filtro.

### API
Per ottenere l'integrità in un'applicazione distribuita in un nodo tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) sul relativo HealthManager. Per specificare i parametri facoltativi, usare [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### PowerShell
Il cmdlet per ottenere l'integrità dell'applicazione distribuita è [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx). Per sapere dove viene distribuita un'applicazione, eseguire [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) e osservare gli elementi figlio dell'applicazione distribuita.

Il cmdlet seguente ottiene l'integrità dell'applicazione **fabric:/WordCount** distribuita nel nodo **\_Node\_2**.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## Get deployed service package health
Restituisce l'integrità di un'entità pacchetto di servizi distribuito. Input:

- [Obbligatorio] Nome dell'applicazione (URI), nome del nodo (stringa) e nome del manifesto del servizio (stringa) che identificano il pacchetto di servizi distribuito.

- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per gli eventi che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità di entità aggregate, vengono usati tutti gli eventi indipendentemente dal filtro.

### API
Per ottenere l'integrità di un pacchetto del servizio distribuito tramite l'API, creare un oggetto `FabricClient` e chiamare il metodo [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) sul relativo HealthManager. Per specificare i parametri facoltativi, usare [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### PowerShell
Il cmdlet per ottenere l'integrità del pacchetto del servizio distribuito è [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx). Per vedere dove viene distribuita un'applicazione, eseguire [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) e osservare le applicazioni distribuite. Per vedere quali pacchetti di servizi sono contenuti in un'applicazione, esaminare gli elementi figlio del pacchetto del servizio distribuito nell'output di [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx).

Il cmdlet seguente ottiene l'integrità del pacchetto del servizio **WordCountServicePkg** dell'applicazione **fabric:/WordCount** distribuita nel nodo **\_Node\_2**. L'entità include report **System.Hosting** per l'attivazione corretta del pacchetto del servizio e del punto di ingresso, nonché la registrazione corretta del tipo di servizio.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## Query sul blocco di integrità
Le query sul blocco di integrità possono restituire gli elementi figlio di un cluster a più livelli (in modo ricorsivo) per ogni filtro di input. Supporta gli elementi figlio avanzati che offrono notevole flessibilità per esprimere gli elementi figlio specifici da restituire, indicati mediante il rispettivo identificatore univoco o un altro identificatore di gruppo e/o lo stato di integrità. Per impostazione predefinita, non sono inclusi gli elementi figlio, a differenza dei comandi relativi all'integrità che includono sempre gli elementi figlio di primo livello.

Le [query sull'integrità](service-fabric-view-entities-aggregated-health.md#health-queries) restituiscono solo gli elementi figlio di primo livello dell'entità specificata per ogni filtro necessario. Per ottenere gli elementi figlio di un elemento figlio, gli utenti devono chiamare altre API di integrità per ogni entità di interesse. Analogamente, per ottenere l'integrità di entità specifiche, gli utenti devono chiamare un'API per ogni entità desiderata. I filtri avanzati per le query sul blocco consentono agli utenti di richiedere più elementi di interesse in una query, riducendo al minimo le dimensioni del messaggio e il numero di messaggi.

Il vantaggio della query sul blocco consiste nel fatto che gli utenti possono ottenere lo stato dell'integrità per più entità del cluster, potenzialmente tutte le entità del cluster a partire dalla radice necessaria, in una chiamata. È possibile esprimere query sull'integrità complesse, ad esempio:

- Restituzione solo delle applicazioni con errore e inclusione di tutti i servizi con avviso|errore per queste applicazioni. Per i servizi restituiti, inclusione di tutte le partizioni.

- Restituzione solo dell'integrità di 4 applicazioni, specificate in base ai rispettivi nomi.

- Restituzione solo dell'integrità delle applicazioni con un tipo di applicazione desiderato.

- Restituzione di tutte le entità distribuite su un nodo. Restituisce tutte le applicazioni, tutte le applicazioni distribuite sul nodo specificato e tutti i pacchetti di servizio distribuiti nel nodo.

- Restituzione di tutte le repliche con errore. Restituzione di tutte le applicazioni, i servizi, le partizioni e delle sole repliche con errore.

- Restituzione di tutte le applicazioni. Per un servizio specificato, inclusione di tutte le partizioni.

La query sul blocco di integrità è attualmente esposta solo per l'entità del cluster. Restituisce un blocco di integrità del cluster che contiene:

- Lo stato di integrità aggregato del cluster.

- L'elenco del blocco dello stato di integrità dei nodi che rispettano i filtri di input.

- L'elenco del blocco dello stato di integrità delle applicazioni che rispettano i filtri di input. Ogni blocco dello stato di integrità dell'applicazione contiene un elenco di blocchi con tutti i servizi che rispettano i filtri di input e un elenco di blocchi con tutte le applicazioni distribuite che rispettano i filtri. Lo stesso vale per gli elementi figlio dei servizi e delle applicazioni distribuite. In questo modo, tutte le entità nel cluster possono essere potenzialmente restituite se richiesto, in modo gerarchico.

### Query sul blocco di integrità del cluster
Restituisce lo stato dell'entità del cluster e contiene blocchi di stato dell'integrità gerarchici che includono gli elementi figlio necessari. Input:

- [Facoltativo] Criteri di integrità del cluster usati per valutare i nodi e gli eventi del cluster.

- [Facoltativo] Mappa dei criteri di integrità dell'applicazione con criteri di integrità usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per i nodi e per le applicazioni che specificano le voci di interesse e da restituire nel risultato. I filtri sono specifici per un'entità/un gruppo di entità o sono applicabili a tutte le entità a tale livello. L'elenco di filtri può contenere un filtro generale e/o un filtro per identificatori specifici per entità dettagliate restituite dalla query. Se l'elenco è vuoto, gli elementi figlio non vengono restituiti per impostazione predefinita. Altre informazioni sui filtri per [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) e [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx). I filtri dell'applicazione possono specificare in modo ricorsivo filtri avanzati per gli elementi figlio.

I risultati del blocco includono gli elementi figlio che rispettano i filtri.

Analogamente, la query sul blocco non restituisce valutazioni non integre o eventi dell'entità. Questi elementi possono essere ottenuti usando la query sull'integrità del cluster.

### API
Per ottenere il blocco di integrità del cluster, creare un oggetto `FabricClient` e chiamare il metodo [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) sul relativo **HealthManager**. È possibile passare [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) per descrivere i criteri di integrità e i filtri avanzati.

Il comando seguente ottiene il blocco di integrità del cluster con filtri avanzati.

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

### PowerShell
Il cmdlet per ottenere l'integrità del cluster è [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx). Connettersi prima di tutto al cluster con il cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

Il comando seguente ottiene i nodi solo se si è verificato un errore, ad eccezione di un nodo specifico che deve essere restituito sempre.

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Il cmdlet seguente restituisce il blocco di cluster con filtri dell'applicazione.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
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

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

Il cmdlet seguente restituisce tutte le entità distribuite su un nodo.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
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
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

## Query generali
Le query generali restituiscono l'elenco delle entità di Service Fabric di un tipo specificato. Le query vengono esposte tramite l'API, con metodi su **FabricClient.QueryManager**, i cmdlet di PowerShell e REST. Queste query aggregano sottoquery da più componenti. Uno di questi è l'[archivio integrità](service-fabric-health-introduction.md#health-store), che popola lo stato di integrità aggregato per i risultati di ogni query.

> [AZURE.NOTE] Le query generali restituiscono lo stato di integrità aggregato dell'entità e non contengono i dati di integrità complessi. Se un'entità non è integra, è possibile procedere con query di integrità per ottenere tutte le informazioni di integrità, come gli eventi, gli stati di integrità degli elementi figlio e le valutazioni non integre.

Se le query generali restituiscono uno stato di integrità sconosciuto per un'entità, è possibile che l'archivio integrità non abbia dati completi sull'entità. È anche possibile che una sottoquery nell'archivio integrità non sia riuscita, ad esempio, si è verificato un errore di comunicazione o l'archivio integrità è stato limitato. Procedere con una query di integrità per l'entità. Se la sottoquery ha rilevato errori temporanei, ad esempio problemi di rete, questa query di completamento può riuscire. Può anche fornire altri dettagli dall'archivio integrità sui motivi che impediscono l'esposizione dell'entità.

Di seguito sono elencate le query che contengono **HealthState** per le entità:

- Elenco di nodi: restituisce i nodi elencati nel cluster (di paging).
  - API: [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell: Get-ServiceFabricNode
- Elenco applicazioni: restituisce l'elenco di applicazioni nel cluster (di paging).
  - API: [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricApplication
- Elenco servizi: restituisce l'elenco dei servizi in un'applicazione (di paging).
  - API: [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell: Get-ServiceFabricService
- Elenco partizioni: restituisce l'elenco delle partizioni in un servizio (di paging).
  - API: [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell: Get-ServiceFabricPartition
- Elenco repliche: restituisce l'elenco delle repliche in una partizione (di paging).
  - API: [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell: Get-ServiceFabricReplica
- Elenco applicazioni distribuite: restituisce l'elenco delle applicazioni distribuite in un nodo.
  - API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication
- Elenco pacchetti di servizi distribuiti: restituisce l'elenco dei pacchetti di servizi in un'applicazione distribuita
  - API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] Alcune query restituiscono risultati di paging. Queste query restituiscono un elenco derivato da [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx). Se questi risultati non corrispondono a un messaggio, viene restituita solo una pagina e ContinuationToken è impostato in modo da tenere traccia del punto in cui si è arrestata l'enumerazione. L'utente deve continuare a chiamare la stessa query e passare il token di continuazione dalla query precedente per ottenere i risultati successivi.

### esempi

Nell’esempio seguente si ottengono le applicazioni non integre nel cluster:

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

Il cmdlet seguente ottiene i servizi con lo stato di integrità di tipo avviso:

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## Aggiornamenti del cluster e dell'applicazione
Durante un aggiornamento monitorato del cluster e dell'applicazione, Service Fabric controlla l'integrità per garantire che lo stato sia integro e che rimanga tale. Se un'entità non è integra ed è stata valutata con i criteri di integrità configurati, l'aggiornamento applica criteri specifici dell'aggiornamento per determinare l'azione successiva. L'aggiornamento può essere sospeso per consentire l'interazione dell'utente, ad esempio per correggere le condizioni di errore o modificare i criteri, oppure può eseguire automaticamente il ripristino dello stato precedente di una versione funzionante.

Durante un aggiornamento del *cluster*, è possibile ottenerne lo stato di aggiornamento. Sono incluse le valutazioni non integre, che puntano a elementi non integri nel cluster. Se viene eseguito il ripristino dello stato precedente dell'aggiornamento a causa di problemi di integrità, lo stato dell'aggiornamento conserverà i motivi più recenti che riguardano la non integrità. In questo modo, gli amministratori possono analizzare la causa dell'errore.

Analogamente, durante l'aggiornamento dell'*applicazione*, lo stato di aggiornamento dell'applicazione stessa include le eventuali valutazioni di non integrità.

Di seguito viene illustrato lo stato di aggiornamento dell’applicazione per un’applicazione fabric:/WordCount modificata. Un watchdog ha segnalato un errore in una delle repliche. Viene eseguito il rollback dell’aggiornamento poiché le verifiche dell’integrità non sono rispettate.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
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

## Usare le valutazioni dell'integrità per risolvere i problemi
Ogni volta che si verifica un problema in cluster o in un'applicazione, osservare l'integrità del cluster o dell'applicazione per individuare il problema riscontrato. Le valutazioni di non integrità mostreranno i dettagli ciò che ha attivato lo stato non integro corrente. È possibile eseguire il drill-down delle entità figlio non integre per identificare la causa radice.

> [AZURE.NOTE] Le valutazioni non integre mostrano il primo motivo per cui l'entità restituisce lo stato di integrità corrente. È possibile che questo stato sia attivato da altri eventi, ma non verranno riflessi nelle valutazioni. È necessario eseguire il drill-down nelle entità di integrità per trovare tutti i report non integri nel cluster.

## Passaggi successivi
[Usare i report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Aggiungere report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)

[Monitorare e diagnosticare servizi in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento di un'applicazione di infrastruttura di servizi](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0518_2016-->