<properties  umbracoNaviHide="0" pageTitle="Application Model" metaKeywords="Azure, Azure, application model, Azure application model, development model, Azure development model, hosted service, Azure hosted service, web role, worker role" description="Learn about the Azure hosted service application model. Understand core concepts, design considerations, defining and configuring your application, and scaling." linkid="dev-net-fundamentals-application-model" urlDisplayName="Application Model" headerExpose="" footerExpose="" disqusComments="1" title="Application Model" authors="" />

# Modelli di esecuzione di Azure

In Azure sono disponibili diversi modelli di esecuzione per l'esecuzione di applicazioni.

Ogni modello offre un insieme specifico di servizi, quindi il modello scelto dipende dalle operazioni che si desidera eseguire. In questo articolo vengono esaminati tre modelli, viene descritta la tecnologia associata a ogni modello e vengono proposti esempi di situazioni in cui utilizzare ogni modello.

## Sommario

* [Macchine virtuali](#VMachine)
* [Siti Web](#WebSites)
* [Servizi cloud](#CloudServices)
* [Scelta del modello da utilizzare](#WhatShouldIUse)

<h2><a  id="VMachine" ></a>Macchine virtuali</h2>


Macchine virtuali di Azure consente agli sviluppatori, ai responsabili IT e ad altri utenti di creare e utilizzare macchine virtuali nel cloud. Grazie all'approccio *IaaS (Infrastructure as a Service)* offerto, è possibile utilizzare questa tecnologia in diversi modi. [Nella
](#Fig1)figura 1 vengono illustrati i componenti di base di questa
tecnologia.

<a name="Fig1"></a>![01_CreatingVMs](./media/fundamentals-application-models/ExecModels_01_CreatingVMs.png)

**Figura 1: In Macchine virtuali di Azure è disponibile l'approccio IaaS
(Infrastructure as a Service).**

Come illustrato nella figura, è possibile creare macchine virtuali utilizzando il portale di gestione di Azure oppure l'API di Gestione servizi di Azure basata su REST. Il portale di gestione è accessibile tramite qualsiasi browser, inclusi Internet Explorer, Mozilla e Chrome. Microsoft rende disponibili strumenti di scripting client specifici per l'API REST per sistemi Windows, Linux e Macintosh. Questa interfaccia può essere utilizzata anche da altro software.

Indipendentemente dalla modalità di accesso alla piattaforma, per creare una nuova macchina virtuale (VM, Virtual Machine) è necessario scegliere un disco rigido virtuale (VHD, Virtual Hard Disk) per l'immagine della macchina virtuale. Tali dischi rigidi virtuali vengono archiviati in BLOB di Azure.

Per iniziare, è possibile eseguire una delle due operazioni seguenti:

1.  Caricare un disco rigido virtuale personalizzato 2.  Utilizzare i dischi rigidi virtuali forniti da Microsoft e dai
    partner Microsoft nella raccolta Macchine virtuali di Azure o nel
    sito Web open source Microsoft [VMDepot][1].

I dischi rigidi virtuali disponibili nella raccolta e nel sito Web VMDepot includono immagini pulite dei sistemi operativi Microsoft e Linux, oltre a immagini che includono prodotti Microsoft e di terzi installati in tali sistemi operativi. Le opzioni disponibili sono sempre più numerose. Gli esempi includono diverse versioni, edizioni e configurazioni di:

* Windows Server
* Server Linux quali Suse, Ubuntu e CentOS
* SQL Server
* BizTalk Server
* SharePoint Server

Oltre al disco rigido virtuale, si specificano le dimensioni della nuova macchina virtuale. Le statistiche complete per ogni dimensione sono elencate [nella raccolta di Azure][2].

* **Extra Small**, con una memoria centrale condivisa e 768 GB di
  memoria.
* **Small**, con 1 memoria centrale e 1,75 GB di memoria.
* **Medium**, con 2 memorie centrali e 3,5 GB di memoria.
* **Large**, con 4 memorie centrali e 7 GB di memoria.
* **Extra Large**, con 8 memorie centrali e 14 GB di memoria.
* **A6**, con 4 memorie centrali e 28 GB di memoria.
* **A7**, con 8 memorie centrali e 56 GB di memoria.

Scegliere quindi il data center di Azure in cui deve essere eseguita la macchina virtuale, negli Stati Uniti, in Europa o in Asia.

Quando la macchina virtuale è in esecuzione, si paga ogni ora di esecuzione e il pagamento viene interrotto quando si rimuove la VM. L'importo pagato non dipende dal livello di utilizzo della macchina virtuale, ma dal tempo. Una macchina virtuale inattiva per un'ora ha lo stesso costo di una macchina virtuale sottoposta a carico di lavoro elevato.

A ogni macchina virtuale in esecuzione è associato un *disco sistema operativo*, archiviato in un BLOB. Quando si crea una macchina virtuale utilizzando un disco rigido virtuale della raccolta, tale disco rigido virtuale verrà copiato nel disco sistema operativo della VM. Eventuali modifiche apportate al sistema operativo della macchina virtuale in esecuzione vengono archiviate qui. Se ad esempio si installa un'applicazione che modifica il Registro di sistema di Windows, tale modifica verrà memorizzata nel disco sistema operativo della macchina virtuale. Alla successiva creazione di una macchina virtuale dal disco sistema operativo, la VM riprenderà l'esecuzione con l'ultimo stato registrato. Quando necessario, Microsoft applica aggiornamenti ai dischi rigidi virtuali, ad esempio patch del sistema operativo. L'applicazione di tali aggiornamenti ai dischi rigidi virtuali presenti nei dischi sistema operativo deve essere tuttavia eseguita dall'utente, anche se Windows Update è attivato per impostazione predefinita.

È inoltre possibile modificare le macchine virtuali in esecuzione e
quindi acquisirle mediante lo strumento sysprep, che consente di rimuovere specifiche quale il nome della macchina, in modo da potere utilizzare un'immagine di disco rigido virtuale per creare macchine virtuali aggiuntive con la stessa configurazione generale. Tali immagini vengono archiviate nel portale di gestione insieme alle immagini caricate, in modo da consentirne l'utilizzo come punto di partenza per la creazione di macchine virtuali aggiuntive.

Macchine virtuali consente inoltre di monitorare l'hardware che ospita ogni VM creata. In caso di errore di un server fisico che esegue una macchina virtuale, la piattaforma rileva l'errore e avvia la stessa VM in un'altra macchina. Se sono disponibili le licenze appropriate, è inoltre possibile copiare un disco rigido virtuale modificato dal disco sistema operativo, quindi eseguirlo da una posizione diversa, ad esempio dal data center locale o da un altro provider di cloud.

Oltre al disco sistema operativo, la macchina virtuale dispone di uno o più dischi dati. Anche se la macchina virtuale considera ognuno di tali dischi come un'unità disco montata, i contenuti di ogni disco vengono in effetti memorizzati in un BLOB. Ogni scrittura in un disco dati viene archiviata in modo persistente nel BLOB sottostante. Come con tutti gli altri BLOB, Azure esegue la replica in un singolo data center e in tutti i data center, per assicurare una migliore protezione da errori.

È possibile gestire l'esecuzione delle macchine virtuali tramite il
portale di gestione, PowerShell e altri strumenti di scripting oppure direttamente tramite l'API REST, che consente, in effetti di eseguire a livello di codice qualsiasi operazione eseguita tramite il portale di gestione. Anche i partner Microsoft, ad esempio RightScale e ScaleXtreme, offrono servizi di gestione basati sull'API REST.

È possibile utilizzare Macchine virtuali di Azure in molti modi. Di
seguito sono riportati alcuni scenari principali a cui Microsoft si rivolge:

* **Macchine virtuali per lo sviluppo e il test.** I gruppi di sviluppo
  necessitano in genere di macchine virtuali con configurazioni
  specifiche per la creazione di applicazioni. Macchine virtuali di
  Azure consente di creare tali macchine virtuali in modo semplice ed
  economico, quindi di utilizzarle e rimuoverle quando non sono più
  necessarie.
* **Esecuzione di applicazioni nel cloud.** L'esecuzione nel cloud
  pubblico risulta economicamente vantaggiosa per alcune applicazioni,
  ad esempio per un'applicazione con picchi elevati a livello di
  domanda. È sempre possibile acquistare un numero di macchine
  sufficienti per consentire al proprio data center di eseguire tale
  applicazione, ma è probabile che la maggior parte di queste macchine
  risulti inattiva per la maggior parte del tempo. L'esecuzione di
  un'applicazione di questo tipo su Azure consente di pagare le macchine
  virtuali aggiuntive solo quando sono necessarie, arrestandole al
  termine del picco nella domanda. Azure rappresenta la soluzione
  ottimale, ad esempio, anche nel caso di una start-up che necessita
  rapidamente di risorse di calcolo su richiesta e senza impegno.
* **Estensione del data center nel cloud pubblico.** Grazie a Rete
  virtuale di Azure, un'organizzazione può creare una rete virtuale
  (VNET) che consente a un gruppo di macchine virtuali di Azure di
  apparire come parte della propria rete locale. Sarà quindi possibile
  eseguire applicazioni quali SharePoint e altro ancora su Azure. Tale
  approccio potrebbe essere più semplice da distribuire e/o meno costoso
  rispetto all'esecuzione nel proprio data center.
* **Ripristino di emergenza.** Invece di effettuare pagamenti continui
  per un data center di backup utilizzato raramente, il ripristino di
  emergenza basato su IaaS consente di pagare le risorse di calcolo
  necessarie solo quando servono effettivamente. In caso, ad esempio, di
  arresto imprevisto del data center principale, è possibile creare
  macchine virtuali in esecuzione su Azure per eseguire applicazioni
  essenziali, quindi arrestarle quando non sono più necessarie.

Gli scenari indicati non costituiscono le uniche possibilità di utilizzo di Macchine virtuali di Azure, ma sono ottimi esempi di utilizzo.

### Raggruppare le macchine virtuali: Servizi cloud

Quando si crea una nuova VM con Macchine virtuali di Azure, è possibile scegliere di eseguirla in modo autonomo o di aggiungerla a un gruppo di macchine virtuali in esecuzione in un *servizio cloud*. Anche se i nomi sono simili, è necessario non confondere questo concetto con Servizi cloud, ovvero la tecnologia PaaS di Azure. A ogni macchina virtuale autonoma viene assegnato un indirizzo IP pubblico specifico, mentre è possibile accedere a tutte le VM nello stesso servizio cloud tramite un singolo indirizzo IP pubblico, come illustrato nella [figura 2](#Fig2).

<a name="Fig2"></a>![02_CloudServices](./media/fundamentals-application-models/ExecModels_02_CloudServices.png)

**Figura 2: ogni macchina virtuale autonoma dispone del proprio
indirizzo IP pubblico, mentre le VM raggruppate in un servizio cloud vengono esposte tramite un singolo indirizzo IP pubblico.**

Se ad esempio si crea una macchina virtuale per la creazione e il test di una semplice applicazione, è probabilmente consigliabile utilizzare una VM autonoma. Se si crea invece un'applicazione multilivello con un front-end Web, un database e magari un livello intermedio, è probabile che si scelga di connettere più macchine virtuali in un servizio cloud, come indicato nella figura 2.

Il raggruppamento delle macchine virtuali in un servizio cloud consente di utilizzare anche altre opzioni. Azure offre il bilanciamento del carico per macchine virtuali nello stesso servizio cloud, distribuendo le richieste degli utenti tra le VM. Le macchine virtuali connesse in tale modo saranno in grado di comunicare direttamente tra loro sulla rete locale in un data center di Azure.

È inoltre possibile raggruppare in uno o più *set di disponibilità* le
macchine virtuali disponibili nello stesso servizio cloud. Per comprendere meglio questo concetto, si pensi a un'applicazione Web che esegue più macchine virtuali front-end. Se tutte le VM vengono distribuite nella stessa macchina fisica o nello stesso rack di macchine, è possibile che un singolo errore hardware le renda tutte inaccessibili. Se tali macchine virtuali vengono invece raggruppate in un set di disponibilità, Azure le distribuirà in diversi data center, in modo che le VM non condividano alcun singolo punto di errore.

### Scenario: Eseguire un'applicazione con SQL Server

Per comprendere meglio il funzionamento di Macchine virtuali di Azure, può risultare utile esaminare in modo più dettagliato un paio di scenari. Si supponga, ad esempio, che si desideri creare un'applicazione Web affidabile e scalabile in esecuzione su Azure. A tale scopo, è possibile ad esempio eseguire la logica dell'applicazione in una o più macchine virtuali di Azure, quindi utilizzare SQL Server per la gestione dati, come illustrato nella [figura 3](#Fig3).

<a name="Fig3"></a>![03_AppUsingSQLServer](./media/fundamentals-application-models/ExecModels_03_AppUsingSQLServer.png)

**Figura 3: un'applicazione in esecuzione in Macchine virtuali di Azure
può utilizzare SQL Server per l'archiviazione.**

In questo esempio entrambi i tipi di macchine virtuali vengono creati da dischi rigidi virtuali standard nella raccolta. La logica dell'applicazione viene eseguita su Windows Server 2008 R2. Lo sviluppatore crea tre macchine virtuali dal disco rigido virtuale, quindi installa l'applicazione su ogni VM. Poiché tutte queste macchine virtuali si trovano nello stesso servizio cloud, lo sviluppatore potrà configurare il bilanciamento del carico hardware, in modo da distribuire le richieste tra le VM. Lo sviluppatore crea inoltre due macchine virtuali dal disco rigido virtuale disponibile nella raccolta e contenente SQL Server 2012. Quando le VM sono in esecuzione, configura SQL Server in ogni istanza in modo che venga utilizzato il mirroring di database con failover automatico. Tale approccio non è obbligatorio, poiché l'applicazione può utilizzare una singola istanza di SQL Server, ma offre una maggiore affidabilità.

### Scenario: Eseguire una farm SharePoint

Si supponga che un'organizzazione desideri creare una farm SharePoint ma non desideri che la farm sia eseguita in un data center specifico. È ad esempio possibile che il data center locale non disponga di risorse sufficienti oppure che la business unit che crea la farm preferisca non interagire con il gruppo IT interno. In casi di questo tipo, l'esecuzione di SharePoint su Macchine virtuali di Azure può risultare ottimale, come illustrato nella [figura 4](#Fig4).

<a name="Fig4"></a>![04_SharePointFarm](./media/fundamentals-application-models/ExecModels_04_SharePointFarm.png)

**Figura 4: Macchine virtuali di Azure consente di eseguire una farm
SharePoint nel cloud.**

Una farm SharePoint è costituita da diversi componenti, ognuno dei quali
è in esecuzione in una macchina virtuale di Azure creata da un disco
rigido virtuale diverso. Di seguito sono elencati gli elementi necessari:

* Microsoft SharePoint. Nella galleria sono disponibili immagini di
  prova oppure l'organizzazione fornisce un disco rigido virtuale
  specifico.
* Microsoft SQL Server. SharePoint dipende da SQL Server, quindi
  l'organizzazione crea macchine virtuali in esecuzione su SQL Server
  2012 da un disco rigido virtuale standard della raccolta.
* Windows Server Active Directory. SharePoint richiede anche Active
  Directory, quindi l'organizzazione crea i controller di dominio nel
  cloud utilizzando un'immagine di Windows Server dalla raccolta. Tale
  procedura non è obbligatoria, poiché è possibile utilizzare anche i
  controller di dominio locali, ma SharePoint interagisce spesso con
  Active Directory, quindi l'approccio illustrato consente di ottenere
  prestazioni migliori.

Benché non sia illustrato nella figura, la farm SharePoint è probabilmente connessa a una rete locale che utilizza Rete virtuale di Azure. Ciò consente alle macchine virtuali e alle applicazioni in esse incluse di sembrare locali per gli utenti di tale rete.

Come illustrato da questi esempi, è possibile utilizzare Macchine virtuali di Azure per creare ed eseguire nuove applicazioni nel cloud, per eseguire applicazioni esistenti o per utilizzare altri approcci. Indipendentemente dall'opzione scelta, l'obiettivo della tecnologia consiste nell'offrire una base di carattere generale per il computing su cloud pubblico.

<h2><a  id="WebSites" ></a>Siti Web</h2>


Gli utenti utilizzano le tecnologie Web in molti modi diversi. È possibile che un'azienda debba eseguire la migrazione o creare un sito Web di presenza in grado di gestire milioni di riscontri alla settimana e da distribuire in diversi data center in tutto il mondo. Una società di progettazione Web può ad esempio collaborare con un team di sviluppatori per la creazione di un'applicazione Web in grado di gestire migliaia di utenti. È possibile che uno sviluppatore aziendale debba configurare un'applicazione per la registrazione di note spese nel cloud per gli utenti autenticati da Active Directory aziendale. Un consulente IT può utilizzare un'applicazione open source nota per configurare un sistema di gestione dei contenuti per una piccola impresa. È possibile ottenere tutti questi risultati utilizzando Macchine virtuali di Azure. La creazione e la gestione di macchine virtuali non elaborate richiedono competenze specifiche e possono risultare impegnative. Se è necessario implementare un sito Web o un'applicazione Web, è possibile scegliere una soluzione più semplice ed economica, ovvero l'approccio comunemente noto come PaaS (Platform as a Service). Come illustrato nella figura 5, tale tecnologia è disponibile in Siti Web di Azure.

<a name="Fig5"></a>![05_Websites](./media/fundamentals-application-models/ExecModels_05_Websites.png)

**Figura 5: Siti Web di Azure supporta siti Web statici, applicazioni
Web note e applicazioni Web personalizzate create con diverse tecnologie.**

Siti Web di Azure si basa su Servizi cloud di Azure per creare una soluzione PaaS ottimizzata per l'esecuzione di applicazioni Web. Come illustrato nella figura, Siti Web viene eseguito in un set di singole macchine virtuali che potrebbe contenere più siti Web creati da più utenti, oltre a VM standard appartenenti a un singolo utente. Le macchine virtuali fanno parte di un pool di risorse gestite da Siti Web di Azure e ciò consente di offrire affidabilità elevata e tolleranza di errore. Le procedure iniziali sono molto semplici. Grazie a Siti Web di Azure, gli utenti possono scegliere tra una vasta gamma di applicazioni, framework e modelli e creare un sito Web in pochi secondi. Gli utenti possono utilizzare i propri strumenti di sviluppo preferiti (WebMatrix, Visual Studio, qualsiasi altro editor) e opzioni di controllo del codice sorgente per configurare l'integrazione continua ed eseguire lo sviluppo in team. Le applicazioni che si basano su un database MySQL possono utilizzare un servizio MySQL fornito da ClearDB, un partner Microsoft, per Azure. Siti Web consente agli sviluppatori di creare applicazioni Web di grandi dimensioni e scalabili. Questa tecnologia supporta la creazione di applicazioni tramite ASP.NET, PHP, Node.js e Python. Le applicazioni possono ad esempio utilizzare sessioni permanenti ed è possibile spostare le applicazioni Web esistenti in questa piattaforma cloud, senza modifiche. Le applicazioni basate su Siti Web possono utilizzare anche altri aspetti di Azure, ad esempio bus di servizio, database SQL e archiviazione BLOB. È inoltre possibile eseguire più copie di un'applicazione in macchine virtuali diverse. Siti Web eseguirà automaticamente il bilanciamento del carico delle richieste tra le diverse VM. Poiché le nuove istanze di Siti Web vengono create in macchine virtuali già esistenti, l'avvio di una nuova istanza dell'applicazione risulta molto veloce, significativamente più veloce rispetto all'attesa della creazione di una nuova VM. Come illustrato nella [figura 5](#Fig5), è possibile pubblicare codice e altro contenuto Web in Siti Web in molti modi, ad esempio tramite FTP, FTPS o la tecnologia WebDeploy Microsoft. Siti Web supporta inoltre la pubblicazione di codice da sistemi di controllo del codice sorgente, inclusi Git, GitHub, CodePlex, BitBucket, Dropbox, Mercurial, Team Foundation Server, e il sistema Team Foundation Service basato su cloud.

<h2><a  id="CloudServices" ></a>Servizi cloud</h2>


Macchine virtuali di Azure offre la tecnologia IaaS, mentre Siti Web di Azure fornisce hosting Web. La terza opzione disponibile, Servizi cloud, offre la tecnologia *PaaS (Platform as a Service)*. Tale tecnologia è stata progettata per il supporto di applicazioni scalabili, affidabili ed economicamente efficienti. Consente inoltre agli sviluppatori di non preoccuparsi della gestione della piattaforma utilizzata e di concentrarsi interamente sulle applicazioni, come illustrato nella
[figura 6](#Fig6).

<a name="Fig6"></a>![06_CloudServices2](./media/fundamentals-application-models/ExecModels_06_CloudServices2.png)

**Figura 6: Servizi cloud di Azure offre la tecnologia PaaS (Platform as
a Service).**

Analogamente alle altre opzioni di calcolo disponibili in Azure, Servizi cloud si basa sulle macchine virtuali. La tecnologia offre due opzioni leggermente diverse relative alle macchine virtuali, ovvero istanze di
*ruoli Web* che eseguono una variante di Windows Server con IIS e
istanze di *ruoli di lavoro* che eseguono la stessa variante di Windows Server senza IIS. Un'applicazione di Servizi cloud si basa su una combinazione di tali opzioni.

Ad esempio, un'applicazione semplice può utilizzare solo un ruolo Web, mentre un'applicazione più complessa può utilizzare un ruolo Web per la gestione delle richieste in arrivo dagli utenti, quindi passare il carico di lavoro generato da tali richieste a un ruolo di lavoro per l'elaborazione. Per tale comunicazione è possibile che venga utilizzato il bus di servizio o il servizio di accodamento di Azure.

Come illustrato nella figura, tutte le macchine virtuali in una singola applicazione vengono eseguite nello stesso servizio cloud, come descritto in precedenza per Macchine virtuali di Azure. Gli utenti accedono pertanto all'applicazione tramite un singolo indirizzo IP pubblico e il carico di lavoro generato dalle richieste viene bilanciato automaticamente nelle macchine virtuali dell'applicazione. Analogamente ai servizi cloud creati utilizzando Macchine virtuali di Azure, la piattaforma distribuirà le VM in un'applicazione di Servizi cloud, in modo da evitare un singolo punto di errore hardware.

Anche se le applicazioni vengono eseguite in macchine virtuali, è importante comprendere che Servizi cloud offre una tecnologia di tipo PaaS, non IaaS, come illustrato di seguito. Una tecnologia IaaS, quale Macchine virtuali di Azure, consente di creare e configurare innanzitutto l'ambiente in cui verrà eseguita l'applicazione, quindi di distribuire l'applicazione in tale ambiente. L'utente è responsabile della gestione di tale ambiente, distribuendo ad esempio nuove versioni con patch del sistema operativo in ogni macchina virtuale. La tecnologia PaaS, invece, consente di procedere come se l'ambiente esistesse già. È sufficiente è distribuire l'applicazione. La gestione della piattaforma in cui viene eseguita l'applicazione, inclusa la distribuzione di nuove versioni del sistema operativo, non è a carico dell'utente.

Se si utilizza Servizi cloud, non sarà necessario creare macchine virtuali. È sufficiente fornire un file di configurazione per indicare ad Azure il numero di ruoli necessari, ad esempio tre istanze di ruolo Web e due istanze di ruolo di lavoro, e tali istanze verranno create automaticamente dalla piattaforma. È comunque possibile scegliere la dimensione desiderata per le macchine virtuali. Le opzioni sono analoghe a quelle delle VM di Azure, ma non è necessario che l'utente le crei esplicitamente. Se l'applicazione deve gestire un carico maggiore, è possibile richiedere macchine virtuali aggiuntive e tali istanze verranno create automaticamente da Azure. Se il carico diminuisce, è possibile chiudere tali istanze e interromperne il pagamento.

Un'applicazione di Servizi cloud viene in genere resa disponibile agli utenti mediante un processo in due fasi. Uno sviluppatore carica innanzitutto l'applicazione nell'area di gestione temporanea della piattaforma. Quando lo sviluppatore è pronto per l'attivazione dell'applicazione, utilizza il portale di gestione di Azure per richiedere il passaggio in produzione. Il passaggio dalla gestione temporanea alla produzione può essere eseguito senza tempi di inattività, consentendo l'aggiornamento di un'applicazione in esecuzione a una nuova versione senza interferire con l'utilizzo da parte degli utenti.

Servizi cloud offre inoltre funzionalità di monitoraggio. Analogamente a Macchine virtuali di Azure, consente di rilevare un server fisico con errore e di riavviare in una nuova macchina le VM in esecuzione su tale server. Servizi cloud consente inoltre di rilevare errori di macchine virtuali e applicazioni, non solo errori hardware. A differenza di Macchine virtuali, dispone di un agente in ogni ruolo Web e di lavoro ed
è quindi in grado di avviare nuove macchine virtuali e nuove istanze di
applicazione in caso di errori.

L'utilizzo della tecnologia PaaS da parte di Servizi cloud comporta anche altre implicazioni, ad esempio il fatto che le applicazioni basate su tale tecnologia devono essere scritte in modo da essere eseguite correttamente in caso di errore di istanze dei ruoli Web o di lavoro. A tale scopo, è necessario che lo stato di un'applicazione di Servizi cloud non venga salvato nel file system delle rispettive macchine virtuali. A differenza delle VM create tramite Macchine virtuali di Azure, le operazioni di scrittura effettuate nelle VM di Servizi cloud non sono persistenti. La persistenza è offerta solo dai dischi dati di Macchine virtuali. È pertanto necessario che tutti gli stati di un'applicazione di Servizi cloud vengano scritti esplicitamente in database SQL, BLOB, tabelle o altre tecnologie di archiviazione esterna. Le applicazioni create in questo modo risulteranno più scalabili e più resistenti agli errori, obiettivi essenziali di Servizi cloud.

<h2><a  id="WhatShouldIUse" ></a>Scelta del modello da utilizzare</h2>


Tutti e tre i modelli di esecuzione di Azure consentono di creare applicazioni scalabili e affidabili nel cloud. La scelta del modello da utilizzare, pertanto, dipende dall'obiettivo che si desidera realizzare.

Macchine virtuali offre la soluzione più generica. Questa opzione risulta quindi ottimale se si desidera il massimo controllo possibile o se si necessita di macchine virtuali generiche, ad esempio per sviluppo e test. Macchine virtuali costituisce inoltre la scelta migliore per l'esecuzione nel cloud di applicazioni locali disponibili sul mercato, come illustrato nell'esempio precedente relativo a SharePoint. Poiché le macchine virtuali create mediante questa tecnologia hanno lo stesso aspetto delle VM locali, questa opzione è probabilmente ideale anche per il ripristino di emergenza. Il livello elevato di controllo offerto all'utente, tuttavia, implica la necessità di farsi carico anche di alcune attività amministrative.

Siti Web costituisce la scelta ottimale se si desidera creare un semplice sito Web, in particolare se il sito sarà basato su un'applicazione esistente quale Joomla, WordPress o Drupal. Siti Web risulta inoltre ideale per la creazione di un'applicazione Web che richiede un livello di amministrazione minimo o un'applicazione che deve essere abbastanza scalabile oppure per lo spostamento di un'app Web IIS nel cloud pubblico. Questa opzione consente una distribuzione rapida e permette di eseguire quasi immediatamente una nuova istanza dell'applicazione, mentre per la distribuzione di una nuova VM con Macchine virtuali o Servizi cloud potrebbero essere necessari alcuni minuti.

Servizi cloud, il modello di esecuzione iniziale offerto da Azure, è un approccio di tipo esplicitamente PaaS. La tecnologia PaaS e l'hosting Web non presentano differenze molto significative, ma Servizi cloud si differenzia da Siti Web per alcuni aspetti importanti, inclusi i seguenti:

* A differenza di Siti Web, Servizi cloud offre l'accesso amministrativo
  alle macchine virtuali dell'applicazione. Ciò consente di installare
  software arbitrario necessario all'applicazione, operazione non
  possibile in Siti Web.
* Poiché offre sia i ruoli Web che i ruoli di lavoro, Servizi cloud
  rappresenta una scelta migliore rispetto a Siti Web per applicazioni
  multilivello che necessitano di macchine virtuali separate per la
  rispettiva logica di business.
* In Servizi cloud sono disponibili ambienti distinti per la gestione
  temporanea e per la produzione e ciò consente aggiornamenti più
  semplici per l'applicazione rispetto a Siti Web.
* A differenza di Siti Web, è possibile utilizzare tecnologie di rete
  quali Rete virtuale di Azure e Azure Connect per agganciare computer
  locali ad applicazioni di Servizi cloud.
* Servizi cloud consente di utilizzare Desktop remoto per connettersi
  direttamente alle macchine virtuali di un'applicazione. Questa opzione
  non è disponibile in Siti Web.

Essendo basato sulla tecnologia PaaS, Servizi cloud presenta inoltre alcuni vantaggi rispetto a Macchine virtuali di Azure. Un numero maggiore di attività di gestione viene eseguito automaticamente, ad esempio la distribuzione di aggiornamenti del sistema operativo. È quindi probabile che i costi di funzionamento siano inferiori rispetto a quelli dell'approccio IaaS di Macchine virtuali di Azure.

Tutti e tre i modelli di esecuzione di Azure presentano vantaggi e svantaggi. Per effettuare una scelta ottimale è necessario esaminare tali vantaggi e svantaggi, definire obiettivi chiari e scegliere il modello più appropriato.

In alcuni casi un singolo modello di esecuzione non è sufficiente ed è pertanto possibile combinare diverse opzioni. Si supponga, ad esempio, di creare un'applicazione per cui si desidera usufruire dei vantaggi a livello di gestione offerti dai ruoli Web di Servizi cloud, ma si necessita anche di SQL Server standard per motivi di compatibilità o prestazioni. In questo caso, la soluzione migliore consiste nella combinazione di modelli di esecuzione, come illustrato nella [figura 7](#Fig7).

<a name="Fig7"></a>![07_CombineTechnologies](./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png)

**Figura 7: una singola applicazione può utilizzare più modelli di
esecuzione.**

Come illustrato nella figura, le macchine virtuali di Servizi cloud sono in esecuzione in un servizio cloud distinto rispetto alle VM di Macchine virtuali. I due modelli possono comunicare comunque in modo efficiente. La creazione di un'app in base a questo approccio costituisce a volte la soluzione migliore.

In Azure sono disponibili diversi modelli di esecuzione poiché le piattaforme cloud devono supportare molti scenari diversi. È necessario che chiunque desideri utilizzare in modo efficace questa piattaforma acquisisca una conoscenza chiara delle caratteristiche di ogni modello.



[1]: http://vmdepot.msopentech.com/
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/dn197896.aspx