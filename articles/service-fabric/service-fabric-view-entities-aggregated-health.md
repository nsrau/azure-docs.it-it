<properties
   pageTitle="Modalità di visualizzazione dello stato aggregato delle entità Infrastruttura di servizi di Azure | Microsoft Azure"
   description="Viene descritto come eseguire una query dello stato aggregato delle entità Infrastruttura di servizi di Azure, come visualizzarlo e come valutarlo, tramite query dello stato e query generali."
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
   ms.date="10/23/2015"
   ms.author="oanapl"/>

# Come visualizzare i report di integrità di Infrastruttura di servizi
Infrastruttura di servizi introduce un [modello di integrità](service-fabric-health-introduction.md) in cui sono incluse entità di integrità per le quali i componenti di sistema e i watchdog possono segnalare le condizioni locali di cui si sta effettuando il monitoraggio. [Archivio integrità](service-fabric-health-introduction.md#health-store) aggrega tutti i dati di integrità per determinare se le entità sono integre.

Per impostazione predefinita, il cluster viene popolato con report sull’integrità inviati dai componenti del sistema. Per ulteriori informazioni, vedere [Utilizzo dei report sull’integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Infrastruttura di servizi offre diversi modi per ottenere l’integrità delle entità aggregate

- [Esplora Infrastruttura di servizi](service-fabric-visualizing-your-cluster.md) o altri strumenti di visualizzazione

- Query relative all’integrità (tramite Powerhsell/API/REST)

- Query generali che restituiscono un elenco di entità per le quali l’integrità costituisce una proprietà (tramite Powershell/API/REST)

Per illustrare queste opzioni, utilizziamo un cluster locale con **5 nodi**. Accanto all’applicazione **fabric:/System** (presente per impostazione predefinita), sono distribuite altre applicazioni, tra cui **fabric:/WordCount**. Questa applicazione contiene un servizio con stato configurato con 7 repliche. Poiché sono presenti solo 5 nodi, i componenti di sistema contrassegneranno che la partizione è al di sotto del conteggio di destinazione con un avviso.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Integrità in Esplora Infrastruttura di servizi
Esplora Infrastruttura di servizi fornisce una panoramica visiva del cluster. Nella figura seguente, è possibile osservare quanto segue:

- L’applicazione **fabric:/WordCount** è di colore rosso (in corrispondenza della condizione di errore) in quanto per questa applicazione è stato segnalato un evento di errore da MyWatchdog per la proprietà Availability.

- Uno dei servizi di questa applicazione, **fabric:/WordCount/WordCount.Service** è di colore giallo (in corrispondenza della condizione di avviso). Come descritto in precedenza, il servizio è configurato con 7 repliche, che non possono essere tutte posizionate (poiché disponiamo solo di 5 nodi). Sebbene non sia mostrato qui, la partizione del servizio è di colore giallo a causa del report relativo al sistema. La partizione gialla avvia il servizio giallo.

- Il **cluster** è di colore rosso in quanto è rossa anche l’applicazione.

La valutazione utilizza criteri predefiniti del manifesto del cluster e del manifesto dell'applicazione, che sono criteri rigorosi (non tollerano errori).

![Vista del cluster con ServiceFabricExplorer.][1]

Vista del cluster con ServiceFabricExplorer.

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE]Ulteriori informazioni su [Esplora Infrastruttura di servizi](service-fabric-visualizing-your-cluster.md).

## Query relative all’integrità
Infrastruttura di servizi espone le query relative all’integrità per ognuno dei [tipi di entità](service-fabric-health-introduction.md#health-entities-and-hierarchy) supportati. È possibile accedere alle query tramite l'API (metodi su FabricClient.HealthManager), i cmdlet Powershell e REST. Queste query restituiscono informazioni di stato complete sull'entità, inclusi lo stato aggregato dell’integrità, gli eventi di integrità segnalati nell'entità, gli stati di integrità degli elementi figlio (se applicabili) e valutazioni non integre quando l'entità non è integra.

> [AZURE.NOTE]Un'entità di integrità viene restituita all'utente quando viene popolata completamente in Archivio integrità: l'entità dispone di un report di sistema, è attiva (non eliminata) e le entità padre nella catena della gerarchia dispongono di report del sistema. Se una di queste condizioni non è soddisfatta, le query relative all’integrità restituiscono un'eccezione che illustra il motivo per cui l'entità non viene restituita.

Le query di integrità richiedono il passaggio dell'identificatore dell'entità, che dipende dal tipo di entità. Esse accettano parametri dei criteri di integrità opzionali. Se non specificato, i [criteri di integrità](service-fabric-health-introduction.md#health-policies) dal manifesto del cluster o dell’applicazione vengono utilizzati per la valutazione. Essi inoltre accettano filtri per restituire unicamente elementi figlio o eventi parziali, quelli che rispettano i filtri specificati.

> [AZURE.NOTE]Sul lato server vengono applicati i filtri di output, in modo che la dimensione della risposta al messaggio venga ridotta. È consigliabile utilizzare i filtri per limitare i dati restituiti invece di applicare filtri sul lato client.

Uno stato entità contiene le seguenti informazioni:

- Lo stato di integrità aggregato dell'entità. Viene calcolato da Archivio integrità in base ai rapporti di integrità di entità, gli stati di integrità degli elementi figlio (se applicabili) e i criteri di integrità. Ulteriori informazioni sulla [valutazione dell’integrità dell’entità](service-fabric-health-introduction.md#entity-health-evaluation).  

- Gli eventi di integrità dell'entità.

- Per le entità che possono disporre di elementi figlio, la raccolta degli stati di integrità per tutti gli elementi figlio. Gli stati di integrità contengono l'identificatore dell'entità e lo stato di integrità aggregato. Per ottenere l’integrità completa per un elemento figlio, chiamare l'integrità di query per il tipo di entità figlio, passando l'identificatore dell'elemento figlio.

- Se l'entità non è integra, le valutazioni non integre che puntano al report che ha attivato lo stato dell'entità.

## Get cluster health
Restituisce l’integrità dell’entità cluster. Contiene gli stati di integrità di applicazioni e nodi (elementi figlio del cluster). Input:

- [facoltativo] Mappa di criteri di integrità applicazione con criteri di integrità usati per sostituire i criteri del manifesto dell’applicazione.

- [facoltativo] Filtro per restituire solo eventi, nodi, applicazioni con un determinato stato di integrità (ad esempio, restituire solo errori o avvisi e così via).

### API
Per ottenere l'integrità del cluster, creare un FabricClient e chiamare il metodo GetClusterHealthAsync sul relativo HealthManager.

La sintassi seguente consente di ottenere l’integrità del cluster:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

La sintassi seguente consente di ottenere l’integrità del cluster utilizzando criteri di integrità del cluster personalizzati e filtri per nodi e applicazioni. Si noti che viene creato System.Fabric.Description.ClusterHealthQueryDescription, che contiene tutti i dati di input.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità del cluster è Get-ServiceFabricClusterHealth. Innanzitutto, effettuare la connessione al cluster con il cmdlet Connect-ServiceFabricCluster. Stato del cluster: 5 nodi, applicazione di sistema e fabric: / WordCount configurato come descritto in precedenza.

I cmdlet seguenti ottengono l'integrità del cluster con criteri di integrità predefiniti. Lo stato di integrità aggregato è Avviso, poiché lo stato dell’applicazione fabric:/WordCount è Avviso. È opportuno notare come le valutazioni non integre mostrino con i dettagli la condizione che ha attivato lo stato di integrità aggregato.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

Il seguente cmdlet Powershell ottiene lo stato di integrità del cluster con il criterio di applicazione personalizzato. Il cmdlet filtra i risultati per ottenere solo le applicazioni e i nodi in stato di errore o avviso. Di conseguenza, non verrà restituito alcun nodo, come se fossero tutti integri. Solo l’applicazione fabric:/WordCount rispetta il filtro delle applicazioni. Poiché il criterio personalizzato specifica di prendere in considerazione l'avviso come errore per l’applicazione fabric:/WordCount, l’applicazione viene valutata in stato di errore e lo stesso accade per il cluster.

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## Get node health
Restituisce lo stato di un'entità nodo. Contiene gli eventi di integrità segnalati sul nodo. Input:

- [obbligatorio] Il nome nodo che identifica il nodo.

- [facoltativo] Impostazioni dei criteri di integrità usate per valutare l’integrità.

- [facoltativo] Filtro per restituire solo gli eventi con un determinato stato di integrità (ad esempio, restituire solo errori o avvisi e così via).

### API
Per ottenere l'integrità tramite l'API, creare un FabricClient e chiamare il metodo GetNodeHealthAsync sul relativo HealthManager.

La sintassi di seguito consente di ottenere l’integrità del nodo per il nome nodo specificato.

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

La sintassi seguente consente di ottenere lo stato del nodo per il nome nodo specificato, passando il filtro eventi e il criterio personalizzato tramite System.Fabric.Description.NodeHealthQueryDescription.

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità del nodo è Get-ServiceFabricNodeHealth. Innanzitutto, effettuare la connessione al cluster con il cmdlet Connect-ServiceFabricCluster. Il cmdlet seguente consente di ottenere l'integrità del nodo con criteri di integrità predefiniti.

```powershell
PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

Il cmdlet seguente consente di ottenere lo stato di tutti i nodi del cluster.

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## Ottieni lo stato dell'integrità dell'applicazione
Restituisce lo stato di un'entità applicazione. Contiene gli stati di integrità dell'applicazione distribuita e gli elementi figlio del servizio. Input:

- [obbligatorio] Nome applicazione (Uri) che identifica l’applicazione

- [facoltativo] Criteri di integrità applicazione usati per sostituire i criteri del manifesto dell’applicazione.

- [facoltativo] Filtro per restituire solo eventi, servizi, applicazioni distribuite con un determinato stato di integrità (ad esempio, restituire solo errori, avvisi e così via).

### API
Per ottenere l’integrità dell’applicazione, creare un FabricClient e chiamare il metodo GetApplicationHealthAsync sul relativo HealthManager.

La sintassi seguente consente di ottenere lo stato dell'applicazione per l'Uri nome dell'applicazione specificato.

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

La sintassi seguente consente di ottenere lo stato dell'applicazione per l'Uri nome applicazione specificato, indicando filtri e criteri personalizzati tramite System.Fabric.Description.ApplicationHealthQueryDescription.

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
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### PowerShell
Il cmdlet per ottenere lo stato dell’applicazione è Get-ServiceFabricApplicationHealth. Innanzitutto, effettuare la connessione al cluster con il cmdlet Connect-ServiceFabricCluster.

Il cmdlet seguente restituisce lo stato dell’applicazione fabric:/WordCount.

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

Il seguente Powershell passa i criteri personalizzati e filtra elementi figlio ed eventi.

```powershell
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## Get service health
Restituisce lo stato di un'entità di servizio. Contiene gli stati di integrità della partizione. Input:

- [obbligatorio] Nome del servizio (Uri) che identifica il servizio
- [facoltativo] Criteri di integrità applicazione usati per sostituire i criteri del manifesto dell’applicazione.
- [facoltativo] Filtro per restituire solo gli eventi e le partizioni con un determinato stato di integrità (ad esempio, restituire solo errori o avvisi e così via).

### API
Per ottenere lo stato del servizio tramite l'API, creare un FabricClient e chiamare il metodo GetServiceHealthAsync sul relativo HealthManager.

Nell'esempio seguente viene ottenuto lo stato di un servizio con il nome di servizio specificato (Uri):

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

L’esempio seguente consente di ottenere lo stato del servizio per l'Uri del nome di servizio specificato, indicando filtri e criteri personalizzati tramite System.Fabric.Description.ServiceHealthQueryDescription.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità del servizio è Get-ServiceFabricServiceHealth. Innanzitutto, effettuare la connessione al cluster con il cmdlet Connect-ServiceFabricCluster.

Il cmdlet seguente consente di ottenere l'integrità del servizio utilizzando i criteri di integrità predefiniti.

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## Get partition health
Restituisce lo stato di un'entità partizione. Contiene gli stati di integrità della replica. Input:

- [obbligatorio] ID partizione (Guid) che identifica la partizione

- [facoltativo] Criteri di integrità applicazione usati per sostituire i criteri del manifesto dell’applicazione.

- [facoltativo] Filtro per restituire solo gli eventi e le repliche con un determinato stato di integrità (ad esempio, restituire solo errori o avvisi e così via).

### API
Per ottenere lo stato della partizione tramite l'API, creare un FabricClient e chiamare il metodo GetPartitionHealthAsync sul relativo HealthManager. Per specificare i parametri facoltativi, creare System.Fabric.Description.PartitionHealthQueryDescription.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
Il cmdlet per ottenere lo stato della partizione è Get-ServiceFabricPartitionHealth. Innanzitutto, effettuare la connessione al cluster con il cmdlet Connect-ServiceFabricCluster.

Il cmdlet seguente consente di ottenere lo stato di tutte le partizioni del servizio di conteggio delle parole.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## Get replica health
Restituisce lo stato di una replica.

- [obbligatorio] ID partizione (Guid) e ID replica che identificano la replica

- [facoltativo] Parametri dei criteri di integrità applicazione usati per sostituire i criteri del manifesto dell’applicazione.

- [facoltativo] Filtro per restituire solo gli eventi con un determinato stato di integrità (ad esempio, restituire solo errori o avvisi e così via).

### API
Per ottenere l’integrità della replica tramite l'API, creare un FabricClient e chiamare il metodo GetReplicaHealthAsync sul relativo HealthManager. Specificare i parametri avanzati con System.Fabric.Description.ReplicaHealthQueryDescription.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità della replica è Get-ServiceFabricReplicaHealth. Innanzitutto, effettuare la connessione al cluster con il cmdlet Connect-ServiceFabricCluster.

Il cmdlet seguente consente di ottenere l’integrità della replica primaria per tutte le partizioni del servizio.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Ottieni lo stato dell'integrità delle applicazioni distribuite.
Restituisce l’integrità di un’applicazione distribuita in un’entità nodo. Contiene gli stati di integrità del pacchetto di servizi distribuito. Input:

- [obbligatorio] Nome applicazione (Uri) e nome nodo (stringa) che identificano l’applicazione distribuita

- [facoltativo] Criteri di integrità applicazione usati per sostituire i criteri del manifesto dell’applicazione.

- [facoltativo] Filtro per restituire solo eventi, pacchetti di servizi distribuiti con un determinato stato di integrità (ad esempio, restituire solo errori, avvisi e così via).

### API
Per visualizzare l'integrità in un'applicazione distribuita in un nodo tramite API, creare un FabricClient e chiamare il metodo GetDeployedApplicationHealthAsync sul relativo HealthManager. Per specificare i parametri facoltativi, utilizzare System.Fabric.Description.DeployedApplicationHealthQueryDescription.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
Il cmdlet per ottenere lo stato dell’applicazione distribuita è Get-ServiceFabricDeployedApplicationHealth. Innanzitutto, effettuare la connessione al cluster con il cmdlet Connect-ServiceFabricCluster. Per sapere dove viene distribuita un'applicazione, eseguire Get-ServiceFabricApplicationHealth e osservare gli elementi figlio dell’applicazione distribuita.

Il cmdlet seguente ottiene lo stato dell’applicazione fabric:/WordCount distribuita nel nodo Node.1.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Get deployed service package health
Restituisce lo stato di un'entità di pacchetto di servizi distribuito. Input:

- [obbligatorio] Nome applicazione (Uri), nome nodo (stringa) e nome del manifesto del servizio (stringa) che identificano il pacchetto di servizi distribuito

- [facoltativo] Criteri di integrità applicazione usati per sostituire i criteri del manifesto dell’applicazione.

- [facoltativo] Filtro per restituire solo gli eventi con un determinato stato di integrità (ad esempio, restituire solo errori o avvisi e così via).

### API
Per ottenere l'integrità di un pacchetto di servizi distribuito tramite API, creare un FabricClient e chiamare il metodo GetDeployedServicePackageHealthAsync sul relativo HealthManager.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità del pacchetto di servizi distribuito è Get-ServiceFabricDeployedServicePackageHealth. Innanzitutto, effettuare la connessione al cluster con il cmdlet Connect-ServiceFabricCluster. Per vedere dove viene distribuita un'applicazione, eseguire Get-ServiceFabricApplicationHealth e osservare le applicazioni distribuite. Per vedere quali pacchetti di servizi sono contenuti in un'applicazione, esaminare gli elementi figlio del pacchetto di servizi nell’output Get-ServiceFabricDeployedApplicationHealth.

Il cmdlet seguente ottiene lo stato del pacchetto di servizi WordCount.Service dell’applicazione fabric:/WordCount distribuita nel nodo Node.1. L'entità dispone di report System.Hosting per l’attivazione corretta del pacchetto di servizi e del punto di immissione e la registrazione corretta del tipo di servizio.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Query generali
Le query generali restituiscono l'elenco delle entità Infrastruttura di servizi del tipo specificato. Le query vengono esposte tramite l'API (metodi in FabricClient.QueryManager), i cmdlet Powershell e REST. Queste query aggregano sottoquery da più componenti. Uno di questi è l'[Archivio integrità](service-fabric-health-introduction.md#health-store), che popola lo stato di integrità aggregato per i risultati di ciascuna query.

> [AZURE.NOTE]Le query generali restituiscono lo stato di integrità aggregato dell’entità e non contengono i dati di integrità in formato RTF. Se un'entità non è integra, è possibile procedere con query di integrità per ottenere tutte le informazioni di integrità, come gli eventi, gli stati di integrità degli elementi figlio e le valutazioni non integre.

Se le query generali restituiscono lo stato di integrità Sconosciuto per un'entità, è possibile che l'Archivio integrità non disponga di dati completi sull'entità oppure che la sottoquery all'Archivio integrità non sia riuscita (ad esempio, errore di comunicazione, archivio integrità limitato e così via). Procedere con una query di integrità per l'entità. Questa operazione può avere esito positivo, se la sottoquery ha rilevato errori temporanei (ad esempio, problemi di rete), o fornire maggiori dettagli sul motivo per cui l'entità non è esposta dall'Archivo integrità.

Di seguito sono elencate le query che contengono HealthState per le entità:

- Elenco di nodi. Restituisce i nodi di elenco nel cluster.
  - Api: FabricClient.QueryManager.GetNodeListAsync.
  - Powershell: Get-ServiceFabricNode.
- Elenco applicazioni. Restituisce l'elenco delle applicazioni del cluster.
  - Api: FabricClient.QueryManager.GetApplicationListAsync.
  - Powershell: Get-ServiceFabricApplication.
- Elenco dei servizi. Restituisce l’elenco dei servizi in un’applicazione.
  - Api: FabricClient.QueryManager.GetServiceListAsync.
  - Powershell: Get-ServiceFabricService.
- Elenco delle partizioni. Restituisce l'elenco delle partizioni in un servizio.
  - Api: FabricClient.QueryManager.GetPartitionListAsync.
  - Powershell: Get-ServiceFabricPartition.
- Elenco delle repliche. Restituisce l'elenco delle repliche in una partizione.
  - Api: FabricClient.QueryManager.GetReplicaListAsync.
  - Powershell: Get-ServiceFabricReplica.
- Elenco delle applicazioni distribuite. Restituisce l'elenco delle applicazioni distribuite in un nodo.
  - Api: FabricClient.QueryManager.GetDeployedApplicationListAsync.
  - Powershell: Get-ServiceFabricDeployedApplication.
- Elenco dei pacchetti di servizi distribuiti Restituisce l’elenco dei pacchetti di servizi in un’applicazione distribuita
  - Api: FabricClient.QueryManager.GetDeployedServicePackageListAsync.
  - Powershell: Get-ServiceFabricDeployedApplication.

### esempi

Nell’esempio seguente si ottengono le applicazioni non integre nel cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Il cmdlet seguente ottiene i dettagli di applicazione per l’applicazione fabric:/WordCount. Si noti che lo stato di integrità è Avviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

Il seguente cmdlet ottiene i servizi con lo stato di integrità Avviso.

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## Aggiornamento del cluster e dell'applicazione
Durante l’aggiornamento monitorato del cluster e dell’applicazione, Infrastruttura di servizi controlla l’integrità per garantire che lo stato sia integro e che rimanga tale. In presenza di elementi non integri per ogni criterio configurato, l'aggiornamento viene sospeso per consentire l'interazione dell'utente o viene eseguito il rollback automatico.

Durante l’aggiornamento del **cluster**, è possibile ottenere lo stato di aggiornamento del cluster, che include le valutazioni on integre che puntano agli elementi non integri nel cluster. Se viene eseguito il rollback dell'aggiornamento a causa di problemi di integrità, lo stato di aggiornamento conserverà i motivi non integri più recenti, in modo che gli amministratori possano analizzare la causa dell'errore. Analogamente, durante l’aggiornamento dell’**applicazione**, lo stato dell’aggiornamento dell’applicazione contiene le valutazioni non integre.

Di seguito viene illustrato lo stato di aggiornamento dell’applicazione per un’applicazione fabric:/WordCount modificata. Tramite un watchdog è stato rilevato un errore in una delle repliche dell’applicazione. Viene eseguito il rollback dell’aggiornamento poiché le verifiche dell’integrità non sono rispettate.

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

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Ulteriori informazioni sull’[aggiornamento dell’applicazione Infrastruttura di servizi](service-fabric-application-upgrade.md).

## Risoluzione dei problemi con lo stato
Ogni volta che si verifica un problema in cluster o in un'applicazione, osservare il cluster o l'integrità dell'applicazione per individuare il problema riscontrato. Le valutazioni non integre mostreranno nei dettagli ciò che ha attivato lo stato non integro corrente. Se necessario, eseguire il drill down nelle entità figlio non integre per individuare i problemi.

## Passaggi successivi
[Uso dei report di integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Aggiunta di report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)

[Come eseguire il monitoraggio e la diagnosi dei servizi localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=Nov15_HO1-->