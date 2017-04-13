---
title: "Aggiungere report personalizzati sull&quot;integrità di Service Fabric | Documentazione Microsoft"
description: "Questo articolo descrive come inviare report sull&quot;integrità personalizzati alle entità di integrità di Azure Service Fabric. Offre consigli per la progettazione e l&quot;implementazione di report efficaci relativi all&quot;integrità."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2017
ms.author: oanapl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d986275612b3e5c97636936a5e448dd6d0fcfc4e
ms.lasthandoff: 11/17/2016


---
# <a name="add-custom-service-fabric-health-reports"></a>Aggiungere report sull'integrità di Service Fabric personalizzati
In Azure Service Fabric è disponibile un [modello di integrità](service-fabric-health-introduction.md) progettato per contrassegnare condizioni di non integrità di cluster e applicazioni in entità specifiche. Il modello di integrità usa **i** , costituiti da watchdog e componenti di sistema. Lo scopo è semplificare e velocizzare la diagnosi e la risoluzione dei problemi. Gli sviluppatori del servizio devono tenere conto dell'integrità fin dall'inizio. È necessario segnalare tutte le condizioni che possono influire sull'integrità, soprattutto se aiutano a risalire alla causa dei problemi. Le informazioni sull'integrità consentono di risparmiare tempo ed energie per il debug e l'analisi quando il servizio sarà in esecuzione su vasta scala nel cloud (privato o Azure).

I generatori di report di Service Fabric eseguono il monitoraggio di condizioni di particolare interesse. Generano report su tali condizioni in base alla visualizzazione locale. L'[archivio integrità](service-fabric-health-introduction.md#health-store) aggrega i dati sull'integrità inviati da tutti i reporter per determinare se le entità sono complessivamente integre. Il modello è concepito per essere completo, flessibile e facile da usare. La qualità dei report sull'integrità determina il livello di accuratezza della visualizzazione dell'integrità del cluster. I falsi positivi che visualizzano erroneamente problemi di non integrità possono influire negativamente sugli aggiornamenti o su altri servizi che usano i dati di integrità, come ad esempio i servizi di ripristino e i meccanismi di avviso. Pertanto, è necessario creare report che segnalino le condizioni a cui si è interessati nel miglior modo possibile.

Per progettare e implementare report sull'integrità, i watchdog e i componenti di sistema devono:

* Definire la condizione di cui devono occuparsi, il modo in cui viene monitorata e l'impatto sulla funzionalità del cluster o dell'applicazione. Tali informazioni determinano la proprietà del report sull'integrità e lo stato di integrità.
* Determinare l' [entità](service-fabric-health-introduction.md#health-entities-and-hierarchy) a cui si applica il report.
* Determinare dove viene eseguito il report, ovvero dall'interno del servizio o da un watchdog interno o esterno.
* Definire un'origine usata per identificare il generatore di report.
* Scegliere una strategia di creazione di report (periodicamente o in caso di transizioni). La creazione periodica di report è la scelta consigliata, perché richiede codice più semplice ed è meno soggetta a errori.
* Determinare per quanto tempo il report sulle condizioni di non integrità deve rimanere nell'archivio integrità e come deve essere eliminato. Queste informazioni determinano la durata (TTL) del report e il comportamento alla scadenza.

Come indicato, i report possono essere creati da:

* La replica del servizio di Service Fabric monitorato.
* Watchdog interni distribuiti come servizio di Service Fabric, ad esempio un servizio senza stato di Service Fabric che monitora le condizioni e genera i report. Il watchdog può essere distribuito su tutti i nodi o è possibile creare un'affinità con il servizio monitorato.
* Watchdog interni eseguiti sui nodi di Service Fabric ma *non* implementati come servizi di Service Fabric.
* Watchdog esterni che interrogano la risorsa dall' *esterno* del cluster di Service Fabric, ad esempio un servizio di monitoraggio come Gomez.

