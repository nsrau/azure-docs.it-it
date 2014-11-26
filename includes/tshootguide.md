
# Risoluzione dei problemi in Azure

Per *risoluzione dei problemi* si intende l'attività generale di individuazione e comprensione di un comportamento imprevisto e di applicazione della correzione appropriata. Durante lo sviluppo, gli sviluppatori testano ed eseguono le applicazioni e ne eseguono il debug prima di implementarle in un ambiente di produzione. Ciò vale sia per le applicazioni eseguite su un computer desktop che per quelle eseguite su un server nel cloud. Può tuttavia essere difficile eseguire il debug di un'applicazione a istanze multiple distribuita su larga scala progettata per la scalabilità orizzontale e gli strumenti e gli approcci standard possono risultare insufficienti.

Per questo motivo è consigliabile progettare le applicazioni cloud tenendo in considerazione la risoluzione dei problemi. Il modo in cui il supporto della risoluzione dei problemi viene incluso nell'applicazione dipende in primo luogo dal punto in cui viene eseguita l'applicazione e in secondo luogo dai linguaggi o runtime utilizzati dall'applicazione o su cui questa si basa.

Se si crea un'applicazione che viene eseguita su una Macchina virtuale di Azure, in molti casi è possibile affrontare la progettazione della risoluzione dei problemi e il debug come accadrebbe sul sistema operativo se fosse in esecuzione sui propri server.

Le applicazioni in esecuzione su Azure sono applicazioni a istanze multiple distribuite su vasta scala di cui può essere difficile eseguire il debug. Per le applicazioni di questo tipo, gli strumenti e gli approcci standard per la risoluzione dei problemi risultano insufficienti. In questo argomento vengono presentate alcune procedure di risoluzione dei problemi collaudate e sono riportati collegamenti a informazioni più dettagliate sulle procedure descritte.

