---
title: Monitorare un cluster di Service Fabric in Azure | Microsoft Docs
description: In questa esercitazione viene illustrato come monitorare un cluster visualizzando gli eventi di Service Fabric, eseguendo query sulle API di EventStore, monitorando i contatori delle prestazioni e visualizzando i report sull'integrità.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: b8234f286f4304b83969a01704735e1f3a7da2c6
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58229151"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Esercitazione: Monitorare un cluster di Service Fabric in Azure

Il monitoraggio e la diagnostica sono essenziali per lo sviluppo, il test e la distribuzione di carichi di lavoro in qualsiasi ambiente cloud. Questa esercitazione è la seconda parte di una serie e mostra come monitorare e diagnosticare un cluster di Service Fabric usando eventi, contatori delle prestazioni e report sull'integrità.   Per altre informazioni, vedere la panoramica sul [monitoraggio del cluster](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) e sul [monitoraggio dell'infrastruttura](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Visualizzare gli eventi di Service Fabric
> * Eseguire query sulle API di EventStore per eventi del cluster
> * Monitorare l'infrastruttura/raccogliere i contatori delle prestazioni
> * Visualizzare i report sull'integrità del cluster

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) protetto in Azure usando un modello
> * Eseguire il monitoraggio di un cluster
> * [Aumentare o ridurre un cluster](service-fabric-tutorial-scale-cluster.md)
> * [Aggiornare il runtime di un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminare un cluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installare il [modulo Azure PowerShell 4.1 o versioni successive](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) o [l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* Creare un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) sicuro 
* Configurare la [raccolta di diagnostica](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) per il cluster
* Abilitare il [servizio EventStore](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) nel cluster
* Configurare i [log di Monitoraggio di Azure e l'agente di Log Analytics](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) per il cluster

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Visualizzare gli eventi di Service Fabric usando i log di Monitoraggio di Azure

I log di Monitoraggio di Azure raccolgono e analizzano i dati telemetrici delle applicazioni e dei servizi ospitati nel cloud e forniscono gli strumenti di analisi per sfruttarne al meglio la disponibilità e le prestazioni. È possibile eseguire query sui log di Monitoraggio di Azure per ottenere informazioni e risolvere i problemi che si verificano nel cluster.

Per accedere alla soluzione Analisi Service Fabric, passare al [portale di Azure](https://portal.azure.com) e selezionare il gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric.

Selezionare la risorsa **ServiceFabric(mysfomsworkspace)**.

In **Panoramica** vengono visualizzati riquadri sotto forma di grafo per ogni soluzione abilitata, tra cui uno per Service Fabric. Fare clic sul grafo **Service Fabric** per passare alla soluzione Analisi Service Fabric.

![Soluzione Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

L'immagine seguente mostra la home page della soluzione Analisi Service Fabric. La home page fornisce una visualizzazione di riepilogo delle operazioni eseguite nel cluster.

![Soluzione Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Se è stata abilitata la diagnostica al momento della creazione del cluster, è possibile visualizzare gli eventi per 

* [Eventi del cluster di Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Eventi del modello di programmazione Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventi relativi al modello di programmazione Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oltre agli eventi predefiniti di Service Fabric, è possibile raccogliere eventi di sistema più dettagliati [aggiornando la configurazione dell'estensione di diagnostica](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Visualizzare gli eventi di Service Fabric che includono azioni sui nodi

Nella pagina Analisi Service Fabric fare clic sul grafo di **Eventi del cluster**.  Vengono visualizzati i log per tutti gli eventi di sistema che sono stati raccolti. Come riferimento, questi elementi provengono da **WADServiceFabricSystemEventsTable** nell'account di Archiviazione di Azure. Analogamente, gli eventi di Reliable Services e Reliable Actors visualizzati accanto provengono dalle rispettive tabelle.
    
![Canale operativo della query](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

La query usa il linguaggio di query Kusto, che è possibile modificare per definire ciò che si sta cercando. Ad esempio, per trovare tutte le azioni eseguite sui nodi nel cluster, è possibile usare la query seguente. Gli ID evento usati di seguito sono disponibili nelle [informazioni di riferimento sugli eventi del canale operativo](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Il linguaggio di query Kusto è potente. Di seguito sono riportate altre query utili.

Creare una tabella di ricerca *ServiceFabricEvent* come funzione definita dall'utente salvando la query come funzione con l'alias ServiceFabricEvent:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Restituire gli eventi operativi registrati nell'ultima ora:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Restituire gli eventi operativi con EventId == 18604 e EventName == 'NodeDownOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Restituire gli eventi operativi con EventId == 18604 e EventName == 'NodeUpOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Restituire i report sull'integrità con HealthState == 3 (errore) ed estrarre le proprietà aggiuntive dal campo EventMessage:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Restituire un grafico temporale degli eventi con EventId != 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Ottenere gli eventi operativi di Service Fabric aggregati con il servizio e il nodo specificati:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Eseguire il rendering del conteggio degli eventi di Service Fabric per EventId/EventName con una query su più risorse:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Visualizzare gli eventi delle applicazioni di Service Fabric

È possibile visualizzare gli eventi per le applicazioni Reliable Services e Reliable Actors distribuite nel cluster.  Nella pagina Analisi Service Fabric fare clic sul grafo di **Eventi dell'applicazione**.

Eseguire la query seguente per visualizzare gli eventi delle applicazioni Reliable Services:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

È possibile visualizzare eventi diversi per l'avvio e il completamento del servizio runasync, come in genere accade durante le distribuzioni e gli aggiornamenti.

![Reliable Services della soluzione Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

È anche possibile trovare eventi per il servizio Reliable Services con ServiceName == "fabric:/Watchdog/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Gli eventi relativi a Reliable Actors possono essere visualizzati in modo simile:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Per configurare eventi più dettagliati per Reliable Actors, è possibile modificare `scheduledTransferKeywordFilter` nel file di configurazione per l'estensione di diagnostica nel modello del cluster. I dettagli sui valori per questi elementi sono disponibili nelle [informazioni di riferimento sugli eventi di Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Visualizzare i contatori delle prestazioni con i log di Monitoraggio di Azure
Per visualizzare i contatori delle prestazioni, passare al [portale di Azure](https://portal.azure.com) e al gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric. 

Selezionare la risorsa **ServiceFabric(mysfomsworkspace)**, quindi l'**Area di lavoro di Log Analytics** e infine **Impostazioni avanzate**.

Fare clic su **Dati**, quindi fare clic su **Contatori delle prestazioni di Windows**. È disponibile un elenco di contatori predefiniti che è possibile scegliere di abilitare ed è anche possibile impostare l'intervallo per la raccolta. È anche possibile aggiungere [altri contatori delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) da raccogliere. Il formato corretto è descritto in questo [articolo](/windows/desktop/PerfCtrs/specifying-a-counter-path). Fare clic su **Salva**, quindi su **OK**.

Chiudere il pannello Impostazioni avanzate e selezionare **Riepilogo dell'area di lavoro** sotto l'intestazione **Generale**. Per ognuna delle soluzioni abilitate è presente un riquadro grafico, tra cui uno per Service Fabric. Fare clic sul grafo **Service Fabric** per passare alla soluzione Analisi Service Fabric.

Sono presenti riquadri grafici per eventi del canale operativo e di Reliable Services. La rappresentazione grafica dei dati trasmessi ai contatori selezionati sarà visualizzata sotto **Metriche del nodo**. 

Selezionare il grafico **Metriche del contenitore** per visualizzare dettagli aggiuntivi. È anche possibile eseguire query sui dati dei contatori delle prestazioni in modo analogo agli eventi cluster e applicare filtri sui nodi, sul nome del contatore delle prestazioni e sui valori usando il linguaggio di query Kusto.

## <a name="query-the-eventstore-service"></a>Eseguire una query sul servizio EventStore
Il [servizio EventStore](service-fabric-diagnostics-eventstore.md) offre la possibilità di comprendere lo stato del cluster o dei carichi di lavoro in un determinato punto nel tempo. EventStore è un servizio di Service Fabric con stato che gestisce gli eventi del cluster. Gli eventi vengono esposti tramite [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST e le API. EventStore esegue direttamente una query sul cluster per ottenere i dati di diagnostica relativi a qualsiasi entità presente nel cluster. Per visualizzare un elenco completo di eventi disponibili in EventStore, vedere [Eventi di Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

È possibile eseguire una query sulle API di EventStore a livello di codice tramite la [libreria client di Service Fabric](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Di seguito è riportato un esempio di richiesta per tutti gli eventi del cluster tra 2018-04-03T18:00:00Z e 2018-04-04T18:00:00Z, tramite la funzione GetClusterEventListAsync.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Di seguito è riportato un altro esempio che esegue una query per l'integrità del cluster e tutti gli eventi di nodo del mese di settembre 2018 e ne esegue la stampa.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>Monitorare l'integrità del cluster
Service Fabric introduce un [modello di integrità](service-fabric-health-introduction.md) con entità di integrità per le quali i componenti di sistema e i watchdog possono creare report sulle condizioni locali sottoposte a monitoraggio. L' [archivio integrità](service-fabric-health-introduction.md#health-store) aggrega tutti i dati di integrità per determinare se le entità sono integre.

Il cluster viene popolato automaticamente con report sull'integrità inviati dai componenti di sistema. Per altre informazioni, vedere [Usare i report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Infrastruttura di servizi espone le query relative all’integrità per ognuno dei [tipi di entità](service-fabric-health-introduction.md#health-entities-and-hierarchy)supportati. È possibile accedervi tramite l'API, usando i metodi in [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), i cmdlet di PowerShell e REST. Queste query restituiscono informazioni complete sull'integrità per l'entità, come lo stato aggregato dell'integrità, gli eventi di integrità dell'entità, gli stati di integrità degli elementi figlio, se applicabili, le valutazioni di non integrità (quando l'entità non è integra) e le statistiche sull'integrità degli elementi figlio, quando applicabili.

### <a name="get-cluster-health"></a>Get cluster health
Il [cmdlet Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) restituisce l'integrità dell'entità cluster e contiene gli stati di integrità di applicazioni e nodi, elementi figlio del cluster.  Connettersi prima di tutto al cluster con il [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

Lo stato del cluster è costituito da 11 nodi, dall'applicazione di sistema e da fabric:/Voting, configurati come descritto.

L'esempio seguente consente di ottenere l'integrità del cluster con criteri di integrità predefiniti. Gli 11 nodi sono integri, ma lo stato di integrità aggregato del cluster è Errore perché l'applicazione fabric:/Voting è in errore. Notare come le valutazioni non integre forniscano dettagli sulle condizioni che hanno attivato lo stato di integrità aggregato.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

L'esempio seguente consente di ottenere lo stato di integrità del cluster con i criteri dell'applicazione personalizzati. Filtra i risultati per ottenere solo le applicazioni e i nodi con stato di errore o avviso. In questo esempio non vengono restituiti nodi, perché sono tutti integri. Solo l'applicazione fabric:/Voting rispetta il filtro delle applicazioni. Poiché i criteri personalizzati specificano di considerare gli avvisi come errori per l'applicazione fabric:/Voting, questa viene valutata in stato di errore e lo stesso accade per il cluster.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Get node health
Il [cmdlet Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) restituisce l'integrità di un'entità nodo e contiene gli eventi di integrità segnalati sul nodo. Connettersi prima di tutto al cluster con il [cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). L'esempio seguente consente di ottenere l'integrità di un nodo specifico con criteri di integrità predefiniti:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

L'esempio seguente consente di ottenere lo stato di tutti i nodi del cluster:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Ottenere l'integrità del servizio di sistema 

Ottenere l'integrità aggregata dei servizi di sistema:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Visualizzare gli eventi di Service Fabric
> * Eseguire query sulle API di EventStore per eventi del cluster
> * Monitorare l'infrastruttura/raccogliere i contatori delle prestazioni
> * Visualizzare i report sull'integrità del cluster

Procedere con l'esercitazione seguente per scoprire come ridimensionare un cluster.
> [!div class="nextstepaction"]
> [Ridimensionare un cluster](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json