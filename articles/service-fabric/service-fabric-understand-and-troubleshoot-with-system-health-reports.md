---
title: "Risoluzione dei problemi con i report sull'integrità del sistema | Microsoft Docs"
description: "Descrive i report di integrità inviati dai componenti dell’Infrastruttura dei servizi di Azure e il relativo utilizzo per la risoluzione dei problemi del cluster o dei problemi delle applicazioni."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: oanapl
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fb3b2ecd73acb0ea65af477bc6a5b887c117574c
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="use-system-health-reports-to-troubleshoot"></a>Usare i report sull'integrità del sistema per la risoluzione dei problemi
I componenti di Azure Service Fabric forniscono report predefiniti su tutte le entità del cluster. L' [archivio integrità](service-fabric-health-introduction.md#health-store) crea ed elimina le entità in base ai report di sistema. Le organizza anche in una gerarchia che acquisisce le interazioni delle entità.

> [!NOTE]
> Per comprendere i concetti correlati all'integrità, vedere altre informazioni sul [modello di integrità di Service Fabric](service-fabric-health-introduction.md).
> 
> 

I report sull'integrità del sistema forniscono la visibilità delle funzionalità del cluster e dell'applicazione e contrassegnano i problemi riscontrati tramite a livello di integrità. Per le applicazioni e i servizi i report sull'integrità del sistema verificano che le entità siano implementate e si comportino correttamente dal punto di vista di Service Fabric. I report non forniscono il monitoraggio dell'integrità della logica di business del servizio o il rilevamento dei processi bloccati. I servizi utente possono arricchire i dati di integrità con informazioni specifiche per la logica.

> [!NOTE]
> I report sull'integrità dei watchdog sono visibili solo *dopo* che i componenti di sistemala hanno creato un'entità. Quando si elimina un'entità, l'archivio integrità elimina automaticamente tutti i report sull'integrità ad essa associati. Lo stesso vale quando si crea una nuova istanza dell'entità, ad esempio viene creata una nuova istanza di replica del servizio persistente con stato. Tutti i report associati all'istanza precedente vengono eliminati e rimossi dall'archivio.
> 
> 

I report sui componenti di sistema vengono identificati dall'origine, che inizia con il prefisso "**System.**" . I watchdog non possono usare lo stesso prefisso per le proprie origini, perché i report con parametri non validi vengono rifiutati.
Si osserveranno alcuni report di sistema per capire da quali eventi vengono attivati e come risolvere gli eventuali problemi che rappresentano.

> [!NOTE]
> Service Fabric continua ad aggiungere report sulle condizioni di interesse che possono migliorare la visibilità degli eventi che si verificano nel cluster o nell'applicazione. È anche possibile migliorare i report esistenti con maggiori dettagli, per consentire di risolvere i problemi più velocemente.
> 
> 

## <a name="cluster-system-health-reports"></a>Report sull'integrità del sistema cluster
L'entità di integrità del cluster viene creata automaticamente nell'archivio integrità. Se tutto funziona correttamente, non è disponibile un report di sistema.

### <a name="neighborhood-loss"></a>Perdita di nodi vicini
**System.Federation** segnala un errore quando rileva una perdita di nodi vicini. Il report è relativo a singoli nodi e l'ID del nodo è incluso nel nome della proprietà. Se si verifica la perdita di un nodo vicino nell'intero anello di Service Fabric, in genere è possibile prevedere due eventi (entrambi i lati del gap effettuano la segnalazione). In caso di perdita di più nodi vicini, si verificano più eventi.

Il report specifica il timeout di lease globale come durata (TTL). Il report viene inviato di nuovo ogni metà della durata TTL finché la condizione rimane attiva. Quando scade, l'evento viene rimosso automaticamente. Il comportamento di rimozione alla scadenza garantisce la corretta eliminazione del report dall'archivio integrità anche quando il nodo da cui è stato creato è inattivo.

