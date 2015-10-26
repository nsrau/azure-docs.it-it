<properties
   pageTitle="Utilizzo dei report di integrità del sistema per la risoluzione dei problemi"
   description="Vengono descritti i rapporti di integrità del sistema e come utilizzarli per la risoluzione dei problemi del cluster o delle applicazioni"
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
   ms.date="09/03/2015"
   ms.author="oanapl"/>

# Utilizzo dei report di integrità del sistema per la risoluzione dei problemi

I componenti di Infrastruttura di servizi forniscono report, per impostazione predefinita,su tutte le entità del cluster. Nell’[Archivio integrità](service-fabric-health-introduction.md#health-store) le entità vengono create ed eliminate in base ai report di sistema e vengono organizzate in una gerarchia che acquisisce le interazioni delle entità.

> [AZURE.NOTE]Ulteriori informazioni sul [modello di integrità di Infrastruttura di servizi](service-fabric-health-introduction.md) per comprendere i concetti correlati all’integrità.

I report di integrità del sistema forniscono visibilità nella funzionalità del cluster e dell'applicazione e contrassegnano i problemi riscontrati nell’integrità. Per applicazioni e servizi, i report di integrità del sistema verificano che le entità vengano implementate e che il comportamento sia corretto dal punto di vista di Infrastruttura di servizi. I report non forniscono il monitoraggio dell'integrità della logica di business del servizio o il rilevamento dei processi bloccati. I servizi utente possono arricchire i dati di integrità con informazioni specifiche per la logica.

> [AZURE.NOTE]I report di integrità watchdog sono visibili solo **dopo** la creazione di un’entità da parte dei componenti di sistema. Quando viene eliminata un'entità, in Archivio integrità tutti i report ad essa associati vengono eliminati automaticamente. Lo stesso accade quando viene creata una nuova istanza dell'entità (ad esempio, viene creata una nuova istanza di replica del servizio): tutti i report associati all'istanza precedente vengono eliminati e rimossi dall’archivio.

I report dei componenti di sistema vengono identificati dall’origine, che inizia con il prefisso *"System."*. I watchdog non possono utilizzare lo stesso prefisso per le rispettive origini, in quanto i report verranno rifiutati con parametri non validi. Osserviamo alcuni report di sistema per capire da quali eventi vengono attivati e come risolvere gli eventuali problemi che rappresentano.

> [AZURE.NOTE]Infrastruttura di servizi continua ad aggiungere report per le condizioni di interesse che possono migliorare la visibilità degli eventi che si verificano nell'applicazione o nel cluster.

## Report di integrità del sistema cluster
L'entità di integrità del cluster viene creata automaticamente nell'archivio di integrità, pertanto se tutto funziona correttamente non dispone di un report di sistema.

### Perdita di risorse
Nei report System.Federation viene segnalato un errore quando si rileva una perdita di risorse. Il rapporto proviene dai singoli nodi e l'ID del nodo è incluso nel nome della proprietà. Se si verifica una sola perdita di risorse nell’intero anello di Infrastruttura di servizi, in genere è possibile prevedere due eventi (entrambi i lati del gap effettueranno la segnalazione). In caso di più perdite di risorse, si verificheranno più eventi. Nel report viene specificato il timeout Durata globale come valore TTL, e il report viene reinviato ogni metà del TTL finché la condizione è ancora attiva. L'evento viene rimosso automaticamente alla scadenza, pertanto, se il nodo di creazione dei report è disattivato, viene correttamente rimosso dall'Archivio integrità.

- SourceId: System.Federation
- Proprietà: inizia con "Neighborhood" e include informazioni sul nodo.
- Passaggi successivi: esaminare il motivo per cui la risorsa viene persa. Ad esempio, controllare la comunicazione tra i nodi del cluster.

## Report di integrità del sistema del nodo
System.FM, che rappresenta il servizio Gestione failover, è l'autorità che gestisce le informazioni sui nodi del cluster. Qualsiasi nodo deve disporre di un report da System.FM in cui viene mostrato lo stato. Le entità del nodo vengono rimosse quando il nodo è disabilitato.

### Nodo su/giù
System.FM segnala OK quando il nodo viene aggiunto l'anello (è attivo e in esecuzione), ed errore quando il nodo si allontana dall'anello (è disattivato, per l'aggiornamento oppure semplicemente per un guasto). La gerarchia dell’integrità creata da Archivio integrità interviene sulle entità distribuite in correlazione con i report del nodo System.FM. Considera il nodo un padre virtuale di tutte le entità distribuite. Le entità distribuite in tale nodo non vengono esposte tramite le query se il nodo è inattivo o non segnalato oppure se il nodo dispone di un'istanza diversa rispetto all'istanza associata alle entità. Quando FM segnala il nodo inattivo o riavviato (nuova istanza), l’Archivio integrità elimina automaticamente le entità distribuite che possono essere presenti unicamente sul nodo inattivo o sull’istanza precedente del nodo.

- SourceId: System.FM
- Proprietà: State
- Passaggi successivi: se il nodo è attivo per l'aggiornamento, dovrebbe diventare attivo una volta aggiornato. In tal caso lo stato dell’integrità dovrebbe essere riportato su OK. Se il nodo non ritorna attivo o se è guasto, sono necessarie ulteriori indagini.

Di seguito viene illustrato l'evento System.FM con stato di integrità OK per il nodo attivo:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### Scadenza dei certificati
System.FabricNode segnala una condizione di avviso quando i certificati utilizzati dal nodo sono vicini alla scadenza. Esistono tre certificati per ogni nodo: Certificate\_cluster, Certificate\_server e Certificate\_default\_client. Quando la scadenza è almeno dopo due settimane, il tipo di report è OK. Se la scadenza è entro due settimane, il tipo di report è Warning. Il valore TTL di questi eventi è infinito, vengono rimossi quando un nodo abbandona il cluster.

- SourceId: System.FabricNode
- Proprietà: inizia con "Certificate" e contiene ulteriori informazioni sul tipo di certificato.
- Passaggi successivi: aggiornare i certificati se sono prossimi alla scadenza.

### Violazione della capacità di carico
Il bilanciamento di carico di Infrastruttura di servizi segnala una condizione Warning se rileva una violazione della capacità del nodo.

 - SourceId: System.PLB
 - Proprietà: inizia con "Capacity".
 - Passaggi successivi: controllare le metriche fornite e visualizzare la capacità corrente nel nodo.

## Report di integrità del sistema dell’applicazione
System.CM, che rappresenta il servizio Cluster Manager, è l'autorità che gestisce le informazioni sull’applicazione.

### State
System.CM segnala OK quando l'applicazione viene creata o aggiornata. Informa l’Archivio integrità quando l'applicazione viene eliminata, in modo che possa essere rimossa dall'archivio.

- SourceId: System.CM
- Proprietà: State
- Passaggi successivi: se l'applicazione viene creata, è necessario che disponga del rapporto di integrità CM. In caso contrario, controllare lo stato dell'applicazione eseguendo una query (ad esempio, il cmdlet Powershell Get-ServiceFabricApplication -ApplicationName <applicationName>).

Di seguito viene illustrato l’evento State nell’applicazione fabric:/WordCount.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None) -DeployedApplicationsHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## Report di integrità del sistema del servizio
System.FM, che rappresenta il servizio Gestione failover, è l'autorità che gestisce le informazioni sui servizi.

### State
System.FM segnala OK quando viene creato il servizio. Elimina l'entità da Archivio integrità quando il servizio viene eliminato.

- SourceId: System.FM
- Proprietà: State.

Di seguito viene illustrato l'evento State nel servizio fabric:/WordCount/WordCountService.

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### Violazione di repliche non collocate
System.PLB segnala una condizione Warning se non riesce a trovare una posizione per uno o più repliche del servizio. Il report viene rimosso quando scade.

- SourceId: System.FM
- Proprietà: State.
- Passaggi successivi: controllare i vincoli del servizio. Controllare lo stato attuale del posizionamento.

## Report di integrità del sistema della partizione
System.FM, che rappresenta il servizio Gestione failover, è l'autorità che gestisce le informazioni sulle partizioni di servizio.

### State
System.FM segnala OK quando la partizione viene creata ed è integra. Elimina l'entità da Archivio integrità quando la partizione viene eliminata. Se la partizione è al di sotto del conteggio min delle repliche, viene segnalata una condizione di errore. Se la partizione non è al di sotto del conteggio min delle repliche, ma è al di sotto del conteggio delle repliche di destinazione, viene segnalata una condizione di avviso. Se la partizione è nella perdita del quorum, FM segnala un errore. Altri eventi importanti sono: avviso quando la riconfigurazione richiede più tempo del previsto e quando la compilazione richiede più tempo del previsto. I tempi previsto tempi per la compilazione o la riconfigurazione sono configurabili in base agli scenari del servizio. Ad esempio, se un servizio dispone di una quantità di stato nell’ordine di TB, come SQL Azure, la compilazione richiederà più tempo rispetto a un servizio con una piccola quantità di stato.

- SourceId: System.FM
- Proprietà: State.
- Passaggi successivi: se lo stato di integrità non è OK, è possibile che alcune repliche non vengano create o aperte/innalzate al livello primario o secondario correttamente. In molti casi, la ragione principale è un bug del servizio nell’implementazione dell’apertura o della modifica del ruolo.

Di seguito viene illustrata una partizione integra.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

Di seguito viene illustrata l’integrità di una partizione che è al di sotto del conteggio delle repliche di destinazione. Passaggi successivi: ottenere la descrizione della partizione, che mostra il modo in cui è stata configurata: MinReplicaSetSize è 2, TargetReplicaSetSize è 7. Quindi, ottenere il numero di nodi nel cluster: 5. Pertanto in questo caso, 2 repliche non possono essere posizionate.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### Violazione del vincolo di replica
System.PLB segnala una condizione di avviso se rileva la violazione del vincolo di replica e non è in grado di posizionare le repliche della partizione.

- SourceId: System.PLB
- Proprietà: inizia con "ReplicaConstraintViolation".

## Report di integrità del sistema delle repliche
System.RA, che rappresenta il componente Agente di riconfigurazione, è l'autorità per lo stato della replica.

### State
System.RA segnala una condizione OK quando viene creata la replica.

- SourceId: System.RA
- Proprietà: State.

Di seguito viene illustrata una replica integra:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### Stato aperto della replica
La descrizione di questo report di integrità contiene l'ora di inizio (UTC) del momento in cui è stata eseguita la chiamata dell'API. System.RA segnala una condizione di avviso se l’apertura della replica richiede più tempo del periodo configurato (impostazione predefinita: 30 minuti). Se l'API influisce sulla disponibilità del servizio, il report viene eseguito molto più rapidamente (intervallo di tempo configurabile, valore predefinito di 30 secondi). Questo tempo include il tempo impiegato per l’apertura del replicatore e l’apertura del servizio. Se l’apertura viene completata, la proprietà cambia in OK.

- SourceId: System.RA
- Proprietà: ReplicaOpenStatus.
- Passaggi successivi: se lo stato di integrità non è OK, controllare perché l’apertura della replica richiede più tempo del previsto.

### Chiamata API del servizio lenta
System.RAP e System.Replicator segnalano una condizione di avviso se una chiamata nel codice del servizio utente richiede più tempo di quello configurato. L'avviso viene cancellato al completamento della chiamata.

- SourceId: System.RAP o System.Replicator
- Proprietà: nome dell'API lenta. Nella descrizione vengono forniti ulteriori dettagli sull’ora in cui l'API è rimasta in sospeso.
- Passaggi successivi: esaminare il motivo per cui la chiamata richiede più tempo del previsto.

Nell'esempio seguente viene illustrata una partizione nella perdita di quorum e i passaggi di indagine effettuati per capire il motivo. Una delle repliche presenta uno stato di integrità Warning, pertanto consideriamo questa integrità. Viene mostrato che l’operazione di servizio richiede più tempo del previsto, evento segnalato da System.RAP. Dopo queste informazioni, il passaggio successivo consiste nell’esaminare il codice del servizio e indagare. In questo caso, l'implementazione di RunAsync del servizio con stato genera un'eccezione non gestita. Si noti che viene eseguito il riciclo delle repliche, pertanto è possibile che non venga visualizzata alcuna replica nello stato Warning. Riprovare a ottenere l'integrità e osservare se sono presenti differenze nell'ID di replica, indicazione che in alcuni casi fornisce qualche indizio.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Quando si avvia l'applicazione difettosa nel debugger, le finestre Eventi di diagnostica mostrano l'eccezione generata da RunAsync:

![Eventi di diagnostica di Visual Studio 2015: errore RunAsync in fabric:/HelloWorldStatefulApplication.][1]

Eventi di diagnostica di Visual Studio 2015: errore RunAsync in fabric:/HelloWorldStatefulApplication.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### Coda di replica piena
System.Replicator segnala una condizione di avviso se la coda di replica è piena. Nel server primario, ciò accade solitamente perché una o più repliche secondarie sono lente nel riconoscere le operazioni. Nel server secondario, ciò si verifica quando il servizio è lento nell’applicare le operazioni. La condizione di avviso viene cancellata quando la coda non è più piena.

- SourceId: System.Replicator
- Proprietà: PrimaryReplicationQueueStatus o SecondaryReplicationQueueStatus, a seconda del ruolo della replica.

## Report di integrità del sistema DeployedApplication
System.Hosting è l'autorità sulle entità distribuite.

### Activation
System.Hosting segnala OK quando un’applicazione viene attivata correttamente nel nodo. In caso contrario, segnala una condizione di errore.

- SourceId: System.Hosting
- Proprietà: Activation. Include la versione di implementazione.
- Passaggi successivi: se non è integro, provare a indagare sui motivi per cui l'attivazione non è riuscita.

Di seguito viene illustrata l'attivazione riuscita:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Download
System.Hosting segnala una condizione di errore se il download del pacchetto di applicazione non è riuscito.

- SourceId: System.Hosting
- Proprietà: Download:<RolloutVersion>
- Passaggi successivi: ricercare la causa della mancata riuscita del download nel nodo.

## Report di integrità del sistema DeployedServicePackage
System.Hosting è l'autorità sulle entità distribuite.

### Attivazione del pacchetto di servizi
System.Hosting segnala OK se l’attivazione del pacchetto di servizi nel nodo ha esito positivo. In caso contrario, segnala una condizione di errore.

- SourceId: System.Hosting
- Proprietà: Activation.
- Passaggi successivi: ricercare la causa della mancata riuscita dell’applicazione.

### Attivazione del pacchetto di codice
System.Hosting segnala OK per ogni pacchetto di codice se l'attivazione ha esito positivo. Se l'attivazione ha esito negativo, segnala una condizione di avviso, come configurato; se l’attivazione di CodePackage non è riuscita oppure è terminata con un errore oltre il valore 'CodePackageHealthErrorThreshold' configurato, Hosting segnala una condizione di errore. Se sono presenti più pacchetti di codice in un pacchetto del servizio, verrà generato un report Activation per ciascun pacchetto.

- SourceId: System.Hosting
- Proprietà: prefisso CodePackageActivation e contiene il nome del pacchetto di codice e il punto di ingresso. CodePackageActivation:<CodePackageName>:<SetupEntryPoint/EntryPoint>. Ad esempio, CodePackageActivation:Code:SetupEntryPoint.

### Registrazione del tipo di servizio
System.Hosting segnala OK se il tipo di servizio è stato registrato correttamente. Viene segnalata una condizione di errore se la registrazione non è stata effettuata in tempo (configurato con ServiceTypeRegistrationTimeout). Se la registrazione del tipo di servizio al nodo viene annullata, perché il runtime è stato chiuso. Hosting segnala una condizione di avviso

- SourceId: System.Hosting
- Proprietà: prefisso ServiceTypeRegistration e contiene il nome del tipo di servizio. ed esempio "ServiceTypeRegistration:FileStoreServiceType"

Di seguito viene mostrato un pacchetto del servizio distribuito integro.

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Download
System.Hosting segnala una condizione di errore se il download del pacchetto del servizio non è riuscito.

- SourceId: System.Hosting
- Proprietà: Download:<RolloutVersion>
- Passaggi successivi: ricercare la causa della mancata riuscita del download nel nodo.

### Convalida dell’aggiornamento
System.Hosting segnala una condizione di errore se si verifica un errore di convalida durante l'aggiornamento o se l'aggiornamento non riesce nel nodo.

- SourceId: System.Hosting
- Proprietà: prefisso FabricUpgradeValidation, contiene la versione di aggiornamento.
- Descrizione: punta all’errore che si è verificato.

## Passaggi successivi
[Come visualizzare i report di integrità di Infrastruttura di servizi](service-fabric-view-entities-aggregated-health.md)

[Come eseguire il monitoraggio e la diagnosi dei servizi localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento di un’applicazione Infrastruttura di servizi](service-fabric-application-upgrade.md)
 

<!---HONumber=Oct15_HO3-->