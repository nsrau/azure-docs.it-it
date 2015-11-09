<properties
   pageTitle="Aggiungere report sull'integrità dell’Infrastruttura di servizi personalizzati | Microsoft Azure"
   description="Questo articolo descrive come inviare report sull'integrità personalizzati alle entità di integrità di Azure Service Fabric. Offre consigli per la progettazione e l'implementazione di report efficaci relativi all'integrità."
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

# Aggiunta di report sull'integrità di Service Fabric personalizzati
In Service Fabric è presente un [modello di integrità](service-fabric-health-introduction.md) che ha lo scopo di segnalare condizioni di mancata integrità di cluster e applicazioni in entità specifiche. Questo avviene tramite **generatori di report sull'integrità** (watchdog e componenti di sistema). Lo scopo è semplificare e velocizzare la diagnosi e la risoluzione dei problemi. Gli sviluppatori del servizio devono tenere conto dell'integrità fin dall'inizio. È necessario creare report per tutte le condizioni che possono influire sull'integrità, soprattutto se questo aiuta a rilevare problemi vicino alla radice. Questo consente di risparmiare molto tempo per il debug e l'analisi una volta che il servizio è in esecuzione su vasta scala nel cloud (privato o Azure).

I generatori di report di Service Fabric eseguono il monitoraggio di condizioni di particolare interesse. Generano report su tali condizioni in base alla visualizzazione locale. L'[archivio di integrità](service-fabric-health-introduction.md#Health-Store) aggrega i dati sull'integrità inviati da tutti i generatori di report per determinare se le entità sono complessivamente integre. Il modello è concepito per essere completo, flessibile e facile da usare. La qualità dei report sull'integrità determina il livello di accuratezza della visualizzazione dell'integrità del cluster. I falsi positivi che visualizzano erroneamente problemi di mancata integrità possono influire negativamente sugli aggiornamenti o altri servizi che usano i dati sull'integrità, come servizi di ripristino o meccanismi di avviso. Pertanto, è necessario creare report che segnalino le condizioni a cui si è interessati nel miglior modo possibile.

Per progettare e implementare report sull'integrità, i watchdog e i componenti di sistema devono:

- Definire la condizione di cui devono occuparsi, il modo in cui viene monitorata e l'impatto sulla funzionalità del cluster o dell'applicazione. Questo definisce la proprietà del report sull'integrità e lo stato dell'integrità.

- Determinare l'[entità](service-fabric-health-introduction.md#health-entities-and-hierarchy) a cui fa riferimento il report.

