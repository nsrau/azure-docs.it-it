<properties
   pageTitle="Come visualizzare l'integrità aggregata delle entità di Service Fabric | Microsoft Azure"
   description="Descrive come eseguire una query dell'integrità aggregata delle entità di Azure Service Fabric, come visualizzarla e come valutarla con query di integrità e query generali."
   services="service-fabric"HealthManager
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
   ms.date="01/26/2016"
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
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Integrità in Esplora Infrastruttura di servizi
Esplora Infrastruttura di servizi fornisce una panoramica visiva del cluster. Nell'immagine seguente è possibile osservare quanto segue:

- L'applicazione **fabric:/WordCount** è di colore rosso (condizione di errore), perché per questa applicazione è stato segnalato un evento di errore da **MyWatchdog** per la proprietà **Availability**.

- Uno dei servizi di questa applicazione, **fabric:/WordCount/WordCount.Service** è di colore giallo (condizione di avviso). Come descritto in precedenza, il servizio è configurato con 7 repliche, che non possono essere tutte posizionate perché sono disponibili solo di 5 nodi. Anche se qui non è illustrata, la partizione del servizio è di colore giallo a causa del report di sistema. La partizione gialla avvia il servizio giallo.

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

- [Facoltativo] Mappa dei criteri di integrità dell'applicazione con criteri di integrità usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per eventi, nodi e applicazioni che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Tutti gli eventi, i nodi e le applicazioni vengono usati per valutare l'integrità aggregata dell'entità, indipendentemente dal filtro.

### API
Per ottenere l'integrità del cluster, creare un oggetto **FabricClient** e chiamare il metodo [**GetClusterHealthAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) sul relativo **HealthManager**.

La sintassi seguente consente di ottenere l’integrità del cluster:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

La sintassi seguente consente di ottenere l'integrità del cluster usando criteri di integrità del cluster personalizzati e filtri per nodi e applicazioni. Viene creato S**System.Fabric.Description.[ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)**, che contiene tutti i dati di input.

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
Il cmdlet per ottenere l'integrità del cluster è **[Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)**. Connettersi prima di tutto al cluster con il cmdlet **Connect-ServiceFabricCluster**.

Lo stato del cluster è 5 nodi, l'applicazione di sistema e fabric:/WordCount sono configurate come descritto in precedenza.

I cmdlet seguenti ottengono l'integrità del cluster con criteri di integrità predefiniti. Lo stato di integrità aggregato è di tipo avviso, come lo è stato dell'applicazione fabric:/WordCount. Notare come le valutazioni non integre forniscano dettagli sulle condizioni che hanno attivato lo stato di integrità aggregato.

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

Il cmdlet PowerShell seguente ottiene lo stato di integrità del cluster con i criteri dell'applicazione personalizzati. Il cmdlet filtra i risultati per ottenere solo le applicazioni e i nodi con stato di errore o avviso. Di conseguenza, non verranno restituiti nodi, perché sono tutti integri. Solo l'applicazione fabric:/WordCount rispetta il filtro delle applicazioni. Poiché i criteri personalizzati specificano di considerare gli avvisi come errori per l'applicazione fabric:/WordCount, questa viene valutata in stato di errore e lo stesso accade per il cluster.

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
Restituisce lo stato di un'entità nodo e contiene gli eventi di integrità segnalati sul nodo. Input:

- [Obbligatorio] Nome del nodo che identifica il nodo.

- [Facoltativo] Impostazioni dei criteri di integrità usate per valutare l'integrità.

- [Facoltativo] Filtri per gli eventi che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità di entità aggregate, vengono usati tutti gli eventi indipendentemente dal filtro.

### API
Per ottenere l'integrità tramite l'API, creare un oggetto FabricClient e chiamare il metodo **GetNodeHealthAsync** sul relativo HealthManager.

La sintassi seguente ottiene l'integrità del nodo per il nome del nodo specificato:

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

La sintassi seguente ottiene lo stato del nodo per il nome nodo specificato e passa un filtro eventi e i criteri personalizzati tramite **System.Fabric.Description.[NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)**:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità del nodo è **Get-ServiceFabricNodeHealth**. Connettersi prima di tutto al cluster con il cmdlet **Connect-ServiceFabricCluster**. I cmdlet seguenti ottengono l'integrità del nodo con criteri di integrità predefiniti:

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

Il cmdlet seguente ottiene lo stato di tutti i nodi del cluster:

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

- [Obbligatorio] Nome dell'applicazione (URI) che identifica l'applicazione.

- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per eventi, servizi e applicazioni distribuite che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Tutti gli eventi, i servizi e le applicazioni distribuite vengono usati per valutare l'integrità aggregata dell'entità, indipendentemente dal filtro.

### API
Per ottenere l'integrità dell'applicazione, creare un oggetto FabricClient e chiamare il metodo **GetApplicationHealthAsync** sul relativo HealthManager.

La sintassi seguente ottiene l'integrità dell'applicazione per il nome dell'applicazione (URI) specificato.

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

