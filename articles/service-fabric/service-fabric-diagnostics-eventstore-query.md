---
title: Eseguire query per gli eventi del cluster usando le API di EventStore nei cluster di Azure Service Fabric | Microsoft Docs
description: Informazioni su come usare le API di EventStore in Azure Service Fabric per eseguire query per eventi della piattaforma
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 1b0b369f0021580d3add583f001bad04c70b03fd
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661752"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Eseguire query sulle API di EventStore per eventi del cluster

Questo articolo illustra come eseguire query sulle API di EventStore disponibili in Service Fabric versione 6.2 e successive. Per altre informazioni su EventStore, vedere la [panoramica del servizio EventStore](service-fabric-diagnostics-eventstore.md). Attualmente, il servizio EventStore può accedere solo ai dati per gli ultimi 7 giorni, in base ai criteri di conservazione dei dati di diagnostica del cluster.

>[!NOTE]
>Le API di EventStore a partire dalla versione 6.4 di Service Fabric sono destinate solo ai cluster di Windows in esecuzione in Azure.

Le API di EventStore sono accessibili direttamente tramite un endpoint REST o a livello di codice. A seconda della query, per raccogliere i dati corretti sono necessari diversi parametri, Questi parametri in genere includono:
* `api-version`: versione delle API di EventStore in uso
* `StartTimeUtc`: definisce l'inizio del periodo che si intende esaminare
* `EndTimeUtc`: fine del periodo di tempo

Oltre a questi parametri sono disponibili anche parametri facoltativi, ad esempio:
* `timeout`: sostituisce il timeout predefinito di 60 secondi per l'esecuzione dell'operazione di richiesta
* `eventstypesfilter`: offre la possibilità di filtrare tipi di eventi specifici.
* `ExcludeAnalysisEvents`: non vengono restituiti eventi di "analisi". Per impostazione predefinita, le query di EventStore restituiscono eventi di "analisi", quando possibile. Gli eventi di analisi sono eventi avanzati del canale operativo che contengono più informazioni o contesto rispetto a un normale evento di Service Fabric e offrono maggiori dettagli.
* `SkipCorrelationLookup`: non vengono cercati potenziali eventi correlati nel cluster. Per impostazione predefinita, EventStore tenta di correlare gli eventi in un cluster e, quando possibile, di collegare gli eventi tra loro. 

È possibile eseguire query per gli eventi di ogni entità di un cluster, nonché per gli eventi di tutte le entità di un determinato tipo. Ad esempio, è possibile eseguire query per gli eventi di un nodo specifico oppure di tutti i nodi nel cluster. Di seguito è riportato l'attuale set di entità per cui è possibile eseguire query per gli eventi, con il modo in cui sarà strutturata la query.
* Cluster: `/EventsStore/Cluster/Events`
* Nodi: `/EventsStore/Nodes/Events`
* Nodo: `/EventsStore/Nodes/<NodeName>/$/Events`
* Applicazioni: `/EventsStore/Applications/Events`
* Applicazione: `/EventsStore/Applications/<AppName>/$/Events`
* Servizi: `/EventsStore/Services/Events`
* Servizio: `/EventsStore/Services/<ServiceName>/$/Events`
* Partizioni: `/EventsStore/Partitions/Events`
* Partizione: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliche: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replica: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Quando si fa riferimento al nome di un'applicazione o un servizio, non è necessario che la query includa il prefisso "fabric:/". Se i nomi delle applicazioni o dei servizi contengono "/", inoltre, modificare tale carattere in "~" per consentire il funzionamento della query. Per l'applicazione "fabric:/App1/FrontendApp", ad esempio, le query specifiche dell'app saranno strutturate nel modo seguente: `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>I report sull'integrità per i servizi, inoltre, sono attualmente inclusi nell'applicazione corrispondente, di conseguenza si eseguirà una query per gli eventi `DeployedServiceHealthReportCreated` dell'entità applicazione appropriata. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Eseguire query su EventStore tramite gli endpoint REST delle API

È possibile eseguire query su EventStore direttamente tramite un endpoint REST, inviando richieste `GET` a `<your cluster address>/EventsStore/<entity>/Events/`.