* **SourceId**: System.Federation
* **Proprietà**: inizia con **Neighborhood** e include informazioni sul nodo
* **Passaggi successivi**: analizzare il motivo della perdita del nodo vicino, ad esempio controllare la comunicazione tra i nodi del cluster.

## <a name="node-system-health-reports"></a>Report sull'integrità del sistema di nodi
**System.FM**, che rappresenta il servizio Gestione failover, è l'autorità che gestisce le informazioni sui nodi del cluster. Ogni nodo deve avere un report generato da System.FM che mostra il relativo stato. Le entità nodo vengono rimosse quando viene rimosso lo stato del nodo (vedere [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync)).

### <a name="node-updown"></a>Nodo attivo/inattivo
System.FM restituisce OK quando il nodo viene aggiunto all'anello, ovvero è operativo. Segnala un errore quando il nodo non fa più parte dell'anello, ovvero è inattivo perché è in corso un aggiornamento o semplicemente perché si è verificato un errore. La gerarchia di integrità creata dall'archivio integrità agisce sulle entità distribuite in correlazione con i report sui nodi di System.FM. Considera il nodo un elemento padre virtuale di tutte le entità distribuite. Le entità distribuite in tale nodo vengono esposte tramite query se il nodo è segnalato come attivo da System.FM, con la stessa istanza associata alle entità. Quando System.FM segnala che il nodo è inattivo o riavviato (nuova istanza), l'archivio integrità elimina automaticamente le entità distribuite eventualmente esistenti solo nel nodo inattivo o nell'istanza precedente del nodo.

* **SourceId**: System.FM
* **Proprietà**: State
* **Passaggi successivi**: se il nodo è inattivo per un aggiornamento, deve tornare attivo dopo l'aggiornamento. In questo caso, lo stato di integrità deve tornare a essere OK. Se il nodo non ritorna attivo o in caso di errore, è necessario proseguire nell'analisi del problema.

L'esempio seguente illustra l'evento System.FM con stato di integrità OK per il nodo attivo:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Scadenza dei certificati
**System.FabricNode** segnala una condizione di avviso quando si avvicina la scadenza dei certificati usati dal nodo. Ogni nodo ha tre certificati: **Certificate_cluster**, **Certificate_server** e **Certificate_default_client**. Quando mancano almeno due settimane alla scadenza, lo stato di integrità del report è OK. Quando la scadenza è entro due settimane, il tipo di report è un avviso. Il valore TTL di questi eventi è infinito e vengono rimossi quando un nodo esce dal cluster.

* **SourceId**: System.FabricNode
* **Proprietà**: inizia con **Certificate** e contiene altre informazioni sul tipo di certificato
* **Passaggi successivi**: aggiornare i certificati se sono prossimi alla scadenza.

### <a name="load-capacity-violation"></a>Violazione della capacità di carico
Il servizio di bilanciamento del carico di Service Fabric segnala un avviso se rileva una violazione della capacità del nodo.

* **SourceId**: System.PLB
* **Proprietà**: inizia con **Capacity**
* **Passaggi successivi**: controllare la metrica fornita e visualizzare la capacità corrente nel nodo.

## <a name="application-system-health-reports"></a>Report sull'integrità del sistema di applicazioni
**System.CM**, che rappresenta il servizio Cluster Manager, è l'autorità che gestisce le informazioni sull'applicazione.

### <a name="state"></a>Stato
System.CM restituisce OK quando l'applicazione viene creata o aggiornata. Informa l'archivio integrità quando l'applicazione viene eliminata, in modo che possa essere rimossa dall'archivio.

* **SourceId**: System.CM
* **Proprietà**: State
* **Passaggi successivi**: se l'applicazione è stata creata o aggiornata, deve includere il report sull'integrità dello strumento di gestione cluster. In caso contrario, controllare lo stato dell'applicazione eseguendo una query, ad esempio con il cmdlet di PowerShell **Get-ServiceFabricApplication -ApplicationName *applicationName***.