> [!NOTE]
> Per impostazione predefinita, il cluster viene popolato con report sull'integrità inviati dai componenti di sistema. Per altre informazioni, vedere [Uso dei report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). I report dell'utente devono essere inviati alle [entità di integrità](service-fabric-health-introduction.md#health-entities-and-hierarchy) già create dal sistema.
> 
> 

Una volta definita la progettazione dei report sull'integrità, è possibile inviarli facilmente. È possibile usare [FabricClient](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.aspx) per segnalare lo stato di integrità se il cluster non è [sicuro](service-fabric-cluster-security.md) o se il client Fabric ha privilegi di amministratore. Questa operazione può essere eseguita tramite l'API con [FabricClient.HealthManager.ReportHealth](https://msdn.microsoft.com/library/system.fabric.fabricclient.healthclient.reporthealth.aspx), tramite PowerShell oppure REST. Sono disponibili controlli di configurazione che riuniscono i report in batch per migliorare le prestazioni.

> [!NOTE]
> L'esecuzione di report sull'integrità è un'operazione sincrona e rappresenta solo l'attività di convalida sul lato client. Il fatto che il report venga accettato dal client di integrità o dagli oggetti `Partition` o `CodePackageActivationContext` non significa che venga applicato nell'archivio. Viene inviato in modo asincrono e possibilmente in batch con altri report. L'elaborazione sul server può comunque non riuscire. È possibile che un numero di sequenza non sia aggiornato, che l'entità a cui deve essere applicato il report sia stata eliminata e così via.
> 
> 

## <a name="health-client"></a>Client di integrità
I report sull'integrità vengono inviati all'archivio integrità tramite un client di integrità, che risiede nel client Fabric. Il client di integrità può essere configurato con gli elementi seguenti:

* **HealthReportSendInterval**: ritardo tra il momento in cui il report viene aggiunto al client e il momento in cui viene inviato all'archivio integrità. Questo valore viene usato per inviare i report in batch un singolo messaggio, anziché inviare un messaggio per ogni report, e migliorare così le prestazioni. Impostazione predefinita: 30 secondi.
* **HealthReportRetrySendInterval**: intervallo dopo il quale il client di integrità invia nuovamente i report sull'integrità accumulati all'archivio integrità. Impostazione predefinita: 30 secondi.
* **HealthOperationTimeout**: periodo di timeout per un messaggio di report inviato all'archivio integrità. In caso di timeout di un messaggio scade, il client di integrità riprova a inviarlo finché l'archivio integrità non conferma che i report sono stati elaborati. Valore predefinito: due minuti.

> [!NOTE]
> Quando i report vengono riuniti in batch, il client Fabric deve restare attivo almeno per il tempo previsto da HealthReportSendInterval per garantire l'invio dei report. Se il messaggio viene perso o l'archivio integrità non può applicare i report a causa di errori temporanei, il client Fabric deve restare attivo più a lungo per dare la possibilità di riprovare.
> 
> 

La memorizzazione nel buffer sul client tiene conto dell'unicità dei report. Se un particolare generatore di report non corretto crea 100 report al secondo per la stessa proprietà della stessa entità, ad esempio, i report vengono sostituiti con l'ultima versione. Nella coda del client sarà presente uno solo di questi report. Se è configurato l'invio in batch, il numero di report inviati all'archivio integrità è solo uno per ogni intervallo di trasmissione. Questo è l'ultimo report aggiunto, che riflette lo stato più recente dell'entità.
Tutti i parametri di configurazione possono essere specificati durante la creazione di `FabricClient` passando [FabricClientSettings](https://msdn.microsoft.com/library/azure/system.fabric.fabricclientsettings.aspx) con i valori desiderati per le voci correlate all'integrità.

Il comando seguente crea un client Fabric e specifica che i report devono essere inviati quando vengono aggiunti. In caso di timeout ed errori che supportano nuovi tentativi, questi vengono eseguiti ogni 40 secondi.

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

> [!NOTE]
> Per assicurare che i servizi non autorizzati non eseguano report sull'integrità per le entità del cluster, è possibile configurare il server in modo che accetti solo le richieste dai client protetti. Per la classe `FabricClient` usata per la creazione di report è necessario abilitare la sicurezza per consentire la comunicazione con il cluster, ad esempio con Kerberos o l'autenticazione del certificato. Per altre informazioni, vedere la pagina relativa alla [sicurezza del cluster](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Report dai servizi con privilegi limitati
Nei servizi di Service Fabric privi di accesso amministrativo al cluster è possibile segnalare lo stato dell'integrità relativo alle entità dal contesto corrente tramite `Partition` o `CodePackageActivationContext`.

* Per i servizi senza stato usare [IStatelessServicePartition.ReportInstanceHealth](https://msdn.microsoft.com/library/system.fabric.istatelessservicepartition.reportinstancehealth.aspx) per creare report relativi all'istanza del servizio corrente.
* Per i servizi con stato usare [IStatefulServicePartition.ReportReplicaHealth](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.reportreplicahealth.aspx) per creare report relativi alla replica corrente.
* Usare [IServicePartition.ReportPartitionHealth](https://msdn.microsoft.com//library/system.fabric.iservicepartition.reportpartitionhealth.aspx) per creare report relativi all'entità della partizione corrente.
* Usare [CodePackageActivationContext.ReportApplicationHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportapplicationhealth.aspx) per creare report relativi all'applicazione corrente.
* Usare [CodePackageActivationContext.ReportDeployedApplicationHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth.aspx) per creare report relativi all'applicazione corrente distribuita sul nodo corrente.
* Usare [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth.aspx) per creare report relativi a un pacchetto del servizio per l'applicazione corrente distribuita sul nodo corrente.

> [!NOTE]
> Internamente gli oggetti `Partition` e `CodePackageActivationContext` includono un client di integrità che viene configurato con le impostazioni predefinite. Sono applicabili le stesse considerazioni illustrate per il [client di integrità](service-fabric-report-health.md#health-client). I report vengono riuniti in batch e inviati in base a un timer, quindi gli oggetti devono essere mantenuti attivi per avere la possibilità di inviare il report.
> 
> 

## <a name="design-health-reporting"></a>Creare report sull'integrità
Il primo passaggio per la creazione di report di alta qualità consiste nell'identificare le condizioni che possono influire sull'integrità del servizio. Tutte le condizioni che facilitano l'identificazione dei problemi nel servizio o nel cluster non appena insorgono, o meglio ancora prima che si verifichino, possono consentire un notevole risparmio, grazie alla riduzione dei tempi di inattività e del numero di ore impiegate per l'analisi e la risoluzione dei problemi, oltre all'aumento della soddisfazione dei clienti.

Dopo aver identificato le condizioni, gli sviluppatori dei watchdog devono stabilire il modo migliore per monitorarle, trovando il giusto equilibrio tra sovraccarico e utilità. Si consideri, ad esempio, un servizio che esegue calcoli complessi usando alcuni file temporanei in una condivisione. Un watchdog può monitorare la condivisione per verificare che sia disponibile spazio sufficiente. Può restare in attesa di notifiche relative a modifiche di file o directory. Può segnalare un avviso se si raggiunge una soglia prestabilita e un errore se la condivisione è piena. In caso di avviso, un sistema di ripristino può avviare la pulizia dei file meno recenti nella condivisione. In caso di errore, un sistema di ripristino può spostare la replica del servizio su un altro nodo. Si noti come vengono descritti gli stati della condizione in termini di integrità: lo stato della condizione può essere considerato integro o non integro (avviso o errore).

Dopo aver impostato i dettagli di monitoraggio, gli sviluppatori del watchdog devono decidere come implementarlo. Se è possibile determinare le condizioni dall'interno del servizio, il watchdog può far parte del servizio stesso che viene monitorato. Ad esempio, il codice del servizio può controllare l'uso della condivisione e quindi generare report usando un client Fabric locale ogni volta che prova a scrivere un file. Il vantaggio di questo approccio è dato dalla semplicità della creazione di report. È tuttavia necessario prestare attenzione per evitare che gli errori del watchdog influiscano negativamente sulla funzionalità del servizio.

Non è sempre possibile creare report dall'interno del servizio monitorato. Un watchdog all'interno del servizio potrebbe non riuscire a rilevare le condizioni. Potrebbe non avere la logica o i dati per prendere una decisione. Il monitoraggio delle condizioni potrebbe comportare un sovraccarico eccessivo. Le condizioni potrebbero anche non essere specifiche di un servizio, ma avere invece ripercussioni sulle interazioni tra i servizi. Un'altra opzione consiste nel creare i watchdog nel cluster come processi separati. I watchdog eseguono semplicemente il monitoraggio delle condizioni e generano report, senza influire in alcun modo sui servizi principali. Ad esempio, questi watchdog possono essere implementati come servizi senza stato nella stessa applicazione, distribuiti in tutti i nodi o negli stessi nodi del servizio.

A volte anche un watchdog in esecuzione nel cluster può non essere una soluzione efficace. Se la condizione monitorate è la disponibilità o la funzionalità del servizio dal punto di vista degli utenti, è preferibile inserire i watchdog nella stessa posizione dei client degli utenti, dove possono testare le operazioni allo stesso modo in cui vengono eseguite dagli utenti. Ad esempio, è possibile configurare un watchdog residente all'esterno del cluster che invia le richieste al servizio e quindi controlla la latenza e la correttezza del risultato. Ad esempio, per un servizio calcolatrice, verificare se l'operazione 2+2 restituisce 4 in tempi ragionevoli.

Una volta finalizzati i dettagli del watchdog, stabilire un ID di origine che lo identifichi in modo univoco. Se nel cluster sono presenti più watchdog dello stesso tipo, devono generare report per entità diverse oppure, se generano report per la stessa entità, verificare che l'ID di origine o la proprietà siano diversa, in modo che i report possano coesistere. La proprietà del report sull'integrità deve contenere la condizione monitorata. Ad esempio, nel caso sopra riportato la proprietà può essere **ShareSize**. Se più report si applicano alla stessa condizione, la proprietà deve contenere alcune informazioni dinamiche per consentire la coesistenza dei report. Ad esempio, se è necessario monitorare più condivisioni, il nome della proprietà può essere **ShareSize-sharename**.

> [!NOTE]
> L'archivio di integrità *non* deve essere usato per conservare informazioni sullo stato. Nei report solo le informazioni correlate all'integrità devono essere segnalate come informazioni sull'integrità, perché influiscono sulla valutazione dell'integrità di un'entità. L'archivio integrità non è stato progettato come archivio per utilizzo generico. Usa la logica di valutazione dell'integrità per aggregare tutti i dati nello stato di integrità. L'invio di informazioni non correlate all'integrità, come la segnalazione di uno stato di integrità corretto, non ha ripercussioni sullo stato di integrità aggregato, ma può influire negativamente sulle prestazioni dell'archivio integrità.
> 
> 

Il passaggio successivo consiste nel decidere per quale entità creare report. Di solito questa decisione è ovvia, a seconda della condizione. È consigliabile scegliere l'entità con la migliore granularità possibile. Se una condizione influisce su tutte le repliche in una partizione, creare report sulla partizione, non sul servizio. Esistono tuttavia casi particolari che richiedono maggiore attenzione. Se la condizione influisce su un'entità, ad esempio una replica, ma si vuole segnalare la condizione per un tempo superiore alla durata della replica, è necessario creare report sulla partizione. In caso contrario, quando viene eliminata la replica, tutti i report associati vengono eliminati dall'archivio. Questo significa che gli sviluppatori dei watchdog devono anche considerare la durata dell'entità e del report. È necessario specificare chiaramente quando rimuovere un report da un archivio, ad esempio quando un errore segnalato per un'entità non si verifica più.

L'esempio seguente illustra i punti descritti. Si consideri un'applicazione di Service Fabric composta da un servizio master con stato persistente e servizi secondari senza stato distribuiti in tutti i nodi, ovvero un tipo di servizio secondario per ogni tipo di attività. Il servizio master ha una coda di elaborazione con i comandi che devono essere eseguiti dai servizi secondari. I servizi secondari eseguono le richieste in ingresso e restituiscono segnali di conferma. Una condizione che è possibile monitorare è la lunghezza della coda di elaborazione del servizio master. Se la lunghezza della coda master raggiunge una soglia, viene generato un avviso. L'avviso indica che i servizi secondari non possono gestire il carico. Se la coda raggiunge la lunghezza massima e i comandi vengono eliminati, viene generato un errore perché il servizio non può essere ripristinato. I report possono riguardare la proprietà **QueueStatus**. Il watchdog si trova all'interno del servizio e viene inviato periodicamente alla replica primaria del servizio master. La durata (TTL) è di 2 minuti e l'invio avviene periodicamente ogni 30 secondi. Se il servizio primario diventa inattivo, il report viene eliminato automaticamente dall'archivio. Se la replica del servizio è attiva ma presenta un deadlock o altri problemi, il report scade nell'archivio integrità. In questo caso, l'entità viene considerata in stato di errore.

Un'altra condizione che può essere monitorata è il tempo di esecuzione delle attività. Il servizio master distribuisce le attività ai server secondari in base al tipo di attività. A seconda della progettazione, il servizio master può eseguire il polling dei servizi secondari per quanto riguarda lo stato delle attività. Può anche attendere che i servizi secondari inviino segnali di conferma al termine dell'operazione. Nel secondo caso, è necessario prestare attenzione a individuare le situazioni in cui i servizi secondari vengono interrotti o i messaggi vengono persi. Un'opzione consiste nell'invio di una richiesta ping da parte del master allo stesso servizio secondario, che restituisce lo stato. Se non riceve alcuno stato, il master lo considera un errore e ripianifica l'attività. Questo comportamento presuppone che le attività siano idempotenti.

La condizione monitorata può essere riportata come avviso se l'attività non viene eseguita entro un certo periodo di tempo **t1**, ad esempio, 10 minuti. La condizione monitorata può essere riportata come errore se l'attività non viene completata entro il periodo di tempo **t2**, ad esempio, 20 minuti. Questo tipo di report può essere creato in diversi modi:

* La replica primaria del servizio master genera periodicamente report su se stessa. È possibile configurare una proprietà per tutte le attività in sospeso nella coda. Se almeno una delle attività richiede più tempo, lo stato del report sulla proprietà **PendingTasks** è un avviso o errore, secondo il caso. Se non sono presenti attività in sospeso o è stata avviata l'esecuzione di tutte le attività, viene segnalato uno stato integro. Le attività sono persistenti. Se il servizio primario si arresta, il nuovo servizio alzato di livello al ruolo di primario può continuare a generare report correttamente.
* Un altro processo di watchdog, nel cloud o esterno, verifica le attività dall'esterno, in base al risultato desiderato per l'attività, per controllare se sono state completate. Se non rispettano le soglie, viene inviato un report sul servizio master. Viene inviato anche un report per ogni attività che include l'identificatore dell'attività, ad esempio **PendingTask+taskId**. I report dovranno essere inviati solo sugli stati non integri. Impostare la durata (TTL) su alcuni minuti e contrassegnare i report da rimuovere alla scadenza, per garantire la pulizia.
* Il servizio secondario che esegue un'attività segnala quando l'esecuzione richiede più tempo del previsto. La segnalazione riguarda l'istanza del servizio della proprietà **PendingTasks**. Il report individua l'istanza del servizio che presenta problemi, ma allo scadere dell'istanza non consente di valutare la situazione, perché i report vengono eliminati. È possibile creare report sul servizio secondario. Se l'attività viene completata, l'istanza del servizio secondario elimina il report dall'archivio. Il report non permette di valutare la situazione se il messaggio di acknowledgement viene perso e l'attività non viene completata dal punto di vista del master.

Se però la creazione di report viene eseguita nei casi descritti sopra, i report vengono acquisiti nell'integrità dell'applicazione quando questa viene valutata.

## <a name="report-periodically-vs-on-transition"></a>Report periodici e report in caso di transizione a confronto
Usando il modello di report sull'integrità, i watchdog possono inviare report periodicamente o in caso di transizioni. Nel caso dei report watchdog, è consigliabile inviarli periodicamente, perché il codice è molto più semplice e meno soggetto a errori. I watchdog devono essere più semplici possibile per evitare bug che generano report non corretti. Eventuali report che segnalano erroneamente la *mancata integrità* influiscono sulla valutazione dell'integrità e sugli scenari basati sull'integrità, inclusi gli aggiornamenti. I report che segnalano erroneamente la *mancata integrità* hanno l'effetto di nascondere problemi del cluster ed è quindi opportuno evitare che vengano generati.

Per i report periodici, il watchdog può essere implementato con un timer. In caso di callback del timer, il watchdog può controllare lo stato e inviare un report in base allo stato corrente. Non è necessario visualizzare il report inviato in precedenza o eseguire ottimizzazioni in termini di messaggistica. Ai fini delle prestazioni, il client di integrità è dotato di logica per l'invio in batch. Finché il client di integrità viene mantenuto attivo, esegue nuovi tentativi internamente finché il report non riceve un acknowledgement dall'archivio integrità o il watchdog genera un report più recente con entità, proprietà e origine identiche.

La creazione di report in caso di transizioni richiede una gestione attenta dello stato. Il watchdog esegue il monitoraggio di alcune condizioni e segnala soltanto eventuali modifiche di tali condizioni. Il lato positivo di questo approccio è che sono necessari meno report. Il lato negativo è che la logica del watchdog è complessa. È necessario che il watchdog mantenga le condizioni o i report per poterli esaminare e determinare i cambiamenti di stato. In caso di failover, prestare attenzione quando viene inviato un report che potrebbe non essere stato inviato in precedenza, ovvero è in coda ma non ancora inviato all'archivio integrità. Il numero di sequenza deve essere crescente. In caso contrario, i report verranno rifiutati come non aggiornati. Nei rari casi in cui si verifica una perdita di dati, potrebbe essere necessaria la sincronizzazione tra lo stato del reporter e quello dell'archivio integrità.

La creazione di report relativi alle transizioni risulta utile per i servizi che inviano report relativi a se stessi, tramite `Partition` o `CodePackageActivationContext`. Quando viene rimosso l'oggetto locale, ovvero una replica o un pacchetto del servizio distribuito oppure un'applicazione distribuita, vengono rimossi anche tutti i rispettivi report. Grazie a questa pulizia automatica, non è strettamente necessario eseguire la sincronizzazione tra il generatore di report e l'archivio integrità. Se il report è relativo a una partizione o un'applicazione padre, è necessario prestare attenzione in caso di failover, in modo da evitare report obsoleti nell'archivio integrità. Occorre aggiungere la logica per mantenere lo stato corretto e cancellare il report dall'archivio quando non è più necessario.

## <a name="implement-health-reporting"></a>Implementare report sull'integrità
Dopo aver definito l'entità e i dettagli del report, l'invio dei report sull'integrità può essere eseguito tramite API, PowerShell o REST.

### <a name="api"></a>API
Per usare un'API, è necessario creare un report sull'integrità specifico per il tipo di entità desiderato. Assegnare il report a un client di integrità. In alternativa, creare informazioni sull'integrità e passarle ai metodi di creazione report corretti in `Partition` o `CodePackageActivationContext` per generare report sulle entità correnti.

L'esempio seguente illustra la generazione periodica di report da un watchdog all'interno del cluster. I controlli del watchdog determinano se è possibile accedere a una risorsa esterna dall'interno di un nodo. La risorsa è richiesta da un manifesto del servizio all'interno dell'applicazione. Se la risorsa non è disponibile, gli altri servizi all'interno dell'applicazione possono comunque funzionare correttamente. Il report viene quindi inviato per l'entità del pacchetto del servizio distribuito ogni 30 secondi.

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

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Inviare report sull'integrità con **Send-ServiceFabric*TipoEntità*HealthReport**.

L'esempio seguente illustra la generazione di report periodica sui valori della CPU in un nodo. I report devono essere inviati ogni 30 secondi e hanno una durata (TTL) di 2 minuti. Se scadono, nel reporter sono presenti problemi e quindi il nodo viene considerato in stato di errore. Quando la CPU è oltre la soglia specificata, il report presenta uno stato di integrità di tipo avviso. Quando la CPU rimane oltre la soglia specificata per più di tempo di quello configurato, verrà segnalato come errore. In caso contrario, il reporter invia uno stato di integrità corretto.

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

L'esempio seguente segnala un avviso temporaneo in una replica. Ottiene prima di tutto l'ID della partizione e quindi all'ID della replica per il servizio interessato. Invia quindi un report da **PowershellWatcher** sulla proprietà **ResourceDependency**. Il report è valido solo per due minuti e viene rimosso automaticamente dall'archivio.

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

### <a name="rest"></a>REST
Inviare report sull'integrità usando REST con richieste POST indirizzate all'entità desiderata e contenenti nel corpo la descrizione del report sull'integrità. Vedere ad esempio come inviare [report sull'integrità di un cluster](https://msdn.microsoft.com/library/azure/dn707640.aspx) o [report sull'integrità di un servizio](https://msdn.microsoft.com/library/azure/dn707640.aspx) con REST. Sono supportate tutte le entità.

## <a name="next-steps"></a>Passaggi successivi
In base ai dati sull'integrità, gli sviluppatori del servizio e gli amministratori di cluster e applicazioni possono valutare come usare le informazioni. Ad esempio, è possibile impostare avvisi in base allo stato integrità per rilevare problemi gravi prima che provochino interruzioni. Gli amministratori possono anche configurare sistemi di ripristino per risolvere i problemi automaticamente.

[Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)

[Come visualizzare i report sull'integrità di Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Creare report e verificare l'integrità dei servizi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Uso dei report sull'integrità del sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorare e diagnosticare servizi in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento di un'applicazione di infrastruttura di servizi](service-fabric-application-upgrade.md)