Per eseguire una query per tutti gli eventi del cluster tra `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, ad esempio, la richiesta si presenterà come segue:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Questo potrebbe non restituire alcun evento o restituire l'elenco degli eventi restituiti in JSON:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

In questo caso è possibile verificare che tra `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z` è stato completato il primo aggiornamento del cluster alla sua prima implementazione, da `"CurrentClusterVersion": "0.0.0.0:"` a `"TargetClusterVersion": "6.2:1.0"`, in `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Eseguire query su EventStore a livello di codice

È anche possibile eseguire query su EventStore a livello di codice, tramite la [libreria client di Service Fabric](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Dopo aver configurato il client di Service Fabric, è possibile eseguire query per gli eventi accedendo a EventStore nel modo seguente: ` sfhttpClient.EventStore.<request>`

Di seguito è riportata una richiesta di esempio per tutti gli eventi del cluster tra `2018-04-03T18:00:00Z` e `2018-04-04T18:00:00Z`, tramite la funzione `GetClusterEventListAsync`.

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

## <a name="sample-scenarios-and-queries"></a>Scenari e query di esempio

Di seguito sono riportati alcuni esempi di come è possibile chiamare le API REST di EventStore per conoscere lo stato del cluster.

*Aggiornamenti del cluster*

Per verificare quando è stato completato o tentato un aggiornamento del cluster nell'ultima settimana, è possibile eseguire una query sulle API per gli aggiornamenti del cluster completati di recente, con una query per gli eventi "ClusterUpgradeComplete" in EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemi di aggiornamento del cluster*

Analogamente, se si sono verificati problemi con un recente aggiornamento del cluster, è possibile eseguire una query per tutti gli eventi dell'entità cluster. Verranno visualizzati vari eventi, che includeranno l'avvio di aggiornamenti e ogni dominio di aggiornamento in cui l'aggiornamento è stato completato. Verranno visualizzati eventi anche per l'avvio del rollback e gli eventi di integrità corrispondenti. A questo scopo si userà la query seguente: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Cambiamenti di stato dei nodi*

Per visualizzare i cambiamenti di stato dei nodi negli ultimi giorni, ossia quando i nodi sono stati avviati o arrestati oppure attivati o disattivati (dalla piattaforma, dal servizio Chaos o dall'input utente), usare la query seguente: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Eventi delle applicazioni*

È anche possibile tenere traccia delle distribuzioni e degli aggiornamenti recenti di applicazioni. Per visualizzare tutti gli eventi di applicazioni nel cluster, usare la query seguente: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Dati cronologici sull'integrità per un'applicazione*

Oltre agli eventi relativi al ciclo di vita delle applicazioni, può essere utile visualizzare anche i dati cronologici sull'integrità di un'applicazione specifica. A tale scopo, è possibile specificare il nome dell'applicazione per cui si vogliono raccogliere i dati. Per ottenere tutti gli eventi di integrità dell'applicazione, usare questa query: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Per includere gli eventi di integrità eventualmente scaduti, ossia di cui potrebbe essere stata superata la durata (TTL), aggiungere `,ApplicationHealthReportExpired` alla fine della query, per filtrare due tipi di eventi.

*Dati cronologici sull'integrità per tutti i servizi in "myApp"*

Attualmente, gli eventi dei report sull'integrità per i servizi sono inclusi come eventi `DeployedServicePackageNewHealthReport` nell'entità applicazione corrispondente. Per visualizzare l'integrità dei servizi per "App1", usare la query seguente: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Riconfigurazione delle partizioni*

Per visualizzare tutti gli spostamenti delle partizioni nel cluster, eseguire una query per l'evento `PartitionReconfigured`. In questo modo è possibile stabilire i carichi di lavoro eseguiti e il nodo in cui sono stati eseguiti in momenti specifici, per la diagnosi dei problemi nel cluster. Una query di esempio a tale scopo è la seguente: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Servizio Chaos*

Per l'avvio o l'arresto del servizio Chaos è disponibile un evento esposto a livello di cluster. Per visualizzare l'uso recente del servizio Chaos, usare la query seguente: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