L'esempio seguente illustra l'evento State nell'applicazione **fabric:/WordCount** :

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Report sull'integrità del sistema di servizi
**System.FM**, che rappresenta il servizio Gestione failover, è l'autorità che gestisce le informazioni sui servizi.

### <a name="state"></a>Stato
System.FM restituisce OK quando il servizio viene creato. Elimina l'entità dall'archivio integrità quando il servizio viene eliminato.

* **SourceId**: System.FM
* **Proprietà**: State

L'esempio seguente illustra l'evento State nel servizio **fabric:/WordCount/WordCountWebService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Errore di correlazione del servizio
**System.PLB** segnala un errore quando rileva che l'aggiornamento di un servizio da correlare con un altro servizio crea una catena di affinità. Il report viene cancellato quando l'aggiornamento viene completato correttamente.

* **SourceId**: System.PLB
* **Proprietà**: ServiceDescription
* **Passaggi successivi**: controllare le descrizioni dei servizi correlati.

## <a name="partition-system-health-reports"></a>Report sull'integrità del sistema di partizioni
**System.FM**, che rappresenta il servizio Gestione failover, è l'autorità che gestisce le informazioni sulle partizioni del servizio.

### <a name="state"></a>Stato
System.FM restituisce OK quando la partizione viene creata ed è integra. Elimina l'entità dall'archivio integrità quando la partizione viene eliminata.

Se il numero di repliche della partizione è inferiore al minimo, viene segnalata una condizione di errore. Se il numero di repliche della partizione non è inferiore al minimo, ma è al di sotto del numero di repliche di destinazione, viene segnalata una condizione di avviso. Se la partizione è in una condizione di perdita del quorum, System.FM segnala un errore.

Altri eventi importanti includono un avviso quando le operazioni di riconfigurazione e di compilazione richiedono più tempo del previsto. I tempi previsti per la compilazione e la riconfigurazione sono configurabili in base agli scenari del servizio. Ad esempio, se un servizio ha uno stato di un terabyte, come ad esempio un database SQL, la compilazione richiederà più tempo rispetto a un servizio con una quantità di stato ridotta.

* **SourceId**: System.FM
* **Proprietà**: State
* **Passaggi successivi**: se lo stato di integrità non è OK, è possibile che alcune repliche non vengano create, aperte o alzate di livello, primario o secondario, nel modo corretto. 

Se nella descrizione viene dichiarata una perdita di quorum, l'esame del dettagliato report sull'integrità per vedere le repliche che hanno smesso di funzionare e ripristinarle dal backup aiuta a rendere nuovamente disponibile online la partizione.

Se la descrizione dichiara che la partizione è bloccata nella fase di [riconfigurazione](service-fabric-concepts-reconfiguration.md), il report sull'integrità della replica primaria fornisce informazioni aggiuntive.

Se si desidera analizzare altri report sull'integrità di System.FM, è consigliabile vedere i report sulle repliche, la partizione o il servizio generati da altri componenti di sistema. 

Gli esempi seguenti descrivono alcuni di questi report. 

L'esempio seguente illustra una partizione integra:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

L'esempio seguente illustra l'integrità di una partizione che è al di sotto del numero di repliche di destinazione. Il passaggio successivo consiste nell'ottenere la descrizione della partizione, che mostra come è configurata: **MinReplicaSetSize** corrisponde a tre e **TargetReplicaSetSize** a sette. Ottenere quindi il numero di nodi nel cluster: cinque. Pertanto, in questo caso, non è possibile collocare due repliche in quanto il numero di repliche di destinazione è superiore al numero di nodi disponibili.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
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
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

