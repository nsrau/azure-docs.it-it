<properties
   pageTitle="Indicazioni sui processi in background | Microsoft Azure"
   description="Indicazioni sulle attività in background eseguite in modo indipendente dall'interfaccia utente."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/13/2016"
   ms.author="masashin"/>

# Linee guida per i processi in background

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## Panoramica

Molti tipi di applicazioni richiedono attività in background che vengono eseguite in modo indipendente dell'interfaccia utente (UI). Alcuni esempi sono i processi batch, le attività con uso intensivo della capacità di elaborazione e i processi a esecuzione prolungata come i flussi di lavoro. I processi in background possono essere eseguiti senza l'interazione dell'utente: l'applicazione può avviare il processo e quindi continuare a elaborare le richieste interattive degli utenti. Questo consente di ridurre al minimo il carico sull'interfaccia utente dell'applicazione, consentendo di migliorare la disponibilità e di abbreviare i tempi di risposta interattiva.

Ad esempio, se un'applicazione deve generare le anteprime delle immagini caricate dagli utenti, può eseguire questa operazione come processo in background e salvare l'anteprima nella risorsa di archiviazione al termine dell'operazione senza che l’utente debba attendere il completamento del processo. Nello stesso modo, un utente che effettua un ordine può avviare un flusso di lavoro di background che elabora l'ordine, mentre continua a visualizzare l'app Web tramite l'interfaccia utente. Al termine del processo in background l'applicazione può aggiornare i dati degli ordini archiviati e inviare un messaggio di posta elettronica all'utente per confermare l'ordine.

Per decidere se implementare un'attività come processo in background, il criterio principale da valutare è stabilire se l'attività può essere eseguita senza l’interazione dell'utente e senza che l'interfaccia utente debba attendere il completamento del processo. Le attività che richiedono che l'utente o l'interfaccia utente attenda il completamento potrebbero non essere appropriate come processi in background.

## Tipi di processi in background

I processi in background includono in genere uno o più dei processi seguenti:

- Processi con uso intensivo della CPU, come calcoli matematici o analisi di modelli strutturali.
- Processi con uso intensivo dell'I/O, come l'esecuzione di una serie di transazioni di archiviazione o l'indicizzazione di file.
- Processi batch, come gli aggiornamenti dei dati nelle ore notturne o l'elaborazione pianificata.
- Flussi di lavoro a esecuzione prolungata, come l'evasione di ordini o il provisioning di servizi e sistemi.
- Elaborazione di dati sensibili dove l'attività viene trasferita in una posizione più sicura per l'elaborazione. Ad esempio, elaborare dati sensibili in un’app Web potrebbe non essere opportuno e si potrebbe preferire l’uso di un modello come [Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx) per trasferire i dati a un processo in background isolato con accesso a una risorsa di archiviazione protetta.

## Trigger

I processi in background possono essere avviati in diversi modi. I trigger sono classificabili nelle seguenti categorie:

