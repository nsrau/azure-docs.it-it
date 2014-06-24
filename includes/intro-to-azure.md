# Introduzione ad Azure

Azure è la piattaforma applicativa di Microsoft per il cloud pubblico. È
possibile utilizzare questa piattaforma in molti modi diversi, ad
esempio per sviluppare un'applicazione Web che viene eseguita e
archivia i propri dati nei data center di Azure oppure solo per
archiviare i dati utilizzati da applicazioni in esecuzione in locale,
ossia all'esterno del cloud pubblico. Azure consente di creare macchine
virtuali per lo sviluppo e il test o di eseguire SharePoint e altre
applicazioni. Con Azure è inoltre possibile sviluppare applicazioni
altamente scalabili con moltissimi utenti. Poiché la piattaforma offre
un'ampia gamma di servizi, è possibile utilizzarla per tutte queste
attività e altro ancora.

Tuttavia, per svolgere queste attività è importante acquisire le nozioni
di base. Anche se non si conoscono i principi del cloud computing, in
questo articolo verranno illustrate le nozioni fondamentali su Azure.
L'obiettivo è quello di fornire una base per riuscire a comprendere e
utilizzare questa piattaforma cloud.
## Sommario

* [Componenti di Azure](#components)
* [Modelli di esecuzione/calcolo](#models)
* [Gestione dati](#data)
* [Rete](#networking)
* [Analisi business](#analytics)
* [Messaggistica](#messaging)
* [Memorizzazione nella cache](#caching)
* [Identità](#identity)
* [High-Performance Computing (HPC)](#HPC)
* [Servizi multimediali](#media)
* [E-commerce](#commerce)
* [SDK](#sdk)
* [Per iniziare](#start)

<h2><a  id="components" ></a>Componenti di Azure</h2>


Per comprendere ciò che Azure offre, è utile raggrupparne i servizi in
categorie distinte. Nella figura 1 è illustrato un modo per eseguire
questa operazione.

![Componenti di Azure](./media/intro-to-azure/IntroAzure1.png)   
 **Figura 1: Azure fornisce servizi per applicazioni accessibili tramite
Internet ed eseguiti nei data center di Azure.**

Per iniziare a utilizzare Azure, è necessario conoscere almeno le
nozioni di base per ogni componente. È inoltre possibile scaricare il
[poster Informazioni su Azure][1] per una rapida panoramica visiva. I
colori delle caselle mostrate nella figura 1 corrispondono al relativo
raggruppamento nel poster.

Nella parte rimanente di questo articolo vengono illustrate le
tecnologie mostrate nella figura, con una descrizione delle funzionalità
offerte da ognuna di esse e dei casi in cui utilizzarle.

<h2><a  id="models" ></a>Modelli di esecuzione/calcolo</h2>


Una delle attività di base svolte da una piattaforma cloud è
l'esecuzione di applicazioni. A questo scopo, in Azure sono disponibili
quattro opzioni: Macchine virtuali, Servizi cloud, Siti Web e Servizi
mobili. Ogni modello di esecuzione di Azure ha un ruolo specifico.

Macchine virtuali di Azure offre un ambiente di elaborazione su
richiesta di carattere generale. Servizi cloud è la scelta ideale per
creare applicazioni scalabili e affidabili con costi di amministrazione
ridotti. Siti Web di Azure offre un'ampia gamma di applicazioni,
framework e modelli che consentono di sviluppare rapidamente
applicazioni Web scalabili di grandi dimensioni e siti Web di presenza e
quindi di gestirne in modo efficiente lo sviluppo, le attività di test e
le operazioni. Allo stesso modo, Servizi mobili di Azure accelera lo
sviluppo di app per i dispositivi mobili, offrendo soluzioni chiavi in
mano per l'archiviazione dei dati nel cloud, l'autenticazione degli
utenti e l'invio di notifiche push.

È possibile utilizzare queste tecnologie separatamente o combinarle
secondo necessità per creare la base appropriata per l'applicazione.
L'approccio scelto dipende dai problemi che si sta tentando di
risolvere.
### Macchine virtuali

La possibilità di creare una macchina virtuale su richiesta, sia da
un'immagine standard che da una fornita dall'utente, può risultare
estremamente utile. Se si aggiunge la possibilità di pagare la macchina
virtuale al minuto e solo quando è in esecuzione, questa opzione risulta
ancora più interessante. Questo approccio, comunemente noto come IaaS
(Infrastructure as a Service), è quanto fornito da Macchine virtuali di
Azure.

Per creare una macchina virtuale, è necessario specificare il disco
rigido virtuale (VHD) da utilizzare e le dimensioni della macchina
virtuale. Si paga quindi il servizio in base al tempo in cui la macchina
virtuale è in esecuzione. Macchine virtuali di Azure offre una raccolta
di dischi rigidi virtuali, tra cui le opzioni fornite da Microsoft, ad
esempio Windows Server 2008 R2, Windows Server 2012 e Windows Server
2008 R2 con SQL Server, unitamente alle immagini Linux fornite dai
partner Microsoft. È inoltre possibile creare macchine virtuali dai
propri dischi rigidi virtuali e aggiungerle alla raccolta.

Indipendentemente dalla provenienza dell'immagine, è possibile
archiviare in modo permanente qualsiasi modifica apportata mentre una
macchina virtuale è in esecuzione. Alla creazione successiva di una
macchina virtuale dallo stesso disco rigido virtuale, le attività
riprendono dal punto in cui sono state interrotte. È inoltre possibile
copiare il disco rigido virtuale modificato all'esterno di Azure e
quindi eseguirlo localmente.

Le macchine virtuali di Azure possono essere utilizzate in vari modi, ad
esempio per creare una piattaforma di sviluppo e test a costi contenuti
da arrestare dopo l'utilizzo oppure per creare ed eseguire applicazioni
basate su qualsiasi linguaggio o libreria. Per queste applicazioni è
possibile scegliere qualsiasi opzione di gestione dati offerta da Azure
e decidere se utilizzare SQL Server o un altro DBMS in esecuzione in una
o più macchine virtuali. Le macchine virtuali di Azure possono inoltre
essere utilizzate come estensione del data center locale per
l'esecuzione di SharePoint o altre applicazioni. Per supportare questa
opzione, è possibile creare domini di Windows nel cloud tramite
l'esecuzione di Active Directory nelle macchine virtuali di Azure. Si
tratta di un approccio generale al cloud computing che consente
risolvere esigenze di natura diversa. Sta all'utente scegliere come
preferisce procedere.
### Siti Web

In genere, il cloud viene utilizzato per eseguire siti Web e
applicazioni Web. Macchine virtuali di Azure fornisce questo tipo di
servizio, ma lascia all'utente la responsabilità di amministrare una o
più macchine virtuali. Se invece si preferisce evitare di occuparsi
delle attività di amministrazione del sito Web,

la risposta a questa esigenza è offerta da Siti Web di Azure. Questo
modello di esecuzione rende disponibile un ambiente Web gestito tramite
il portale di gestione di Azure e con diverse API. È possibile spostare
un sito Web esistente in Siti Web di Azure lasciandolo invariato o
crearne uno nuovo direttamente nel cloud. Non appena il sito Web è in
esecuzione, è possibile aggiungere o rimuovere istanze in modo dinamico,
utilizzando Siti Web di Azure per bilanciare il carico delle richieste.
Siti Web di Azure offre sia un'opzione condivisa, in base alla quale il
sito viene eseguito in una macchina virtuale insieme ad altri siti, e
un'opzione standard, che consente l'esecuzione di un sito in una
specifica macchina virtuale. L'opzione standard consente inoltre di
aumentare le dimensioni (potenza di elaborazione) delle istanze, se
necessario.

Siti Web di Azure è ideale per aziende, sviluppatori e agenzie di
progettazione Web. Per le aziende, si tratta di una soluzione di facile
gestione, scalabile che garantisce sicurezza e disponibilità elevate per
l'esecuzione di siti Web di presenza. Per lo sviluppo, supporta .NET,
PHP, Node.js e Python unitamente a database SQL e MySQL (di ClearDB, un
partner Microsoft) per l'archiviazione relazionale. Fornisce inoltre il
supporto incorporato per numerose applicazioni ampiamente diffuse, ad
esempio WordPress, Joomla e Drupal. Si tratta di una piattaforma a basso
costo, scalabile ed estremamente utile per la creazione di siti e
applicazioni Web nel cloud pubblico.
### Servizi cloud

Si supponga di voler sviluppare un'applicazione cloud in grado di
supportare molti utenti contemporaneamente, che richieda poca
amministrazione e che rimanga sempre attiva. Potrebbe essere il caso di
un fornitore di software che ha deciso di utilizzare una piattaforma
SaaS (Software as a Service) per sviluppare una versione delle proprie
applicazioni nel cloud. Oppure di una start-up che si propone di creare
un'applicazione per il grande pubblico per la quale è prevista una
crescita rapida. Se si sceglie di utilizzare Azure, sarà necessario
individuare il modello di esecuzione più adatto.

Siti Web di Azure consente la creazione di questo tipo di applicazione
Web, ma con alcuni vincoli, come ad esempio il fatto che non è
disponibile un accesso amministrativo e pertanto non è possibile
installare software arbitrario. Macchine virtuali di Azure offre molta
più flessibilità, incluso l'accesso amministrativo, ed è possibile
utilizzare il servizio per creare un'applicazione con scalabilità
elevata, ma occorre gestire in autonomia molti aspetti legati
all'affidabilità e all'amministrazione. Sarebbe quindi preferibile
un'opzione che offra il controllo necessario e che gestisca al contempo
anche le attività necessarie ai fini dell'affidabilità e
dell'amministrazione.

Questo è ciò che offre Servizi cloud di Azure. Questa tecnologia è
appositamente progettata per supportare applicazioni scalabili,
affidabili e con carico amministrativo ridotto ed è un esempio di ciò
che viene comunemente denominato PaaS (Platform as a Service). Per
utilizzare il servizio, occorre creare un'applicazione con la
tecnologia che si preferisce, ad esempio C#, Java, PHP, Python, Node.js
o altro. Il codice viene quindi eseguito nelle macchine virtuali
(denominate istanze) che eseguono una versione di Windows Server.

Queste macchine virtuali sono tuttavia distinte da quelle che si creano
con Macchine virtuali di Azure. Innanzitutto, sono gestite da Azure, che
effettua operazioni quali l'installazione di patch del sistema
operativo e l'implementazione di nuove immagini con patch applicate. Di
conseguenza l'applicazione non deve gestire lo stato nelle istanze del
ruolo Web o di lavoro. Tali attività devono essere gestite tramite una
delle opzioni di gestione dati di Azure descritte nella sezione
successiva. Azure esegue inoltre il monitoraggio delle macchine
virtuali, riavviando quelle che presentano errori.

Quando si crea un'istanza, sono disponibili due ruoli, entrambi basati
su Windows Server. La differenza principale tra i due ruoli è che
un'istanza di un ruolo Web esegue IIS, mentre un'istanza di un ruolo
di lavoro no. Entrambi i ruoli vengono tuttavia gestiti nello stesso
modo e spesso le applicazioni li usano ambedue. Un'istanza del ruolo
Web, ad esempio, potrebbe accettare le richieste degli utenti e quindi
passarle a un'istanza del ruolo di lavoro per l'elaborazione. Per
aumentare o ridurre le risorse di un'applicazione, è possibile
richiedere ad Azure di creare più istanze di entrambi i ruoli o di
arrestare le istanze esistenti. Come per il servizio Macchine virtuali
di Azure, viene addebitato solo il tempo in cui è in esecuzione ogni
istanza del ruolo Web o di lavoro.
### Servizi mobili

Se si crea un'app per dispositivi mobili, Servizi mobili di Azure
accelera lo sviluppo offrendo una soluzione chiavi in mano per
archiviare i dati nel cloud, autenticare gli utenti e inviare notifiche
push.  
 Le librerie client native per Android, iOS, HTML/JavaScript, Windows
Phone e Windows Store semplificano la creazione di app per tutte le
principali piattaforme mobili. Un'API REST flessibile e aperta consente
inoltre di utilizzare i dati di Servizi mobili e la funzionalità di
autenticazione con le app su qualsiasi piattaforma. Un singolo servizio
mobile può supportare più app client in modo da offrire un'esperienza
utente coerente su tutti i dispositivi.

Servizi mobili permette di eseguire il provisioning e la gestione dei
dati archiviati in un database SQL, eseguire l'autenticazione degli
utenti tramite provider di identità noti quali Facebook, Twitter,
account Microsoft o account Google e utilizzare i servizi di notifica
per inviare notifiche push all'app. Grazie al codice lato server è
possibile utilizzare ulteriori opzioni di archiviazione dei dati, ad
esempio l'archiviazione BLOB o MongoDB, utilizzare componenti
aggiuntivi disponibili nel negozio di Azure, come SendGrid o Pusher,
oppure utilizzare altri servizi di Azure come ad esempio il bus di
servizio e i ruoli di lavoro o persino connettersi a sistemi locali. Il
servizio garantisce la scalabilità, ad esempio se l'app diventa molto
popolare, e supporta le funzionalità di monitoraggio e registrazione.

Sebbene sia certamente possibile creare il back-end per un'app per
dispositivi mobili utilizzando Macchine virtuali, Servizi cloud o Siti
Web, scegliendo Servizi mobili è possibile dedicare molto meno tempo
alla scrittura dei componenti del servizio sottostanti.

<h2><a  id="data" ></a>Gestione dati</h2>


Alle applicazioni servono i dati e ad applicazioni diverse servono tipi
di dati diversi. Per questo motivo, in Azure sono disponibili diversi
modi per archiviare e gestire i dati.

Uno di questi è già stato citato in precedenza, ovvero la possibilità di
eseguire SQL Server o un altro DBMS in una macchina virtuale creata con
Macchine virtuali di Azure. È importante notare che questa opzione non è
limitata ai sistemi relazionali. È infatti possibile eseguire tecnologie
NoSQL, come ad esempio MongoDB e Cassandra. L'esecuzione di un sistema
di database personale è semplice, in quanto rientra nelle attività
normalmente eseguite in un data center, ma richiede anche la gestione
dell'amministrazione di uno specifico DBMS. Per facilitare le cose,
Azure offre tre opzioni di gestione dati, per lo più gestite
automaticamente. Nella figura 2 sono illustrate le opzioni disponibili.

![Gestione dati di Azure](./media/intro-to-azure/IntroAzure3.png)   
 **Figura 2: per la gestione dati, Azure fornisce funzionalità di
archiviazione relazionale, tabelle NoSQL scalabili e archiviazione di
dati binari non strutturati.**

Ognuna di queste opzioni soddisfa un'esigenza diversa: archiviazione
relazionale, accesso rapido a quantità potenzialmente elevate di
semplici dati tipizzati e archiviazione di dati binari non strutturati.
In tutti e tre i casi, i dati vengono automaticamente replicati tra tre
computer diversi in un data center di Azure per garantire la
disponibilità elevata. È inoltre importante sottolineare che è possibile
accedere a tutte e tre le opzioni dalle applicazioni di Azure o da
applicazioni in esecuzione altrove, ad esempio in un data center locale,
in un computer portatile o in un telefono. Indipendentemente da come
vengono applicati, il pagamento dei servizi di gestione dati di Azure è
basato sull'utilizzo, incluso l'addebito di un gigabyte al mese per i
dati archiviati.
### Database SQL

Per l'archiviazione relazionale, Azure offre il servizio Database SQL.
Precedentemente denominato SQL Azure, Database SQL offre tutte le
funzionalità principali di un sistema di gestione di database
relazionale, ad esempio transazioni atomiche, accesso simultaneo ai dati
da parte di più utenti con integrità dei dati, query SQL ANSI e un
modello di programmazione noto. Come per SQL Server, è possibile
accedere a Database SQL mediante Entity Framework, ADO.NET, JDBC e altre
tecnologie di accesso ai dati note. Database SQL supporta inoltre la
maggior parte del linguaggio T-SQL, oltre agli strumenti di SQL Server,
ad esempio SQL Server Management Studio. Per chiunque abbia familiarità
con SQL Server (o altro database relazionale), l'utilizzo di SQL
Database risulta estremamente semplice.

Tuttavia, Database SQL non rappresenta solo un DBMS nel cloud: si tratta
infatti di un vero e proprio servizio PaaS che consente di mantenere il
controllo sui dati e sugli utenti che possono accedervi, ma gestisce
tutto le attività amministrative, ad esempio la gestione
dell'infrastruttura hardware e l'aggiornamento automatico del software
di database e del sistema operativo. Database SQL offre inoltre
un'opzione di federazione per la distribuzione dei dati in più server.
Questa opzione è utile per le applicazioni che utilizzano grandi
quantità di dati o che necessitano di distribuire le richieste di
accesso ai dati tra più server per garantire prestazioni migliori.

Se si desidera creare un'applicazione di Azure (con uno qualsiasi dei
tre metodi di esecuzione) che richiede una soluzione di archiviazione
relazionale, Database SQL rappresenta una scelta ottimale. Anche le
applicazioni in esecuzione all'esterno del cloud possono utilizzare
questo servizio, pertanto gli scenari possibili sono davvero numerosi.
Ad esempio, è possibile accedere ai dati archiviati in SQL Database da
diversi sistemi client, inclusi desktop, portatili, tablet e telefoni
cellulari. Poiché offre disponibilità elevata incorporata tramite
replica, Database SQL consente inoltre di ridurre i tempi di inattività.
### Tabelle

Si supponga di voler creare un'applicazione di Azure che necessita di
accesso rapido a grandi quantità di dati tipizzati ma che non debba
eseguire query SQL complesse su tali dati. Ad esempio, un'applicazione
consumer che deve archiviare le informazioni relative al profilo del
cliente per ogni utente. Questa app diventerà molto popolare, pertanto
occorrerà prevedere la possibilità di archiviare grandi quantità di
dati, ma non altre operazioni, da recuperare in modo semplice. Questo è
esattamente lo scenario in cui il servizio Tabelle di Azure può
risultare utile.

Anche se il nome può confondere, questa tecnologia non fornisce servizi
di archiviazione relazionale. In realtà, si tratta di un esempio di
approccio NoSQL denominato archivio chiave-valore. Il servizio Tabelle
di Azure consente a un'applicazione di archiviare proprietà di vari
tipi, ad esempio stringhe, numeri interi e date. Un'applicazione può
quindi recuperare un gruppo di proprietà fornendo una chiave univoca per
tale gruppo. Anche se le operazioni complesse quali i join non sono
supportate, le tabelle offrono accesso rapido ai dati tipizzati. Sono
inoltre molto scalabili e una singola tabella può contenere fino a un
terabyte di dati. Oltre a essere semplici, sono anche in genere meno
costose da utilizzare rispetto all'archivio relazionale di database
SQL.
### BLOB

La terza opzione per la gestione dati, ovvero il servizio BLOB di Azure,
è progettata per l'archiviazione di dati binari non strutturati. Come
il servizio tabelle, il servizio BLOB è una soluzione economica e un
singolo BLOB può contenere fino a un terabyte di dati. Un'applicazione
per l'archiviazione di video, di dati di backup o di altre informazioni
binarie può utilizzare i BLOB come soluzione di archiviazione semplice e
a un costo contenuto. Le applicazioni di Azure possono inoltre
utilizzare le unità Azure, tramite le quali i BLOB forniscono un
archivio permanente per un file system Windows montato in un'istanza di
Azure. L'applicazione vede i file di Windows ordinari, ma il contenuto
è effettivamente archiviato in un BLOB.

<h2><a  id="networking" ></a>Rete</h2>


Azure è attualmente eseguito in numerosi data center negli Stati Uniti,
in Europa e in Asia. Durante l'esecuzione di un'applicazione o
l'archiviazione dei dati, è possibile selezionare uno o più data center
da utilizzare. È inoltre possibile connettersi a questi data center in
vari modi, ad esempio

* utilizzando Rete virtuale di Azure per connettere la rete locale in
  uso a un set definito di macchine virtuali di Azure.

* Se l'applicazione Azure è in esecuzione in più data center, è
  possibile utilizzare Gestione traffico di Azure per indirizzare le
  richieste degli utenti in modo intelligente tra le istanze
  dell'applicazione.

Nella figura 3 sono illustrate queste opzioni.

![Rete di Azure](./media/intro-to-azure/IntroAzure4.png)   
 **Figura 3: Azure consente la creazione di una VPN cloud e la
distribuzione intelligente delle richieste degli utenti tra vari data
center.**
### Rete virtuale

È possibile utilizzare un cloud pubblico considerandolo come
un'estensione del proprio data center. Grazie alla possibilità di
creare macchine virtuali su richiesta e quindi di rimuoverle
(interrompendo il pagamento) quando non sono più necessarie, è possibile
disporre di ulteriore potenza di elaborazione solo quando serve. Dal
momento che Macchine virtuali di Azure consente di creare macchine
virtuali che eseguono SharePoint, Active Directory e altri noti software
locali, questo approccio può funzionare con le applicazioni di cui già
si dispone.

Affinché questa opzione risulti veramente utile, tuttavia, gli utenti
dovrebbero essere in grado di trattare le applicazioni come se fossero
in esecuzione nel proprio data center. E questo è il servizio offerto da
Rete virtuale di Azure. Utilizzando un dispositivo gateway VPN, un
amministratore può configurare una rete privata virtuale (VPN) tra la
rete locale e un gruppo definito di macchine virtuali eseguite in Azure.
Assegnando indirizzi IPv4 personalizzati alle macchine virtuali del
cloud, risulteranno come appartenenti alla rete in uso. Gli utenti
dell'organizzazione potranno accedere alle applicazioni contenute in
tali macchine virtuali come se fossero eseguite localmente.
### Gestione traffico

Un'applicazione Azure con utenti in una sola parte del mondo può essere
eseguita in un solo data center di Azure. Un'applicazione con utenti
sparsi in tutto il mondo, verrà probabilmente eseguita in più data
center, forse anche in tutti. In questo secondo caso, occorre affrontare
il problema relativo all'assegnazione intelligente degli utenti alle
istanze dell'applicazione. Nella maggior parte dei casi, si preferirà
che ogni utente acceda al data center più vicino per garantire i tempi
di risposta migliori. Potrebbe tuttavia accadere che l'istanza
dell'applicazione sia sovraccarica o non disponibile. In questo caso,
sarebbe opportuno indirizzare le richieste automaticamente a un altro
data center. Questo è ciò che offre Gestione traffico di Azure.

Il proprietario di un'applicazione definisce le regole che consentono
di specificare il modo in cui le richieste dagli utenti devono essere
indirizzate ai data center, quindi si affida a Gestione traffico per
l'applicazione di tali regole. Ad esempio, è possibile decidere che gli
utenti vengano normalmente indirizzati al data center di Azure più
vicino, ma che vengano reindirizzati a un altro quando il tempo di
risposta del data center predefinito è superiore a quello di altri data
center. Per le applicazioni distribuite a livello locale con molti
utenti, un servizio incorporato per la gestione dei problemi di traffico
può risultare molto utile.

<h2><a  id="analytics" ></a>Analisi business</h2>


L'analisi dei dati è un aspetto fondamentale del modo in cui le aziende
utilizzano l'IT. Una piattaforma cloud offre un pool di risorse su
richiesta con pagamento a consumo, ideale per questo tipo di utilizzo
delle risorse informatiche. Anche per l'analisi business, Azure offre
due opzioni. Nella figura 4 sono illustrate le scelte disponibili.

![Analisi di Azure](./media/intro-to-azure/IntroAzure5.png)   
 **Figura 4: per l'analisi business, Azure offre un servizio di
creazione report e il supporto per Big Data.**

Poiché l'analisi dei dati può assumere forme diverse, anche le opzioni
offerte presentano differenze significative. È consigliabile esaminarle
separatamente.
### Creazione di report con database SQL

Uno dei modi più comuni per utilizzare i dati archiviati prevede la
creazione di report in base a tali dati.

L'esecuzione di SQL Server Reporting Services (SSRS) in Macchine
virtuali di Azure consente di creare funzionalità di creazione report di
facile accesso nell'applicazione Azure. È possibile creare report con
tabelle, grafici, mappe, misuratori e altro ancora in vari formati, ad
esempio HTML, XML, PDF ed Excel.

È inoltre possibile eseguire analisi con i dati di database SQL
utilizzando strumenti di business intelligence locali, incluso SSRS. Per
un client, database SQL non è diverso da SQL Server, pertanto le stesse
tecnologie funzionano con entrambi.
### HDInsight (Hadoop)

Per molti anni, la maggior parte dell'analisi dei dati è stata eseguita
su dati relazionali archiviati in un data warehouse creato con un DBMS
relazionale. Questo tipo di analisi business è ancora importante e lo
sarà anche per molti anni a venire. È tuttavia possibile che la quantità
di dati che si desidera analizzare sia così elevata da non poter essere
gestita dai database relazionali. I dati potrebbero inoltre non essere
relazionali, ad esempio i log dei server in un data center oppure i dati
cronologici relativi agli eventi di un sensore. In questi casi, si
verifica un problema relativo ai Big Data. Occorre procedere con un
approccio diverso.

La principale tecnologia utilizzata oggi per l'analisi dei Big Data è
Hadoop. Progetto open source Apache, questa tecnologia archivia i dati
tramite Hadoop Distributed File System (HDFS) e quindi consente agli
sviluppatori di creare processi MapReduce per analizzarli. HDFS
distribuisce i dati tra più server, quindi esegue blocchi del processo
MapReduce in ognuno di essi per consentire l'elaborazione di Big Data
in parallelo.

HDInsight è il nome del servizio basato su Hadoop Apache di Azure. Come
mostrato nella figura 4, HDInsight consente a HDFS di archiviare i dati
nel cluster e di distribuirli tra più macchine virtuali. Distribuisce
inoltre la logica di un processo MapReduce tra queste macchine virtuali.
Come già accade con Hadoop in locale, i dati vengono elaborati
localmente, ovvero la logica e i dati su cui questa funziona si trovano
nella stessa macchina virtuale, e in parallelo per garantire prestazioni
migliori. HDInsight consente inoltre di archiviare i dati nell'insieme
di credenziali di Archiviazione di Azure che utilizza i BLOB.
L'utilizzo dell'insieme di credenziali di Archiviazione di Azure
genera risparmi significativi in quanto consente di eliminare il cluster
HDInsight quando non è in uso pur continuando a mantenere i dati nel
cloud.

HDinsight supporta anche altri componenti dell'ecosistema Hadoop, ad
esempio Hive e Pig. Microsoft ha inoltre creato componenti che
semplificano l'utilizzo dei dati creati da HDInsight mediante strumenti
di business intelligence tradizionali, quali l'adattatore HiveODBC e il
visualizzatore per i dati che funzionano con Excel.

<h2><a  id="messaging" ></a>Messaggistica</h2>


Indipendentemente dall'attività svolta, spesso il codice deve
interagire con altro codice. In alcune situazioni, è sufficiente un
messaggio in coda di base. In altri casi, sono necessarie interazioni
più complesse. In Azure sono disponibili diversi modi per risolvere i
problemi di questo tipo. Nella figura 5 sono illustrate le scelte
disponibili.

![Messaggistica di Azure](./media/intro-to-azure/IntroAzure6.png)   
 **Figura 5: per la connessione di applicazioni, in Azure sono
disponibili le code, il modello pubblicazione/sottoscrizione e le
connessioni sincrone tramite il cloud.**
### Code

L'accodamento si basa su un concetto semplice: un'applicazione colloca
un messaggio in una coda e il messaggio viene quindi letto da un'altra
applicazione. Se l'applicazione richiede solo questo semplice servizio,
il servizio Code di Azure risulta la scelta ottimale.

Uno degli utilizzi comuni delle code è consentire a un'istanza del
ruolo Web di comunicare con un'istanza del ruolo di lavoro all'interno
della stessa applicazione di Servizi cloud. Si supponga di creare
un'applicazione Azure per la condivisione video. L'applicazione è
composta da codice PHP in esecuzione in un ruolo Web che consente agli
utenti di caricare e guardare video, insieme a un ruolo di lavoro
implementato in C# che converte il video caricato in vari formati.
Quando un'istanza del ruolo Web riceve un nuovo video da un utente, può
archiviare il video in un BLOB, quindi inviare un messaggio a un ruolo
di lavoro tramite una coda per comunicargli la posizione del nuovo
video. Un'istanza del ruolo di lavoro, non è importante quale, leggerà
quindi il messaggio dalla coda ed eseguirà le conversioni video
necessarie in background. Un'applicazione strutturata in questo modo
consente l'elaborazione asincrona e favorisce la scalabilità
dell'applicazione in quanto il numero di istanze del ruolo Web e del
ruolo di lavoro può variare in modo indipendente.
### Bus di servizio

Le applicazioni, indipendentemente dal fatto che siano eseguite nel
cloud, in un data center, in un dispositivo mobile o altrove, devono
interagire. Il bus di servizio di Azure consente alle applicazioni
eseguite praticamente ovunque di scambiarsi i dati.

Come illustrato nella figura 5, il bus di servizio fornisce un servizio
di accodamento. Tuttavia, questo servizio non è identico a quello delle
code appena descritto. Diversamente da Code di Azure, ad esempio, bus di
servizio fornisce sia il meccanismo di accodamento (uno a uno) sia
quello di pubblicazione-sottoscrizione. Il meccanismo di
pubblicazione-sottoscrizione consente a un'applicazione di inviare
messaggi a un argomento, mentre altre applicazioni possono creare
sottoscrizioni a tale argomento. Questo genera una comunicazione uno a
molti tra un set di applicazioni che consente allo stesso messaggio di
essere letto da più destinatari. L'accodamento non è l'unica opzione:
Il bus di servizio consente inoltre la comunicazione diretta tramite il
relativo servizio di inoltro, offrendo così un modo sicuro di interagire
attraverso i firewall. Inoltro del bus di servizio consente alle
applicazioni di comunicare scambiando messaggi tramite un endpoint
ospitato nel cloud anziché localmente.

Le applicazioni che comunicano tramite il bus di servizio possono essere
applicazioni Azure o software in esecuzione in altre piattaforme cloud.
Possono anche essere applicazioni in esecuzione all'esterno del cloud.
Un esempio potrebbe essere una compagnia aerea che implementa servizi di
prenotazione in computer all'interno del proprio data center. La
compagnia aerea deve esporre tali servizi a molti clienti, ad esempio i
banchi del check-in negli aeroporti, i terminali delle agenzie viaggio e
perfino i telefoni cellulari degli utenti. A questo scopo può utilizzare
il bus di servizio, creando interazioni accoppiate liberamente tra le
diverse applicazioni.

<h2><a  id="caching" ></a>Memorizzazione nella cache</h2>


Le applicazioni tendono ad accedere ripetutamente sempre agli stessi
dati. Per migliorare le prestazioni, è possibile mantenere una copia di
quei dati più vicino all'applicazione, riducendo così il tempo
necessario per recuperarli. In Azure sono disponibili due servizi
diversi per eseguire questa operazione: la cache in memoria dei dati
utilizzati dalle applicazioni Azure e una rete per la distribuzione di
contenuti (CDN) che memorizza nella cache i dati BLOB in un disco più
vicino ai relativi utenti. Nella figura 6 sono illustrati entrambi.

![Cache di Azure](./media/intro-to-azure/IntroAzure7.png)   
 **Figura 6: un'applicazione Azure può memorizzare dati nella cache in
memoria e le copie di un BLOB possono essere memorizzate nella cache in
siti in diverse parti del mondo.**
### Memorizzazione nella cache

L'accesso ai dati archiviati nei servizi di gestione dati di Azure,
ovvero database SQL, tabelle o BLOB, è molto veloce. Ma l'accesso ai
dati archiviati in memoria è ancora più rapido. Di conseguenza,
mantenere una copia in memoria dei dati utilizzati di frequente consente
di migliorare le prestazioni delle applicazioni. A questo scopo, è
possibile utilizzare il servizio Cache di Azure in memoria.

Un'applicazione Servizi cloud può archiviare i dati in questa cache e
quindi recuperarli direttamente senza dover accedere all'archivio
permanente. Come illustrato nella figura 6, la cache può essere
mantenuta all'interno delle macchine virtuali dell'applicazione o
essere fornita dalle macchine virtuali dedicate solo alla memorizzazione
nella cache. In entrambi i casi, la cache può essere distribuita con i
dati in essa contenuti distribuiti tra più macchine virtuali in un data
center di Azure.

Questo tipo di memorizzazione nella cache, ad esempio, potrebbe
risultare utile a un'applicazione che legge ripetutamente un catalogo
di prodotti, poiché i dati che le servono saranno disponibili più
rapidamente. La tecnologia supporta inoltre il blocco che può essere
utilizzato con i dati in lettura/scrittura e con i dati di sola lettura.
Le applicazioni ASP.NET possono utilizzare il servizio per archiviare i
dati di sessione con una semplice modifica della configurazione.
### Rete CDN

Si supponga di dover archiviare dati BLOB ai quali accederanno utenti di
tutto il mondo. Potrebbe trattarsi di un video dell'ultima Coppa del
Mondo, di aggiornamenti di driver o di un e-book molto popolare.
Archiviare una copia dei dati in più data center di Azure potrebbe
essere utile, ma in presenza di un numero elevato di utenti potrebbe non
essere sufficiente. Per usufruire di prestazioni ancora migliori, è
possibile utilizzare la rete CDN di Azure.

Nella rete CDN sono presenti decine di siti in tutto il mondo, in ognuno
dei quali è possibile archiviare copie dei BLOB di Azure. La prima volta
che un utente che si trova in una determinata parte del mondo accede a
un BLOB specifico, le informazioni in esso contenute vengono copiate da
una data center di Azure nell'archivio della rete CDN in quell'area
geografica. Successivamente, tutti gli accessi da quella parte del mondo
utilizzeranno la copia del BLOB memorizzata nella cache nella rete CDN e
non verranno reindirizzati al data center di Azure più vicino. Ne
risulta un accesso più rapido ai dati utilizzati di frequente dagli
utenti in qualsiasi parte del mondo.

<h2><a  id="identity" ></a>Identità</h2>


L'utilizzo delle identità è importante per la maggior parte delle
applicazioni. Le informazioni relative all'identità di un utente
consentono a un'applicazione di decidere la modalità di interazione con
quell'utente. Azure Active Directory è il servizio Microsoft progettato
per questo scopo.

Come la maggior parte dei servizi directory, Azure Active Directory
archivia le informazioni relative agli utenti e alle organizzazioni a
cui appartengono. Consente agli utenti di eseguire l'accesso e quindi
fornisce loro i token che dovranno presentare alle applicazioni per
dimostrare la propria identità. Consente inoltre di sincronizzare le
informazioni relative agli utenti con Windows Server Active Directory in
esecuzione nella rete locale. Sebbene i meccanismi e i formati di dati
utilizzati da Azure Active Directory non siano identici a quelli
utilizzati in Windows Server Active Directory, le funzioni che eseguono
sono molto simili.

È importante ricordare che Azure Active Directory è progettato
principalmente per l'utilizzo da parte di applicazioni cloud. Può ad
esempio essere utilizzato da applicazioni in esecuzione in Azure o in
altre piattaforme cloud. Viene anche utilizzato dalle applicazioni di
Microsoft, ad esempio le applicazioni di Office 365. Se si desidera
estendere il data center al cloud mediante Macchine virtuali di Azure e
Rete virtuale di Azure, Azure Active Directory non è la scelta adatta.
Sarà più opportuno eseguire Windows Server Active Directory nelle
macchine virtuali cloud, come descritto in precedenza.

Per consentire alle informazioni in esso contenute, Azure Active
Directory fornisce un'API RESTful denominata Azure Active Directory
Graph. Questa API consente alle applicazioni in esecuzione in qualsiasi
piattaforma di accedere agli oggetti directory e alle relazioni tra di
essi. Un'applicazione autorizzata potrebbe ad esempio utilizzare questa
API per acquisire informazioni su un utente, sui gruppi ai quali
appartiene e altri dettagli. Le applicazioni possono inoltre vedere le
relazioni tra gli utenti, ovvero il relativo grafico sociale,
consentendo loro di lavorare in modo più intelligente con le connessioni
tra persone.

Un'altra funzionalità del servizio, Controllo di accesso di Azure
Active Directory, consente alle applicazioni di accettare le
informazioni di identità da Facebook, Google, Windows Live ID e i più
diffusi provider di identità con maggiore semplicità. Anziché richiedere
all'applicazione di accettare i vari formati di dati e protocolli
utilizzati da ognuno di questi provider, Controllo di accesso li
converte in un unico formato comune. Consente inoltre a un'applicazione
di accettare le informazioni di accesso da uno o più domini Active
Directory. Il fornitore di un'applicazione SaaS potrebbe ad esempio
utilizzare Controllo di accesso di Azure Active Directory per concedere
agli utenti dei propri clienti l'accesso Single Sign-On
all'applicazione.

I servizi directory rappresentano un rinforzo essenziale delle risorse
di elaborazione locali. Non sorprenderà quindi che rivestano grande
importanza anche nel cloud.

<h2><a  id="media" ></a>Servizi multimediali</h2>


I video costituiscono gran parte del traffico Internet attuale e questa
percentuale è destinata ad aumentare in futuro. Tuttavia, fornire video
sul Web non è così semplice. Esistono numerose variabili, ad esempio
l'algoritmo di codifica e la risoluzione dello schermo dell'utente. I
video tendono inoltre a registrare picchi nella domanda, ad esempio il
sabato sera quando molte persone decidono di guardare un film online.

È probabile che i video saranno inclusi in molte delle nuove
applicazioni create. Poiché i problemi riscontrati da tali applicazioni
saranno simili, è inutile che questi vengano risolti singolarmente per
ognuna di esse. È quindi consigliabile creare una piattaforma in grado
di fornire soluzioni comuni per molte applicazioni. Inoltre, la
creazione di questa piattaforma nel cloud presenta alcuni vantaggi. Può
essere disponibile per il pubblico con pagamento in base al consumo e
consente anche di gestire la variabilità nella domanda spesso
riscontrata dalle applicazioni video.

Servizi multimediali di Azure consente di risolvere questo problema.
Offre un set di componenti cloud per semplificare le attività di coloro
che creano ed eseguono applicazioni che utilizzano video e altri
elementi multimediali. Nella figura 7 è illustrata questa tecnologia.

![Servizi multimediali di Azure](./media/intro-to-azure/IntroAzure8.png)

 **Figura 7: Servizi multimediali è una piattaforma per le applicazioni
che forniscono video e altri elementi multimediali a client in tutto il
mondo.**

Come illustrato nella figura, Servizi multimediali fornisce un set di
componenti che utilizzano video e altri elementi multimediali. Include
ad esempio un componente di inserimento video che consente di caricare i
video in Servizi multimediali (archiviati nei BLOB di Azure), un
componente di codifica che supporta vari formati audio e video, un
componente di protezione del contenuto che offre funzionalità di Digital
Righes Management, un componente per l'inserimento di annunci in un
flusso video, componenti per lo streaming e altro ancora. I partner
Microsoft possono inoltre fornire componenti per la piattaforma che
verranno successivamente distribuiti e fatturati da Microsoft per conto
dei partner.

Le applicazioni che utilizzano questa piattaforma possono essere
eseguite in Azure o altrove. Ad esempio, un'applicazione desktop per
una società di produzione video potrebbe consentire agli utenti di
caricare video in Servizi multimediali e quindi elaborarli in vari modi.
In alternativa, un servizio di gestione del contenuto basato sul cloud
in esecuzione in Azure potrebbe utilizzare Servizi multimediali per
l'elaborazione e la distribuzione dei video. Indipendentemente dalla
posizione in cui viene eseguita e dalle operazioni che svolge, ogni
applicazione sceglie i componenti da utilizzare, accedendovi tramite
interfacce RESTful.

Per distribuire ciò che produce, un'applicazione può utilizzare la rete
CDN di Azure, un'altra rete CDN o inviare i bit direttamente agli
utenti. Indipendentemente dal modo in cui vengono caricati, i video
creati tramite Servizi multimediali possono essere utilizzati da vari
sistemi client, tra cui Windows, Macintosh, HTML 5, iOS, Android,
Windows Phone, Flash e Silverlight. L'obiettivo è semplificare la
creazione di applicazioni multimediali moderne.

Per una panoramica visiva del funzionamento di Servizi multimediali,
scaricare il [poster di Servizi multimediali di Azure][2].

<h2><a  id="HPC" ></a>High-Performance Computing</h2>


La tecnologia High-Performance Computing (HPC) rappresenta uno dei modi
più interessanti per utilizzare una piattaforma cloud, in quanto
consente di eseguire il codice contemporaneamente in più computer. In
Azure questo significa eseguire contemporaneamente molte macchine
virtuali in parallelo per risolvere eventuali problemi. Questa
operazione richiede una pianificazione delle applicazioni, ad esempio
per distribuire il relativo lavoro tra tali istanze. A questo scopo, in
Azure è disponibile l'Utilità di pianificazione HPC.

Questo componente può essere utilizzato con applicazioni HPC create per
utilizzare il sistema MPI (Message Passing Interface) standard di
settore. I software di analisi degli elementi finiti, ad esempio le
simulazioni dei crash test per le automobili, costituiscono tra gli
altri, un esempio di questo tipo di applicazione. L'Utilità di
pianificazione HPC può inoltre essere utilizzata con le cosiddette
applicazioni parallele, come le simulazioni del Gran Premio di
Montecarlo. Indipendentemente dal problema, il valore restituito è lo
stesso: l'Utilità di pianificazione HPC gestisce problemi complessi
relativi alla pianificazione di attività di elaborazione parallele tra
molte macchine virtuali di Azure. L'obiettivo è semplificare la
creazione di applicazioni HPC in esecuzione nel cloud.

<h2><a  id="commerce" ></a>E-commerce</h2>


La diffusione della tecnologia SaaS (Software as a Service) sta
trasformando il modo in cui si creano le applicazioni e anche il modo in
cui queste vengono vendute. Poiché un'applicazione SaaS vive nel cloud,
è normale che i potenziali clienti cerchino soluzioni online. Questo
cambiamento si applica ai dati come alle applicazioni. Non è quindi
strano che gli utenti si rivolgano al cloud per cercare set di dati
disponibili per l'acquisto. A questo scopo, Microsoft offre [Azure
Marketplace][3] e [Azure Store](/en-us/store/overview/), come illustrato
nella figura 8.

![E-commerce di Azure](./media/intro-to-azure/IntroAzure9.png)   
 **Figura 8: in Azure Marketplace e Azure Store è possibile cercare e
acquistare applicazioni e set di dati commerciali Azure..**

La differenza tra i due servizi sta nel fatto che il Marketplace si
trova all'esterno del portale di gestione di Azure, mentre è possibile
accedere al negozio direttamente dal portale. I potenziali clienti
possono eseguire ricerche in entrambi per trovare le applicazioni Azure
desiderate e quindi eseguire l'iscrizione sia tramite l'autore
dell'applicazione che direttamente dal Marketplace o dal negozio. I
clienti possono inoltre cercare set di dati commerciali, ad esempio dati
demografici, dati finanziari, dati geografici e così via. Quando trovano
qualcosa di interessante, possono accedervi sia dal fornitore,
direttamente tramite i siti Web del Marketplace o del negozio oppure, in
alcuni casi, direttamente dal portale di gestione. Le applicazioni
possono inoltre utilizzare l'API di ricerca Bing tramite il Marketplace
per consentire agli utenti di accedere ai risultati delle ricerche Web.

<h2><a  id="sdk" ></a>SDK</h2>


Nel 2008 la prima versione non definitiva di Azure supportava solo lo
sviluppo in .NET. Oggi è invece possibile creare applicazioni Azure in
qualsiasi linguaggio. Attualmente Microsoft fornisce SDK specifici per
linguaggio per .NET, Java, PHP, Node.js, Ruby e Python. È inoltre
disponibile un SDK di Azure generale che offre il supporto di base per
qualsiasi linguaggio, ad esempio C++.

Questi SDK supportano l'utente nelle attività di creazione,
distribuzione e gestione di applicazioni Azure. È possibile scaricarli
da www.windowsazure.com o GitHub e possono essere utilizzati con Visual
Studio ed Eclipse. Azure offre inoltre strumenti da riga di comando che
gli sviluppatori possono utilizzare con qualsiasi editor di testo o
ambiente di sviluppo, inclusi gli strumenti per la distribuzione di
applicazioni Azure da sistemi Linux e Macintosh.

Questi SDK, oltre a supportare gli utenti per la creazione di
applicazioni Azure, forniscono librerie client che consentono di creare
software in esecuzione all'esterno del cloud che utilizza tali servizi
di Azure. Ad esempio, è possibile creare un'applicazione in esecuzione
presso un provider di servizi di hosting che utilizza i BLOB di Azure
oppure creare uno strumento che distribuisce applicazioni Azure tramite
l'interfaccia di gestione di Azure.

<h2><a  id="start" ></a>Per iniziare</h2>


Dopo avere acquisito una panoramica generale, sarà possibile iniziare a
scrivere la prima applicazione Azure. Scegliere la lingua, [scaricare
l'SDK appropriato](/en-us/downloads/) e installarlo. Il cloud computing
è la nuova piattaforma predefinita. Ora è possibile iniziare.



[1]: http://www.microsoft.com/en-us/download/details.aspx?id=35473
[2]: http://www.microsoft.com/en-us/download/details.aspx?id=38195
[3]: http://datamarket.azure.com/