La sintassi seguente ottiene l'integrità dell'applicazione per il nome dell'applicazione (URI) specificato, indicando filtri e criteri personalizzati tramite **System.Fabric.Description.ApplicationHealthQueryDescription**.

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
Il cmdlet per ottenere l'integrità dell'applicazione è **Get-ServiceFabricApplicationHealth**. Connettersi prima di tutto al cluster con il cmdlet **Connect-ServiceFabricCluster**.

Il cmdlet seguente restituisce l'integrità dell'applicazione fabric:/WordCount:

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

Il cmdlet PowerShell seguente passa i criteri personalizzati. Filtra anche gli elementi figlio e gli eventi.

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
Restituisce l'integrità di un'entità servizio. Contiene gli stati di integrità della partizione. Input:

- [Obbligatorio] Nome del servizio (URI) che identifica il servizio.
- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.
- [Facoltativo] Filtri per eventi e partizioni che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi e tutte le partizioni, indipendentemente dal filtro.

### API
Per ottenere lo stato del servizio tramite l'API, creare un oggetto FabricClient e chiamare il metodo **GetServiceHealthAsync** sul relativo HealthManager.

L'esempio seguente ottiene l'integrità di un servizio con il nome di servizio (URI) specificato:

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

L'esempio seguente ottiene l'integrità del servizio per il nome del servizio (URI) specificato, indicando filtri e criteri personalizzati tramite System.Fabric.Description.ServiceHealthQueryDescription.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità del servizio è **Get-ServiceFabricServiceHealth**. Connettersi prima di tutto al cluster con il cmdlet **Connect-ServiceFabricCluster**.

Il cmdlet seguente ottiene l'integrità del servizio usando i criteri di integrità predefiniti:

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
Restituisce l'integrità di un'entità partizione. Contiene gli stati di integrità della replica. Input:

- [Obbligatorio] ID partizione (GUID) che identifica la partizione.

- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per eventi e repliche che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità aggregata dell'entità, vengono usati tutti gli eventi e tutte le repliche, indipendentemente dal filtro.

### API
Per ottenere lo stato della partizione tramite l'API, creare un oggetto FabricClient e chiamare il metodo **GetPartitionHealthAsync** sul relativo HealthManager. Per specificare i parametri facoltativi, creare **System.Fabric.Description.PartitionHealthQueryDescription**.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità della partizione è **Get-ServiceFabricPartitionHealth**. Connettersi prima di tutto al cluster con il cmdlet **Connect-ServiceFabricCluster**.

Il cmdlet seguente ottiene l'integrità di tutte le partizioni del servizio di conteggio delle parole.

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
Restituisce l'integrità di una replica. Input:

- [Obbligatorio] ID partizione (GUID) e ID replica che identificano la replica.

- [Facoltativo] Parametri dei criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per gli eventi che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità di entità aggregate, vengono usati tutti gli eventi indipendentemente dal filtro.

### API
Per ottenere l'integrità della replica tramite l'API, creare un oggetto FabricClient e chiamare il metodo **GetReplicaHealthAsync** sul relativo HealthManager. Per specificare parametri avanzati, usare **System.Fabric.Description.ReplicaHealthQueryDescription**.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità della replica è **Get-ServiceFabricReplicaHealth**. Connettersi prima di tutto al cluster con il cmdlet **Connect-ServiceFabricCluster**.

Il cmdlet seguente ottiene l'integrità della replica primaria per tutte le partizioni del servizio.

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
Restituisce l'integrità di un'applicazione distribuita in un'entità nodo. Contiene gli stati di integrità del pacchetto di servizi distribuito. Input:

- [Obbligatorio] Nome dell'applicazione (URI) e nome del nodo (stringa) che identificano l'applicazione distribuita

- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per eventi e pacchetti di servizi distribuiti che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Tutti gli eventi e i pacchetti di servizi distribuiti vengono usati per valutare l'integrità aggregata dell'entità, indipendentemente dal filtro.

### API
Per ottenere l'integrità in un'applicazione distribuita in un nodo tramite l'API, creare un oggetto FabricClient e chiamare il metodo **GetDeployedApplicationHealthAsync** sul relativo HealthManager. Per specificare i parametri facoltativi, usare **System.Fabric.Description.DeployedApplicationHealthQueryDescription**.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità dell'applicazione distribuita è **Get-ServiceFabricDeployedApplicationHealth**. Connettersi prima di tutto al cluster con il cmdlet **Connect-ServiceFabricCluster**. Per sapere dove viene distribuita un'applicazione, eseguire **Get-ServiceFabricApplicationHealth** e osservare gli elementi figlio dell'applicazione distribuita.

Il cmdlet seguente ottiene l'integrità dell'applicazione fabric:/WordCount distribuita nel nodo Node.1.

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
Restituisce l'integrità di un'entità pacchetto di servizi distribuito. Input:

- [Obbligatorio] Nome dell'applicazione (URI), nome del nodo (stringa) e nome del manifesto del servizio (stringa) che identificano il pacchetto di servizi distribuito.

- [Facoltativo] Criteri di integrità dell'applicazione usati per sostituire i criteri del manifesto dell'applicazione.