**Nota**: in questo argomento si presuppone che durante la
progettazione dell'applicazione o dopo la distribuzione dell'applicazione Azure si verifichi una situazione imprevista. Non viene illustrato come distribuire un'applicazione in Azure. Per ulteriori informazioni sullo sviluppo e sulla distribuzione di un'applicazione Azure, vedere
[https://www.windowsazure.com/it-it/develop/overview/][1].

In questo argomento vengono innanzitutto presentate alcune procedure consigliate che consentono di progettare l'applicazione in modo da agevolare la risoluzione dei problemi quando si verificano. Se l'applicazione non viene progettata fin dall'inizio in modo da prevedere la possibilità di seguire il flusso del codice, può risultare molto difficile individuare i problemi quando si manifestano. Queste procedure consigliate sono valide per tutti i tipi di applicazione in esecuzione su Azure, indipendentemente dal modello o linguaggio utilizzato.

Negli scenari seguenti vengono illustrati alcuni approcci specifici per lo sviluppo di applicazioni Azure supportabili, indipendentemente dal tipo di applicazione: Siti Web, Servizi cloud o Macchine virtuali.

Ogni sezione contiene una descrizione di alto livello delle procedure consigliate, nonché puntatori a risorse che le illustrano in dettaglio o ne descrivono l'implementazione.

## Contenuto del documento

* [Procedure consigliate per la risoluzione dei problemi in
  Azure](#BestPractices)
* [Siti Web di Azure](#Websites)
* [Servizi cloud di Azure](#CloudServices)
* [Macchine virtuali di Azure](#Vms)
* [Servizi di Azure](#PlatformServices)
* [Risoluzione dei problemi relativi al database
  SQL](#SQLTroubleshooting)

<h2><a id="BestPractices" ></a>Procedure consigliate per la risoluzione dei problemi in Azure</h2>


In questa sezione vengono descritte procedure consigliate valide per le applicazioni Azure, a prescindere dal modello di hosting o dal linguaggio utilizzato. Sono inoltre indicate risorse di approfondimento su tali procedure.

Per porre le basi per risolvere i problemi in modo efficiente in Azure, concentrare l'attenzione sulle seguenti tre aree principali:

* Gestione degli errori in modo normale: *ogni servizio componente deve
  essere in grado di tollerare il mancato funzionamento dei servizi
  dipendenti o dell'infrastruttura*.
* Traccia, registrazione e monitoraggio: *ogni servizio componente deve
  includere risorse appropriate di debug, traccia e registrazione di
  eventi ed errori.*
* Debug degli errori dove possibile: *prima del passaggio in produzione,
  ma anche a livello di componente e di rete durante l'esecuzione.*

Se un'applicazione viene progettata tenendo conto di questi aspetti, fornirà le informazioni necessarie per il rilevamento di comportamenti inattesi quando si verificano.

### Progettazione per gestire gli errori in modo normale

Le applicazioni dovrebbero gestire le condizioni di errore in modo normale, laddove possibile. Questo aspetto è ancora più importante se si considera la natura distribuita di Azure. La risoluzione efficiente dei problemi inizia con una buona progettazione della gestione degli errori temporanei. Gli errori temporanei sono una delle aree principali in cui le applicazioni cloud mostrano un comportamento imprevisto a causa delle condizioni di errore temporanee insite nelle applicazioni Internet.

Gli errori temporanei sono errori correlati alle latenze e alle connessioni di rete intermittenti che caratterizzano le risorse condivise su Internet. Di seguito sono riportati alcuni esempi:

* Le risorse di elaborazione condivise come Servizi cloud e il database
  SQL di Azure, ad esempio, possono essere leggermente meno o più
  reattive in momenti diversi.
* Ritardi di risposta dovuti all'integrazione della durabilità per i
  servizi. Ad esempio, SQL Azure mantiene più copie dei database
  coerenti tra loro per assicurare la durabilità e questo incide sulla
  velocità di risposta.
* Ritardi causati dall'interruzione di connessioni HTTP o di altri
  protocolli prima del completamento del lavoro. Ad esempio, è possibile
  che le richieste HTTP non raggiungano un endpoint e restituiscano un
  valore prima del timeout corrispondente.

Per attenuare l'impatto degli errori temporanei, le applicazioni Azure dovrebbero:

* Essere a regime di controllo libero, in modo che i componenti non
  siano localmente dipendenti da servizi soggetti a errore con maggiore
  frequenza rispetto agli ambienti locali.
* Effettuare chiamate asincrone quando possibile, per evitare che i
  processi diventino dipendenti da risposte immediate.
* Utilizzare un approccio di gestione degli errori temporanei che rilevi
  determinate categorie di errore e sia in grado di implementare un
  comportamento di ripetizione del tentativo per tali errori in base ad
  alcuni criteri specifici configurabili.

Le chiamate ai servizi dovrebbero generare o utilizzare un livello di gestione degli errori temporanei per rilevare scenari di errore comuni e ritentare la chiamata in base a una specifica impostazione di configurazione. Per gli sviluppatori .NET, una libreria consigliata è
[Microsoft Enterprise Library 5.0 Integration Pack per Azure][2].
Microsoft Enterprise Library è una raccolta di blocchi di applicazioni riutilizzabili che affrontano problemi comuni nello sviluppo di software aziendale. Microsoft Enterprise Library Integration Pack per Azure è un'estensione di Microsoft Enterprise Library 5.0 che può essere utilizzata con Azure. Include il blocco di applicazioni per la scalabilità automatica, il blocco di applicazioni per la gestione degli errori temporanei, origine di configurazione BLOB, provider di configurazione protetto e materiali di formazione. Un'altra libreria
.NET più semplice con un numero inferiore di funzionalità è [Cloud
Application Framework & Extensions (CloudFx)][3]. CloudFx offre un set di componenti di produzione di qualità e di blocchi predefiniti progettati per avviare rapidamente l'implementazione di soluzioni e servizi basati su Azure ricchi di funzionalità, affidabili ed estensibili.

### Traccia e registrazione appropriate

Data la complessità delle applicazioni distribuite a scalabilità orizzontale, i debugger tradizionali che agiscono su un processo sono molto meno utili nella diagnostica di problemi che si verificano quando l'applicazione è in esecuzione. Da qui l'importanza fondamentale delle funzionalità di traccia e registrazione. L'esecuzione dell'app e dei dati corrispondenti è condivisa da molti servizi, ospitati su molte macchine diverse. In un'applicazione distribuita su vasta scala è difficile, se non impossibile, determinare l'istanza del servizio a cui collegarsi e di cui eseguire il debug. Traccia e registrazione consentono di seguire l'esecuzione e il flusso di dati dell'applicazione per comprendere meglio lo stato dell'applicazione.

Le applicazioni Azure strutturate correttamente sono basate su una filosofia progettuale che prevede fin dall'inizio una strategia di registrazione e traccia. In questo modo è possibile individuare e correggere i problemi in modo più rapido e con minori interventi, senza dover ricorrere al supporto Microsoft.

**Nota**: la scrittura di tracce e registrazioni estese ha un impatto
sulle prestazioni. Se questo avviene in modo intensivo, l'impatto è ancora più rilevante. La progettazione dell'applicazione dovrebbe pertanto includere un criterio configurabile di traccia e registrazione, adattabile in base alle esigenze. Idealmente, il livello di registrazione dovrebbe essere adattabile dal file
**ServiceConfiguration.cscfg** in modo da poterlo modificare senza
ripetere la distribuzione.

Un volume elevato di log non garantisce rapidità di rilevamento e correzione dei bug. Una grande quantità di dati è più lunga da decifrare e la relativa raccolta incide sulle prestazioni dell'applicazione. La registrazione regolabile consente di contenere sia le dimensioni che il costo di archiviazione dei dati di log.

Nell'articolo relativo al [controllo della registrazione e della traccia in Azure][4], pubblicato in MSDN Magazine, Mike Kelly descrive quattro tipi di output di diagnostica da considerare:

* Output di debug: solo nella build di debug, include le asserzioni
* Traccia: tiene traccia del flusso di controllo durante l'esecuzione
* Registrazione eventi: eventi principali nell'esecuzione del programma
* Registrazione errori: situazioni eccezionali o pericolose

Altri linguaggi, altre piattaforme applicative e altri sistemi operativi possono utilizzare una terminologia diversa per le funzioni di traccia e registrazione. Se si utilizza una di queste piattaforme di sviluppo in Azure, impiegare la strategia e gli strumenti equivalenti per il linguaggio o la piattaforma in uso.

Le applicazioni in modalità mista sono applicazioni eseguite in una combinazione di Macchine virtuali, Siti Web e Servizi cloud di Azure. Quando si creano applicazioni di questo tipo, le funzionalità di traccia e registrazione diventano ancora più importanti, dato che sono distribuite su scala più vasta. Per la risoluzione dei problemi relativi a queste applicazioni in modalità mista, è necessario seguire il flusso complessivo di dati ed esecuzione per identificare eventuali problemi. Il meccanismo di traccia e registrazione utilizzato per un'applicazione in modalità mista dipende dal modello di hosting del componente.

### Monitoraggio dell'applicazione

Traccia e registrazione rientrano nel quadro più ampio del monitoraggio. Il monitoraggio consente di:

* Individuare le applicazioni Azure
* Determinare lo stato di ogni istanza del ruolo
* Raccogliere e monitorare informazioni sulla prestazioni, tra cui
  latenza e velocità effettiva
* Raccogliere e monitorare eventi
* Raccogliere e monitorare messaggi di traccia
* Monitorare l'utilizzo delle risorse
* Monitorare le metriche di qualità del servizio
* Eseguire la pianificazione della capacità
* Eseguire analisi del traffico (utenti, visualizzazioni, ore di punta)
* Stimare la fatturazione
* Eseguire il controllo

Il monitoraggio viene effettuato con un unico strumento o con un set di strumenti. La scelta dello strumento dipende dalla piattaforma e/o dal linguaggio utilizzato dall'applicazione e dagli obiettivi e requisiti del monitoraggio.

**Monitoring Pack di Microsoft System Center per applicazioni Azure**

Questo [Monitoring Pack][5] consente di utilizzare [Microsoft System Center Operations Manager][6] per monitorare la disponibilità e le prestazioni delle applicazioni Azure.

Microsoft System Center Operations Manager 2007 rappresenta la soluzione ottimale per monitorare l'integrità di un'applicazione Azure.

**Strumento di gestione della piattaforma Azure**

Un altro strumento è lo [strumento di gestione della piattaforma Azure
(MMC)][7], che consente di gestire i servizi ospitati e gli account di
archiviazione di Azure. Questo strumento viene fornito come esempio corredato da codice sorgente completo per illustrare l'esecuzione di varie attività di gestione e configurazione mediante le API di gestione e diagnostica di Azure.

**Strumenti Cerebrata**

Cerebrata offre numerosi strumenti che consentono di monitorare e gestire le applicazioni Azure, tra cui [Azure Diagnostics Manager][8],
[Cloud Storage Studio][9] e [Azure Management Cmdlets][10].

Azure Diagnostics Manager 2 è un client basato su Windows (WPF) per la gestione di Diagnostica Azure. Consente di visualizzare, scaricare e gestire i dati di diagnostica raccolti dalle applicazioni in esecuzione in Azure. Per ulteriori informazioni su questi prodotti, vedere
[http://www.cerebrata.com][11].

Cloud Storage Studio 2 è un client basato su Windows (WPF) per la gestione di Archiviazione di Azure.

Azure Management Cmdlets è un set di cmdlet di Windows PowerShell per la gestione di Archiviazione, Servizi ospitati, istanze del database SQL e Diagnostica Azure. Include anche cmdlet per il backup e il ripristino di account di archiviazione.

**Monitoraggio di rete: AlertBot, Gomez, Keynote, Pingdom**

Gomez[ Application Performance Management, ][12]Keynote[, ][13]Pingdom[ e ][14]AlertBot[ di Compuware sono soluzioni per il monitoraggio di un'applicazione Azure da remoto.][15] Consentono di monitorare la disponibilità dell'applicazione e di ottimizzare le prestazioni. Alcuni servizi, come Pingdom, abilitano la notifica tramite posta elettronica, SMS o applicazione desktop se viene rilevato un errore. Questo tipo di monitoraggio simula le attività di un utente finale per monitorare un'applicazione in modo efficace.

**Strumenti Apica**

Gli strumenti Apica consentono di monitorare un'applicazione Web Azure
"dall'esterno". Per ulteriori informazioni, vedere
[http://www.apicasystem.com/integration-partners/][16].

**AVIcode**

Microsoft ha acquisito AVIcode, che ora fa parte di Microsoft System Center. [AVIcode][17] fornisce funzionalità di monitoraggio delle prestazioni delle applicazioni .NET con un insieme completo di funzionalità di monitoraggio delle applicazioni.

**Profilatura delle prestazioni**

È possibile [profilare][18] un'applicazione Azure quando viene eseguita
in Azure per individuare eventuali problemi di prestazioni. Quando si pubblica l'applicazione Azure da Visual Studio, è possibile decidere di profilarla e selezionare le impostazioni di profilazione necessarie.

**VM Assistant di Azure**

Lo strumento [VM Assistant][19] è un progetto CodePlex che raccoglie in un'unica posizione tutti i dati rilevanti per la risoluzione dei problemi quando si accede a un'istanza di una macchina virtuale tramite Desktop remoto. Tramite il pulsante **VM Health** è possibile ottenere lo stato corrente dell'istanza.

### Debug degli errori quando possibile

Prima di distribuire un'app in Azure, è buona norma eseguire il debug dell'applicazione in locale. Azure SDK contiene emulatori che simulano l'ambiente cloud di Azure, consentendo di eseguire l'app e di effettuare test rudimentali senza necessità di distribuire l'applicazione. Gli strumenti di debug utilizzati variano a seconda del linguaggio di programmazione e degli strumenti di sviluppo utilizzati.

Dopo avere distribuito un'applicazione, è possibile eseguire il debug nel cloud mediante un debugger come Visual Studio. Ciò richiede la creazione di una build di debug e la relativa distribuzione. A questo scopo, è necessario connettersi a una specifica istanza del ruolo. Se di dispone di un'applicazione complessa con più ruoli e istanze del ruolo, può essere difficile determinare l'istanza a cui connettersi. Visual Studio Ultimate supporta IntelliTrace, che consente ai ruoli .NET di tenere traccia delle informazioni di debug. Quando si verifica un problema, è possibile scaricare questa informazioni e caricarle in Visual Studio. Per stabilire dove si è verificato il problema, è possibile osservare il log di IntelliTrace relativo alle singole istanze del ruolo. Sebbene il debug nel cloud presenti alcuni svantaggi, in alcune circostanza è necessario. Non tutti i servizi di Azure dispongono di un emulatore, ad esempio il bus di servizio, e non tutti gli strumenti di sviluppo supportati, ad esempio Mac e Linux, sono dotati di emulatori.

Dopo avere eseguito il debug dell'applicazione in locale, è probabile che si debba fare affidamento sulla strumentazione integrata nell'applicazione per stabilire la posizione in cui si verificano i problemi.

**Debug di Node.js**

Per il debug di applicazioni Node.JS, è possibile utilizzare lo strumento Node-Inspector disponibile su [GitHub][20]. Node-Inspector può essere eseguito in locale nel computer di sviluppo mediante l'emulatore di archiviazione di Azure. Per ulteriori informazioni, vedere il blog di Jim Wang relativo al [debug di Node nell'emulatore di Azure][21].

Se si esegue il debug di un'applicazione in Azure, installare la versione completa di IISNode da [GitHub][22] nel ruolo Web, nel ruolo di lavoro o nell'istanza della macchina virtuale. Come indicato in precedenza, questa modalità di debug non è consigliata quando l'applicazione è in produzione e scalata su più istanze poiché si potrebbe non conoscere l'istanza del ruolo o della macchina virtuale di cui eseguire il debug.

Per utilizzare Node-Inspector su un ruolo Web, installare il pacchetto nel ruolo Web e utilizzare lo strumento Node-Inspector come di consueto. Per ulteriori informazioni sul debug con Node-Inspector, vedere l'articolo relativo al [debug con Node-Inspector][23].

**IntelliTrace**

Microsoft Visual Studio Ultimate include [IntelliTrace][24], che può essere abilitato per eseguire il debug delle applicazioni prima della distribuzione in produzione. IntelliTrace supporta applicazioni ASP.NET e WCF. IntelliTrace non è supportato quando viene abilitato in un servizio di produzione, ma può essere utilizzato per ottenere le eccezioni per un'applicazione dopo la distribuzione in Azure. Nel post di blog di Jim Nakashima viene descritto come utilizzare [IntelliTrace per il debug di Servizi cloud di Azure].

**Fiddler**

[Fiddler][25] è un proxy di debug Web che registra tutto il traffico
HTTP(S) tra il computer e Internet. Fiddler consente di ispezionare il traffico, di impostare punti di interruzione e di "armeggiare" con i dati in ingresso e in uscita. Fiddler si rivela particolarmente utile per le risoluzione dei problemi relativi ad Archiviazione di Azure.

Per utilizzare Fiddler sull'infrastruttura di sviluppo locale, utilizzare ipv4.fiddler anziché 127.0.0.1:

* Avviare Fiddler.
* Avviare il servizio nell'infrastruttura di sviluppo.
* Passare a http://ipv4.fiddler:/. Fiddler dovrebbe tracciare la
  richiesta.

Per utilizzare Fiddler sull'archivio di sviluppo locale, modificare il file di configurazione del servizio in modo che punti a Fiddler:

Aprire il file ServiceConfiguration.cscfg e modificare la stringa di connessione nel modo seguente:

    Value="UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://ipv4.fiddler"

* Avviare Fiddler.
* Avviare il servizio. Fiddler dovrebbe tracciare tutte le richieste di
  archiviazione.

## Risoluzione dei problemi e modelli di hosting di Azure

In questa sezione vengono descritte le procedure consigliate per il debug delle applicazioni con i diversi modelli di hosting di Azure.

<h2><a id="Websites" ></a>Siti Web di Azure</h2>


Quando si progetta un sito Web di Azure supportabile, seguire i consigli forniti in precedenza in questo documento, quando possibile, inclusi l'individuazione e la gestione degli errori, l'applicazione della logica di ripetizione del tentativo per gli errori temporanei, la traccia e la registrazione. La risoluzione dei problemi relativi ai siti Web di Azure prevede la configurazione dei siti Web in modo da visualizzare gli errori dell'applicazione, la configurazione della diagnostica per un sito Web, la raccolta di dati diagnostici e la successiva analisi dei dati raccolti per identificare e risolvere i problemi.

I siti Web di Azure consentono di configurare le opzioni di diagnostica seguenti:

* Registrazione del server Web
* Messaggi di errore dettagliati
* Traccia delle richieste non riuscite

Per ulteriori informazioni su questi argomenti, vedere l'articolo relativo alla [risoluzione dei problemi relativi a un sito Web di Azure](/it-it/develop/net/best-practices/troubleshooting-web-sites/).

Quando sono abilitati i log del server Web per i siti Web di Azure, il sito Web registrerà tutte le transazioni HTTP in un file di log utilizzando il [formato di file di log esteso W3C][26]. È quindi possibile utilizzare [Log Parser][27] per eseguire query nel file di log. Alcuni esempi di query del parser di log sono disponibili in [Log Parser Plus][28] e nella pagina di [esempi relativi a Log Parser su TechNet][29]. Per generare il tipo di output CHART in un computer che esegue Office 2007/2010, installare [Componenti Web Microsoft Office 2003][30] seguendo le istruzioni riportate in [Log Parser Plus][31].

I siti Web di Azure utilizzano la stessa funzionalità di traccia delle richieste non riuscite disponibile in IIS 7.0 e versioni successive. Non
è tuttavia configurabile come la traccia delle richieste non riuscite di
IIS. Quando si abilita la traccia delle richieste non riuscite per i siti Web di Azure, vengono acquisite **tutte** le richieste non riuscite.

<h2><a id="Cloudservices" ></a>Servizi cloud di Azure</h2>


Data la natura distribuita dei Servizi cloud di Azure, è importante proteggere l'applicazione effettuando chiamate in modo asincrono e gestendo la ripetizione dei tentativi per gli errori temporanei, come descritto in precedenza.

La tecnica di debug utilizzata per i Servizi cloud di Azure dipende dal tipo di problema che si verifica. I problemi che coinvolgono un particolare ruolo o una particolare istanza del ruolo, ad esempio un ruolo che non si avvia o in esecuzione ciclica, possono essere indagati meglio mediante Desktop remoto. In questi casi, si conoscerà il ruolo o l'istanza del ruolo oggetto del problema e sarà possibile connettersi al ruolo interessato. Quando si verifica un problema e non si conosce con sicurezza l'istanza del ruolo che lo causa, traccia e registrazione rappresentano un metodo migliore per la risoluzione dei problemi. In Diagnostica Azure è disponibile un meccanismo per raccogliere e gestire le informazioni di traccia e log.

In Azure SDK 1.7 sono state aggiunte alcune nuove funzionalità di debug, quali la semplificazione dell'individuazione delle analisi dello stack in caso di eccezioni e miglioramenti alla connettività di Desktop remoto. Le analisi dello stack ora sono incluse nel Registro eventi di Windows, facilitando l'identificazione del problema. È stata inoltre migliorata la connettività di Desktop remoto. Se il ruolo è in esecuzione ciclica o viene interrotto, sarà possibile utilizzare Desktop remoto per connettersi al ruolo interessato ed esaminare il problema.

Il portale Azure fornisce accesso ai dati di monitoraggio che consentono ai professionisti IT e agli sviluppatori di prevedere e diagnosticare i problemi nei Servizi cloud di Azure. Per impostazione predefinita, la macchina virtuale host raccoglie valori quali percentuale della CPU, dati in ingresso, dati in uscita, velocità effettiva di lettura dal disco e velocità effettiva di scrittura sul disco. Non sono necessarie operazioni di configurazione per abilitare queste metriche per le istanze del ruolo e non vi sono ripercussioni in termini di costo per i clienti. Possono essere raccolte anche ulteriori informazioni relative alle prestazioni. Per raccogliere informazioni diagnostiche dettagliate,
è necessario disporre di una stringa di connessione diagnostica valida
in quanto queste informazioni verranno archiviate in Archiviazione di Azure e saranno quindi soggette a costi di archiviazione aggiuntivi. Quando l'utente abilita il monitoraggio dettagliato, il portale configurerà le istanze del ruolo in remoto per la raccolta del set predefinito di contatori delle prestazioni.

### Diagnostica Azure

Azure SDK 1.0 include funzionalità per la raccolta di dati diagnostici e la relativa archiviazione in Azure, denominate collettivamente Diagnostica Azure. Questo software, basato sul framework Event Tracing for Windows (ETW), soddisfa due requisiti di progettazione introdotti dall'architettura a scalabilità orizzontale Azure:

* Salvare i dati di diagnostica che andrebbero persi durante la
  ricreazione dell'immagine dell'istanza.
* Fornire un archivio centrale per la diagnostica da più istanze.

Dopo la configurazione nel ruolo, Diagnostica raccoglie i dati di diagnostica da tutte le istanze di quel determinato ruolo. I dati di diagnostica possono essere utilizzati per il debug e la risoluzione dei problemi, per la misurazione delle prestazioni, per il monitoraggio dell'utilizzo delle risorse, per l'analisi del traffico e la pianificazione della capacità e per il controllo. I trasferimenti nell'account di archiviazione di Azure per la persistenza possono essere pianificati o su richiesta.

Diagnostica Azure modifica il paradigma server in quattro modi importanti:

* La diagnostica deve essere abilitata in fase di creazione
  dell'applicazione.
* Per visualizzare i risultati di diagnostica sono necessari specifici
  strumenti/passaggi.
* Gli arresti anomali determineranno la perdita dei dati diagnostici se
  questi non sono scritti in un archivio durevole (Archiviazione di
  Azure anziché nelle singole istanze).
* L'archiviazione diagnostica comporta un costo mensile se avviene in
  Archiviazione di Azure.

Il costo è un aspetto particolarmente importante poiché la riduzione dei costi rappresenta uno dei vantaggi principali di Azure. Attualmente, l'unico modo per eliminare il costo di utilizzo di Diagnostica consiste nel lasciare i dati sulla macchina virtuale. Questa soluzione può essere appropriata in una piccola distribuzione, ma non è praticabile in presenza di molte istanze. Di seguito sono indicati alcuni modi per ridurre al minimo l'impatto finanziario:

* Assicurarsi che l'account di archiviazione sia ubicato nello stesso
  data center in cui si trova l'applicazione. Se per qualche motivo non
  si trovano nello stesso data center, scegliere con attenzione
  l'intervallo dei trasferimenti pianificati. A una maggiore frequenza
  di trasferimento corrisponde una maggiore rilevanza dei dati, ma il
  compromesso potrebbe non essere di portata tale da giustificare
  l'incremento di larghezza di banda e del carico di elaborazione.
* Copiare e cancellare periodicamente i dati di diagnostica da
  Archiviazione di Azure. I dati di diagnostica transiteranno
  nell'archiviazione di Azure, senza risiedervi inutilmente. Sono
  disponibili numerosi strumenti utili a questo scopo: Monitoring Pack
  di System Center per Azure, Azure Diagnostics Manager di Cerebrata e i
  cmdlet di PowerShell per Azure.
* Scegliere solo i dati di diagnostica che saranno necessari per la
  risoluzione dei problemi e per il monitoraggio dell'applicazione.
  L'acquisizione di troppi dati può ostacolare la risoluzione dei
  problemi, oltre a incrementare significativamente il costo.
* Controllare la raccolta e la portata dei dati di diagnostica
  implementando un'opzione su richiesta nell'applicazione.
* Utilizzare il livello di registrazione (dettagliato, informazione,
  avviso, errore) per assicurare che tutte le informazioni siano
  disponibili, quindi utilizzare la configurazione di Diagnostica
  post-distribuzione per raccogliere i dati in modo selettivo.

<h2><a id="Vms" ></a>Macchine virtuali di Azure</h2>


La risoluzione dei problemi relativi alle applicazioni in esecuzione su Macchine virtuali di Azure prevede generalmente le stesse tecniche impiegate con i sistemi operativi e le piattaforme in uso, ad esempio:

* Windows Server 2008 R2 x64 (RTM e SP1), Windows 8 Service x64
  (Datacenter e Core): sono disponibili moltissime informazioni
  sull'argomento e molti strumenti utili per operare rapidamente.

* Per ulteriori informazioni sugli approcci, vedere [Procedura: eseguire
  il debug di applicazioni di servizio per Windows][32].

* Per un video che illustra gli approcci migliori, vedere la
  [presentazione di Daniel Pearson sul debug e la risoluzione dei
  problemi in Windows][33].

* Open Suse Linux. Sono disponibili anche moltissime risorse per la
  risoluzione dei problemi relativi alle applicazioni in Suse Linux, sia
  nella [documentazione di Suse Linux][34] che su Internet.
* Ubuntu Linux. Anche in questo caso sono reperibili molte informazioni.
  Per iniziare con la documentazione del prodotto, vedere
  [https://help.ubuntu.com/][35].
* CentOS Linux. Per ulteriori informazioni, vedere
  [http://centos.org/][36].

<h2><a id="PlatformServices" ></a>Risoluzione dei problemi relativi ai servizi di Azure</h2>


Per molti dei servizi di Azure, come Database SQL di Azure, Azure Active Directory e Archiviazione di Azure, sono disponibili consigli specifici per la risoluzione dei problemi, indipendentemente dal fatto che l'applicazione venga eseguita in Azure, dal linguaggio di programmazione o dalle librerie con cui è stata creata o dal fatto che venga eseguita su un sistema operativo non Microsoft. Di seguito sono descritte le procedure consigliate specifiche per alcuni di questi servizi.

Esistono molte librerie supportate che implementano le procedure consigliate per le chiamate asincrone, le tracce e la registrazione degli eventi illustrate nella parte di questo documento dedicata alla progettazione.

#### Risoluzione dei problemi relativi ad Archiviazione di Azure

Tramite i collegamenti seguenti è possibile accedere a indicazioni sulla progettazione o sulle procedure per limitare i problemi che richiedono interventi di risoluzione o le posizioni in cui è consigliabile aggiungere attività di traccia o registrazione.

* Stato dell'archiviazione e codici di errore:
  [http://msdn.microsoft.com/it-it/library/windowsazure/dd179382.aspx][37]

* Analisi dell'archiviazione:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx][38]
  (vedere i tre collegamenti a ulteriori informazioni nella parte
  inferiore).

* Panoramica dei criteri di ripetizione dei tentativi nella libreria del
  client di archiviazione di Azure:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx][39]

* Come sfruttare al massimo le tabelle di Azure:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx][40]
  
  * Vedere le sezioni relative a procedure consigliate, suggerimenti per
    la programmazione di Azure ed errori Timeout e ServerBusy.

* Protezione dei BLOB da errori dell'applicazione:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx][41]

**Strumenti di esplorazione degli archivi di Azure**

Esistono molti modi per esplorare gli archivi di Azure. Il team di Archiviazione di Azure ha fornito un [elenco degli strumenti di esplorazione degli archivi][42]. Tutti questi strumenti consentono di visualizzare i file di Diagnostica e i file di Analisi archiviazione di Azure. Lo [strumento di esplorazione per l'archiviazione BLOB di Azure][43] di Cloudberry Lab offre un'interfaccia utente che consente di abilitare Analisi archiviazione direttamente nell'applicazione facendo clic su **Storage Settings**.

<h2><a id="Misc" ></a>Risoluzione dei problemi relativi al bus di servizio di Azure</h2>


In questa sezione vengono fornite indicazioni di alto livello sullo sviluppo di un'applicazione che utilizza il bus di servizio di Azure in modo affidabile e gestibile, riducendo al minimo i problemi più comuni. Vengono inoltre fornite informazioni dettagliate su come identificare e affrontare gli errori comuni relativi al bus di servizio.

### Indicazioni generali

Il bus di servizio è un [Enterprise Service Bus][44] di scala Internet che supporta funzionalità di messaggistica inoltrata e negoziata. Il bus di servizio implementa quote e soglie a livello di sistema per entrambi i tipi di messaggistica. Se queste quote vengono superate, l'applicazione verrà limitata oppure le richieste o i messaggi verranno rifiutati. Per informazioni dettagliate sulle quote del bus di servizio e sulle conseguenze del relativo superamento, vedere l'argomento relativo alle [quote del bus di servizio di Azure][45]. Alcune quote sono definite dall'utente, ad esempio le dimensioni di una coda o di un argomento, al momento della creazione dell'entità.

Per una panoramica dei dati nell'entità di messaggistica del bus di servizio e sulla relativa modalità di elaborazione, è possibile utilizzare lo [strumento di esplorazione del bus di servizio][46] o Esplora server negli strumenti di Azure per Visual Studio (versione 1.7 o successive) per creare, eliminare e testare code, argomenti, sottoscrizioni e regole. Si tratta di un ottimo modo per risolvere i problemi di un'applicazione che è in esecuzione ma non elabora i dati nel modo previsto. Questi strumenti includono funzionalità che consentono di testare code, argomenti ed entità di inoltro, di tracciare le operazioni eseguite da singole attività mittente e ricevitore, di monitorare l'avanzamento e le prestazioni di un'esecuzione di test continuativa e di generare log dettagliati dei risultati, inclusi gli eventuali messaggi di errore.

### Inoltro del bus di servizio

Il servizio di "inoltro" del bus di servizio viene eseguito nel cloud e supporta diversi protocolli di trasporto e standard dei servizi Web, tra cui SOAP, WS-\* e REST. L'inoltro del bus di servizio può essere utilizzato come delegato per l'ascolto delle sessioni in ingresso e delle richieste inviate a un servizio WCF. Nel modello di messaggistica inoltrata un servizio locale o basato sul cloud si connette al servizio di inoltro attraverso una porta in uscita e crea un socket bidirezionale per la comunicazione associato a un determinato indirizzo rendezvous. Il client non deve necessariamente conoscere la posizione in cui risiede il servizio e il servizio locale non richiede porte in ingresso aperte sul firewall. Tuttavia, a seconda delle configurazione di rete, è possibile che si verifichino problemi durante la connessione all'inoltro del bus di servizio attraverso un firewall o un server proxy. In [Hosting con Service Bus attraverso un firewall][47] viene descritto come risolvere tali problemi di connessione.

### Code e argomenti del bus di servizio

Le code e gli argomenti del bus di servizio forniscono la funzionalità di messaggistica negoziata: i messaggi vengono inviati alla coda o all'argomento del bus di servizio, dove possono permanere in modo affidabile finché il ricevitore non è pronto per utilizzarli. I mittenti e i ricevitori dei messaggi non devono essere online contemporaneamente. L'infrastruttura di messaggistica archivia in modo affidabile i messaggi finché il consumer non è pronto a riceverli. L'API di messaggistica può rilevare una serie di errori che possono incidere sull'applicazione e possono essere suddivisi nelle seguenti categorie principali:

* Errore utente, ad esempio un codice corrispondente a un argomento non
  valido. Azione consigliata: provare a correggere il codice prima di
  continuare.
* Errore di installazione/configurazione, ad esempio una coda o un
  argomento associato all'azione non esiste o è stato eliminato. Azione
  consigliata: controllare la configurazione e modificarla, se
  necessario.
* Errore temporaneo, ad esempio una risposta che indica che il servizio
  non è riuscito a elaborare la richiesta all'ora corrente. Azione
  consigliata: ripetere l'operazione o inviare una notifica agli
  utenti. Per ulteriori informazioni, vedere [Gestione di errori di
  comunicazione temporanei][48].
* Altri errori, ad esempio errori di timeout o errori che indicano che
  il blocco del messaggio è andato perso. Azione consigliata: queste
  eccezioni in genere non vengono gestite per eseguire operazioni di
  pulizia o per interrompere azioni. Possono essere utilizzate per
  eseguire operazioni di traccia.

L'articolo relativo alle [eccezioni di messaggistica][49] contiene una panoramica delle eccezioni che gli utenti delle librerie client del bus di servizio per .NET potrebbero riscontrare, nonché consigli per la gestione di ogni tipo di eccezione. Dato che le librerie client per .NET sono strettamente allineate alla struttura delle librerie del bus di servizio per altri linguaggi, queste indicazioni possono essere utili anche se non si programma in un linguaggio .NET. In alcuni casi, ad esempio con gli errori temporanei, è possibile riprovare l'azione. Per gestire gli errori temporanei in modo efficace, è possibile seguire le linee guida per la gestione degli errori temporanei illustrate in precedenza in questo articolo. Per informazioni più dettagliate, procedure consigliate e codice di esempio che illustra come gestire gli errori temporanei del bus di servizio in un'applicazione .NET, vedere la sezione [Gestione di errori di comunicazione temporanei][48] nell'articolo di istruzioni per sviluppatori di Azure [Procedure consigliate per l'utilizzo dell'API di messaggistica negoziata del bus di servizio di Azure][50].

Durante lo sviluppo di un'applicazione che utilizza la messaggistica negoziata è importante anche implementare una logica affidabile di ricezione dei messaggi che consenta di gestire le anomalie nei messaggi in modo preciso ed efficiente. Nella sezione relativa all'implementazione di loop affidabili per la ricezione di messaggi dell'articolo [Procedure consigliate per l'utilizzo dell'API di messaggistica negoziata del bus di servizio di Azure][50] vengono descritte molte tecniche per l'utilizzo della modalità di ricezione
**PeekLock**, la modalità che supporta recapiti multipli se il messaggio
non viene consegnato correttamente al primo tentativo. In questo articolo vengono indicate le procedure consigliate per fare in modo che l'applicazione non elabori messaggi duplicati. Vengono inoltre fornite informazioni utili per evitare i problemi dovuti a timeout di blocco e per migliorare le prestazioni complessive in modalità **PeekLock** assicurando la rapida elaborazione dei messaggi. In questo articolo è presente anche codice di esempio che utilizza le librerie client del bus di servizio per .NET.

### Ulteriori risorse per la risoluzione dei problemi

Per ulteriori dettagli sugli errori comuni del bus di servizio e su come esaminarli e affrontarli, vedere [Risoluzione dei problemi relativi a Service Bus][51].

## Servizio di controllo di accesso (ACS) di Azure Active Directory

Risoluzione dei problemi

* Codici di errore:
  [http://msdn.microsoft.com/it-it/library/windowsazure/gg185949.aspx][52]
* Limitazioni del servizio ACS:
  [http://msdn.microsoft.com/it-it/library/windowsazure/gg185909.aspx][53]

<h2><a id="SQLTroubleshooting" ></a>Risoluzione dei problemi relativi al database SQL di Azure</h2>


Quando si interagisce con un database SQL di Azure è importante prestare particolare attenzione alla natura distribuita delle applicazioni di database SQL di Azure. In questa sezione vengono presentati molti aspetti di particolare rilevanza. Si tratta di un elenco non esaustivo. Per scrivere codice supportabile per il database SQL di Azure è fondamentale esaminare i codici restituiti e assicurarsi di disporre di un codice di ripetizione affidabile per gestire gli errori.

L'applicazione deve gestire gli errori di accesso in modo normale. Le istanze del database SQL richiedono l'utilizzo dell'autenticazione SQL. Se non è possibile eseguire l'accesso, le credenziali non sono valide oppure il database richiesto non è disponibile.

L'applicazione deve gestire la mancata accessibilità del servizio. Se è già stato eseguito il provisioning del server e il servizio database SQL di Azure è disponibile (per verificarlo, è possibile utilizzare la pagina relativa allo [stato di integrità di Azure][54], è probabile che la situazione sia causata da problemi di configurazione nell'installazione in sede. Ad esempio, è possibile che il nome non possa essere risolto (per il test è possibile utilizzare strumenti come tracert), che un firewall blocchi la porta 1433 utilizzata dal database SQL o che il server proxy in uso non sia configurato correttamente. Per risolvere questi problemi, utilizzare le stesse tecniche di risoluzione dei problemi impiegate per SQL Server. Per ulteriori informazioni, vedere la [guida alla risoluzione dei problemi di connettività del database SQL][55] e [Risoluzione dei problemi di database SQL][56].

L'applicazione deve gestire gli errori generali di rete. Gli errori generali di rete possono essere causati dal fatto che il database SQL di Azure può disconnettere gli utenti nelle circostanze seguenti:

* Quando una connessione è inattiva per un determinato periodo di tempo
* Quando una connessione utilizza un numero eccessivo di risorse o tiene
  occupata una transazione per un periodo di tempo prolungato
* Se il server è troppo occupato

Per migliorare le prestazioni nel database SQL di Azure, utilizzare le stesse tecniche impiegate con SQL Server. Per ulteriori informazioni, vedere gli argomenti seguenti:

* [Risoluzione dei problemi relativi alle query][57] nella
  documentazione online di SQL Server
* [Risoluzione dei problemi e ottimizzazione delle query con il database
  SQL][58]
* [Considerazioni e risoluzione dei problemi relativi alle prestazioni
  del database SQL][59]
* [Miglioramento delle prestazioni di I/O][60]
* [Monitoring Pack di System Center per il database SQL][61]

Il database SQL di Azure utilizza un subset di messaggi di errore di SQL Server. Per ulteriori informazioni sugli errori di SQL Server, vedere
[Guida di riferimento a errori ed eventi (Motore di database)][62] nella
documentazione online di SQL Server.

Per recuperare nomi di accesso o password, rivolgersi all'amministratore del servizio che potrà concedere l'accesso adeguato al server e al database. Gli amministratori dei servizi possono reimpostare le proprie password anche tramite il portale di gestione di Azure.

Le query sul database SQL possono avere esito negativo per diverse ragioni, ad esempio se il formato della query non è corretto o se si verificano problemi di rete. Alcuni errori sono temporanei, ovvero si risolvono autonomamente in molti casi. Per questo subset di errori, è utile riprovare la query dopo un breve intervallo di tempo. Se la query ha ancora esito negativo dopo diversi tentativi, è necessario segnalare un errore. Naturalmente non tutti gli errori sono temporanei. L'errore SQL 102 ("Sintassi non corretta") non si risolve, indipendentemente da quante volte si invia la stessa query. In breve, l'implementazione di una solida logica di ripetizione richiede una certa cautela. Prima della disconnessione degli utenti, se possibile, viene inviato un token di errore TDS (Tabular Data Stream). Per migliorare l'esperienza delle applicazioni, è consigliabile implementare la logica di ripetizione dei tentativi nelle applicazioni di database SQL per individuare questi errori. Quando si verifica un errore, ristabilire la connessione, quindi eseguire nuovamente i comandi non riusciti o la query. Per ulteriori informazioni, vedere i collegamenti seguenti:

* [Logica di ripetizione dei tentativi per gli errori temporanei nel
  database SQL][63]
* [Esempio di logica di ripetizione dei tentativi nel database SQL][64]
* [Blocco di applicazioni per la gestione degli errori temporanei][65]

### Strategia di backup e ripristino per il database SQL di Azure

Il database SQL di Azure richiede una strategia di backup e ripristino specifica a causa dell'ambiente e degli strumenti disponibili. Per molti versi, i rischi sono stati mediati situando il database nei data center Microsoft. Gli strumenti attualmente a disposizione coprono gli altri fattori di rischio, ma stanno emergendo strumenti migliori in grado di semplificare notevolmente il lavoro. Red-gate ha pubblicato di recente uno strumento gratuito per il backup e il ripristino del database SQL, disponibile al seguente indirizzo:
[http://www.red-gate.com/products/dba/sql-azure-backup/][66].

La sincronizzazione dati SQL consente di creare e pianificare facilmente sincronizzazioni bidirezionali dal sito Web di sincronizzazione dati eliminando del tutto la necessità di scrivere codice. Ulteriori informazioni sono disponibili all'indirizzo
[http://msdn.microsoft.com/it-it/library/windowsazure/hh456371.aspx][67].

Per ulteriori informazioni sulle strategie di backup e ripristino del database SQL, vedere gli articoli seguenti:

* In questo argomento viene fornita una panoramica delle strategia di
  backup e ripristino del database SQL:
  [http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx][68]
* In questo argomento viene spiegato come eseguire il backup di un
  database in un altro database sullo stesso server:
  [http://msdn.microsoft.com/it-it/library/windowsazure/ff951631.aspx][69]
* In questo argomento viene spiegato come esportare un'istanza
  esistente di database SQL in un BLOB su un determinato account di
  archiviazione:
  [http://msdn.microsoft.com/it-it/library/windowsazure/hh335292.aspx][70]
* In questo argomento viene spiegato come importare un'istanza
  esistente di database SQL da un file bacpac archiviato in un BLOB:
  [http://msdn.microsoft.com/it-it/library/windowsazure/hh335291.aspx][71]
* In questo argomento vengono descritte le funzionalità di continuità
  aziendale fornite dal database SQL:
  [http://msdn.microsoft.com/it-it/library/windowsazure/hh852669.aspx][72]

<h2><a id="Cache" ></a>Cache di Azure</h2>


Cache di Azure è disponibile in due forme: Memorizzazione nella cache condivisa di Azure e Cache di Azure (Anteprima) basato su ruolo. Memorizzazione nella cache condivisa è un servizio multi-tenant di Azure che fornisce servizi di memorizzazione nella cache. Cache di Azure
(Anteprima) ospita la memorizzazione nella cache in un ruolo utilizzando
una parte della memoria delle macchine virtuali che ospitano le istanze del ruolo. Per la risoluzione dei problemi relativi a Cache di Azure, osservare il comportamento della cache controllando i codici di errore e rilevando le eccezioni. Quando si utilizza Cache (Anteprima) basato sul ruolo, è possibile utilizzare anche i contatori delle prestazioni. I problemi di memorizzazione nella cache rientrano generalmente in una delle categorie seguenti:

* Errori correlati alle quote: è stata superata una quota
  (Memorizzazione nella cache condivisa)
* Limitazione: si verifica quando la memoria fisica è insufficiente per
  supportare la memorizzazione di ulteriori voci nella cache
* Rimozione: vengono rimossi elementi esistenti in modo forzato per
  liberare spazio per nuovi elementi, compromettendo le prestazioni
  dell'applicazione
* Scadenza: le scadenze impostate sono troppo brevi o lunghe

Per ulteriori informazioni sugli errori correlati alle quote, vedere
[Informazioni sulle quote][73].



[1]: https://www.windowsazure.com/it-it/develop/overview/
[2]: http://msdn.microsoft.com/it-it/library/hh680918%28v=pandp.50%29.aspx
[3]: http://nuget.org/packages/Microsoft.Experience.CloudFx
[4]: http://msdn.microsoft.com/it-it/magazine/ff714589.aspx
[5]: http://www.microsoft.com/download/en/details.aspx?id=11324
[6]: http://www.microsoft.com/it-it/server-cloud/system-center/operations-manager.aspx
[7]: http://wapmmc.codeplex.com/
[8]: http://cerebrata.com/Products/AzureDiagnosticsManager/
[9]: http://cerebrata.com/Products/CloudStorageStudio/
[10]: http://cerebrata.com/Products/AzureManagementCmdlets/
[11]: http://www.cerebrata.com
[12]: http://www.compuware.com/application-performance-management/
[13]: http://www.keynote.com/solutions/
[14]: http://pingdom.com/
[15]: http://www.alertbot.com/products/website-monitoring/default.aspx
[16]: http://www.apicasystem.com/integration-partners/
[17]: http://www.microsoft.com/it-it/server-cloud/system-center/avicode.aspx
[18]: http://msdn.microsoft.com/it-it/library/windowsazure/hh369930.aspx
[19]: http://azurevmassist.codeplex.com/
[20]: https://github.com/dannycoates/node-inspector
[21]: http://weblogs.asp.net/jimwang/archive/2012/04/17/debugging-node-node-inspector-in-the-azure-emulator.aspx
[22]: https://github.com/windowsazure/iisnode/downloads
[23]: http://howtonode.org/debugging-with-node-inspector
[24]: http://msdn.microsoft.com/it-it/library/dd264915.aspx
[25]: http://www.fiddler2.com/fiddler2/
[26]: http://go.microsoft.com/fwlink/?LinkID=90561
[27]: http://go.microsoft.com/fwlink/?LinkId=246619
[28]: http://logparserplus.com/Examples
[29]: http://technet.microsoft.com/it-it/library/ee692659.aspx
[30]: http://www.microsoft.com/downloads/en/details.aspx?familyid=7287252C-402E-4F72-97A5-E0FD290D4B76&displaylang=enBlockquote
[31]: http://logparserplus.com/article/2
[32]: http://msdn.microsoft.com/it-it/library/7a50syb3%28v=vs.90%29.aspx
[33]: http://technet.microsoft.com/it-it/edge/Video/hh867800
[34]: https://www.suse.com/documentation/
[35]: https://help.ubuntu.com/
[36]: http://centos.org/
[37]: http://msdn.microsoft.com/it-it/library/windowsazure/dd179382.aspx
[38]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx
[39]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx
[40]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
[41]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx
[42]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/17/windows-azure-storage-explorers.aspx
[43]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[44]: http://en.wikipedia.org/wiki/Enterprise_service_bus
[45]: http://msdn.microsoft.com/it-it/library/windowsazure/ee732538.aspx
[46]: http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a
[47]: http://msdn.microsoft.com/it-it/library/windowsazure/ee706729.aspx
[48]: http://msdn.microsoft.com/it-it/library/hh851746(VS.103).aspx
[49]: http://msdn.microsoft.com/it-it/library/hh418082.aspx
[50]: http://msdn.microsoft.com/it-it/library/windowsazure/hh545245.aspx
[51]: http://msdn.microsoft.com/it-it/library/windowsazure/ee706702.aspx
[52]: http://msdn.microsoft.com/it-it/library/windowsazure/gg185949.aspx
[53]: http://msdn.microsoft.com/it-it/library/windowsazure/gg185909.aspx
[54]: http://go.microsoft.com/fwlink/p/?LinkId=168847
[55]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-connectivity-troubleshooting-guide.aspx
[56]: http://msdn.microsoft.com/it-it/library/ee730906.aspx
[57]: http://msdn.microsoft.com/it-it/library/ms186351(SQL.100).aspx
[58]: http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-sql-azure.aspx
[59]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI314
[60]: http://blogs.msdn.com/b/sqlazure/archive/2010/07/27/10043069.aspx?PageIndex=2#comments
[61]: http://www.microsoft.com/it-it/download/details.aspx?id=10631
[62]: http://go.microsoft.com/fwlink/p/?LinkId=166622
[63]: http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-sql-azure.aspx
[64]: http://code.msdn.microsoft.com/windowsazure/SQL-Azure-Retry-Logic-2d0a8401
[65]: http://msdn.microsoft.com/it-it/library/hh680934(PandP.50).aspx
[66]: http://www.red-gate.com/products/dba/sql-azure-backup/
[67]: http://msdn.microsoft.com/it-it/library/windowsazure/hh456371.aspx
[68]: http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx
[69]: http://msdn.microsoft.com/it-it/library/windowsazure/ff951631.aspx
[70]: http://msdn.microsoft.com/it-it/library/windowsazure/hh335292.aspx
[71]: http://msdn.microsoft.com/it-it/library/windowsazure/hh335291.aspx
[72]: http://msdn.microsoft.com/it-it/library/windowsazure/hh852669.aspx
[73]: http://msdn.microsoft.com/it-it/library/gg185683.aspx