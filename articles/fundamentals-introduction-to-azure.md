<properties 
	pageTitle="Introduzione ad Azure | Microsoft Azure" 
	description="Nuovo utente di Microsoft Azure? Visualizzare una panoramica di base dei servizi offerti assieme a esempi di uso." 
	services=" " 
	documentationCenter=".net" 
	authors="Rboucher" 
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015"  
	ms.author="robb"/>

# Introduzione a Microsoft Azure

Microsoft Azure è la piattaforma applicativa di Microsoft per il cloud pubblico. Lo scopo di questo articolo è offrire all'utente le basi per comprendere i dati fondamentali di Azure, anche se non si ha alcuna nozione del cloud computing.

**Come leggere questo articolo**

Azure è in continua crescita, pertanto può accadere che l'utente si senta sovraccaricato di informazioni. I servizi di base sono indicati in precedenza in questo documento. Si consiglia di iniziare con tali servizi, per poi passare ai servizi aggiuntivi. Ciò non significa che non è possibile usare i servizi aggiuntivi; tuttavia, i servizi di base costituiscono l'elemento centrale di un'applicazione che esegue Azure.

**Commenti e suggerimenti**

I commenti degli utenti sono importanti. Questo articolo dovrebbe fornire una panoramica efficiente di Azure. In caso contrario, invitiamo gli utenti a inviare i propri commenti usando l'apposita sezione in fondo alla pagina, fornendo alcune informazioni sulle proprie aspettative e sulle aree di miglioramento dell'articolo.
   

## Componenti di Azure

Azure raggruppa i servizi in categorie nel portale di gestione e in vari supporti visivi come l'[infografica su Microsoft Azure](http://azure.microsoft.com/documentation/infographics/azure/). Il portale di gestione è il portale usato per gestire la maggior parte dei servizi in Azure (ma non tutti).

In questo documento verrà usata un'**organizzazione diversa** per descrivere i servizi in base a una funzione simili e per richiamare sottoservizi che fanno parte dei servizi di maggiori dimensioni.

![Componenti di Azure](./media/fundamentals-introduction-to-azure/AzureComponentsIntroNew780.png) *Figura: Azure fornisce servizi per applicazioni accessibili tramite Internet ed eseguiti nei data center di Azure.*