L'esempio seguente mostra l'integrità di una partizione che è bloccata nella fase di riconfigurazione perché l'utente non adempie al token di annullamento nel metodo RunAsync. L'esame del report sull'integrità di una qualsiasi replica contrassegnata come Primaria (P) consente di approfondire ulteriormente il problema.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Questo report sull'integrità mostra lo stato delle repliche della partizione in fase di riconfigurazione. 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Per ogni replica il report include le informazioni seguenti:
- Ruolo di configurazione precedente
- Ruolo di configurazione attuale
- [Stato della replica](service-fabric-concepts-replica-lifecycle.md)
- Nodo in cui la replica è in esecuzione
- ID della replica

In una situazione di questo tipo, ad esempio, è consigliabile approfondire il problema analizzando l'integrità di ogni singola replica, partendo da quelle contrassegnate come primarie (131482789658160654 e 131482789688598467) nell'esempio precedente.

### <a name="replica-constraint-violation"></a>Violazione del vincolo di replica
**System.PLB** segnala un avviso se rileva una violazione del vincolo di replica e non può posizionare tutte le repliche della partizione. Il report indica in modo dettagliato i vincoli e le proprietà che impediscono il posizionamento della replica.

* **SourceId**: System.PLB
* **Proprietà**: inizia con **ReplicaConstraintViolation**

## <a name="replica-system-health-reports"></a>Report sull'integrità del sistema di repliche
**System.RA**, che rappresenta il componente agente di riconfigurazione, è l'autorità per lo stato della replica.

### <a name="state"></a>Stato
**System.RA** restituisce OK quando viene creata la replica.

* **SourceId**: System.RA
* **Proprietà**: State

L'esempio seguente illustra una replica integra:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Questa proprietà viene usata per indicare avvisi o errori quando si tenta di aprire o chiudere una replica o trasferirla da un ruolo a un altro (vedere il [ciclo di vita di una replica](service-fabric-concepts-replica-lifecycle.md)). Gli errori potrebbero essere eccezioni generate dalle chiamate API o da arresti anomali del processo host del servizio durante questo periodo. Per eventuali errori causati da chiamate API dal codice C#, Service Fabric aggiunge l'eccezione e l'analisi dello stack al report sull'integrità.

Questi avvisi sull'integrità vengono generati dopo un numero specificato di tentativi di eseguire l'operazione in locale (a seconda dei criteri). Service Fabric continuerà a ripetere l'operazione fino a una soglia massima dopo la quale potrebbe provare a intervenire per correggere la situazione, cancellando così questi avvisi mentre rinuncia ad agire su questo nodo. Se ad esempio, l'apertura di una replica su un nodo ha esito negativo, Service Fabric genera un avviso di integrità. Se l'apertura della replica continua a non riuscire, Service Fabric esegue l'azione di ripristino automatico che può comportare l'esecuzione della stessa operazione in un altro nodo. L'avviso generato per la replica viene così cancellato. 

* **SourceId**: System.RA
* **Proprietà**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, **ReplicaChangeRoleStatus**
* **Passaggi successivi**: esaminare il codice del servizio o i dump di arresto anomalo del sistema per identificare il motivo per cui l'operazione ha esito negativo.

L'esempio seguente mostra l'integrità di una replica che genera `TargetInvocationException` dal relativo metodo open. La descrizione contiene il punto di errore (**IStatefulServiceReplica.Open**), il tipo di eccezione (**TargetInvocationException**) e l'analisi dello stack.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

L'esempio seguente mostra una replica che si arresta in modo anomalo ogni volta che viene chiusa.

```Powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Riconfigurazione
Questa proprietà viene usata per indicare quando una replica in fase di [riconfigurazione](service-fabric-concepts-reconfiguration.md) rileva che la riconfigurazione è in stallo o bloccata. Questo report sull'integrità deve riguardare la replica il cui ruolo attuale è primario (ad eccezione dei casi di una riconfigurazione con scambio di elemento primario in cui può riguardare la replica che è stata abbassata da primaria a secondaria attiva).

Il blocco della riconfigurazione può dipendere dai motivi seguenti:

- Non è stata completata un'azione nella replica locale (la stessa replica di quella in fase di riconfigurazione). I report sull'integrità della replica prodotti da altri componenti (System.RAP or System.RE) possono offrire in questo caso informazioni aggiuntive.

- Un'azione rimane in sospeso in una replica remota. Le repliche con azioni che restano in sospeso vengono elencate nel report sull'integrità. È consigliabile eseguire altre indagini in merito a queste repliche remote nei report sull'integrità. Potrebbero inoltre essere presenti problemi di comunicazione tra un nodo e quello remoto.

In casi rari, la riconfigurazione può bloccarsi a causa di problemi di comunicazione o di altra natura tra il nodo e il servizio Failover Manager.

* **SourceId**: System.RA
* **Proprietà**: **Riconfigurazione**
* **Passaggi successivi**: analizzare le repliche locali o remote in base alla descrizione nel report sull'integrità.

L'esempio seguente mostra un report sull'integrità in cui una riconfigurazione è bloccata nella replica locale (a causa di un servizio che non adempie al token di annullamento).

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

L'esempio seguente mostra un report sull'integrità in cui una riconfigurazione è bloccata in attesa di una risposta da due repliche remote (sono presenti tre repliche nella partizione inclusa quella primaria corrente). 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Questo report sull'integrità mostra che la riconfigurazione è bloccata in attesa di una risposta da due repliche. 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Per ogni replica vengono specificate le informazioni seguenti:
- Ruolo di configurazione precedente
- Ruolo di configurazione attuale
- [Stato della replica](service-fabric-concepts-replica-lifecycle.md)
- Id del nodo
- ID della replica

Per sbloccare la riconfigurazione:
- Le repliche **in arresto** devono essere ripristinate 
- Le repliche **InBuild** devono completare la compilazione e la transizione allo stato pronto

### <a name="slow-service-api-call"></a>Chiamata API del servizio lenta
**System.RAP** e **System.Replicator** segnalano una condizione di avviso se una chiamata al codice del servizio utente richiede più tempo di quello configurato. L'avviso viene cancellato al completamento della chiamata.

* **SourceId**: System.RAP o System.Replicator
* **Proprietà**: nome dell'API lenta. La descrizione fornisce altri dettagli sull'ora in cui l'API è rimasta in sospeso.
* **Passaggi successivi**: esaminare il motivo per cui la chiamata richiede più tempo del previsto.

L'esempio seguente mostra l'evento di integrità da System.RAP per un servizio affidabile che non adempie al token di annullamento in RunAsync.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

La proprietà e il testo indicano quale API può rimanere bloccata. I passaggi successivi per le diverse API bloccate sono diversi. Qualsiasi API in *IStatefulServiceReplica* o *IStatelessServiceInstance* è in genere un bug nel codice del servizio. La sezione seguente illustra come questi elementi si traducono nel [modello servizi affidabili](service-fabric-reliable-services-lifecycle.md).

- **IStatefulServiceReplica.Open**: indica che una chiamata a `CreateServiceInstanceListeners` o a `ICommunicationListener.OpenAsync` o se ignorato `OnOpenAsync` è bloccata.

- **IStatefulServiceReplica.Close** e **IStatefulServiceReplica.Abort**: il caso più comune è un servizio che non adempie al token di annullato passato a `RunAsync`. È possibile inoltre che `ICommunicationListener.CloseAsync` o se ignorato `OnCloseAsync` sia bloccato.

- **IStatefulServiceReplica.ChangeRole(S)** e **IStatefulServiceReplica.ChangeRole(N)**: il caso più comune è un servizio che non adempie al token di annullato passato a `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: il caso più comune è rappresentato dal servizio che non ha restituito un'attività da `RunAsync`.

Altre chiamate API che possono rimanere bloccate sono presenti nell'interfaccia di **IReplicator**, ad esempio:

- **IReplicator.CatchupReplicaSet**: indica che le repliche disponibili non sono sufficienti (fatto che si può stabilire osservando lo stato delle repliche nella partizione o cercando nel report sull'integrità di System.FM eventuali riconfigurazioni bloccate) o che le repliche non riconoscono le operazioni. È possibile usare il command-let `Get-ServiceFabricDeployedReplicaDetail` di PowerShell per determinare lo stato di tutte le repliche. Il problema è con le repliche il cui valore di `LastAppliedReplicationSequenceNumber` è dietro al valore di `CommittedSequenceNumber` della replica primaria.

- **IReplicator.BuildReplica(<Remote ReplicaId>)**: indica un problema nel processo di compilazione (vedere il [ciclo di vita di una replica](service-fabric-concepts-replica-lifecycle.md)). La causa del problema potrebbe essere un'errata configurazione dell'indirizzo del replicator (vedere [questo](service-fabric-reliable-services-configuration.md) e [questo](service-fabric-service-manifest-resources.md)). Potrebbe anche essere un problema del nodo remoto.

### <a name="replication-queue-full"></a>Coda di replica piena
**System.Replicator** segnala un avviso se la coda di replica è piena. Nel server primario la coda di replica in genere si riempie perché una o più repliche secondarie sono lente nel riconoscere le operazioni. Nel server secondario ciò si verifica di solito quando il servizio è lento nell'applicare le operazioni. La condizione di avviso viene cancellata quando la coda non è più piena.

* **SourceId**: System.Replicator
* **Proprietà**: **PrimaryReplicationQueueStatus** o **SecondaryReplicationQueueStatus**, a seconda del ruolo della replica

### <a name="slow-naming-operations"></a>Operazioni di Naming lente
**System.NamingService** segnala lo stato di integrità per la replica primaria quando un'operazione di Naming richiede più tempo di quanto sia accettabile. Esempi di operazioni di Naming sono [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) e [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Altri metodi sono disponibili in FabricClient, ad esempio nell'ambito dei [metodi di gestione dei servizi](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) o dei [metodi di gestione delle proprietà](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Il servizio Naming risolve i nomi del servizio in una posizione nel cluster e consente agli utenti di gestire i nomi e le proprietà del servizio. È un servizio permanente partizionato di Service Fabric. Una partizione rappresenta l'authority owner, contenente i metadati relativi a tutti i nomi e i servizi di Service Fabric. I nomi di Service Fabric sono mappati a partizioni diverse, denominate name owner, e il servizio è quindi estendibile. Per altre informazioni, vedere [Architettura di Service Fabric](service-fabric-architecture.md).
> 
> 

Quando un'operazione di Naming richiede più tempo del previsto, viene contrassegnata con un report di tipo avviso nella *replica primaria della partizione del servizio Naming che gestisce l'operazione*. Se l'operazione viene completata, l'avviso viene cancellato. Se l'operazione viene completata con un errore, il report sull'integrità include i relativi dettagli.

* **SourceId**: System.NamingService
* **Proprietà**: inizia con il prefisso **Duration_** e identifica l'operazione lenta e il nome di Service Fabric su cui viene applicata. Se l'operazione di creazione servizio per il nome fabric:/MyApp/MyService richiede troppo tempo, ad esempio, la proprietà è Duration_AOCreateService.fabric:/MyApp/MyService. AO punta al ruolo della partizione di Naming per il nome e l'operazione.
* **Passaggi successivi**: controllare i motivi per cui l'operazione di Naming non è riuscita. A ogni operazione può corrispondere una causa radice diversa. L'eliminazione di un servizio, ad esempio, può bloccarsi perché l'host applicazione continua ad arrestarsi in modo anomalo in un nodo a causa di un bug di utente nel codice del servizio.

L'esempio seguente illustra un'operazione di creazione servizio. L'operazione ha richiesto un tempo superiore alla durata configurata. AO riprova e invia l'attività a NO. NO completa l'ultima operazione con Timeout. In questo caso, la stessa replica è primaria per entrambi i ruoli AO e NO.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Report sull'integrità del sistema DeployedApplication
**System.Hosting** è l'autorità per le entità distribuite.

### <a name="activation"></a>Activation
System.Hosting restituisce OK quando un'applicazione viene attivata correttamente nel nodo. In caso contrario, restituisce un errore.

* **SourceId**: System.Hosting
* **Proprietà**: Activation, inclusa la versione di implementazione
* **Passaggi successivi**: se l'applicazione non è integra, provare ad analizzare i motivi per cui l'attivazione non è riuscita.

L'esempio seguente illustra l'attivazione riuscita:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Scaricare
**System.Hosting** segnala un errore se il download del pacchetto dell'applicazione non è riuscito.

* **SourceId**: System.Hosting
* **Proprietà**: **Download:*VersioneRollout***
* **Passaggi successivi**: ricercare la causa del download non riuscito nel nodo.

## <a name="deployedservicepackage-system-health-reports"></a>Report sull'integrità del sistema DeployedServicePackage
**System.Hosting** è l'autorità per le entità distribuite.

### <a name="service-package-activation"></a>Attivazione del pacchetto di servizi
System.Hosting restituisce OK se l'attivazione del pacchetto di servizi nel nodo è riuscita. In caso contrario, restituisce un errore.

* **SourceId**: System.Hosting
* **Proprietà**: Activation
* **Passaggi successivi**: analizzare i motivi per cui l'attivazione non è riuscita.

### <a name="code-package-activation"></a>Attivazione del pacchetto di codice
**System.Hosting** restituisce OK per ogni pacchetto di codice se l'attivazione è riuscita. In caso contrario, restituisce l'avviso configurato. Se l'attivazione di **CodePackage** non riesce o termina con un errore superiore alla soglia configurata per **CodePackageHealthErrorThreshold**, viene restituito un errore. Se un pacchetto servizio contiene più pacchetti di codice, viene generato un report sull'attivazione per ognuno.

* **SourceId**: System.Hosting
* **Proprietà**: usa il prefisso **CodePackageActivation** e contiene il nome del pacchetto di codice e il punto di ingresso come **CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint*** ad esempio **CodePackageActivation:Code:SetupEntryPoint**

### <a name="service-type-registration"></a>Registrazione del tipo di servizio
**System.Hosting** restituisce OK se il tipo di servizio è stato registrato correttamente. Viene restituito un errore se la registrazione non è stata eseguita in tempo, secondo quanto configurato con **ServiceTypeRegistrationTimeout**. In caso di chiusura del runtime, viene annullata la registrazione del tipo di servizio nel nodo e viene segnalato un avviso.

* **SourceId**: System.Hosting
* **Proprietà**: usa il prefisso **ServiceTypeRegistration** e contiene il nome del tipo di servizio, ad esempio **ServiceTypeRegistration:FileStoreServiceType**

L'esempio seguente illustra un pacchetto servizio distribuito integro:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Scaricare
**System.Hosting** segnala un errore se il download del pacchetto del servizio non è riuscito.

* **SourceId**: System.Hosting
* **Proprietà**: **Download:*VersioneRollout***
* **Passaggi successivi**: ricercare la causa del download non riuscito nel nodo.

### <a name="upgrade-validation"></a>Convalida dell’aggiornamento
**System.Hosting** segnala un errore se la convalida durante l'aggiornamento non è riuscita oppure se non è riuscito l'aggiornamento nel nodo.

* **SourceId**: System.Hosting
* **Proprietà**: usa il prefisso **FabricUpgradeValidation** e contiene la versione dell'aggiornamento
* **Descrizione**: punta all'errore che si è verificato

## <a name="next-steps"></a>Passaggi successivi
[Come visualizzare i report sull'integrità di Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Creare report e verificare l'integrità dei servizi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorare e diagnosticare servizi in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento di un'applicazione di infrastruttura di servizi](service-fabric-application-upgrade.md)