- [Facoltativo] Filtri per gli eventi che specificano le voci di interesse che devono essere restituite nel risultato (ad esempio, solo gli errori o avvisi ed errori). Per valutare l'integrità di entità aggregate, vengono usati tutti gli eventi indipendentemente dal filtro.

### API
Per ottenere l'integrità di un pacchetto di servizi distribuito tramite l'API, creare un oggetto FabricClient e chiamare il metodo **GetDeployedServicePackageHealthAsync** sul relativo HealthManager.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
Il cmdlet per ottenere l'integrità del pacchetto di servizi distribuito è **Get-ServiceFabricDeployedServicePackageHealth**. Connettersi prima di tutto al cluster con il cmdlet **Connect-ServiceFabricCluster**. Per vedere dove viene distribuita un'applicazione, eseguire **Get-ServiceFabricApplicationHealth** e osservare le applicazioni distribuite. Per vedere quali pacchetti di servizi sono contenuti in un'applicazione, esaminare gli elementi figlio del pacchetto di servizi distribuito nell'output di **Get-ServiceFabricDeployedApplicationHealth**.

Il cmdlet seguente ottiene l'integrità del pacchetto di servizi **WordCount.Service** dell'applicazione fabric:/WordCount distribuita nel nodo Node.1. L'entità include report **System.Hosting** per l'attivazione corretta del pacchetto di servizi e del punto di ingresso, nonché la registrazione corretta del tipo di servizio.

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
Le query generali restituiscono l'elenco delle entità di Service Fabric di un tipo specificato. Le query vengono esposte tramite l'API (con metodi su **FabricClient.QueryManager**), i cmdlet PowerShell e REST. Queste query aggregano sottoquery da più componenti. Uno di questi è l'[archivio integrità](service-fabric-health-introduction.md#health-store), che popola lo stato di integrità aggregato per i risultati di ogni query.

> [AZURE.NOTE] Le query generali restituiscono lo stato di integrità aggregato dell'entità e non contengono i dati di integrità complessi. Se un'entità non è integra, è possibile procedere con query di integrità per ottenere tutte le informazioni di integrità, come gli eventi, gli stati di integrità degli elementi figlio e le valutazioni non integre.

Se le query generali restituiscono uno stato di integrità sconosciuto per un'entità, è possibile che l'archivio integrità non abbia dati completi sull'entità. È anche possibile che una sottoquery nell'archivio integrità non sia riuscita, ad esempio, si è verificato un errore di comunicazione o l'archivio integrità è stato limitato. Procedere con una query di integrità per l'entità. Se la sottoquery ha rilevato errori temporanei, ad esempio problemi di rete, questa query di completamento può riuscire. Può anche fornire altri dettagli dall'archivio integrità sui motivi che impediscono l'esposizione dell'entità.

Di seguito sono elencate le query che contengono **HealthState** per le entità:

- Elenco di nodi: restituisce i nodi elencati nel cluster.
  - API: FabricClient.QueryManager.GetNodeListAsync
  - PowerShell: Get-ServiceFabricNode
- Elenco applicazioni: restituisce l'elenco di applicazioni nel cluster.
  - API: FabricClient.QueryManager.GetApplicationListAsync
  - PowerShell: Get-ServiceFabricApplication
- Elenco servizi: restituisce l'elenco dei servizi in un'applicazione.
  - API: FabricClient.QueryManager.GetServiceListAsync
  - PowerShell: Get-ServiceFabricService
- Elenco partizioni: restituisce l'elenco delle partizioni in un servizio.
  - API: FabricClient.QueryManager.GetPartitionListAsync
  - PowerShell: Get-ServiceFabricPartition
- Elenco repliche: restituisce l'elenco delle repliche in una partizione.
  - API: FabricClient.QueryManager.GetReplicaListAsync
  - PowerShell: Get-ServiceFabricReplica
- Elenco applicazioni distribuite: restituisce l'elenco delle applicazioni distribuite in un nodo.
  - API: FabricClient.QueryManager.GetDeployedApplicationListAsync
  - PowerShell: Get-ServiceFabricDeployedApplication
- Elenco pacchetti di servizi distribuiti: restituisce l'elenco dei pacchetti di servizi in un'applicazione distribuita
  - API: FabricClient.QueryManager.GetDeployedServicePackageListAsync
  - PowerShell: Get-ServiceFabricDeployedApplication

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
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

Il cmdlet seguente ottiene i servizi con lo stato di integrità di tipo avviso:

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

Per altre informazioni, vedere [Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md).

## Usare le valutazioni dell'integrità per risolvere i problemi
Ogni volta che si verifica un problema in cluster o in un'applicazione, osservare l'integrità del cluster o dell'applicazione per individuare il problema riscontrato. Le valutazioni di non integrità mostreranno i dettagli ciò che ha attivato lo stato non integro corrente. È possibile eseguire il drill-down delle entità figlio non integre per identificare la causa radice.

## Passaggi successivi
[Usare i report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Aggiungere report sull'integrità di Service Fabric personalizzati](service-fabric-report-health.md)

[Monitorare e diagnosticare servizi in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento di un'applicazione di infrastruttura di servizi](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->