- Determinare da dove viene eseguito il report (dall'interno del servizio o da un watchdog interno o esterno).

- Definire un'origine usata per identificare il generatore di report.

- Scegliere una strategia di creazione di report (periodicamente o in caso di transizioni). La creazione periodica di report è la scelta consigliata, poiché richiede codice più semplice e pertanto è meno soggetta a errori.

- Determinare per quanto tempo il report sulle condizioni di mancata integrità deve rimanere nell'archivio di integrità e come deve essere eliminato. Questo definisce il tempo per cui il report deve essere conservato e quando deve essere rimosso al verificarsi delle condizioni di scadenza.

Come indicato in precedenza, il report può essere eseguito da:

- La replica del servizio di Service Fabric monitorato.

- I watchdog interni distribuiti come servizio di Service Fabric. Ad esempio, un servizio di Service Fabric senza stato che esegue il monitoraggio delle condizioni e genera il report. Il watchdog può essere distribuito su tutti i nodi o è possibile creare un'affinità con il servizio monitorato.

- Watchdog interni in esecuzione sui nodi Service Fabric ma **non** implementati come servizi di Service Fabric.

- Watchdog esterni che interrogano la risorsa dall'**esterno** del cluster Service Fabric. Ad esempio, un servizio di monitoraggio simile a Gomez.

> [AZURE.NOTE]Per impostazione predefinita, il cluster viene popolato con report sull'integrità inviati dai componenti di sistema. Per altre informazioni, vedere [Uso dei report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). I report dell'utente devono essere inviati alle [entità di integrità](service-fabric-health-introduction.md#health-entities-and-hierarchy) già create dal sistema.

Una volta definita la progettazione dei report sull'integrità, l'invio di report è semplice. Può avvenire tramite API con FabricClient.HealthManager.ReportHealth, tramite PowerShell o tramite REST. Internamente, tutti i metodi usano un client di integrità contenuto all'interno di un client dell'infrastruttura. Sono disponibili manopole di configurazione per riunire i report in batch, in modo da migliorare le prestazioni.

> [AZURE.NOTE]L'integrità dei report è sincronizzata e rappresenta solo l'attività di convalida sul lato client. Il fatto che il report venga accettato dal client di integrità non significa che venga applicato nell'archivio. Viene inviato in modo asincrono, se possibile in batch con altri report e l'elaborazione sul server potrebbe non riuscire (ad esempio, il numero di sequenza non è stato aggiornato, l'entità a cui deve essere applicato il report è stata eliminata e così via).

## Client di integrità
I report sull'integrità vengono inviati all'archivio di integrità tramite un client di integrità, che risiede all'interno del client dell'infrastruttura. Il client di integrità può essere configurato con gli elementi seguenti:

- HealthReportSendInterval. Il ritardo tra il momento in cui il report viene aggiunto al client e il momento in cui viene inviato all'archivio di integrità. Questo valore viene usato per raggruppare i report in un singolo messaggio, anziché inviare un messaggio per ogni report, in modo da migliorare le prestazioni. Impostazione predefinita: 30 secondi.

- HealthReportRetrySendInterval. L'intervallo dopo il quale il client di integrità invia nuovamente i report sull'integrità accumulati all'archivio di integrità. Impostazione predefinita: 30 secondi.

- HealthOperationTimeout. Il timeout per un messaggio di report inviato all'archivio di integrità. Se un messaggio scade, il client di integrità riprova a inviarlo finché l'archivio di integrità non conferma che i report sono stati elaborati. Impostazione predefinita: 2 minuti.

> [AZURE.NOTE]Quando i report vengono elaborati in batch, il client dell'infrastruttura deve essere mantenuto attivo almeno per il tempo previsto da HealthReportSendInterval per garantire l'invio dei report. Se il messaggio viene perso o l'archivio di integrità non è in grado di applicare i report a causa di errori temporanei, il client dell'infrastruttura deve essere mantenuto attivo più a lungo per dare la possibilità di riprovare.

La memorizzazione nel buffer sul client tiene conto dell'unicità dei report. Ad esempio, se un generatore di report crea 100 report al secondo per la stessa proprietà della stessa entità, i report verranno sostituiti con l'ultima versione. Nella coda del client sarà presente uno solo di tali report. Se si configura l'invio in batch, il numero di report inviato all'archivio di integrità è soltanto uno per intervallo di trasmissione, l'ultimo report aggiunto, che riflette lo stato più recente dell'entità. Tutti i parametri di configurazione possono essere specificati al momento della creazione di FabricClient, passando FabricClientSettings con i valori desiderati per le voci correlate all'integrità.

Il comando seguente crea un client dell'infrastruttura e specifica che i report devono essere inviati non appena vengono aggiunti. In caso di timeout o errori non irreversibili, i nuovi tentativi si verificano ogni 40 secondi.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

È possibile specificare gli stessi parametri quando si crea una connessione a un cluster tramite PowerShell. Il comando seguente avvia una connessione a un cluster locale:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE]Per garantire che i servizi non autorizzati non eseguano report sull'integrità per le entità del cluster, è possibile configurare il server in modo che accetti solo le richieste dai client protetti. Poiché la creazione di report avviene tramite FabricClient, è necessario abilitare la sicurezza su FabricClient per comunicare con il cluster, ad esempio Kerberos o l'autenticazione dei certificati.

## Creare report sull'integrità
Il primo passaggio per la creazione di report di alta qualità consiste nell'identificare le condizioni che possono influire sull'integrità del servizio. Tutte le condizioni che aiutano a identificare problemi nel servizio o nel cluster non appena insorgono o, ancor meglio, prima che si verifichino, consentono un notevole risparmio, riducendo i tempi di inattività e il numero di ore impiegate per l'analisi e la risoluzione dei problemi, oltre ad aumentare la soddisfazione dei clienti.

Dopo aver identificato le condizioni, gli sviluppatori dei watchdog devono stabilire il modo migliore per monitorarle, trovando il giusto equilibrio tra sovraccarico e utilità. Si consideri, ad esempio, un servizio che esegue alcuni calcoli complessi usando alcuni file temporanei in una condivisione. Un watchdog potrebbe monitorare la condivisione per verificare che sia disponibile spazio sufficiente. Può restare in attesa di notifiche relative a modifiche di file o directory. Può segnalare un avviso se si raggiunge una soglia prestabilita e un errore se la condivisione è piena. In caso di avviso, un sistema di ripristino può avviare la pulizia dei file meno recenti nella condivisione. In caso di errore, un sistema di ripristino può spostare la replica del servizio su un altro nodo. Si noti il modo in cui gli stati della condizione vengono descritti in termini di integrità: qual è lo stato della condizione che può essere considerato integro o non integro (avviso o errore).

Dopo aver impostato i dettagli di monitoraggio, gli sviluppatori del watchdog devono decidere come implementarlo. Se è possibile determinare le condizioni dall'interno del servizio, il watchdog può far parte del servizio stesso che viene monitorato. Ad esempio, il codice del servizio può controllare l'uso della condivisione e generare report usando un client dell'infrastruttura locale ogni volta che cerca di scrivere un file. Il vantaggio di questo approccio è dato dalla semplicità della creazione di report. È necessario prestare attenzione per evitare che gli errori del watchdog influiscano negativamente sulla funzionalità del servizio.

Non è sempre possibile creare report dall'interno del servizio monitorato. Un watchdog all'interno del servizio potrebbe non essere in grado di rilevare le condizioni. Il watchdog potrebbe non disporre della logica o dei dati per prendere una decisione, il monitoraggio delle condizioni potrebbe comportare un sovraccarico eccessivo o le condizioni potrebbero non essere specifiche di un servizio, ma avere ripercussioni sulle interazioni tra servizi. Un'altra opzione consiste nel creare i watchdog nel cluster come processi separati. I watchdog eseguono semplicemente il monitoraggio delle condizioni e generano report, senza influire in alcun modo sui servizi principali. Ad esempio, questi watchdog possono essere implementati come servizi senza stato nella stessa applicazione, distribuiti su tutti i nodi o sugli stessi nodi del servizio.

A volte, anche un watchdog in esecuzione sul cluster potrebbe non essere una soluzione efficace. Se le condizioni monitorate sono la disponibilità o la funzionalità del servizio dal punto di vista degli utenti, è preferibile collocare i watchdog nella stessa posizione dei client degli utenti, verificando le operazioni allo stesso modo in cui vengono eseguite dagli utenti. Ad esempio, un watchdog potrebbe trovarsi all'esterno del cluster e inviare richieste al servizio, controllando la latenza e la correttezza del risultato (ad esempio, per un servizio di calcolatrice, verificare se l'operazione "2+2" restituisce "4" in tempi ragionevoli).

Una volta finalizzati i dettagli del watchdog, stabilire un ID di origine che lo identifichi in modo univoco. Se nel cluster sono presenti più watchdog dello stesso tipo, devono generare report per entità diverse, o se generano report per la stessa entità, verificare che l'ID di origine o la proprietà siano diversi, in modo che i report possano coesistere. La proprietà del report sull'integrità deve contenere la condizione monitorata. Ad esempio, nel caso sopra riportato la proprietà potrebbe essere ShareSize. Se si applicano più dati alla stessa condizione, la proprietà deve contenere alcune informazioni dinamiche per consentire la coesistenza dei report. Ad esempio, se sono presenti più condivisioni da sottoporre a monitoraggio, il nome della proprietà potrebbe essere ShareSize-sharename.

> [AZURE.NOTE]L'archivio di integrità **non** deve essere usato per conservare informazioni sullo stato. Nei report, solo le informazioni correlate all'integrità devono essere riportate come informazioni sull'integrità, che influiscono sulla valutazione dell'integrità di un'entità. L'archivio di integrità non è stato progettato come archivio generale. Si avvale della logica di valutazione dell'integrità per aggregare tutti i dati nello stato di integrità. L'invio di informazioni non relative all'integrità (ad esempio, la segnalazione di stati integri) non ha ripercussioni sullo stato di integrità aggregato, ma può influire negativamente sulle prestazioni dell'archivio di integrità.

Il passaggio successivo consiste nel decidere l'entità per la quale creare report. Di solito, questa decisione è ovvia a seconda della condizione. È consigliabile scegliere l'entità con la migliore granularità possibile. Se una condizione influisce su tutte le repliche in una partizione, creare report sulla partizione, non sul servizio. Vi sono casi particolari che richiedono maggiore attenzione. Se la condizione influisce su un'entità, ad esempio una replica, ma si desidera segnalare la condizione per un tempo superiore alla durata della vita della replica, è necessario creare report sulla partizione. In caso contrario, quando viene eliminata la replica, tutti i report associati vengono eliminati dall'archivio. Questo significa che gli sviluppatori dei watchdog devono anche considerare la durata dell'entità e del report. È necessario specificare chiaramente quando rimuovere un report da un archivio, ad esempio quando un errore segnalato per un'entità non si verifica più.

L'esempio seguente illustra i punti sopra indicati. Si consideri un'applicazione di Service Fabric composta da un servizio master con stato persistente e servizi slave senza stato distribuiti su tutti i nodi (un tipo di servizio slave per ogni tipo di attività). Il servizio master dispone di una coda di elaborazione con i comandi che devono essere eseguiti dai servizi slave. I servizi slave eseguono le richieste in entrata e restituiscono acknowledgement. Una condizione che potrebbe essere monitorata è la lunghezza della coda di elaborazione del servizio master. Se la lunghezza della coda del master raggiunge una soglia prestabilita, può essere generato un avviso, che segnala che i servizi slave non sono in grado di gestire il carico. Se la coda ha raggiunto la lunghezza massima consentita e i comandi vengono eliminati, viene generato un errore in quanto il servizio non può essere ripristinato. I report possono riguardare la proprietà "QueueStatus". Il watchdog si trova all'interno del servizio e viene inviato periodicamente alla replica primaria del servizio master. Il valore TTL è 2 minuti e l'invio avviene periodicamente ogni 30 secondi. Se il servizio primario diventa inattivo, il report viene eliminato automaticamente dall'archivio. Se la replica del servizio è attiva ma in deadlock o presenta altri problemi, il report scade nell'archivio di integrità e l'entità viene considerata in stato di errore.

Un'altra condizione che può essere monitorata è il tempo di esecuzione delle attività. Il servizio master distribuisce le attività ai server slave in base al tipo di attività. A seconda della struttura, il servizio master può eseguire il polling dei servizi slave per quanto riguarda lo stato delle attività o può attendere che i servizi slave inviino acknowledgement al termine dell'operazione. Nel secondo caso, è necessario prestare attenzione a individuare le situazioni in cui i servizi slave vengono interrotti o i messaggi vengono persi. Un'opzione consiste nell'invio di una richiesta ping da parte del master allo stesso slave, che restituisce lo stato. Se non viene ricevuto alcuno stato, si è verificato un errore e l'attività deve essere pianificata nuovamente. Questo presuppone che le attività siano idempotenti. La condizione monitorata può essere riportata come avviso, se l'attività non viene eseguita entro un periodo di tempo t1 (ad esempio, 10 minuti), e come errore, se l'attività non viene completata entro un periodo di tempo t2 (ad esempio, 20 minuti). Questo tipo di report può essere creato in diversi modi:

- La replica primaria del servizio master genera periodicamente report su se stessa. È possibile usare una proprietà per tutte le attività in sospeso nella coda: se almeno un'attività richiede più tempo, si genererà un avviso o un errore per la proprietà "PendingTasks", come appropriato. Se non sono presenti attività in sospeso o tutte le attività sono state appena avviate, viene segnalato uno stato integro. Le attività sono persistenti, pertanto se il servizio primario si arresta, il nuovo servizio promosso al ruolo di primario può continuare a generare report correttamente.

- Un altro processo di watchdog (nel cloud o esterno) verifica le attività (dall'esterno, in base al risultato desiderato per l'attività) per controllare se sono state completate. Se non rispettano le soglie, creare report sul servizio master. Creare report su ogni attività e includere l'identificatore dell'attività, ad esempio PendingTask+taskid. Generare report solo per gli stati non integri. Impostare il valore TTL su alcuni minuti e indicare i report da rimuovere alla scadenza, per garantire la pulizia.

- Il servizio slave che esegue un'attività segnala se l'esecuzione richiede più tempo del previsto. La segnalazione riguarda l'istanza del servizio della proprietà "PendingTasks". Questo individua l'istanza del servizio che presenta problemi, ma allo scadere dell'istanza non consente di valutare la situazione, in quanto i report vengono eliminati. È possibile creare report per il servizio slave. Se l'attività viene completata, l'istanza slave elimina il report dall'archivio. Questo non permette di valutare la situazione se il messaggio di acknowledgement viene perso e l'attività non viene completata dal punto di vista del master.

Tuttavia, se la creazione di report viene eseguita nei casi descritti in precedenza, i report verranno acquisiti nell'integrità dell'applicazione quando questa viene valutata.

## Report periodici e report in caso di transizione a confronto
Usando il modello di report sull'integrità, i watchdog possono inviare report periodicamente o in caso di transizioni. È consigliabile inviarli periodicamente, perché il codice è molto più semplice, pertanto genera meno errori. I watchdog devono essere più semplici possibile per evitare bug che generano report errati. Eventuali report che segnalano erroneamente la mancata integrità influiscono sulla valutazione dell'integrità e sugli scenari basati sull'integrità, ad esempio gli aggiornamenti. I report che segnalano erroneamente la mancata integrità hanno l'effetto di nascondere problemi del cluster ed è quindi opportuno evitare che vengano generati.

Per i report periodici, il watchdog può essere implementato con un timer. Alla richiamata del timer, il watchdog controlla lo stato e invia un report in base allo stato corrente. Non è necessario visualizzare il report precedentemente inviato o eseguire ottimizzazioni in termini di messaggistica. A questo scopo, il client di integrità è dotato di logica per la creazione di batch. Finché il client di integrità viene mantenuto attivo, esegue nuovi tentativi internamente finché il report non riceve un acknowledgement dall'archivio di integrità o il watchdog genera un report più recente con entità, proprietà e origine identiche.

La creazione di report in caso di transizioni richiede una gestione attenta dello stato. Il watchdog esegue il monitoraggio di alcune condizioni e segnala soltanto eventuali modifiche di tali condizioni. L'aspetto positivo è che sono necessari meno report. L'aspetto negativo è che la logica del watchdog è complessa. È necessario mantenere le condizioni o i report in modo da poterli esaminare per determinare le modifiche dello stato. In caso di failover, è necessario prestare attenzione a inviare report che potrebbero non essere stati inviati in precedenza (in coda, ma non ancora inviati all'archivio di integrità). Il numero di sequenza deve essere sempre incrementato, altrimenti i report verranno rifiutati in quanto obsoleti. Nei rari casi in cui si verifica una perdita di dati, potrebbe essere necessaria la sincronizzazione tra lo stato del generatore di report e quello dell'archivio di integrità.

## Implementare report sull'integrità
Dopo aver definito l'entità e i dettagli, l'invio dei report sull'integrità può essere eseguito tramite API, PowerShell o REST.

### API
Per usare un'API, gli utenti devono creare un report sull'integrità specifico per il tipo di entità per la quale si desidera generare il report e quindi assegnarvi un client di integrità.

L'esempio seguente illustra la generazione periodica di report da un watchdog all'interno del cluster. Il controllo determina se è possibile accedere a una risorsa esterna dall'interno di un nodo. La risorsa è richiesta da un manifesto del servizio all'interno dell'applicazione. Se la risorsa non è disponibile, gli altri servizi all'interno dell'applicazione non sono in grado di funzionare correttamente. Pertanto, il report viene inviato per l'entità del pacchetto di servizio distribuito, periodicamente ogni 30 secondi.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snipet brevity.
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### PowerShell
Gli utenti possono inviare rapporti sull'integrità con Send-ServiceFabric*EntityType*HealthReport.

L'esempio seguente illustra la generazione di report periodica sui valori della CPU in un nodo. I report devono essere inviati ogni 30 secondi e hanno un valore TTL di 2 minuti. Se scadono, significa che il generatore di report presenta problemi e pertanto il nodo viene considerato in stato di errore. Quando la CPU supera una soglia specificata, il report genera un avviso sullo stato di integrità. Se la CPU supera la soglia specificata più di una volta, viene segnalata come in stato di errore. In caso contrario, il generatore di report segnala uno stato integro.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

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

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

L'esempio seguente indica un avviso temporaneo in una replica. Prima di tutto ottiene l'ID della partizione e della replica per il servizio interessato, quindi invia un report da PowershellWatcher sulla proprietà ResourceDependency. Il report è valido solo per 2 minuti e viene automaticamente rimosso dall'archivio.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

## Passaggi successivi

In base ai dati sull'integrità, gli sviluppatori del servizio e gli amministratori di cluster e applicazioni possono valutare come usare le informazioni. Ad esempio, è possibile impostare avvisi in base allo stato dell'integrità per rilevare problemi gravi prima che provochino interruzioni. È possibile impostare sistemi di correzione automatica per risolvere i problemi automaticamente.

[Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)

[Come visualizzare i report sull'integrità di Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Uso dei report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Come eseguire il monitoraggio e la diagnosi dei servizi localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=Nov15_HO1-->