- [**Trigger guidati dagli eventi**](#event-driven-triggers). L'attività viene avviata in risposta a un evento, in genere un'azione eseguita da un utente o un passaggio di un flusso di lavoro.
- [**Trigger guidati dalla pianificazione**](#schedule-driven-triggers). L'attività viene richiamata in una pianificazione basata su un timer. Può trattarsi di una pianificazione ricorrente o di una chiamata occasionale specificata per un momento successivo.

### Trigger guidati dagli eventi

Una chiamata guidata dagli eventi usa un trigger per avviare l'attività in background. Di seguito sono riportati alcuni esempi di uso di trigger guidati dagli eventi:

- L'interfaccia utente o un altro processo inserisce un messaggio in una coda. Il messaggio contiene dati su un'azione che ha avuto luogo, ad esempio un ordine effettuato dall'utente. L'attività in background è in attesa nella coda e rileva l'arrivo di un nuovo messaggio. Legge il messaggio e usa i dati come input per il processo in background.
- L'interfaccia utente o un altro processo salva o aggiorna un valore nella risorsa di archiviazione. L'attività in background monitora la risorsa di archiviazione e rileva i cambiamenti. Legge i dati e li usa come input per il processo in background.
- L'interfaccia utente o un altro processo invia una richiesta a un endpoint, ad esempio un URI HTTPS o un'API esposta come servizio Web. Come parte della richiesta, passa i dati necessari per completare l'attività in background. Il servizio Web o l'endpoint richiama l'attività in background che usa i dati come input.

Esempi tipici di attività appropriate per una chiamata guidata dagli eventi includono l'elaborazione delle immagini, i flussi di lavoro, l'invio di informazioni ai servizi remoti, l'invio di messaggi di posta elettronica e il provisioning di nuovi utenti in applicazioni multi-tenant.

### Trigger guidati dalla pianificazione

Una chiamata guidata dalla pianificazione usa un timer per avviare l'attività in background. Di seguito sono riportati alcuni esempi di uso di trigger guidati dalla pianificazione:

- Un timer eseguito in locale all'interno dell'applicazione o come parte del sistema operativo dell'applicazione richiama un'attività in background a intervalli regolari.
- Un timer in esecuzione in un'altra applicazione o un servizio timer come Utilità di pianificazione di Azure invia una richiesta a un’API o a un servizio Web a intervalli regolari. Il servizio Web o l'API richiama l'attività in background.
- Un processo separato o un'applicazione avvia un timer che causa il richiamo dell'attività in background una volta dopo un ritardo di tempo specificato o in un momento specifico.

Esempi tipici di attività appropriate per una chiamata guidata dalla pianificazione sono le routine di elaborazione batch (come l'aggiornamento di elenchi di prodotti correlati per gli utenti in base al loro comportamento recente), le attività di elaborazione dati routinarie (come l'aggiornamento di indici o la generazione di risultati cumulativi), l'analisi dei dati per report giornalieri, la pulizia per la conservazione dei dati e i controlli di coerenza dei dati.

Se si usa un'attività guidata dalla pianificazione che deve essere eseguita come singola istanza, tenere presente quanto segue:

- Se l'istanza di calcolo che esegue l'utilità di pianificazione (ad esempio una macchina virtuale che usa operazioni pianificate di Windows) viene dimensionata, saranno presenti più istanze dell'utilità di pianificazione in esecuzione e queste potrebbero avviare più istanze dell'attività.
- Se l'esecuzione delle attività dura più a lungo del tempo che intercorre tra gli eventi dell'utilità di pianificazione, tale utilità può avviare un'altra istanza dell'attività mentre quella precedente è ancora in esecuzione.

## Restituzione di risultati

I processi in background vengono eseguiti in modo asincrono in un processo separato o anche in una posizione diversa rispetto all'interfaccia utente o al processo che ha richiamato l'attività in background. In teoria, le attività in background sono operazioni "fire and forget" e lo stato di avanzamento dell'esecuzione non ha alcun impatto sull'interfaccia utente o sul processo chiamante. Questo significa che il processo chiamante non attende il completamento delle attività. Pertanto non può rilevare automaticamente quando l’attività termina.

Se occorre che un'attività in background comunichi con l'attività chiamante per indicare lo stato di avanzamento o il completamento, è necessario implementare un meccanismo apposito. Di seguito sono riportati alcuni esempi:

- Scrivere un valore di stato nella risorsa di archiviazione che sia accessibile all'attività dell'interfaccia utente o del chiamante in modo che questa possa monitorare o controllare tale valore quando richiesto. Altri dati che l'attività in background deve restituire al chiamante possono essere collocati nella stessa risorsa di archiviazione.
- Stabilire una coda di risposta in cui l'interfaccia utente o il chiamante restano in ascolto. L'attività in background può inviare messaggi alla coda indicanti lo stato e il completamento. I dati che l'attività in background deve restituire al chiamante possono essere inseriti nei messaggi. Se si utilizza Bus di servizio di Azure, è possibile utilizzare le proprietà **ReplyTo** e **CorrelationId** per implementare questa funzionalità. Per ulteriori informazioni, vedere [Correlazione nella messaggistica negoziata di Bus di servizio](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Esporre un'API o un endpoint dell'attività in background a cui l'interfaccia utente o il chiamante possa accedere per ottenere informazioni sullo stato. I dati che l'attività in background deve restituire al chiamante possono essere inclusi nella risposta.
- Fare in modo che l'attività in background richiami l'interfaccia utente o il chiamante tramite un'API per indicare lo stato in determinati punti o al completamento. A tal fine si possono usare eventi generati in locale oppure un meccanismo di pubblicazione e sottoscrizione. I dati che l'attività in background deve restituire al chiamante possono essere inclusi nella richiesta o nel payload dell'evento.

## Ambiente di hosting

È possibile ospitare le attività in background usando vari servizi diversi della piattaforma Azure:

- [**App Web e Processi Web di Azure**](#azure-web-apps-and-webjobs). È possibile usare Processi Web per eseguire processi personalizzati basati su tipi diversi di script o programmi eseguibili nell'ambito di un'app Web.
- [**Ruoli di lavoro e Web di Servizi cloud di Azure**](#azure-cloud-services-web-and-worker-roles). È possibile scrivere codice all'interno di un ruolo eseguito come attività in background.
- [**Macchine virtuali di Azure**](#azure-virtual-machines). Se si dispone di un servizio Windows o si desidera usare Utilità di pianificazione di Windows, è pratica comune ospitare le attività in background in una macchina virtuale dedicata.

Le sezioni seguenti descrivono dettagliatamente le opzioni e contengono considerazioni che consentono di scegliere quella appropriata.

## App Web e Processi Web di Azure

È possibile usare Processi Web di Azure per eseguire processi personalizzati come attività in background all'interno di un'app Web di Azure. I processi Web vengono eseguiti nell'ambito dell'app Web come processi continui. I processi Web vengono eseguiti anche in risposta a un evento di attivazione di Utilità di pianificazione di Azure o a fattori esterni come ad esempio le modifiche dei BLOB di archiviazione e le code di messaggi. I processi possono essere avviati e interrotti su richiesta e arrestati in modo normale. Se un processo Web in esecuzione continua si interrompe viene riavviato automaticamente. Le azioni relative a nuovi tentativi ed errori sono configurabili.

Quando si configura un processo Web:

- Se il processo dovrà rispondere a un trigger guidato dagli eventi, è necessario configurarlo come **Esegui in modo continuo**. Lo script o il programma viene archiviato nella cartella denominata site/wwwroot/app\_data/jobs/continuous.
- Se il processo dovrà rispondere a un trigger guidato dalla pianificazione, è necessario configurarlo come **Esegui in base a una pianificazione**. Lo script o il programma viene archiviato nella cartella denominata site/wwwroot/app\_data/jobs/triggered.
- Se nella configurazione di un processo si sceglie l'opzione **Esegui su richiesta**, verrà eseguito lo stesso codice eseguito con l'opzione **Esegui in base a una pianificazione** al momento dell'avvio.

I processi Web di Azure vengono eseguiti all'interno della sandbox dell'app Web. Perciò possono accedere alle variabili di ambiente e condividere informazioni come le stringhe di connessione con l'app Web. Il processo ha accesso all'identificatore univoco del computer che esegue il processo stesso. La stringa di connessione denominata **AzureWebJobsStorage** fornisce l'accesso alle code, ai BLOB e alle tabelle di archiviazione di Azure per i dati applicativi nonché l'accesso al bus di servizio per la messaggistica e la comunicazione. La stringa di connessione denominata **AzureWebJobsDashboard** fornisce accesso ai file di log delle azioni del processo.

I processi Web di Azure presentano le caratteristiche seguenti:

- **Sicurezza**: i processi Web sono protetti dalle credenziali di distribuzione dell'app Web.
- **Tipi di file supportati**: è possibile definire i processi Web usando script di comando (cmd), file batch (bat), script di PowerShell (ps1), script bash shell (sh), script PHP (php), script Python (py), codice JavaScript (js) e programmi eseguibili (exe, jar e così via).
- **Distribuzione**: è possibile distribuire gli script e gli eseguibili tramite il Portale di Azure, usando il componente aggiuntivo [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) per Visual Studio o [Visual Studio 2013 Update 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs) (questa opzione consente la creazione e la distribuzione), con [Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md) o copiandoli direttamente nei percorsi seguenti:
  - Per l'esecuzione con trigger: site/wwwroot/app\_data/jobs/triggered/{nome processo}
  - Per l'esecuzione continua: site/wwwroot/app\_data/jobs/continuous/{nome processo}
- **Registrazione in file di log**: Console.Out viene trattato (contrassegnato) come INFO. Console.Error viene trattato come errore. È possibile accedere alle informazioni di monitoraggio e diagnostica dal Portale di Azure. I file di log possono essere scaricati direttamente dal sito. Tali file vengono salvati nei percorsi seguenti:
  - Per l'esecuzione con trigger: Vfs/data/jobs/triggered/nomeProcesso
  - Per l'esecuzione continua: Vfs/data/jobs/continuous/nomeProcesso
- **Configurazione**: è possibile configurare WebJobs tramite il portale, l'API REST e PowerShell. Si può usare un file di configurazione denominato settings.job nella stessa directory radice dello script del processo per fornire le informazioni di configurazione per un processo. Ad esempio:
  - { "stopping\_wait\_time": 60 }
  - { "is\_singleton": true }

### Considerazioni

- Per impostazione predefinita, i processi Web sono ridimensionati con l'app Web. I processi possono tuttavia essere configurati per l'esecuzione in una singola istanza impostando la proprietà di configurazione **is\_singleton** su **true**. I processi Web a istanza singola sono utili per le attività che non si vuole scalare o eseguire come istanze multiple simultanee, come ad esempio la reindicizzazione, l'analisi dei dati e attività simili.
- Per ridurre al minimo l'impatto dei processi sulle prestazioni dell'app Web, è consigliabile creare un'istanza vuota di App Web di Azure in un nuovo piano di servizio app per ospitare i processi Web che potrebbero avere un'esecuzione prolungata o usare in modo intensivo le risorse.

### Altre informazioni

- In [Risorse di documentazione di Processi Web di Azure](./app-service-web/websites-webjobs-resources.md) sono elencate molte risorse utili, download ed esempi relativi a Processi Web.

## Ruoli di lavoro e Web di Servizi cloud di Azure

È possibile eseguire attività in background all'interno di un ruolo Web o in un ruolo di lavoro distinto. Per decidere se usare un ruolo di lavoro è necessario considerare i requisiti di scalabilità ed elasticità, la durata dell'attività, la cadenza di rilascio, la sicurezza, la tolleranza di errore, la contesa, la complessità e l'architettura logica. Per altre informazioni, vedere [Modello di consolidamento delle risorse di calcolo](http://msdn.microsoft.com/library/dn589778.aspx).

Esistono diversi modi per implementare le attività di background in un ruolo Servizi cloud:

- Creare un'implementazione della classe **RoleEntryPoint** nel ruolo e usarne i metodi per eseguire attività in background. Le attività vengono eseguite nel contesto di WaIISHost.exe. Possono usare il metodo **GetSetting** della classe **CloudConfigurationManager** per caricare le impostazioni di configurazione. Per altre informazioni, vedere [Ciclo di vita (Servizi cloud)](#lifecycle-cloud-services).
- Usare le attività di avvio per eseguire attività in background all'avvio dell'applicazione. Per forzare il proseguimento dell'esecuzione delle attività in background, impostare la proprietà **taskType** su **background** (in caso contrario il processo di avvio dell'applicazione si arresterà e attenderà il completamento dell'attività). Per altre informazioni, vedere [Come configurare ed eseguire attività di avvio per un servizio cloud](./cloud-services/cloud-services-startup-tasks.md).
- Usare WebJobs SDK per implementare attività in background come ad esempio WebJobs, che vengono avviate come attività di avvio. Per altre informazioni, vedere [Creare un processo Web .NET nel servizio app di Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md).
- Usare un'attività di avvio per installare un servizio Windows che esegue una o più attività in background. È necessario impostare la proprietà **taskType** su **background** in modo che il servizio venga eseguito in background. Per altre informazioni, vedere [Come configurare ed eseguire attività di avvio per un servizio cloud](./cloud-services/cloud-services-startup-tasks.md).

### Esecuzione di attività in background nel ruolo Web

Il vantaggio principale dell'esecuzione di attività in background nel ruolo Web è il risparmio dei costi di hosting perché non è richiesta la distribuzione di ruoli aggiuntivi.

### Esecuzione di attività in background in un ruolo di lavoro

L'esecuzione di attività in background in un ruolo di lavoro presenta diversi vantaggi:

- Consente di gestire il ridimensionamento separatamente per ogni tipo di ruolo. Ad esempio, potrebbero essere necessarie più istanze di un ruolo Web per supportare il carico corrente, ma un minor numero di istanze del ruolo di lavoro che esegue le attività in background. Il dimensionamento delle istanze di calcolo dell'attività in background separatamente dai ruoli dell'interfaccia utente consente di ridurre i costi di hosting mantenendo prestazioni accettabili.
- Scarica il sovraccarico dell'elaborazione per le attività in background del ruolo Web. Il ruolo Web che fornisce l'interfaccia utente può rimanere attivo per cui è necessario un minor numero di istanze per supportare un determinato volume di richieste dagli utenti.
- Consente di implementare la separazione delle problematiche. Ogni tipo di ruolo può implementare un set specifico di attività ben definite e correlate. La progettazione e la gestione del codice si semplificano grazie alla minore interdipendenza di codice e funzionalità tra ogni ruolo.
- Può essere utile per isolare dati e processi sensibili. Ad esempio, i ruoli Web che implementano l'interfaccia utente non richiedono l'accesso ai dati gestiti e controllati da un ruolo di lavoro. Ciò può essere utile a rafforzare la sicurezza, soprattutto quando si usa un modello come il [modello Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx).

### Considerazioni

Nella scelta del modo e della posizione per la distribuzione delle attività in background quando si usano i ruoli di lavoro e Web di Servizi Cloud, tenere presenti le considerazioni seguenti:

- Ospitare attività in background in un ruolo Web esistente può far risparmiare il costo di eseguire un ruolo di lavoro separato solo per queste attività. Questo comporta tuttavia la probabilità di influire sulle prestazioni e sulla disponibilità dell’applicazione in caso di contesa per l’elaborazione e per altre risorse. L'uso di un ruolo di lavoro distinto protegge il ruolo Web dall'impatto delle attività in background a esecuzione prolungata o a uso intensivo delle risorse.
- Se si ospitano le attività in background usando la classe **RoleEntryPoint**, è possibile spostarla facilmente in un altro ruolo. Ad esempio, se si crea la classe in un ruolo Web e successivamente si decide di eseguire le attività in un ruolo di lavoro, è possibile spostare l'implementazione della classe **RoleEntryPoint** nel ruolo di lavoro.
- Le attività di avvio sono progettate per eseguire un programma o uno script. La distribuzione di un processo in background come programma eseguibile potrebbe essere più difficile, soprattutto se richiede anche la distribuzione degli assembly dipendenti. Potrebbe essere più facile distribuire e usare uno script per definire un processo in background quando si usano attività di avvio.
- Le eccezioni che provocano un esito negativo dell'attività in background hanno un impatto diverso a seconda del modo in cui sono ospitate:
  - Se si usa l'approccio della classe **RoleEntryPoint**, un'attività non riuscita comporterà il riavvio del ruolo in modo che l'attività venga riavviata automaticamente. Ciò può influire sulla disponibilità dell'applicazione. Per evitare questo problema, assicurarsi di includere un sistema di gestione delle eccezioni efficace nella classe **RoleEntryPoint** e in tutte le attività di background. Usare il codice per riavviare le attività che hanno esito negativo laddove appropriato e generare l'eccezione per riavviare il ruolo solo se non è possibile correggere l'errore normalmente all'interno del codice.
  - Se si usano attività di avvio, l'utente è responsabile della gestione e del controllo dell'esecuzione dell'attività in caso di esito negativo.
- La gestione e il monitoraggio delle attività di avvio è più difficile dell'utilizzo dell'approccio della classe **RoleEntryPoint**. Azure WebJobs SDK include tuttavia un dashboard per semplificare la gestione di WebJobs avviato tramite attività di avvio.

### Altre informazioni

- [Modello di consolidamento delle risorse di calcolo](http://msdn.microsoft.com/library/dn589778.aspx)
- [Introduzione ad Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)

## Macchine virtuali di Azure

Le attività in background possono essere implementate in modo da impedirne la distribuzione in App Web o Servizi cloud di Azure. Esempi tipici sono i servizi Windows nonché le utilità e i programmi eseguibili di terze parti. O ancora i programmi scritti per un ambiente di esecuzione diverso da quello che ospita l'applicazione: potrebbe trattarsi ad esempio di un programma Unix o Linux che deve essere eseguito da un'applicazione Windows o .NET. È possibile scegliere tra una gamma di sistemi operativi per una macchina virtuale di Azure ed eseguire il servizio o l'eseguibile su tale macchina virtuale.

Per scegliere se usare le macchine virtuali, vedere [Confronto tra Servizio app, Servizi cloud e Macchine virtuali di Azure](./app-service-web/choose-web-site-cloud-service-vm.md). Per informazioni sulle opzioni per le Macchine virtuali, vedere [Dimensioni dei servizi cloud](http://msdn.microsoft.com/library/azure/dn197896.aspx). Per altre informazioni sui sistemi operativi e le immagini predefinite disponibili per le Macchine virtuali, vedere [Marketplace per Macchine virtuali](https://azure.microsoft.com/gallery/virtual-machines/).

Per avviare l'attività in background in una macchina virtuale separata, sono disponibili diverse opzioni:

- È possibile eseguire l'attività su richiesta direttamente dall'applicazione inviando una richiesta a un endpoint esposto dall'attività. In questo modo vengono passati tutti i dati richiesti dall'attività. Questo endpoint richiama l'attività.
- È possibile configurare l'attività perché venga eseguita in base a una pianificazione usando un'utilità di pianificazione o un timer disponibile nel sistema operativo scelto. In Windows, ad esempio, è possibile usare Utilità di pianificazione per eseguire script e attività. In alternativa, se SQL Server è installato nella macchina virtuale, è possibile usare SQL Server Agent per eseguire script e attività.
- È possibile usare l'utilità di pianificazione di Azure per avviare l'attività mediante l'aggiunta di un messaggio a una coda in cui l'attività è in ascolto o inviando una richiesta a un'API che espone l'attività.

Per altre informazioni su come avviare le attività in background, vedere la sezione precedente [Trigger](#triggers).

### Considerazioni

Per decidere se distribuire le attività in background in una macchina virtuale di Azure, tenere presente quanto segue:

- L'hosting di attività in background in una macchina virtuale di Azure separata offre flessibilità e consente un controllo preciso su avvio, esecuzione, pianificazione e allocazione delle risorse, ma determina un aumento dei costi di runtime se è necessario distribuire una macchina virtuale solo per eseguire attività in background.
- Non sono disponibili funzionalità di monitoraggio delle attività nel Portale di Azure o di riavvio automatico di operazioni non riuscite, anche se è possibile monitorare lo stato di base della macchina virtuale e gestirla usando i [cmdlet di gestione dei servizi di Azure](http://msdn.microsoft.com/library/azure/dn495240.aspx). Non sono tuttavia disponibili funzioni di controllo dei processi e dei thread nei nodi di calcolo. In genere, l'uso di una macchina virtuale richiederà un impegno aggiuntivo per implementare un meccanismo che raccoglie i dati dalla strumentazione nell'attività e dal sistema operativo nella macchina virtuale. Una soluzione appropriata potrebbe essere l'uso di [System Center Management Pack per Azure](http://technet.microsoft.com/library/gg276383.aspx).
- È possibile considerare la creazione di probe di monitoraggio esposti tramite endpoint HTTP. Il codice per questi probe potrebbe eseguire controlli di integrità, raccogliere informazioni operative e statistiche o raggruppare informazioni sugli errori e restituirle a un'applicazione di gestione. Per altre informazioni, vedere [Modello di monitoraggio integrità Endpoint](http://msdn.microsoft.com/library/dn589789.aspx).

### Altre informazioni

- [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) in Azure
- [Domande frequenti su Macchine virtuali di Azure](virtual-machines/virtual-machines-linux-classic-faq.md)

## Considerazioni sulla progettazione

Esistono diversi fattori fondamentali da considerare per la progettazione delle attività in background. Le sezioni seguenti illustrano il partizionamento, i conflitti e il coordinamento.

## Partizionamento

Se si decide di includere le attività in background all'interno di un'istanza di calcolo esistente (ad esempio un'app Web, un ruolo Web, un ruolo di lavoro esistente o una macchina virtuale), è necessario valutare l'impatto di questa condizione sugli attributi di qualità dell'istanza di calcolo e sull'attività in background stessa. Questi fattori saranno utili per decidere se posizionare le attività insieme all'istanza di calcolo esistente o separarle in un'istanza di calcolo distinta:

- **Disponibilità**: potrebbe non essere necessario che le attività in background abbiano lo stesso livello di disponibilità di altre parti dell'applicazione, in particolare dell'interfaccia utente e di altre parti direttamente coinvolte nell'interazione dell'utente. Le attività in background potrebbero avere una maggiore tolleranza alla latenza, ai tentativi di connessione non riusciti e ad altri fattori che interessano la disponibilità poiché le operazioni possono essere accodate. Deve essere tuttavia una capacità sufficiente a impedire il backup di richieste che potrebbero bloccare le code e influire sull'intera applicazione.
- **Scalabilità**: è probabile che le attività in background presentino requisiti di scalabilità diversi rispetto all'interfaccia utente e alle parti interattive dell'applicazione. Il dimensionamento dell'interfaccia utente potrebbe essere necessario per soddisfare i picchi della richiesta, mentre le attività in background in sospeso potrebbero essere completate nelle ore di lavoro meno intenso da un numero inferiore di istanze di calcolo.
- **Resilienza**: un errore di un'istanza di calcolo contenente solo attività in background non può avere un impatto fatale sull'intera applicazione se le richieste di esecuzione di tali attività possono essere accodate o posticipate finché l'attività non diventa nuovamente disponibile. Se l'istanza di calcolo e/o le attività possono essere riavviate entro un intervallo di tempo appropriato, gli utenti dell'applicazione non ne saranno interessati.
- **Protezione**: le attività in background potrebbero presentare requisiti di sicurezza o limitazioni differenti rispetto all'interfaccia utente o ad altre parti dell'applicazione. Usando un'istanza di calcolo separata, è possibile specificare un ambiente di protezione diverso per le attività. Inoltre, è possibile usare modelli, ad esempio Gatekeeper, per isolare le istanze di calcolo in background dall'interfaccia utente al fine di ottimizzare sicurezza e separazione.
- **Prestazioni**: è possibile scegliere il tipo di istanza di calcolo per le attività in background per soddisfare i requisiti di prestazioni specifici delle attività. Ciò potrebbe consentire l'uso di un'opzione di calcolo meno costosa, se le attività non richiedono le stesse funzionalità di elaborazione dell'interfaccia utente, o di un'istanza di dimensione maggiore, se richiedono risorse e capacità aggiuntive.
- **Gestibilità**: le attività in background possono avere un ritmo di sviluppo e distribuzione diverso dal codice dell'applicazione principale o dall'interfaccia utente. La distribuzione in un'istanza di calcolo separata consente di semplificare gli aggiornamenti e il controllo delle versioni.
- **Costo**: l'aggiunta di istanze di calcolo per eseguire attività in background aumenta i costi di hosting. È necessario valutare attentamente un compromesso tra capacità aggiuntiva e questi costi aggiuntivi.

Per altre informazioni, vedere gli articoli relativi al [modello di designazione leader](http://msdn.microsoft.com/library/dn568104.aspx) e al [modello di consumer concorrenti](http://msdn.microsoft.com/library/dn568101.aspx).

## Conflitti

Se sono presenti più istanze di un processo in background, è possibile che esse competano per l'accesso alle risorse e ai servizi, come i database e le risorse di archiviazione. L'accesso simultaneo può causare la contesa di risorse con conseguenti conflitti di disponibilità dei servizi e integrità dei dati nella risorsa di archiviazione. È possibile risolvere la contesa usando un approccio basato sul blocco pessimistico. In questo modo si impedisce che le istanze in competizione di un'attività accedano contemporaneamente a un servizio o danneggino i dati.

Un altro approccio per risolvere i conflitti consiste nel definire le attività in background come un singleton, in modo che vi sia sempre un'unica istanza in esecuzione. Ciò elimina tuttavia i vantaggi di affidabilità e prestazioni che si possono ottenere da una configurazione a più istanze. In particolare quando l'interfaccia utente può fornire lavoro sufficiente da tenere occupata più di un'attività in background.

È fondamentale garantire che l'attività in background possa essere riavviata automaticamente e che disponga di capacità sufficiente per far fronte a picchi della richiesta. Questo scopo può essere raggiunto allocando un'istanza di calcolo con risorse sufficienti, implementando un meccanismo di accodamento che possa archiviare le richieste per un'esecuzione successiva quando la richiesta diminuisce o con una combinazione di queste tecniche.

## Coordinamento

Le attività in background possono essere complesse e potrebbe essere necessaria l'esecuzione di più attività singole al fine di produrre un determinato risultato o soddisfare tutti i requisiti. In tali scenari è comune dividere l'attività in passaggi discreti più piccoli o sottoattività che possono essere eseguite da più consumer. I processi a più passaggi possono essere maggiormente efficienti e flessibili in quanto i singoli passaggi sono riutilizzabili in più processi. È facile, inoltre, aggiungere, rimuovere o modificare l'ordine dei passaggi.

Il coordinamento di più attività e passaggi può essere difficile, ma sono disponibili tre modelli comuni utilizzabili come guida per l'implementazione di una soluzione:

- **Scomposizione di un'attività in più passaggi riutilizzabili**. Un'applicazione potrebbe dover eseguire una serie di attività di varia complessità sulle informazioni che elabora. Un approccio semplice ma rigido per implementare tale applicazione potrebbe essere quello di eseguire l'elaborazione come modulo monolitico. Tuttavia, questo approccio probabilmente ridurrà le opportunità di refactoring, ottimizzazione o riutilizzo del codice se parti della stessa elaborazione sono necessarie in un altro punto dell'applicazione. Per altre informazioni, vedere [Modelli di pipe e filtri](http://msdn.microsoft.com/library/dn568100.aspx).
- **Gestione dell'esecuzione dei passaggi per un'attività**. Un'applicazione potrebbe eseguire attività che comprendono diversi passaggi, alcuni dei quali potrebbero richiamare servizi remoti o accedere a risorse remote. I singoli passaggi potrebbero essere indipendenti tra loro, ma sono coordinati dalla logica dell'applicazione che implementa l'attività. Per altre informazioni, vedere [Modello di supervisione agente di pianificazione](http://msdn.microsoft.com/library/dn589780.aspx).
- **Gestione del ripristino per i passaggi non riusciti di un'attività**. Un'applicazione potrebbe dover annullare il lavoro eseguito da una serie di passaggi (che insieme definiscono un'operazione finale coerente) in caso di esito negativo di uno o più di tali passaggi. Per altre informazioni, vedere [Modello di transazioni di compensazione](http://msdn.microsoft.com/library/dn589804.aspx).

## Ciclo di vita (Servizi cloud)

 Se si decide di implementare processi in background per le applicazioni di Servizi cloud che usano i ruoli Web e di lavoro tramite la classe **RoleEntryPoint**, è importante comprendere il ciclo di vita di questa classe per usarla in modo corretto.

I ruoli Web e di lavoro attraversano una serie di fasi distinte quando vengono avviati, eseguiti e arrestati. La classe **RoleEntryPoint** espone una serie di eventi che indicano quando si verificano queste fasi. Vengono usati per inizializzare, eseguire e arrestare le attività di background personalizzate. Il ciclo completo è:

- Azure carica l'assembly del ruolo e vi cerca una classe che deriva da **RoleEntryPoint**.
- Se trova la classe, chiama **RoleEntryPoint.OnStart()**. Si esegue l'override di questo metodo per inizializzare le attività in background.
- Al termine del metodo **OnStart** Azure chiama **Application\_Start ()** nel file Global dell'applicazione, se presente (ad esempio, Global.asax in un ruolo Web che esegue ASP.NET).
- Azure chiama **RoleEntryPoint.Run()** in un nuovo thread di primo piano eseguito in parallelo con **OnStart()**. Si esegue l'override di questo metodo per avviare le attività in background.
- Al termine dell'esecuzione del metodo Run, Azure chiama innanzitutto **Application\_End()** nel file Global dell'applicazione, se presente, quindi chiama **RoleEntryPoint.OnStop()**. Si esegue l'override del metodo **OnStop** per interrompere le attività in background, pulire le risorse, eliminare gli oggetti e chiudere le connessioni che possono essere state usate dalle attività.
- Il ruolo di lavoro di Azure viene arrestato. A questo punto, il ruolo sarà riciclato e verrà riavviato.

Per altre informazioni e un esempio di utilizzo dei metodi della classe **RoleEntryPoint**, vedere [Modello di consolidamento delle risorse di calcolo](http://msdn.microsoft.com/library/dn589778.aspx).

## Considerazioni

Per la pianificazione della modalità di esecuzione delle attività in background in un ruolo Web o di lavoro, tenere presente quanto segue:

- L'implementazione predefinita del metodo **Run** nella classe **RoleEntryPoint** contiene una chiamata a **Thread.Sleep(Timeout.Infinite)**, che mantiene il ruolo attivo per un tempo illimitato. Se si esegue l'override del metodo **Run** (operazione generalmente necessaria per eseguire attività in background), non deve essere consentito al codice di uscire dal metodo a meno che non si desideri riutilizzare l'istanza del ruolo.
- Un'implementazione tipica del metodo **Run** include il codice per l'avvio di ciascuna attività in background e un costrutto di ciclo che controlla periodicamente lo stato di tutte le attività in background. Tale implementazione consente di riavviare le attività non riuscite o di monitorare i token di annullamento che indicano il completamento dei processi.
- Se un'attività in background genera un'eccezione non gestita, l'attività deve essere riavviata, consentendo al contempo il proseguimento dell'esecuzione delle altre attività in background nel ruolo. Se l'eccezione è invece causata dal danneggiamento di oggetti esterni all'attività, come una risorsa di archiviazione condivisa, l'eccezione deve essere gestita dalla classe **RoleEntryPoint**, tutte le attività devono essere annullate ed è necessario consentire al metodo **Run** di terminare. Azure riavvierà quindi il ruolo.
- Utilizzare il metodo **OnStop** per sospendere o arrestare le attività in background e pulire le risorse. Questo potrebbe comportare l'arresto di attività a esecuzione prolungata o a più passaggi. È fondamentale considerare come eseguire questa operazione in modo da evitare incoerenze nei dati. Se un'istanza del ruolo viene arrestata per un motivo diverso da un arresto avviato dall'utente, il codice in esecuzione nel metodo **OnStop** deve essere completato entro cinque minuti prima della terminazione forzata. Verificare che il codice possa essere completato in quel periodo di tempo o che possa tollerare di non essere eseguito fino al completamento.
- Il servizio di bilanciamento del carico di Azure inizia a indirizzare il traffico all’istanza del ruolo quando il metodo **RoleEntryPoint.OnStart** restituisce il valore **true**. È quindi consigliabile inserire tutto il codice di inizializzazione nel metodo **OnStart** in modo che le istanze del ruolo non inizializziate correttamente non ricevano il traffico.
- È possibile usare le attività di avvio in aggiunta ai metodi della classe **RoleEntryPoint**. È consigliabile usare le attività di avvio per inizializzare le impostazioni da modificare nel servizio di bilanciamento del carico di Azure, poiché queste operazioni verranno eseguite prima che il ruolo riceva tutta le richieste. Per altre informazioni, vedere [Come configurare ed eseguire attività di avvio per un servizio cloud](./cloud-services/cloud-services-startup-tasks.md).
- Un eventuale errore in un'attività di avvio potrebbe determinare il continuo riavvio forzato del ruolo. Ciò può impedire di effettuare lo scambio di un indirizzo VIP con una versione temporanea precedente in quanto lo scambio richiede l'accesso esclusivo al ruolo. Questo non può essere ottenuto durante il riavvio del ruolo. Per risolvere il problema:
	-  Aggiungere il codice seguente all'inizio dei metodi **OnStart** e **Run** nel ruolo:

	```C#
	var freeze = CloudConfigurationManager.GetSetting("Freeze");
	if (freeze != null)
	{
		if (Boolean.Parse(freeze))
	  	{
		    Thread.Sleep(System.Threading.Timeout.Infinite);
		}
	}
	```

   - Aggiungere la definizione dell’impostazione **Freeze** come valore booleano nei file ServiceDefinition.csdef e ServiceConfiguration*.cscfg per il ruolo e impostarla su **false**. Se il ruolo passa alla modalità di riavvio ripetuto, è possibile modificare l'impostazione in **true** per bloccare l'esecuzione del ruolo e consentire lo scambio con una versione precedente.

## Considerazioni sulla resilienza

Le attività in background devono essere resilienti per fornire servizi affidabili all'applicazione. Durante la pianificazione e progettazione di attività in background, tenere presente quanto segue:

- Le attività in background devono poter gestire correttamente i riavvii del ruolo o servizio senza danneggiare i dati o introdurre incoerenze nell'applicazione. Per le attività a esecuzione prolungata o a più passaggi, è consigliabile usare un _checkpoint_ salvando lo stato dei processi in un archivio permanente o come messaggi in una coda, se appropriato. Ad esempio, è possibile rendere permanenti le informazioni di stato in un messaggio in una coda e aggiornare in modo incrementale le informazioni sullo stato con l'avanzamento dell'attività, affinché questa possa essere elaborata a partire dall'ultimo checkpoint valido conosciuto anziché ricominciare dall'inizio. Quando si usano le code del bus di servizio di Azure, è possibile usare le sessioni dei messaggi per abilitare lo stesso scenario. Le sessioni consentono di salvare e recuperare lo stato di elaborazione dell'applicazione usando i metodi [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx). Per altre informazioni sulla progettazione di processi e flussi di lavoro affidabili a più passaggi, vedere l'articolo relativo al [modello di supervisione agente di pianificazione](http://msdn.microsoft.com/library/dn589780.aspx).
- Quando si usano i ruoli di lavoro o Web per ospitare più attività in background, progettare l'override del metodo **Run** in modo da individuare le attività bloccate o non riuscite e riavviarle. Se questo metodo non è pratico e si usa un ruolo di lavoro, forzare il riavvio del ruolo di lavoro uscendo dal metodo **Run**.
- Se si usano code per comunicare con le attività in background, le code possono fungere da buffer in cui archiviare le richieste inviate alle attività quando l'applicazione ha un carico maggiore del solito. In questo modo le attività possono tornare al passo con l'interfaccia utente durante i periodi di minore attività. Il riavvio del ruolo, inoltre, non bloccherà l'interfaccia utente. Per altre informazioni, vedere [Modello di livellamento del carico basato sulle code](http://msdn.microsoft.com/library/dn589783.aspx). Se alcune attività sono più importanti di altre, valutare l'implementazione del [modello di coda di priorità](http://msdn.microsoft.com/library/dn589794.aspx) per garantire che tali attività vengano eseguite prima di quelle meno importanti.
- Le attività in background avviate da messaggi o da messaggi del processo devono essere progettate in modo da gestire le incoerenze, ad esempio i messaggi che arrivano in un ordine errato, i messaggi che causano ripetutamente un errore (detti anche messaggi _non elaborabili_) e i messaggi recapitati più volte. Valutare gli aspetti seguenti:
  - I messaggi che devono essere elaborati in un ordine specifico, ad esempio quelli che modificano i dati in base al valore esistente (come l'aggiunta di un valore a un valore esistente), potrebbero non arrivare nell'ordine in cui sono stati originariamente inviati. Oppure potrebbero essere gestiti da istanze diverse di un'attività in background in un ordine differente a causa di carichi variabili in ogni istanza. I messaggi che devono essere elaborati in un ordine specifico devono includere un numero di sequenza, una chiave o un altro indicatore utilizzabile dalle attività in background per garantire l'elaborazione nell'ordine corretto. Se si usa il bus di servizio di Azure, è possibile usare le sessioni di messaggistica per garantire l'ordine di recapito. In genere, tuttavia, la soluzione più efficace è progettare il processo in modo che l'ordine dei messaggi non sia importante.
  - Un'attività in background normalmente visualizza i messaggi della coda, nascondendoli temporaneamente agli altri consumer di messaggi. Quindi elimina i messaggi dopo che sono stati elaborati e l'elaborazione è riuscita. Se un'attività in background non riesce a portare a termine l'elaborazione di un messaggio, il messaggio ricompare nella coda dopo la scadenza del periodo di tempo di visualizzazione. Sarà elaborato da un'altra istanza dell'attività o durante il successivo ciclo di elaborazione di questa istanza. Se il messaggio provoca regolarmente un errore nel consumer, blocca l'attività, la coda e infine l'applicazione stessa quando la coda diventa piena. È essenziale quindi rilevare e rimuovere i messaggi non elaborabili dalla coda. Se si usa il bus di servizio di Azure, i messaggi che causano un errore possono essere spostati automaticamente o manualmente in una coda dei messaggi non recapitabili associata.
  - Per le code è garantito un meccanismo che prevede il recapito _almeno una volta_, ma possono recapitare lo stesso messaggio più volte. Inoltre, se un'attività in background ha esito negativo dopo l'elaborazione di un messaggio ma prima che questo venga eliminato dalla coda, il messaggio diventerà di nuovo disponibile per l'elaborazione. Le attività in background dovranno essere idempotenti, ossia l'elaborazione multipla dello stesso messaggio non deve causare un errore o un'incoerenza nei dati dell'applicazione. Alcune operazioni sono naturalmente idempotenti, ad esempio l'impostazione di un valore archiviato su un nuovo valore specifico. Tuttavia operazioni quali l'aggiunta di un valore a un valore archiviato esistente, senza verificare che il valore archiviato non sia cambiato da quando è stato originariamente inviato il messaggio, causeranno incoerenze. Le code del bus di servizio di Azure possono essere configurate per rimuovere automaticamente i messaggi duplicati.
  - Alcuni sistemi di messaggistica, quali le code di archiviazione di Azure e le code del bus di servizio di Azure, supportano una proprietà di conteggio di rimozioni dalla coda che indica il numero di volte in cui un messaggio è stato letto dalla coda. Tale indicazione può essere utile per la gestione dei messaggi non elaborabili e ripetuti. Per altre informazioni, vedere [Introduzione alla messaggistica asincrona](http://msdn.microsoft.com/library/dn589781.aspx) e [Modelli di idempotenza](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## Considerazioni su ridimensionamento e prestazioni

Le attività in background devono offrire prestazioni sufficienti a garantire che l'applicazione non si blocchi o causi incoerenze dovute all'operazione ritardata quando il sistema è sotto carico. In genere, le prestazioni risultano migliorate ridimensionando le istanze di calcolo che ospitano le attività in background. Durante la pianificazione e la progettazione di attività in background, tenere presenti i seguenti aspetti relativi al dimensionamento e alle prestazioni:

- Azure supporta il dimensionamento automatico (aumento e riduzione) in base alla domanda e al carico corrente o in base a una pianificazione predefinita per App Web, ruoli Web e di lavoro di Servizi cloud, nonché distribuzioni ospitate su Macchine virtuali. Usare questa funzionalità per garantire che l'applicazione nel suo complesso offra prestazioni sufficienti, riducendo al minimo i costi di runtime.
- Se le attività in background hanno una capacità di prestazioni diversa dalle altre parti di un'applicazione di Servizi Cloud (ad esempio l'interfaccia utente o componenti come il livello di accesso ai dati), l'hosting delle attività in background insieme in un ruolo di lavoro separato consente all'interfaccia utente e all'attività in background di ridimensionarsi in modo indipendente per gestire il carico. Se più attività in background hanno prestazioni notevolmente diverse, è consigliabile dividerle in ruoli di lavoro separati e dimensionare ogni tipo di ruolo in modo indipendente. Si noti tuttavia che ciò potrebbe accrescere i costi di runtime rispetto alla combinazione di tutte le attività in un minor numero di ruoli.
- Dimensionare semplicemente i ruoli potrebbe non essere sufficiente a evitare la perdita di prestazioni in condizioni di carico. Potrebbe essere anche necessario dimensionare le code di archiviazione e altre risorse per evitare che un singolo punto dell'intera catena di elaborazione diventi un collo di bottiglia. È opportuno considerare anche altre limitazioni, ad esempio la velocità massima di archiviazione e altri servizi su cui l'applicazione e le attività in background fanno affidamento.
- Le attività in background devono essere progettate per la scalabilità. Ad esempio, devono essere in grado di rilevare in modo dinamico il numero di code di archiviazione in uso per attendere o inviare messaggi alla coda appropriata.
- Per impostazione predefinita, i processi Web vengono ridimensionati con la relativa istanza di App Web di Azure associata. Tuttavia, quando un processo Web deve essere eseguito solo come singola istanza, è possibile creare un file Settings.job contenente i dati JSON **{"is\_singleton": true}**. In questo modo si costringe Azure a eseguire solo un'istanza del processo Web, anche se ci sono più istanze dell'app Web associata. Questa tecnica può essere utile per i processi pianificati che devono essere eseguiti solo come singola istanza.

## Modelli correlati

- [Introduzione alla messaggistica asincrona](http://msdn.microsoft.com/library/dn589781.aspx)
- [Indicazioni sulla scalabilità automatica](http://msdn.microsoft.com/library/dn589774.aspx)
- [Modello di transazioni di compensazione](http://msdn.microsoft.com/library/dn589804.aspx)
- [Modello di consumer concorrenti](http://msdn.microsoft.com/library/dn568101.aspx)
- [Indicazioni sul partizionamento del calcolo](http://msdn.microsoft.com/library/dn589773.aspx)
- [Modello di consolidamento delle risorse di calcolo](http://msdn.microsoft.com/library/dn589778.aspx)
- [Modello gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx)
- [Modello di designazione leader](http://msdn.microsoft.com/library/dn568104.aspx)
- [Modelli di pipe e filtri](http://msdn.microsoft.com/library/dn568100.aspx)
- [Modello di coda di priorità](http://msdn.microsoft.com/library/dn589794.aspx)
- [Modello di livellamento del carico basato sulle code](http://msdn.microsoft.com/library/dn589783.aspx)
- [Modello di supervisione agente di pianificazione](http://msdn.microsoft.com/library/dn589780.aspx)

## Altre informazioni

- [Scalabilità delle applicazioni Azure con ruoli di lavoro](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Esecuzione di attività in background](http://msdn.microsoft.com/library/ff803365.aspx)
- [Ciclo di vita di avvio del ruolo di Azure](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (post di blog)
- [Ciclo di vita del ruolo di Servizi cloud di Azure](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (video)
- [Introduzione a Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- [Analogie e differenze tra le code di Azure e le code del bus di servizio](./service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Come abilitare il modulo Diagnostica in un servizio cloud](./cloud-services/cloud-services-dotnet-diagnostics.md)

<!---HONumber=AcomDC_0720_2016-->