## Portale di gestione
Azure offre un'interfaccia Web denominata [portale di gestione](http://manage.windowsazure.com) che consente agli amministratori di accedere e gestire la maggior parte delle funzionalità di Azure, ma non tutte. Microsoft in genere rilascia il portale dell'interfaccia utente più recente in versione beta prima di ritirare una versione precedente. Il portale più recente è denominato ["Portale di anteprima di Azure"](https://portal.azure.com/).

Quando entrambi i portali sono attivi si verifica in genere una lunga sovrapposizione: benché i servizi principali siano disponibili in entrambi i portali, non tutte le funzionalità potrebbero esserlo. I servizi più recenti potrebbero essere visualizzati prima nel nuovo portale, mentre i servizi e le funzionalità più obsoleti potrebbero esistere solo sul vecchio portale. In altre parole, se non si trova un contenuto nel vecchio portale, è consigliabile verificare sul nuovo portale, e viceversa.



## Calcolo

Una delle attività di base svolte da una piattaforma cloud è l'esecuzione di applicazioni. Ogni modello di calcolo di Azure ha un ruolo specifico.

È possibile utilizzare queste tecnologie separatamente o combinarle secondo necessità per creare la base appropriata per l'applicazione. L'approccio scelto dipende dai problemi che si sta tentando di risolvere.


### Macchine virtuali di Azure

![Macchine virtuali di Azure ROBBCSIART\_TEST](./media/fundamentals-introduction-to-azure/mscsiart_VirtualMachinesIntroNew_12345.png) *Figura: Macchine virtuali offre il controllo completo sulle istanze della macchina virtuale nel cloud.*

La possibilità di creare una macchina virtuale su richiesta, sia da un'immagine standard che da una fornita dall'utente, può risultare estremamente utile. Questo approccio, comunemente noto come IaaS (Infrastructure as a Service), è quanto fornito da Macchine virtuali di Azure. La figura 2 mostra una combinazione di esecuzione della macchina virtuale (VM) e di creazione di una macchina virtuale da un VHD.

Per creare una macchina virtuale, è necessario specificare il disco rigido virtuale (VHD) da usare e le dimensioni della macchina virtuale. Si paga quindi il servizio in base al tempo in cui la macchina virtuale è in esecuzione. Si paga a minuto e solo mentre è in esecuzione, nonostante sia presente un costo minimo di archiviazione per la disponibilità del VHD. Azure offre una galleria di VHD predefiniti ("immagini") che contengono un sistema operativo avviabile da cui iniziare. Includono opzioni Microsoft e dei partner, ad esempio Windows Server e Linux, SQL Server, Oracle e molte altre ancora. L'utente può creare liberamente VHD e immagini, per poi caricarle personalmente. Può persino caricare VHD che contengono solo dati e quindi accedere a tali VHD dalle macchine virtuali in esecuzione.

Indipendentemente dalla provenienza del VHD, è possibile archiviare in modo permanente qualsiasi modifica apportata mentre una macchina virtuale è in esecuzione. Alla creazione successiva di una macchina virtuale dallo stesso disco rigido virtuale, le attività riprendono dal punto in cui sono state interrotte. I VHD che supportano le macchine virtuali sono archiviati nei BLOB di archiviazione di Azure, che verranno descritti successivamente. Ciò significa che è possibile ottenere ridondanza per garantire che le macchine virtuali non scompaiano a causa degli errori dell'hardware e dei dischi. È inoltre possibile copiare il disco rigido virtuale modificato all'esterno di Azure e quindi eseguirlo localmente.
 
L'applicazione viene eseguita all'interno di una o più macchine virtuali, in base al modo in cui è stata precedentemente creata; in alternativa, è possibile decidere di crearne una completamente nuova.

Si tratta di un approccio generale al cloud computing che consente risolvere esigenze di natura diversa.

**Scenari di macchine virtuali**

1.	**viluppo/Test**: è possibile usarle per creare una piattaforma di test e sviluppo conveniente che è possibile arrestare dopo l'uso oppure per creare ed eseguire applicazioni basate su qualsiasi linguaggio o libreria. Per queste applicazioni è possibile scegliere qualsiasi opzione di gestione dati offerta da Azure e decidere se utilizzare SQL Server o un altro DBMS in esecuzione in una o più macchine virtuali. 
2.	**Spostamento delle applicazioni in Azure (sollevamento e spostamento)** - "Sollevamento e spostamento" indica lo spostamento dell'applicazione in modo analogo a un'operazione di sollevamento di un oggetto di grandi dimensioni effettuato con un carrello elevatore. L'utente "solleva" il VHD dal data center locale e lo "sposta" in Azure, dove viene eseguito. In genere è necessario effettuare alcune operazioni per rimuovere le dipendenze da altri sistemi. Se sono presenti troppe dipendenze, è possibile scegliere l'opzione 3.  
3.	**Estensione del data center**: usare le macchine virtuali Azure come estensione del data center locale, che esegue SharePoint o altre applicazioni. Per supportare questa opzione, è possibile creare domini di Windows nel cloud tramite l'esecuzione di Active Directory nelle macchine virtuali di Azure. È possibile usare la rete virtuale di Azure (citata in seguito) per collegare la rete locale e la propria rete in Azure.
 


### App Web

![Applicazioni Web di Azure ROBBCSIART\_TEST](./media/fundamentals-introduction-to-azure/mscsiart_AzureWebsitesIntroNew_12345.png) *Figura: App Web di Azure esegue un'applicazione di Sito Web nel cloud senza dover gestire il server Web sottostante.*

In genere, il cloud viene usato per eseguire siti Web e applicazioni Web. Macchine virtuali di Azure fornisce questo tipo di servizio, ma lascia all'utente la responsabilità di amministrare uno o più sistemi operativi sottostanti. I ruoli Web dei servizi cloud possono effettuare questa operazione, ma implementarli e distribuirli richiede un'attività amministrativa. Se invece si preferisce evitare di occuparsi delle attività di amministrazione del sito Web,

Questo è ciò che fornisce esattamente App Web. Questo modello di calcolo rende disponibile un ambiente Web gestito tramite il portale di gestione di Azure e diverse API. È possibile spostare un'applicazione Sito Web esistente in App Web lasciandola invariata o crearne una nuova direttamente nel cloud. Non appena un sito Web è in esecuzione, è possibile aggiungere o rimuovere istanze in modo dinamico, usando App Web di Azure per bilanciare il carico delle richieste. App Web di Azure offre un'opzione condivisa grazie alla quale il sito viene eseguito in una macchina virtuale assieme ad altri siti, e un'opzione standard che consente l'esecuzione di un sito in una macchina virtuale specifica. L'opzione standard consente inoltre di aumentare le dimensioni (potenza di elaborazione) delle istanze, se necessario.

Per gli ambienti di sviluppo, App Web supporta .NET, PHP, Node.js, Java e Python insieme a database SQL e MySQL (di ClearDB, un partner Microsoft) per l'archivio relazionale. Fornisce inoltre il supporto incorporato per numerose applicazioni ampiamente diffuse, ad esempio WordPress, Joomla e Drupal. Si tratta di una piattaforma a basso costo, scalabile ed estremamente utile per la creazione di siti e applicazioni Web nel cloud pubblico.


**Scenari di Applicazioni Web**

Applicazioni Web è ideale per aziende, sviluppatori e agenzie di progettazione Web. Per le aziende, si tratta di una soluzione di facile gestione, scalabile, estremamente sicura e a disponibilità elevate per l'esecuzione di siti Web di presenza. Quando è necessario impostare un sito Web, è preferibile iniziare con Applicazioni Web di Azure e procedere con Servizi cloud quando è necessario avvalersi di una funzionalità non disponibile. Vedere la fine della sezione "Calcolo" per ulteriori collegamenti utili per scegliere tra le varie opzioni.

### Servizi cloud
![Servizio cloud di Azure](./media/fundamentals-introduction-to-azure/CloudServicesIntroNew.png) *Figura: Servizi cloud di Azure fornisce una posizione in cui eseguire un codice personalizzato altamente scalabile in un ambiente di piattaforma distribuita come servizio (PaaS)*

Si supponga di voler sviluppare un'applicazione cloud in grado di supportare molti utenti contemporaneamente, che richieda poca amministrazione e che rimanga sempre attiva. Potrebbe essere il caso di un fornitore di software che ha deciso di utilizzare una piattaforma SaaS (Software as a Service) per sviluppare una versione delle proprie applicazioni nel cloud. Oppure di una start-up che si propone di creare un'applicazione per il grande pubblico per la quale è prevista una crescita rapida. Se si sceglie di utilizzare Azure, sarà necessario individuare il modello di esecuzione più adatto.

Applicazioni Web di Azure consente la creazione di questo tipo di applicazione Web, ma con alcuni vincoli, come ad esempio il fatto che non è disponibile un accesso amministrativo e pertanto non è possibile installare software arbitrario. Macchine virtuali di Azure offre molta più flessibilità, incluso l'accesso amministrativo, ed è possibile utilizzare il servizio per creare un'applicazione con scalabilità elevata, ma occorre gestire in autonomia molti aspetti legati all'affidabilità e all'amministrazione. Sarebbe quindi preferibile un'opzione che offra il controllo necessario e che gestisca al contempo anche le attività necessarie ai fini dell'affidabilità e dell'amministrazione.

Questo è ciò che offre Servizi cloud di Azure. Questa tecnologia è appositamente progettata per supportare applicazioni scalabili, affidabili e con carico amministrativo ridotto ed è un esempio di ciò che viene comunemente denominato PaaS (Platform as a Service). Per utilizzare il servizio, occorre creare un'applicazione con la tecnologia che si preferisce, ad esempio C#, Java, PHP, Python, Node.js o altro. Il codice viene quindi eseguito nelle macchine virtuali (denominate istanze) che eseguono una versione di Windows Server.

Queste macchine virtuali sono tuttavia distinte da quelle che si creano con Macchine virtuali di Azure. Innanzitutto, sono gestite da Azure, che effettua operazioni quali l'installazione di patch del sistema operativo e l'implementazione di nuove immagini con patch applicate. Di conseguenza l'applicazione non deve gestire lo stato nelle istanze del ruolo Web o di lavoro. Tali attività devono essere gestite tramite una delle opzioni di gestione dati di Azure descritte nella sezione successiva. Azure esegue inoltre il monitoraggio di queste macchine virtuali, riavviando quelle che presentano errori. È possibile impostare i servizi cloud in modo da creare automaticamente un numero maggiore o minore di istanze in base alla domanda. In questo modo è possibile gestire un uso maggiore per poi scalare in modo da ridurre i costi in caso di riduzione dell'uso.

Quando si crea un'istanza, sono disponibili due ruoli, entrambi basati su Windows Server. La differenza principale tra i due ruoli è che un'istanza di un ruolo Web esegue IIS, mentre un'istanza di un ruolo di lavoro no. Entrambi i ruoli vengono tuttavia gestiti nello stesso modo e spesso le applicazioni li usano ambedue. Un'istanza del ruolo Web, ad esempio, potrebbe accettare le richieste degli utenti e quindi passarle a un'istanza del ruolo di lavoro per l'elaborazione. Per aumentare o ridurre le risorse di un'applicazione, è possibile richiedere ad Azure di creare più istanze di entrambi i ruoli o di arrestare le istanze esistenti. Come per il servizio Macchine virtuali di Azure, viene addebitato solo il tempo in cui è in esecuzione ogni istanza del ruolo Web o di lavoro.

**Scenari di servizi cloud**

I servizi cloud sono ideali per supportare un livello avanzato di scalabilità orizzontale quando occorre esercitare un controllo maggiore sulla piattaforma rispetto a quello fornito da Applicazioni Web di Azure ma non sul sistema operativo sottostante.

#### Scelta di un modello di calcolo
La [pagina di confronto tra Applicazioni Web , Servizi cloud e Macchine virtuali di Azure](choose-web-site-cloud-service-vm.md) fornisce informazioni più dettagliate su come scegliere un modello di calcolo.



## Gestione dati

Alle applicazioni servono i dati e ad applicazioni diverse servono tipi di dati diversi. Per questo motivo, in Azure sono disponibili diversi modi per archiviare e gestire i dati. Azure fornisce numerose opzioni di archiviazione, ma sono tutte progettate per un'archiviazione prolungata nel tempo. Con una qualsiasi di queste opzioni sono sempre presenti tre copie dei dati sincronizzati con il data center Azure; sei copie nel caso in cui si consenta ad Azure di usare la ridondanza geografica per eseguire il backup in un altro data center ubicato a una distanza di almeno 480 chilometri.


### All'interno delle macchine virtuali
Si è già accennato alla possibilità di eseguire SQL Server o un altro DBMS in una macchina virtuale creata con macchine virtuali di Azure. Questa opzione non è limitata ai sistemi relazionali. È infatti possibile eseguire tecnologie NoSQL, come ad esempio MongoDB e Cassandra. L'esecuzione di un sistema di database personale è semplice, in quanto rientra nelle attività normalmente eseguite in un data center, ma richiede anche la gestione dell'amministrazione di uno specifico DBMS. In altre opzioni Azure gestisce un maggior numero di attività amministrative, o anche tutte, automaticamente.

È importante ricordare che lo stato della macchina virtuali e di eventuali dischi di dati aggiuntivi creati o caricati sono supportati dall'archiviazione BLOB (che verrà descritta in seguito).


### Database SQL di Azure
![Database SQL di archiviazione di Azure](./media/fundamentals-introduction-to-azure/StorageAzureSQLDatabaseIntroNew.png)

*Figura: il database SQL di Azure fornisce un servizio di database relazionale gestito nel cloud.*

Per l'archiviazione relazionale, Azure offre la funzionalità Database SQL. Il nome non deve trarre in inganno. Si tratta di un database diverso rispetto a un tipico database SQL fornito da SQL Server in esecuzione su Windows Server.

Precedentemente denominato SQL Azure, il database SQL di Azure offre tutte le funzionalità principali di un sistema di gestione di database relazionale, ad esempio transazioni atomiche, accesso simultaneo ai dati da parte di più utenti con integrità dei dati, query SQL ANSI e un modello di programmazione noto. Come per SQL Server, è possibile accedere al database SQL mediante Entity Framework, ADO.NET, JDBC e altre tecnologie di accesso ai dati note. Supporta inoltre la maggior parte del linguaggio T-SQL, oltre agli strumenti di SQL Server, ad esempio SQL Server Management Studio. Per chiunque abbia familiarità con SQL Server (o altro database relazionale), l'utilizzo del database SQL risulta estremamente semplice.

Tuttavia, il database SQL non rappresenta solo un DBMS nel cloud: si tratta infatti di un vero e proprio servizio PaaS che consente di mantenere il controllo sui dati e sugli utenti che possono accedervi, ma gestisce tutto le attività amministrative, ad esempio la gestione dell'infrastruttura hardware e l'aggiornamento automatico del software di database e del sistema operativo. Il database SQL fornisce disponibilità elevata, backup automatici, funzionalità di ripristino a un momento specifico e possibilità di replicare copie tra varie aree geografiche.

Dietro pagamento di un importo leggermente superiore è inoltre disponibile un'opzione Premium che consente di avere il proprio server sottostante dedicato. Con l'opzione Standard il database viene eseguito su hardware condiviso, che può limitare la velocità delle query su database in un server molto occupato.

**Scenari per il database SQL**

Se si crea un'applicazione di Azure (con uno qualsiasi dei tre metodi di calcolo) che richiede una soluzione di archiviazione relazionale, il database SQL rappresenta una scelta ottimale. Anche le applicazioni in esecuzione all'esterno del cloud possono usare questo servizio, pertanto gli scenari possibili sono davvero numerosi. Ad esempio, è possibile accedere ai dati archiviati in SQL Database da diversi sistemi client, inclusi desktop, portatili, tablet e telefoni cellulari. Poiché offre disponibilità elevata incorporata tramite replica, Database SQL consente inoltre di ridurre i tempi di inattività.


### Tabelle
![Tabelle di archiviazione di Azure](./media/fundamentals-introduction-to-azure/StorageTablesIntroNew.png)
  
*Figura: Tabelle di Azure fornisce una soluzione NoSQL semplice per archiviare i dati.*

Questa funzionalità è a volte indicata con un nome diverso in quanto fa parte di una funzionalità più ampia denominata "archiviazione di Azure". I termini "tabelle", "tabelle di Azure" o "tabelle di archiviazione" indicano pertanto tutti la stessa funzionalità.

Anche se il nome può confondere, questa tecnologia non fornisce servizi di archiviazione relazionale. Di fatto, è un esempio di approccio NoSQL denominato archivio chiave-valore. Le tabelle di Azure consentono a un'applicazione di archiviare proprietà di vari tipi, ad esempio stringhe, numeri interi e date. Un'applicazione può quindi recuperare un gruppo di proprietà fornendo una chiave univoca per tale gruppo. Anche se le operazioni complesse quali i join non sono supportate, le tabelle offrono accesso rapido ai dati tipizzati. Sono inoltre molto scalabili e una singola tabella può contenere fino a un terabyte di dati. Oltre a essere semplici, sono anche in genere meno costose da usare rispetto all'archivio relazionale di database SQL.

**Scenari per le tabelle**

Si supponga di voler creare un'applicazione di Azure che necessita di accesso rapido a grandi quantità di dati tipizzati ma che non debba eseguire query SQL complesse su tali dati. Ad esempio, un'applicazione consumer che deve archiviare le informazioni relative al profilo del cliente per ogni utente. Questa app diventerà molto popolare, pertanto occorrerà prevedere la possibilità di archiviare grandi quantità di dati, ma non altre operazioni, da recuperare in modo semplice. Questo è esattamente lo scenario in cui il servizio Tabelle di Azure può risultare utile.


### Blobs
![BLOB di archiviazione di Azure:](./media/fundamentals-introduction-to-azure/StorageBlobsIntroNew.png) *Figura: i blob di Azure forniscono dati binari non strutturati.*

I BLOB di Azure (si ricorda che i termini "archiviazione BLOB" e "BLOB di archiviazione" sono sinonimi) sono progettati per archiviare dati binari non strutturati. Analogamente alle tabelle, i BLOB offrono una funzionalità di storage conveniente e un singolo BLOB può contenere fino a 1 TB (un terabyte) di dati. Le applicazioni di Azure possono inoltre utilizzare le unità Azure, tramite le quali i BLOB forniscono un archivio permanente per un file system Windows montato in un'istanza di Azure. L'applicazione vede i file di Windows ordinari, ma il contenuto è effettivamente archiviato in un BLOB.

L'archiviazione BLOB è usata da molte altre funzionalità di Azure (tra cui le macchine virtuali), pertanto è sicuramente in grado di gestire anche i carichi di lavoro dell'utente.

**Scenari per i BLOB**

Un'applicazione per l'archiviazione di video, file di grandi dimensioni o altre informazioni binarie può usare i BLOB come soluzione di archiviazione semplice e a un costo contenuto. I BLOB sono anche comunemente usati insieme ad altri servizi come la rete per la distribuzione di contenuti, che verrà descritta in seguito.

### Importazione/esportazione
![Azure Import Export Service](./media/fundamentals-introduction-to-azure/ImportExportIntroNew.png)
 
*Figura: la funzionalità di importazione/esportazione di Azure consente di inviare un disco rigido fisico verso o da Azure per eseguire un'importazione o un'esportazione di dati in blocco in modo più rapido e conveniente.*

Potrebbe essere necessario spostare un'elevata quantità di dati in Azure. Questa operazione richiederebbe molto tempo, addirittura giorni, e l'uso di una elevata quantità di larghezza di banda. In questi casi è possibile usare la funzionalità di importazione/esportazione di Azure, che consente di inviare dischi rigidi SATA da 3.5" crittografati con Bitlocker direttamente ai data center di Azure, dove Microsoft trasferirà automaticamente i dati nello storage BLOB. Al termine del caricamento, Microsoft invia le unità al cliente. È anche possibile richiedere l'esportazione di elevate quantità di dati dallo storage BLOB in dischi rigidi e il relativo invio all'utente tramite e-mail.

**Scenari di importazione/esportazione**

- **Migrazione dei dati**: quando sono presenti elevate quantità di dati (terabyte) da caricare in Azure, il servizio di Importazione/Esportazione è spesso molto più veloce e anche più conveniente del trasferimento tramite Internet. Quando i dati sono nei BLOB, è possibile elaborarli in altri modi, ad esempio l'archiviazione tabelle o un database SQL.
 
- **Ripristino dei dati archiviati**: è possibile usare la funzionalità Importazione/Esportazione per trasferire tramite Microsoft elevate quantità di dati presenti nell'archivio BLOB di Azure in un dispositivo di archiviazione inviato dall'utente, che viene poi restituito in una posizione specificata dall'utente. Poiché questa operazione richiede del tempo, non è un'opzione idonea per il disaster recovery. È preferibile per i dati archiviati per i quali non occorre effettuare l'accesso velocemente.


### Servizio file
![Servizio file di Azure](./media/fundamentals-introduction-to-azure/FileServiceIntroNew.png) *Figura: Servizi file di Azure fornisce percorsi SMB \\\server\\share per le applicazioni in esecuzione nel cloud.*

In locale è frequente la disponibilità di un'elevata quantità di archiviazione di file accessibile tramite il protocollo Server Message Block (SMB) usando un formato \\\Server\\share. Azure ora offre un servizio che consente di usare questo protocollo nel cloud. Le applicazioni eseguite in Azure possono usarlo per condividere file tra macchine virtuali tramite API del file system note, quali ReadFile e WriteFile. Inoltre, è possibile accedere contemporaneamente ai file tramite un'interfaccia REST, che consente di accedere alle condivisioni in locale quando si imposta anche una rete virtuale. File di Azure si basa sul servizio BLOB, pertanto eredita le stesse caratteristiche di disponibilità, durata, scalabilità e ridondanza geografica integrate nell'archiviazione di Azure.

**Scenari per File di Azure**

- **Migrazione delle app esistenti nel cloud**: è più semplice eseguire la migrazione di applicazioni locali nel cloud che usare condivisioni di file tra parti dell'applicazione. Ogni macchina virtuale si connette alla condivisione file e quindi esegue la lettura e la scrittura di file come farebbe su una condivisione file locale.

- **Impostazioni delle applicazioni condivise**: in uno schema comune per le applicazioni distribuite i file di configurazione si trovano in una posizione centralizzata dove numerose macchine virtuali diverse possono accedervi. Questi file di configurazione possono essere archiviati in una condivisione di File Azure ed essere letti da tutte le istanze delle applicazioni. Le impostazioni possono inoltre essere gestite tramite l'interfaccia REST, che consente l'accesso a livello globale ai file di configurazione.

- **Condivisione di diagnostica**: è possibile salvare e condividere file di diagnostica come log, metriche e dump di arresto anomalo del sistema. La disponibilità di questi file tramite SMB e l'interfaccia REST consente alle applicazioni di usare una varietà di strumenti di analisi per l'elaborazione e l'analisi dei dati di diagnostica.

- **Sviluppo/Test/Debug**: quando gli sviluppatori o gli amministratori usano le macchine virtuali nel cloud, spesso necessitano di un set di strumenti o di utilità. L'installazione e la distribuzione di queste utilità su ogni macchina virtuale richiede molto tempo. Con File di Azure uno sviluppatore o amministratore può archiviare i propri strumenti preferiti in una condivisione file e connettersi a essi da qualsiasi macchina virtuale.



## Rete

Oggi Azure viene eseguito in numerosi data center distribuiti in tutto il mondo. Durante l'esecuzione di un'applicazione o l'archiviazione dei dati, è possibile selezionare uno o più data center da usare. È inoltre possibile connettersi a questi data center in vari modi mediante i servizi seguenti.


### Rete virtuale
![VirtualNetwork](./media/fundamentals-introduction-to-azure/VirtualNetworkIntroNew.png)

*Figura: le reti virtuali forniscono una rete privata nel cloud in modo da consentire a servizi diversi di comunicare reciprocamente o in risorse locali se si imposta una connessione VPN tra più sedi locali.*


È possibile usare un cloud pubblico considerandolo come un'estensione del proprio data center.
 
Grazie alla possibilità di creare macchine virtuali su richiesta e quindi di rimuoverle (interrompendo il pagamento) quando non sono più necessarie, è possibile disporre di ulteriore potenza di elaborazione solo quando serve. Inoltre, poiché le macchine virtuali di Azure consentono di creare macchine virtuali che eseguono SharePoint, Active Directory e altre applicazioni software locali note, questo approccio può funzionare con le applicazioni già disponibili.

Affinché questa opzione risulti veramente utile, tuttavia, gli utenti dovrebbero essere in grado di trattare le applicazioni come se fossero in esecuzione nel proprio data center. E questo è il servizio offerto da Rete virtuale di Azure. Usando un dispositivo VPN, un amministratore può impostare una rete virtuale privata (VPN) tra la rete locale e le macchine virtuali distribuite in una rete virtuale di Azure. Assegnando indirizzi IPv4 personalizzati alle macchine virtuali del cloud, risulteranno come appartenenti alla rete in uso. Gli utenti dell'organizzazione potranno accedere alle applicazioni contenute in tali macchine virtuali come se fossero eseguite localmente.

Per altre informazioni sulla pianificazione e sulla creazione di una rete virtuale adatta alle esigenze dell'utente, vedere [Rete virtuale](../virtual-network/virtual-networks-overview.md).

### Express Route

![ExpressRoute](./media/fundamentals-introduction-to-azure/ExpressRouteIntroNew.png)

*Figura: ExpressRoute usa una rete virtuale di Azure, ma reindirizza le connessioni tramite linee dedicate più veloci invece della rete Internet pubblica.*

Se è necessaria ulteriore larghezza di banda o sicurezza rispetto a quelle che la rete virtuale di Azure è in grado di fornire, è possibile valutare ExpressRoute. In alcuni casi ExpressRoute può anche offrire un risparmio di denaro. Sarà comunque necessaria una rete virtuale di Azure, ma il collegamento tra Azure e il sito dell'utente si avvale di una connessione dedicata che non usa la rete Internet pubblica. Per usare questo servizio è necessario avere un contratto con un provider di servizi di rete o con un provider di Exchange.

L'impostazione di una connessione ExpressRoute richiede una maggiore quantità di tempo e di pianificazione, pertanto potrebbe essere necessario iniziare con una VPN da sito a sito ed effettuare quindi la migrazione a una connessione ExpressRoute.

Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

### Gestione traffico

![TrafficManager](./media/fundamentals-introduction-to-azure/TrafficManagerIntroNew.png)

*Figura: Gestione traffico di Azure consente di instradare il traffico globale al servizio dell'utente in base a regole intelligenti.*
 
Se l'applicazione Azure è in esecuzione in più data center, è possibile usare Gestione traffico di Azure per indirizzare le richieste degli utenti in modo intelligente tra le istanze dell'applicazione. È inoltre possibile instradare il traffico ai servizi non in esecuzione in Azure a condizione che siano accessibili da Internet.

Un'applicazione Azure con utenti in una sola parte del mondo può essere eseguita in un solo data center di Azure. Un'applicazione con utenti sparsi in tutto il mondo, verrà probabilmente eseguita in più data center, forse anche in tutti. In questo secondo caso, occorre affrontare il problema relativo all'assegnazione intelligente degli utenti alle istanze dell'applicazione. Nella maggior parte dei casi, si preferirà che ogni utente acceda al data center più vicino per garantire i tempi di risposta migliori. Potrebbe tuttavia accadere che l'istanza dell'applicazione sia sovraccarica o non disponibile. In questo caso, sarebbe opportuno indirizzare le richieste automaticamente a un altro data center. Questo è ciò che offre Gestione traffico di Azure.

Il proprietario di un'applicazione definisce le regole che consentono di specificare il modo in cui le richieste dagli utenti devono essere indirizzate ai data center, quindi si affida a Gestione traffico per l'applicazione di tali regole. Ad esempio, è possibile decidere che gli utenti vengano normalmente indirizzati al data center di Azure più vicino, ma che vengano reindirizzati a un altro quando il tempo di risposta del data center predefinito è superiore a quello di altri data center. Per le applicazioni distribuite a livello locale con molti utenti, un servizio incorporato per la gestione dei problemi di traffico può risultare molto utile.

Gestione traffico usa il DNS (Directory Name Service) per instradare gli utenti agli endpoint di servizio, ma l'ulteriore traffico non procede attraverso Gestione traffico una volta effettuata la connessione. In tal modo, Gestione traffico non costituisce un collo di bottiglia che potrebbe rallentare le comunicazioni del servizio.


## Servizi per gli sviluppatori
Azure offre numerosi strumenti che aiutano gli sviluppatori e i professionisti IT a creare e gestire le applicazioni nel cloud.

### Azure SDK
Nel 2008 la prima versione non definitiva di Azure supportava solo lo sviluppo in .NET. Oggi è invece possibile creare applicazioni Azure in qualsiasi linguaggio. Attualmente Microsoft fornisce SDK specifici per linguaggio per .NET, Java, PHP, Node.js, Ruby e Python. È inoltre disponibile un SDK di Azure generale che offre il supporto di base per qualsiasi linguaggio, ad esempio C++.

Questi SDK supportano l'utente nelle attività di creazione, distribuzione e gestione di applicazioni Azure. È possibile scaricarli da [www.microsoftazure.com](http://azure.microsoft.com/downloads/) o GitHub e possono essere usati con Visual Studio ed Eclipse. Azure offre inoltre strumenti da riga di comando che gli sviluppatori possono usare con qualsiasi editor di testo o ambiente di sviluppo, inclusi gli strumenti per la distribuzione di applicazioni Azure da sistemi Linux e Macintosh.

Questi SDK, oltre a supportare gli utenti per la creazione di applicazioni Azure, forniscono librerie client che consentono di creare software che usa tali servizi di Azure. Ad esempio, è possibile creare un'applicazione che legga e scriva i BLOB di Azure oppure creare uno strumento che distribuisca applicazioni Azure tramite l'interfaccia di gestione di Azure.

### Visual Studio Online

Visual Studio Online è un nome di marketing riferito a numerosi servizi che consentono di sviluppare applicazioni in Azure.

A scanso di equivoco, si tenga presente che Visual Studio Online non fornisce una versione ospitata o basata su Web di Visual Studio. È sempre necessario avere una copia in esecuzione in locale di Visual Studio. Tuttavia, fornisce molti altri strumenti che possono rivelarsi molto utili.

Include un sistema di controllo del codice sorgente ospitato denominato Team Foundation Service, che offre controllo della versione e rilevamento degli elementi di lavoro. Per il controllo della versione è persino possibile usare Git, se si preferisce questa opzione. Inoltre, è possibile variare il sistema di controllo del codice sorgente usato per progetto, nonché creare un numero illimitato di progetti team accessibili da qualsiasi parte del mondo.

Visual Studio Online fornisce un servizio di test del carico. È possibile eseguire test di carico creati in Visual Studio su macchine virtuali nel cloud. Si specifica il numero totale di utenti con i quali si desidera eseguire il test di carico e Visual Studio Online determinerà automaticamente il numero di agenti necessari, avvierà le macchine virtuali richieste ed eseguirà i test di carico. I sottoscrittori di MSDN ricevono migliaia di minuti-utente gratuiti di test di carico ogni mese.

Visual Studio Online offre inoltre il supporto per lo sviluppo Agile con funzionalità quali compilazione di integrazione continua, bacheche kanban e chat del team virtuali.

**Scenari di Visual Studio Online**

Visual Studio Online è un'opzione adatta alle aziende che hanno la necessità di collaborare in tutto il mondo ma che non dispongono ancora dell'infrastruttura idonea a tale scopo. È possibile impostare in pochi minuti, scegliere un sistema di controllo del codice sorgente e iniziare a scrivere il codice e compilare il giorno stesso. Gli strumenti del team offrono uno spazio per il coordinamento e la collaborazione e gli strumenti aggiuntivi forniscono le analisi necessarie per testare e mettere a punto l'applicazione rapidamente.

Tuttavia, le organizzazioni che hanno già un sistema locale possono testare nuovi progetti su Visual Studio Online per verificarne l'eventuale maggiore efficienza.

### Application Insights

![Application Insights](./media/fundamentals-introduction-to-azure/ApplicationInsights.png)

*Figura: Application Insights esegue il monitoraggio delle prestazioni e dell'utilizzo dell'app Web o del dispositivo attiva.*

Dopo aver pubblicato l'app, indipendentemente dal fatto che venga eseguita su dispositivi mobili, desktop o browser Web, Application Insights ne indica le prestazioni e le operazioni eseguite dagli utenti. Tiene il conto del numero di arresti anomali e dei tempi di risposta rallentati, invia avvisi se le cifre raggiungono valori di soglia inaccettabili e consente di diagnosticare eventuali problemi.

Quando si sviluppa una nuova funzionalità, è opportuno pianificare la valutazione del relativo successo con gli utenti. Analizzando i modelli di utilizzo, è possibile ottenere informazioni sulle soluzioni migliori per i clienti e migliorare l'app in ogni ciclo di sviluppo.

Anche se è ospitato in Azure, Application Insights funziona per una gamma sempre più vasta di app, sia in Azure che in altri ambienti. Sono supportate le app Web J2EE e ASP.NET, nonché le applicazioni per iOS, Android, OSX e Windows. I dati di telemetria vengono inviati da un SDK compilato con l'app per essere analizzati e visualizzati nel servizio Application Insights in Azure.

Se si desidera un'analisi più specializzata, esportare il flusso di dati di telemetria in un database, in Power BI o in altri strumenti.

**Scenari di Application Insights**

Si sta sviluppando un'app. Può trattarsi di un'app Web o un'app per dispositivi mobili o un'app per dispositivi mobili con back-end Web.

* Ottimizzare le prestazioni dell'app dopo la pubblicazione o durante il test di carico. Application Insights aggrega i dati di telemetria di tutte le istanze installate e presenta i grafici dei tempi di risposta, del numero di richieste ed eccezioni, dei tempi di risposta della dipendenza e di altri indicatori delle prestazioni. In questo modo, è possibile ottimizzare le prestazioni dell'app. È possibile inserire codice per segnalare dati più specifici, se necessario.
* Rilevare e diagnosticare i problemi nell'app attiva. Se gli indicatori di prestazioni raggiungono valori di soglia accettabili, è possibile ottenere avvisi tramite posta elettronica. È possibile esaminare sessioni utente specifiche, ad esempio per visualizzare la richiesta che ha causato un'eccezione. 
* Tenere traccia dell'utilizzo per valutare la riuscita di ogni nuova funzionalità. Quando si progetta una nuova cronologia utente, pianificare di misurarne l'uso e se gli utenti raggiungono gli obiettivi previsti. Application Insights fornisce dati di utilizzo di base, ad esempio le visualizzazioni di pagina Web, e consente di inserire codice per tenere traccia dell'esperienza dell'utente in modo più dettagliato.

### Automazione
A nessun piace perdere tempo ripetendo continuamente gli stessi processi manuali. L'automazione di Azure consente di creare, monitorare, gestire e implementare risorse nell'ambiente Azure.

L'automazione usa "runbook", che a loro volta usano flussi di lavoro Windows PowerShell (e non il normale PowerShell) in modo invisibile. I runbook sono destinati a essere eseguiti senza interazione dell'utente. I flussi di lavoro PowerShell consentono il salvataggio dello stato di uno script a determinati checkpoint lungo il percorso. Se si verifica un errore, non è necessario avviare uno script dall'inizio. È possibile riavviarlo all'ultimo checkpoint. Ciò consente di risparmiare una notevole quantità di lavoro associata al tentativo di far gestire allo script ogni possibile errore.

**Scenari di automazione**

L'automazione di Azure è una scelta ottimale per automatizzare le attività manuali, con esecuzione prolungata, soggette a errori e ripetute frequentemente.


### Gestione API

La creazione e la pubblicazione di Application Programmer Interfaces (API) in Internet costituisce un modo comune di fornire servizi alle applicazioni. Se tali servizi sono rivendibili (ad esempio dati meteorologici), un'organizzazione può consentire ad altre terze parti di accedere, a pagamento, agli stessi servizi. Quando si scala per includere un maggior numero di partner, in genere è necessario ottimizzare e controllare l'accesso. Alcuni partner possono perfino necessitare dei dati in un formato diverso.

La gestione delle API di Azure consente alle organizzazioni di pubblicare in modo semplice API per partner, dipendenti e sviluppatori terzi in modo sicuro e su vasta scala. Fornisce un endpoint dell'API diverso e agisce come proxy per chiamare l'endpoint effettivo, fornendo al contempo servizi quali caching, trasformazione, limitazione, controllo degli accessi e aggregazione delle analisi.

**Scenari di Gestione API**

Si supponga, ad esempio, che un'azienda abbia una serie di dispositivi che devono effettuare chiamate a un servizio centrale per ottenere dati, ad esempio un'azienda di spedizioni i cui veicoli su strada siano dotati di tali dispositivi. Sicuramente l'azienda dovrà impostare un sistema per monitorare i propri veicoli per prevedere e aggiornare in modo affidabile i tempi di consegna. Può sapere quanti sono i veicoli in dotazione e pianificare in modo appropriato. Ogni veicolo dovrà avere un dispositivo che effettua chiamate a una posizione centrale per fornire informazioni sulla propria posizione, sui dati di velocità e altro ancora.

Anche un cliente dell'azienda di spedizioni probabilmente trarrebbe vantaggio da questi dati di posizionamento. Il cliente potrebbe usarli per conoscere la distanza del trasporto, dove sono bloccati i prodotti, l'importo pagato lungo determinati itinerari (se unito all'importo pagato per la spedizione). Se l'azienda di spedizioni aggrega già questi dati, molti clienti potrebbero essere disposti a pagare per averli, ma a quel punto sorge per l'azienda di spedizioni la necessità di avere a disposizione uno strumento per fornire i dati ai clienti. Dopo aver fornito accesso ai clienti, potrebbero non avere il controllo sulla frequenza di interrogazione dei dati. Dovranno fornire delle regole che definiscano le autorizzazioni di accesso ai dati e i dati ai quali è possibile accedere. Tutte queste regole devono essere integrate nella loro API esterna, ed è in questa situazione che Gestione API può essere di aiuto.
 

## Identità e accesso
 
L'utilizzo delle identità è importante per la maggior parte delle applicazioni. Le informazioni relative all'identità di un utente consentono a un'applicazione di decidere la modalità di interazione con tale utente. Azure fornisce servizi che consentono di tenere traccia dell'identità nonché di integrarla con gli archivi di identità eventualmente già usati dall'utente.


### Active Directory

Come la maggior parte dei servizi directory, Azure Active Directory archivia le informazioni relative agli utenti e alle organizzazioni a cui appartengono. Consente agli utenti di eseguire l'accesso e quindi fornisce loro i token che dovranno presentare alle applicazioni per dimostrare la propria identità. Consente inoltre di sincronizzare le informazioni relative agli utenti con Windows Server Active Directory in esecuzione nella rete locale. Sebbene i meccanismi e i formati di dati utilizzati da Azure Active Directory non siano identici a quelli utilizzati in Windows Server Active Directory, le funzioni che eseguono sono molto simili.
 
È importante ricordare che Azure Active Directory è progettato principalmente per l'utilizzo da parte di applicazioni cloud. Può ad esempio essere usato da applicazioni in esecuzione in Azure o in altre piattaforme cloud. Viene anche usato dalle applicazioni di Microsoft, ad esempio le applicazioni di Office 365. Se si vuole estendere il data center al cloud mediante Macchine virtuali di Azure e Rete virtuale di Azure, Azure Active Directory non è la scelta adatta. Sarà più opportuno eseguire Windows Server Active Directory nelle macchine virtuali.

Per consentire alle informazioni in esso contenute, Azure Active Directory fornisce un'API RESTful denominata Azure Active Directory Graph. Questa API consente alle applicazioni in esecuzione in qualsiasi piattaforma di accedere agli oggetti directory e alle relazioni tra di essi. Un'applicazione autorizzata potrebbe ad esempio utilizzare questa API per acquisire informazioni su un utente, sui gruppi ai quali appartiene e altri dettagli. Le applicazioni possono inoltre vedere le relazioni tra gli utenti, ovvero il relativo grafico sociale, consentendo loro di lavorare in modo più intelligente con le connessioni tra persone.

Un'altra funzionalità del servizio, Controllo di accesso di Azure Active Directory, consente alle applicazioni di accettare le informazioni di identità da Facebook, Google, Windows Live ID e i più diffusi provider di identità con maggiore semplicità. Anziché richiedere all'applicazione di accettare i vari formati di dati e protocolli utilizzati da ognuno di questi provider, Controllo di accesso li converte in un unico formato comune. Consente inoltre a un'applicazione di accettare le informazioni di accesso da uno o più domini Active Directory. Il fornitore di un'applicazione SaaS potrebbe ad esempio utilizzare Controllo di accesso di Azure Active Directory per concedere agli utenti dei propri clienti l'accesso Single Sign-On all'applicazione.

I servizi directory rappresentano un rinforzo essenziale delle risorse di elaborazione locali. Non sorprenderà quindi che rivestano grande importanza anche nel cloud.

### Autenticazione a più fattori
![Azure Multi-Factor Authentication](./media/fundamentals-introduction-to-azure/MFAIntroNew.png)

*Figura: Multi-Factor Authentication è una funzionalità che consente di verificare più forme di identificazione a livello di applicazione.*
 
La protezione è sempre importante. La Multi-Factor Authentication (MFA) garantisce che solo gli utenti stessi possano accedere ai propri account. La MFA (nota anche come autenticazione a due fattori, o "2FA") richiede agli utenti di fornire due di questi tre metodi di verifica dell'identità per gli accessi e le transazioni degli utenti.

- Un'informazione nota (in genere una password)
- Un oggetto che si possiede (un dispositivo attendibile non facile da duplicare, ad esempio un telefono)
- Una caratteristica fisica dell'utente (biometrica)

Quindi, quando gli utenti effettuano l'accesso, è possibile richiedere loro di verificare la propria identità anche con un'app mobile, una telefonata o un messaggio di testo insieme alla propria password. Per impostazione predefinita, Azure Active Directory supporta l'uso delle password poiché è l'unico metodo di autenticazione supportato per gli accessi degli utenti. È possibile usare MFA insieme ad Azure AD o con applicazioni e directory personalizzate MFA SDK. È anche possibile usarla insieme ad applicazioni locali tramite il server Multi-Factor Authentication.

**Scenari di MFA**

Protezione degli accessi ad account sensibili, ad esempio dati di accesso bancari e accesso al codice sorgente quando l'immissione non autorizzata potrebbe comportare un costo elevato sulla proprietà finanziaria o intellettuale.






## Mobile

Se si crea un'app per dispositivi mobili, Azure può archiviare i dati nel cloud, autenticare gli utenti e inviare notifiche push senza necessità di scrivere un'elevata quantità di codice personalizzato.

Nonostante sia certamente possibile creare il back-end per un'app per dispositivi mobili tramite Macchine virtuali, Servizi cloud o Applicazioni Web, usando i servizi di Azure è possibile dedicare molto meno tempo alla scrittura dei componenti del servizio sottostanti.


### App per dispositivi mobili

![App per dispositivi mobili](./media/fundamentals-introduction-to-azure/MobileServicesIntroNew.png)
  
*Figura: App per dispositivi mobili fornisce funzionalità comunemente richieste dalle applicazioni che si interfacciano con dispositivi mobili.*

App per dispositivi mobili di Azure fornisce molte funzionalità utili che consentono di risparmiare tempo nella creazione di un back-end per un'applicazione per dispositivi mobili. Consente di effettuare semplici attività di provisioning e gestione dei dati archiviati in un database SQL. Con il codice lato server è possibile usare facilmente opzioni aggiuntive di archiviazione di dati come lo storage BLOB o MongoDB. App per dispositivi mobili fornisce il supporto per le notifiche, anche se in determinati casi è possibile usare gli hub di notifica come descritto in seguito. Il servizio include inoltre un'API REST che l'applicazione mobile può chiamare per l'esecuzione delle attività. App per dispositivi mobili offre inoltre la possibilità di autenticare gli utenti tramite Microsoft e Active Directory, nonché altri provider di identità noti come Facebook, Twitter e Google.


È possibile usare altri servizi Azure come bus di servizio e ruoli di lavoro e connettersi a sistemi locali. È anche possibile usare componenti aggiuntivi di terze parti da Azure Store (come SendGrid per la posta elettronica) per fornire ulteriori funzionalità.


Le librerie client native per Android, iOS, HTML/JavaScript, Windows Phone e Windows Store semplificano lo sviluppo di app su tutte le principali piattaforme mobili. Un'API REST consente di usare funzionalità di dati e autenticazione di Servizi mobili con app su piattaforme diverse. Un singolo servizio mobile può supportare più app client in modo da offrire un'esperienza utente coerente su tutti i dispositivi.

Poiché Azure supporta già un livello avanzato di scalabilità, è possibile gestire il traffico man mano che l'app acquisisce popolarità. Il monitoraggio e la registrazione sono supportati per semplificare la risoluzione dei problemi e la gestione delle prestazioni.


### Hub di notifica

![NotificationHubs](./media/fundamentals-introduction-to-azure/NotificationHubsIntroNew.png)

*Figura: Hub di notifica fornisce le funzionalità comunemente richieste dalle applicazioni che si interfacciano con dispositivi mobili.*

Nonostante sia possibile scrivere un codice per l'invio di notifiche in App per dispositivi mobili di Azure, Hub di notifica è ottimizzato per trasmettere milioni di notifiche push altamente personalizzate nel giro di pochi minuti. Non è necessario preoccuparsi di dettagli come il gestore di servizi mobili o il produttore del dispositivo. Ora è possibile raggiungere singoli utenti o milioni di utenti con una singola chiamata API.

Hub di notifica è progettato per funzionare con qualsiasi back-end. È possibile usare App per dispositivi mobili di Azure, un back-end personalizzato nel cloud eseguito su qualsiasi provider o su un back-end locale.

**Scenari di Hub di notifica** Si supponga, ad esempio, di compilare un gioco per dispositivi mobili in cui i vari giocatori si alternano nell'azione di gioco; potrebbe essere necessario comunicare al giocatore 2 che il giocatore 1 ha terminato il proprio turno. Se non occorre effettuare altre azioni, è sufficiente usare App per dispositivi mobili. Se invece fossero presenti 100.000 giocatori e si volesse inviare a tutti un'offerta gratuita in cui il fattore tempo è un elemento determinante, Hub di notifica è la scelta migliore.

Consente di inviare ultime notizie, informazioni sugli eventi sportivi e annunci di prodotti a milioni di utenti con bassa latenza. Le aziende possono inviare ai propri dipendenti nuove comunicazioni in cui il fattore tempo è un elemento determinante, ad esempio contatti di vendita, in modo che i dipendenti non debbano controllare costantemente l'e-mail o altre applicazioni per restare aggiornati. È possibile inviare password monouso richieste per l'autenticazione a più fattori.
   



## Back-up
Ogni azienda deve eseguire il backup e il ripristino dei dati. È possibile usare Azure per eseguire il backup e il ripristino dell'applicazione nel cloud o in locale. Azure offre opzioni diverse a seconda del tipo di backup.

### Site Recovery
 
Azure Site Recovery (in precedenza Gestione di ripristino di Hyper-V) consente di proteggere le applicazioni importanti coordinando la replica e il ripristino in più siti. Site Recovery consente di proteggere le applicazioni basate su Hyper-v, VMWare o SAN nel sito secondario, nel sito di un host o in Azure e di evitare difficoltà e costi correlati con le operazioni di creazione e gestione della propria posizione secondaria. Azure crittografa i dati e le comunicazioni ed è possibile abilitare la crittografia anche per i dati at-rest.

Monitora continuamente l'integrità dei servizi e contribuisce ad automatizzare il ripristino ordinato dei servizi nell'eventualità di un'interruzione del sito presso il data center primario. Le macchine virtuali possono essere attivate in modo orchestrato per facilitare il rapido ripristino del servizio, anche per carichi di lavoro multilivello complessi.

Site Recovery funziona con tecnologie esistenti quali la replica Hyper-V, System Center e SQL Server AlwaysOn. Per informazioni dettagliate, consultare [Panoramica di Azure Site Recovery](site-recovery/site-recovery-overview.md).

### Backup di Azure
![Backup di Azure](./media/fundamentals-introduction-to-azure/AzureBackupIntroNew.png)

*Figura: il Backup di Azure esegue il backup dei dati da server Windows locali nel cloud.*

Backup di Azure esegue il backup dei dati da server locali che eseguono Windows Server nel cloud. È possibile gestire i backup direttamente dagli strumenti di backup disponibili in Windows Server 2012, Windows Server 2012 Essentials o System Center 2012 - Data Protection Manager. In alternativa, è possibile usare un agente di backup specializzato.

La protezione dei dati aumenta perché i backup vengono crittografati prima della trasmissione, archiviati in Azure e protetti con un certificato caricato dall'utente. Il servizio si avvale della stessa protezione dei dati ridondante e ad alta disponibilità di archiviazione di Azure. È possibile eseguire il backup di file e cartelle in base a una pianificazione regolare oppure immediatamente, eseguendo backup completi o incrementali. Dopo aver eseguito il backup dei dati nel cloud, gli utenti autorizzati possono facilmente recuperare i backup in qualsiasi server. Offre inoltre criteri di conservazione dati configurabili, compressione dei dati e limitazione del trasferimento dati in modo da offrire all'utente la possibilità di gestire i costi di archiviazione e trasferimento dei dati.

**Scenari per Backup di Azure**

Se si usa già Windows Server o System Center, Backup di Azure è una scelta naturale per eseguire il backup del file system del server, di macchine virtuali e di database SQL Server. Backup di Azure funziona con file crittografati, sparse e compressi. Sono presenti alcune limitazioni, quindi si consiglia di [controllare prima i prerequisiti di Backup di Azure](http://technet.microsoft.com/library/dn296608.aspx).



## Messaggistica e integrazione

Indipendentemente dall'attività svolta, spesso il codice deve interagire con altro codice. In alcune situazioni, è sufficiente un messaggio in coda di base. In altri casi, sono necessarie interazioni più complesse. In Azure sono disponibili diversi modi per risolvere i problemi di questo tipo. Nella figura 5 sono illustrate le scelte disponibili.

### Queues
![Inoltro del bus di servizio di Azure](./media/fundamentals-introduction-to-azure/QueuesIntroNew.png)

*Figura: le code consentono l'accoppiamento di tipo loose tra parti di un'applicazione e facilitano la scalabilità.*

L'accodamento si basa su un concetto semplice: un'applicazione colloca un messaggio in una coda e il messaggio viene quindi letto da un'altra applicazione. Se l'applicazione richiede solo questo semplice servizio, il servizio Code di Azure risulta la scelta ottimale.

Per effetto del modo in cui la piattaforma Azure è cresciuta nel corso del tempo, Code di archiviazione e Code bus di servizio di Azure forniscono servizi di accodamento simili. I motivi per i quali è preferibile usare una o l'altra soluzione sono descritti nel documento tecnico [<LINK>](http://msdn.microsoft.com/library/azure/hh767287.aspx "Analogie e differenze tra le code di Azure e le code del bus di servizio"). In molti scenari saranno valide entrambe le soluzioni.

**Scenari di coda**

Uno degli usi comuni delle code è quello di consentire a un'istanza del ruolo Web di comunicare con un'istanza del ruolo di lavoro all'interno della stessa applicazione di Servizi cloud.

Si supponga di creare un'applicazione Azure per la condivisione video. L'applicazione è composta da codice PHP in esecuzione in un ruolo Web che consente agli utenti di caricare e guardare video, insieme a un ruolo di lavoro implementato in C# che converte il video caricato in vari formati.

Quando un'istanza del ruolo Web riceve un nuovo video da un utente, può archiviare il video in un BLOB, quindi inviare un messaggio a un ruolo di lavoro tramite una coda per comunicargli la posizione del nuovo video. Un'istanza del ruolo di lavoro, non è importante quale, leggerà quindi il messaggio dalla coda ed eseguirà le conversioni video necessarie in background.

Un'applicazione strutturata in questo modo consente l'elaborazione asincrona e favorisce la scalabilità dell'applicazione in quanto il numero di istanze del ruolo Web e del ruolo di lavoro può variare in modo indipendente. È inoltre possibile usare la dimensione della coda per attivare il dimensionamento verso l'alto o verso il basso del numero di ruoli di lavoro. Se il valore è molto elevato vengono aggiunti altri ruoli. Se il numero si riduce, è possibile ridurre il numero di ruoli in esecuzione per risparmiare denaro.

È possibile usare lo stesso criterio tra diverse parti dell'applicazione, anche se non usano ruoli Web e di lavoro. Ciò consente di ridimensionare le parti su ogni lato della coda secondo la domanda e i tempi di elaborazione.


### Bus di servizio
Le applicazioni, indipendentemente dal fatto che siano eseguite nel cloud, in un data center, in un dispositivo mobile o altrove, devono interagire. Il bus di servizio di Azure consente alle applicazioni eseguite praticamente ovunque di scambiarsi i dati.

Oltre al meccanismo di accodamento (uno a uno) descritto in precedenza, bus di servizio fornisce altri metodi di comunicazione.

#### Inoltro del bus di servizio
![Inoltro del bus di servizio di Azure](./media/fundamentals-introduction-to-azure/ServiceBusRelayIntroNew.png)

*Figura: l’inoltro del bus di servizio consente la comunicazione tra applicazioni su diversi lati di un firewall.*

Il bus di servizio consente la comunicazione diretta con il relativo servizio di inoltro, offrendo così un modo sicuro di interagire attraverso i firewall. Inoltro del bus di servizio consente alle applicazioni di comunicare scambiando messaggi tramite un endpoint ospitato nel cloud anziché localmente.

**Scenari di Inoltro del bus di servizio**

Le applicazioni che comunicano tramite il bus di servizio possono essere applicazioni Azure o software in esecuzione in altre piattaforme cloud. Possono anche essere applicazioni in esecuzione all'esterno del cloud. Un esempio potrebbe essere una compagnia aerea che implementa servizi di prenotazione in computer all'interno del proprio data center. La compagnia aerea deve esporre tali servizi a molti clienti, ad esempio i banchi del check-in negli aeroporti, i terminali delle agenzie viaggio e perfino i telefoni cellulari degli utenti. A questo scopo può usare il bus di servizio, creando interazioni accoppiate liberamente tra le diverse applicazioni.

#### Argomenti del bus di servizio e sottoscrizioni
![Argomenti del bus di servizio di Azure](./media/fundamentals-introduction-to-azure/ServiceBusTopicsSubsIntroNew.png) *Figura: Argomenti del bus di servizio consente a più applicazioni di pubblicare messaggi e ad altre applicazioni di sottoscrivere la ricezione di messaggi che soddisfano requisiti specifici.*

Bus di servizio fornisce un meccanismo di pubblicazione e sottoscrizione denominato Argomenti e sottoscrizioni. Il meccanismo di pubblicazione-sottoscrizione consente a un'applicazione di inviare messaggi a un argomento, mentre altre applicazioni possono creare sottoscrizioni a tale argomento. Questo genera una comunicazione uno a molti tra un set di applicazioni che consente allo stesso messaggio di essere letto da più destinatari.

**Argomenti del bus di servizio e scenari di sottoscrizione**

Ogni volta che si individuano le aree in cui sono molti messaggi tutti importanti, ma vari sistemi di downstream devono solo essere in ascolto di diversi subset di tali comunicazioni, Argomenti e sottoscrizioni del bus di servizio costituisce una scelta appropriata.
  

### Servizi BizTalk
![Servizi BizTalk](./media/fundamentals-introduction-to-azure/BizTalkServicesIntroNew.png) *Figura: i Servizi BizTalk consentono di trasformare i formati dei messaggi XML nel cloud.*

A volte è necessario connettere sistemi che comunicano usando formati di messaggistica diversi. Spesso accade che le aziende abbiano schemi di database e formati di messaggistica XML diversi, anche quando è disponibile uno standard comune. Invece di scrivere un'elevata quantità di codice personalizzato, è possibile usare BizTalk Server in locale per integrare vari sistemi. Servizi BizTalk di Azure fornisce lo stesso tipo di servizio, ma nel cloud. È possibile pagare solo in base all'uso effettivo senza preoccuparsi della scalabilità come nel caso del servizio fornito in locale.
 

**Scenari di Servizi BizTalk**

Le interazioni business-to-business (B2B) in genere richiedono questo tipo di conversione. Un produttore di aeroplani, ad esempio, ha la necessità di ordinare parti dai suoi numerosi fornitori. Tali ordini devono essere automatizzati per passare direttamente dai sistemi del produttore di aeroplani ai sistemi dei fornitori. L'azienda non vuole modificare i propri sistemi di base e formati dei messaggi ed è molto improbabile che tali formati siano gli stessi. Servizi BizTalk può convertire questi messaggi tra i nuovi formati in entrambe le direzioni. Tale conversione può essere eseguita dal fornitore di aeroplani o dai vari fornitori, a seconda di chi desidera un controllo maggiore e della quantità di conversione necessaria.


## Assistenza per il calcolo
Azure fornisce assistenza per servizi che non devono essere eseguiti continuamente.

### Utilità di pianificazione

![Utilità di pianificazione di Azure](./media/fundamentals-introduction-to-azure/SchedulerIntroNew.png) *Figura: l'utilità di pianificazione di Azure consente di pianificare i processi in modo che vengano eseguiti in un momento specifico e per una durata specifica.*

A volte è necessario eseguire le applicazioni solo in un preciso momento. In Azure è possibile risparmiare con questo tipo di app invece di lasciare che un'applicazione venga eseguita 24 ore 24, 7 giorni su 7 in attesa dei dati da elaborare. L'utilità di pianificazione di Azure consente di pianificare l'esecuzione di un'applicazione in base a un intervallo di tempo o a un calendario. È affidabile e verificherà che un processo venga eseguito anche se sono presenti errori di rete, di computer e di data center. È possibile gestire queste azioni con L'API REST dell'utilità di pianificazione.

Quando si verifica un allarme pianificato, l'utilità di pianificazione invia messaggi HTTP o HTTPS a un endpoint specifico oppure può mettere un messaggio in una coda di archiviazione. Pertanto è necessario che l'applicazione abbia un endpoint accessibile o che monitori una coda di archiviazione. Quindi, una volta che riceve il messaggio, può eseguire qualsiasi azione per la quale è programmata.

**Scenari dell'utilità di pianificazione**

- Azioni ricorrenti delle applicazioni: un servizio potrebbe ad esempio ottenere periodicamente dati da Twitter e raccoglierli in un feed regolare.
- Manutenzione quotidiana: elaborazione o eliminazione dei registri, esecuzione di backup e altre attività di pianificazione eseguite a intermittenza.
- Attività eseguite di notte. 
- Per le applicazioni Web come l'eliminazione giornaliera dei registri è necessario eseguire attività di manutenzione quotidiane, ad esempio l'esecuzione di backup e altre attività di manutenzione. Un amministratore può ad esempio scegliere di eseguire il backup del database ogni giorno alle 13.00 per i 9 mesi successivi.

L'API dell'utilità di pianificazione consente di creare, aggiornare, eliminare, visualizzare e gestire raccolte di processi e processi pianificati a livello di codice.





## Prestazioni

Le prestazioni sono sempre importanti per un'applicazione. Le applicazioni tendono ad accedere ripetutamente sempre agli stessi dati. Per migliorare le prestazioni, è possibile mantenere una copia di quei dati più vicino all'applicazione, riducendo così il tempo necessario per recuperarli. A questo scopo, Azure fornisce diversi servizi.


### Servizio di memorizzazione nella cache di Azure

![Servizio di memorizzazione nella cache di Azure](./media/fundamentals-introduction-to-azure/AzureCacheIntroNew.png) **Figura: un'applicazione Azure può memorizzare i dati nella cache e perfino suddividerli in numerosi ruoli di lavoro**

L'accesso ai dati archiviati nei servizi di gestione dati di Azure, ovvero database SQL, tabelle o BLOB, è molto veloce. Ma l'accesso ai dati archiviati in memoria è ancora più rapido. Di conseguenza, mantenere una copia in memoria dei dati utilizzati di frequente consente di migliorare le prestazioni delle applicazioni. A questo scopo, è possibile usare il servizio Cache di Azure in memoria.


Un'applicazione Servizi cloud può archiviare i dati in questa cache e quindi recuperarli direttamente senza dover accedere all'archivio permanente. La cache può essere gestita all'interno delle macchine virtuali dell'applicazione o essere fornita da macchine virtuali dedicate esclusivamente alla memorizzazione nella cache. In entrambi i casi, la cache può essere distribuita con i dati in essa contenuti distribuiti tra più macchine virtuali in un data center di Azure.

Azure offre numerose tecnologie di cache che sono cambiate nel corso del tempo. Nell'ordine in cui sono state introdotte sono presenti una cache condivisa, una cache nel ruolo, una cache gestita e una cache Redis. La memorizzazione nella cache condivisa è una precedente tecnologia che non è necessario usare per creare nuove implementazioni. La cache gestita ha le stesse funzionalità della cache nel ruolo, ma come servizio gestito all'esterno del portale di gestione di Azure. La cache Redis è in anteprima. L'implementazione di Redis presenta il numero più elevato di funzionalità ed è consigliato quando si scrive nuovo codice di memorizzazione nella cache.


**Scenari della Cache di Azure**

Questo tipo di memorizzazione nella cache, ad esempio, potrebbe risultare utile a un'applicazione che legge ripetutamente un catalogo di prodotti, poiché i dati che le servono saranno disponibili più rapidamente. La tecnologia supporta inoltre il blocco che può essere utilizzato con i dati in lettura/scrittura e con i dati di sola lettura. Le applicazioni ASP.NET possono usare il servizio per archiviare i dati di sessione con una semplice modifica della configurazione.

### Rete per la distribuzione di contenuti (CDN)
![Rete CDN di Azure](./media/fundamentals-introduction-to-azure/CDNIntroNew.png) **Figura: è possibile memorizzare nella cache copie di un BLOB nei siti di tutto il mondo.**

Si supponga di dover archiviare dati BLOB ai quali accederanno utenti di tutto il mondo. Potrebbe trattarsi di un video dell'ultima Coppa del Mondo, di aggiornamenti di driver o di un e-book molto popolare. Archiviare una copia dei dati in più data center di Azure potrebbe essere utile, ma in presenza di un numero elevato di utenti potrebbe non essere sufficiente. Per usufruire di prestazioni ancora migliori, è possibile usare la rete CDN di Azure.

Nella rete CDN sono presenti decine di siti in tutto il mondo, in ognuno dei quali è possibile archiviare copie dei BLOB di Azure. La prima volta che un utente che si trova in una determinata parte del mondo accede a un BLOB specifico, le informazioni in esso contenute vengono copiate da una data center di Azure nell'archivio della rete CDN in quell'area geografica. Successivamente, tutti gli accessi da quella parte del mondo utilizzeranno la copia del BLOB memorizzata nella cache nella rete CDN e non verranno reindirizzati al data center di Azure più vicino. Ne risulta un accesso più rapido ai dati usati di frequente dagli utenti in qualsiasi parte del mondo.

**Scenari di rete CDN**

Accade spesso di usare CDN con Servizi multimediali per fornire contenuti video in tutto il mondo. Un video è in genere di grandi dimensioni e richiede una notevole quantità di larghezza di banda. Servizi media è citato in altre sezioni di questa pagina.



## Big Data e Big Compute

### HDInsight (Hadoop)
![HDInsight](./media/fundamentals-introduction-to-azure/HDInsightIntroNew.png) **Figura: HDInsight è utile per l'elaborazione in blocco di elevate quantità di dati**

Per molti anni, la maggior parte dell'analisi dei dati è stata eseguita su dati relazionali archiviati in un data warehouse creato con un DBMS relazionale. Questo tipo di analisi business è ancora importante e lo sarà anche per molti anni a venire. È tuttavia possibile che la quantità di dati che si desidera analizzare sia così elevata da non poter essere gestita dai database relazionali. I dati potrebbero inoltre non essere relazionali, ad esempio i log dei server in un data center oppure i dati cronologici relativi agli eventi di un sensore. In questi casi, si verifica un problema relativo ai Big Data. Occorre procedere con un approccio diverso.

La principale tecnologia utilizzata oggi per l'analisi dei Big Data è Hadoop. Progetto open source Apache, questa tecnologia archivia i dati tramite Hadoop Distributed File System (HDFS) e quindi consente agli sviluppatori di creare processi MapReduce per analizzarli. HDFS distribuisce i dati tra più server, quindi esegue blocchi del processo MapReduce in ognuno di essi per consentire l'elaborazione di Big Data in parallelo.

HDInsight è il nome del servizio basato su Hadoop Apache di Azure. HDInsight consente a HDFS di memorizzare dati nel cluster e di distribuirli tra più macchine virtuali. Distribuisce inoltre la logica di un processo MapReduce tra queste macchine virtuali. Come già accade con Hadoop in locale, i dati vengono elaborati localmente, ovvero la logica e i dati su cui questa funziona si trovano nella stessa macchina virtuale, e in parallelo per garantire prestazioni migliori. HDInsight consente inoltre di archiviare i dati nell'insieme di credenziali di Archiviazione di Azure che utilizza i BLOB. L'utilizzo dell'insieme di credenziali di Archiviazione di Azure genera risparmi significativi in quanto consente di eliminare il cluster HDInsight quando non è in uso pur continuando a mantenere i dati nel cloud.
 
HDinsight supporta anche altri componenti dell'ecosistema Hadoop, ad esempio Hive e Pig. Microsoft ha inoltre creato componenti che semplificano l'utilizzo dei dati creati da HDInsight mediante strumenti di business intelligence tradizionali, quali l'adattatore HiveODBC e il visualizzatore per i dati che funzionano con Excel.

### High-Performance Computing (Big Compute)

L'esecuzione di High-Performance Computing (HPC) e altre applicazioni "Big Compute" rappresenta uno dei modi più interessanti di usare una piattaforma cloud. Gli esempi includono le applicazioni di tecniche specializzate create per usare la Message Passing Interface (MPI) standard di settore nonché le cosiddette applicazioni "embarrassingly parallel", come i modelli di rischio finanziario.

L'essenza del Big Compute è l'esecuzione di codice su numerosi computer contemporaneamente. In Azure questo significa eseguire contemporaneamente molte macchine virtuali in parallelo per risolvere eventuali problemi. Questa operazione richiede risorse e una pianificazione delle applicazioni, ad esempio per distribuire il relativo lavoro tra tali istanze. Il pacchetto HPC gratuito di Microsoft e altre soluzioni cluster di calcolo possono raggiungere prestazioni elevate in Azure, usufruendo dei servizi di calcolo e infrastruttura di Azure per aggiungere capacità on demand a un cluster di calcolo locale o per eseguire applicazioni Big Compute interamente nel cloud.

Azure fornisce una gamma di dimensioni delle istanze delle macchine virtuali con configurazioni diverse di core CPU, memoria, capacità del disco e di altre caratteristiche per soddisfare i requisiti delle diverse applicazioni. Le istanze A8 e A9 introdotte di recente funzionano bene per molti carichi di lavoro con utilizzo intensivo di calcolo e, in particolare, per applicazioni MPI parallele, poiché sono caratterizzate da CPU multicore, grandi quantità di memoria e velocità elevata. In determinate configurazioni le istanze sfruttano una rete di applicazioni con bassa latenza e alta velocità che include la tecnologia Accesso diretto a memoria remota (RDMA, Remote Direct Memory Access) per ottenere la massima efficienza delle applicazioni MPI parallele.

Azure offre inoltre a sviluppatori e partner delle applicazioni Big Compute un set completo di funzionalità di calcolo, servizi, scelte di architettura e strumenti di sviluppo. Azure supporta flussi di lavoro di Big Compute personalizzati che comportano flussi di lavoro di dati specializzati, modelli di pianificazione di attività e processi scalabili a migliaia di core di calcolo.



## Contenuti multimediali

![Servizi multimediali di Azure](./media/fundamentals-introduction-to-azure/MediaServicesIntroNew.png) **Figura: Servizi multimediali è una piattaforma per le applicazioni che forniscono video e altri elementi multimediali a client in tutto il mondo.**

I video costituiscono gran parte del traffico Internet attuale e questa percentuale è destinata ad aumentare in futuro. Tuttavia, fornire video sul Web non è così semplice. Esistono numerose variabili, ad esempio l'algoritmo di codifica e la risoluzione dello schermo dell'utente. I video tendono inoltre a registrare picchi nella domanda, ad esempio il sabato sera quando molte persone decidono di guardare un film online.

È probabile che i video saranno inclusi in molte delle nuove applicazioni create. Poiché i problemi riscontrati da tali applicazioni saranno simili, è inutile che questi vengano risolti singolarmente per ognuna di esse. È quindi consigliabile creare una piattaforma in grado di fornire soluzioni comuni per molte applicazioni. Inoltre, la creazione di questa piattaforma nel cloud presenta alcuni vantaggi. Può essere disponibile per il pubblico con pagamento in base al consumo e consente anche di gestire la variabilità nella domanda spesso riscontrata dalle applicazioni video.

Servizi multimediali di Azure consente di risolvere questo problema. Offre un set di componenti cloud per semplificare le attività di coloro che creano ed eseguono applicazioni che utilizzano video e altri elementi multimediali.

Come illustrato nella figura, Servizi multimediali fornisce un set di componenti che utilizzano video e altri elementi multimediali. Include ad esempio un componente di inserimento video che consente di caricare i video in Servizi multimediali (archiviati nei BLOB di Azure), un componente di codifica che supporta vari formati audio e video, un componente di protezione del contenuto che offre funzionalità di Digital Rights Management, un componente per l'inserimento di annunci in un flusso video, componenti per lo streaming e altro ancora. I partner Microsoft possono inoltre fornire componenti per la piattaforma che verranno successivamente distribuiti e fatturati da Microsoft per conto dei partner.

Le applicazioni che utilizzano questa piattaforma possono essere eseguite in Azure o altrove. Ad esempio, un'applicazione desktop per una società di produzione video potrebbe consentire agli utenti di caricare video in Servizi multimediali e quindi elaborarli in vari modi. In alternativa, un servizio di gestione del contenuto basato sul cloud in esecuzione in Azure potrebbe utilizzare Servizi multimediali per l'elaborazione e la distribuzione dei video. Indipendentemente dalla posizione in cui viene eseguita e dalle operazioni che svolge, ogni applicazione sceglie i componenti da usare, accedendovi tramite interfacce RESTful.

Per distribuire ciò che produce, un'applicazione può utilizzare la rete CDN di Azure, un'altra rete CDN o inviare i bit direttamente agli utenti. Indipendentemente dal modo in cui vengono caricati, i video creati tramite Servizi multimediali possono essere utilizzati da vari sistemi client, tra cui Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash e Silverlight. L'obiettivo è semplificare la creazione di applicazioni multimediali moderne.

**Riferimenti**

Per una panoramica visiva del funzionamento di Servizi multimediali, scaricare il [poster di Servizi multimediali di Azure][Azure Media Services Poster].






## E-commerce

La diffusione della tecnologia SaaS (Software as a Service) sta trasformando il modo in cui si creano le applicazioni e anche il modo in cui queste vengono vendute. Poiché un'applicazione SaaS vive nel cloud, è normale che i potenziali clienti cerchino soluzioni online. Questo cambiamento si applica ai dati come alle applicazioni. Non è quindi strano che gli utenti si rivolgano al cloud per cercare set di dati disponibili per l'acquisto. Microsoft gestisce entrambi questi aspetti con [Azure Marketplace](http://datamarket.azure.com/) e [Azure Store](../articles/overview.md).

![E-commerce di Azure](./media/fundamentals-introduction-to-azure/CommerceIntroNew.png) **Figura: Azure Marketplace e Azure Store consentono di trovare e acquistare applicazioni e set di dati commerciali Azure e di usarli come parte delle applicazioni Azure.**

La differenza tra i due servizi sta nel fatto che il Marketplace si trova all'esterno del portale di gestione di Azure, mentre è possibile accedere al negozio direttamente dal portale. I clienti potenziali possono effettuare ricerche per trovare applicazioni Azure che soddisfano le loro esigenze. I clienti possono cercare set di dati commerciali, ad esempio dati demografici, dati finanziari, dati geografici e così via. Quando trovano qualcosa di interessante, possono accedervi sia dal fornitore, direttamente tramite i siti Web del Marketplace o del negozio oppure, in alcuni casi, direttamente dal portale di gestione. Le applicazioni possono inoltre usare l'API di ricerca Bing tramite il Marketplace per consentire agli utenti di accedere ai risultati delle ricerche Web.


**Scenari di e-commerce**

SendGrid è un'applicazione di Azure Store che consente di inviare e-mail. Offre funzionalità aggiuntive come la consegna affidabile e statistiche. È possibile acquistare questa applicazione e servizi correlati invece di provare a creare tale infrastruttura autonomamente.


## Introduzione

Dopo avere acquisito una panoramica generale, sarà possibile iniziare a scrivere la prima applicazione Azure. Scegliere la lingua, [scaricare l'SDK appropriato](/downloads/) e installarlo. Il cloud computing è la nuova piattaforma predefinita. Ora è possibile iniziare.


[Azure Media Services Poster]: http://azure.microsoft.com/documentation/infographics/media-services/

<!---HONumber=Sept15_HO2-->