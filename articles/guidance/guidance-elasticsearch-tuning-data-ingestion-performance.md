<properties
   pageTitle="Ottimizzazione delle prestazioni di inserimento dei dati con Elasticsearch in Azure | Microsoft Azure"
   description="Come ottimizzare le prestazioni di inserimento dei dati con Elasticsearch in Azure."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Ottimizzazione delle prestazioni di inserimento dei dati con Elasticsearch in Azure

Questo articolo fa [parte di una serie](guidance-elasticsearch.md).

## Panoramica

Determinare il modo migliore per strutturare il sistema in funzione dell'inserimento rapido ed efficiente di dati ricercabili, è un aspetto importante della creazione di un database di ricerca. Le considerazioni riguardanti questo requisito interessano non solo la scelta dell'infrastruttura in cui si implementa il sistema, ma anche le diverse ottimizzazioni che è possibile usare per garantire che il sistema possa supportare i livelli di afflusso di dati previsti.

Questo documento descrive le opzioni di distribuzione e configurazione da considerare per l'implementazione di un cluster Elasticsearch che prevede una frequenza di inserimento dei dati elevata. Per fornire dati validi a scopo illustrativo, questo documento descrive anche i risultati del benchmarking di varie configurazioni usando un semplice carico di lavoro di inserimento dei dati con volumi elevati. I dettagli del carico di lavoro sono descritti nell'[Appendice](#appendix-the-bulk-load-data-ingestion-performance-test) alla fine di questo documento.

Lo scopo dei benchmark non è quello di generare dati assoluti sulle prestazioni per l'esecuzione di Elasticsearch o anche per suggerire una topologia particolare, bensì per illustrare i metodi che è possibile usare per valutare le prestazioni, ridimensionare i nodi dati e implementare cluster che possano soddisfare i requisiti delle prestazioni.

Quando si ridimensionano i sistemi, è importante eseguire accuratamente il test delle prestazioni in base ai propri carichi di lavoro. Raccogliere dati di telemetria che consentano di ottenere informazioni sulla configurazione hardware ottimale da usare e i fattori di scalabilità orizzontale da considerare. In particolare, è necessario:

- Considerare le dimensioni complessive del payload inviato e non solo il numero di elementi in ogni richiesta di inserimento in blocco. Un numero inferiore di elementi in blocco di grandi dimensioni in ogni richiesta può essere più appropriato di un numero maggiore, a seconda della risorsa disponibile per elaborare ogni richiesta.

È possibile monitorare gli effetti derivanti dalla variazione della richiesta di inserimento in blocco con Marvel, usando i contatori di I/O *readbytes*/*writebytes* con JMeter e strumenti del sistema operativo quali *iostat* e *vmstat* in Ubuntu.

- Eseguire il test delle prestazioni e raccogliere dati di telemetria per misurare i tempi di elaborazione della CPU e di attesa di I/O, la latenza del disco, la velocità effettiva e i tempi di risposta. Queste informazioni possono risultare utili per identificare i colli di bottiglia potenziali e valutare i costi e i vantaggi che derivano dall'uso dell'Archiviazione Premium. Tenere presente che l'utilizzo della CPU e del disco potrebbe non essere uniforme tra tutti i nodi, a seconda della modalità di distribuzione di partizioni e repliche nel cluster, ovvero alcuni nodi possono contenere più partizioni di altri.

- Considerare come verrà distribuito nel cluster il numero di richieste simultanee per il carico di lavoro e valutare l'impatto dell'uso di un numero di nodi diverso per gestire il carico di lavoro.

- Considerare come possono aumentare i carichi di lavoro con l'espansione dell'azienda. Valutare l'impatto di tale crescita sui costi delle macchine virtuali e delle risorse di archiviazione usate dai nodi.

- Comprendere che l'uso di un cluster con un maggior numero di nodi con dischi normali può essere più conveniente se il proprio scenario necessita di un numero di richieste elevato e l'infrastruttura dei dischi mantiene una velocità effettiva che soddisfa i contratti di servizio. Tuttavia, l'aumento del numero di nodi può introdurre un sovraccarico sotto forma di comunicazioni tra i nodi e sincronizzazione aggiuntive.

- Comprendere che un maggior numero di core per ogni nodo può generare più traffico su disco, perché possono essere elaborati più documenti. In questo caso, misurare l'utilizzo del disco per verificare se il sottosistema di I/O può diventare un collo di bottiglia e determinare i vantaggi dell'uso dell'Archiviazione Premium.

- Testare e analizzare i compromessi per un numero più elevato di nodi con un minor numero di core, rispetto a meno nodi con più core. Tenere presente che l'aumento del numero di repliche causa un escalation delle richieste al cluster e potrebbe essere necessario aggiungere nodi.

- Considerare che l'uso di dischi temporanei può richiedere un recupero più frequente degli indici.

- Misurare l'utilizzo del volume di archiviazione per valutare la capacità e il sottoutilizzo della risorsa di archiviazione. Ad esempio, in questo scenario sono stati archiviati 1,5 miliardi di documenti usando una risorsa di archiviazione di 350 GB.

- Misurare le velocità di trasferimento per i carichi di lavoro e considerare qual è la probabilità di raggiungere il limite totale di velocità di trasferimento di I/O per un account di archiviazione specificato in cui sono stati creati dischi virtuali.

## Progettazione di indici e nodi

In un sistema che deve supportare l'inserimento di dati su larga scala è opportuno porsi le domande seguenti:

- **I dati cambiano rapidamente o sono relativamente statici?** A una maggiore dinamicità dei dati corrisponde un maggiore sovraccarico di manutenzione per Elasticsearch. Se i dati vengono replicati, ogni replica viene gestita in modo sincrono. Per i dati che cambiano rapidamente e hanno solo una durata limitata o che possono essere ricostruiti facilmente, può risultare utile disattivare completamente la replica. Questa opzione è discussa più avanti nella sezione [Ottimizzazione dell'inserimento dei dati su larga scala](#tuning-large-scale-data-ingestion).

- **Con quale frequenza devono essere aggiornati i dati individuati eseguendo una ricerca?** Per garantire prestazioni ottimali, Elasticsearch memorizza nel buffer quanti più dati possibile. Ciò significa che non tutte le modifiche sono immediatamente disponibili per le richieste di ricerca. Il processo mediante il quale Elasticsearch rende le modifiche persistenti e visibili è illustrato nel documento online che descrive come [rendere persistenti le modifiche](https://www.elastic.co/guide/en/elasticsearch/guide/current/translog.html#translog).

    La velocità con cui dati diventano visibili è regolata dall'impostazione*refresh\_interval* dell'indice interessato. Per impostazione predefinita, questo intervallo è impostato su 1 secondo. Tuttavia, non tutte le situazioni richiedono che gli aggiornamenti avvengano così rapidamente. Ad esempio, è possibile che gli indici che registrano i dati di log debbano gestire un afflusso rapido e continuo di informazioni che devono essere inserite rapidamente, ma che non devono necessariamente essere subito disponibili per l'esecuzione di query. In questo caso, provare a ridurre la frequenza degli aggiornamenti. Questa funzionalità è descritta anche nella sezione [Ottimizzazione dell'inserimento dei dati su larga scala](#tuning-large-scale-data-ingestion).

- **Con quale rapidità è prevedibile un aumento dei dati?** La capacità dell'indice è determinata dal numero di partizioni specificato al momento della creazione dell'indice. Per tenere conto della crescita, specificare un numero sufficiente di partizioni. Il valore predefinito è 5. Se l'indice viene creato inizialmente per un singolo nodo, le cinque partizioni si troveranno tutte in quel nodo, ma è possibile che con l'aumento del volume dei dati vengano aggiunti altri nodi. In questo caso, Elasticsearch distribuirà in modo dinamico le partizioni tra i nodi. Ogni partizione è tuttavia sottoposta a un sovraccarico, perché le query per tutte le ricerche in un indice saranno eseguite su tutte le partizioni, quindi la creazione di un numero elevato di partizioni per una piccola quantità di dati può rallentare il recupero dei dati. Evitare lo scenario con un'[enorme quantità di partizioni](https://www.elastic.co/guide/en/elasticsearch/guide/current/kagillion-shards.html).

    Alcuni carichi di lavoro, ad esempio la registrazione, possono creare un nuovo indice ogni giorno e se si osserva che il numero di partizioni è insufficiente per il volume di dati, è consigliabile modificarlo prima di creare l'indice successivo. Gli indici esistenti non saranno interessati. Se è necessario distribuire i dati esistenti in più partizioni, è possibile reindicizzare le informazioni. Creare un nuovo indice con la configurazione appropriata e copiarvi i dati. Questo processo può essere reso trasparente alle applicazioni con [alias di indice](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html).

- **È necessario partizionare i dati tra gli utenti in uno scenario multi-tenancy?** È possibile creare indici separati per ogni utente, ma questa scelta può risultare costosa se ogni utente ha solo una quantità di dati moderata. Considerare invece la possibilità di creare [indici condivisi ](https://www.elastic.co/guide/en/elasticsearch/guide/current/shared-index.html) e di usare [alias basati su filtri](https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html) per indirizzare le richieste ai dati per ogni utente. Per mantenere insieme i dati di un utente nella stessa partizione, ignorare la configurazione di routing predefinita per l'indice e instradare i dati in base a un attributo di identificazione dell'utente.

- **I dati sono di lunga o breve durata?** Se si usa un set di macchine virtuali di Azure per implementare un cluster Elasticsearch, è possibile archiviare i dati temporanei in un disco di sistema di risorse locale invece di un'unità collegata. L'uso dello SKU di una VM che utilizza un'unità SSD per il disco di risorse può migliorare le prestazioni di I/O. Tuttavia, le informazioni contenute nel disco di risorse sono temporanee e possono andare perse se la VM viene riavviata. Per altre informazioni, vedere la sezione relativa alla perdita dei dati su un'unità temporanea nel documento di [informazioni sull'unità temporanea in Macchine virtuali di Microsoft Azure](http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx). Se si vogliono mantenere i dati tra un riavvio e l'altro, creare dischi dati per conservare queste informazioni e collegarli alla macchina virtuale.

- **Qual è il livello di attività dei dati?** I VHD di Azure sono soggetti a limitazione se la quantità di attività di lettura/scrittura supera i parametri specificati. Attualmente 500 IOPS per un disco collegato a una macchina virtuale con un piano Standard e 5000 IOPs per un disco di Archiviazione Premium.

    Per ridurre le probabilità di limitazione e migliorare le prestazioni di I/O, è consigliabile creare più dischi dati per ogni VM e configurare Elasticsearch per lo striping dei dati tra questi dischi, come descritto nella sezione [Requisiti del disco e del file system](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements).

    È consigliabile selezionare una configurazione hardware che consenta di ridurre al minimo il numero di operazioni di I/O in lettura, assicurando la disponibilità di memoria sufficiente per memorizzare nella cache i dati a cui si accede di frequente. Questo processo è descritto nella sezione [Requisiti di memoria](guidance-elasticsearch-running-on-azure.md#memory-requirements) del documento che illustra l'implementazione di Elasticsearch in Azure.

- **Che tipo di carico di lavoro dovrà essere supportato da ogni nodo?** Elasticsearch trae vantaggio dalla disponibilità di memoria per la memorizzazione dei dati nella cache, sotto forma di cache del file system, e per l'heap JVM, come descritto nella sezione [Requisiti di memoria](guidance-elasticsearch-running-on-azure.md#memory-requirements) del documento che illustra l'implementazione di Elasticsearch in Azure.

    La quantità di memoria, il numero di core CPU e la quantità di dischi disponibili sono limitate dallo SKU della macchina virtuale. Per altre informazioni, vedere la pagina [Prezzi di Macchine virtuali](http://azure.microsoft.com/pricing/details/virtual-machines/) nel sito Web di Azure.

### Opzioni della macchina virtuale

È possibile effettuare il provisioning di macchine virtuali in Azure con più SKU diversi. Le risorse disponibili per una VM di Azure dipendono dallo SKU selezionato. Ogni SKU offre una combinazione diversa di core, memoria e archiviazione. È necessario selezionare dimensioni appropriate della VM che gestirà il carico di lavoro previsto, che però risultino anche economicamente convenienti. Iniziare con una configurazione che soddisfi i requisiti correnti. Effettuare un benchmark per eseguire il test, come descritto più avanti in questo documento. È possibile ridimensionare un cluster in un secondo momento aggiungendo più VM che eseguono nodi di Elasticsearch.

La pagina [Dimensioni delle macchine virtuali](virtual-machines-size-specs/) nel sito Web di Azure documenta la diverse opzioni e gli SKU disponibili per le VM.

È opportuno che le dimensioni e le risorse di una macchina virtuale corrispondano al ruolo che avranno i nodi in esecuzione nella VM.

Per un nodo dati:

- Allocare fino a 30 GB o il 50% della memoria RAM disponibile all'heap Java, usando il valore più basso. Lasciare il resto al sistema operativo da usare per la memorizzazione dei file nella cache. Se si usa Linux, è possibile specificare la quantità di memoria da allocare per l'heap Java, impostando la variabile di ambiente ES\_HEAP\_SIZE prima di eseguire Elasticsearch. In alternativa, se si usa Windows o Linux, si possono stabilire le dimensioni della memoria con i parametri *Xmx* e *Xms* quando si avvia Elasticsearch.

    A seconda del carico di lavoro, un numero inferiore di VM di grandi dimensioni può non essere altrettanto efficace ai fini delle prestazioni rispetto all'uso di un numero maggiore di VM di dimensioni limitate. È consigliabile eseguire test che consentano di valutare i compromessi tra il traffico di rete aggiuntivo e la manutenzione richiesta rispetto ai costi per l'aumento del numero di core disponibili e la minore contesa del disco in ogni nodo.

- Usare Archiviazione Premium per l'archiviazione dei dati di Elasticsearch. Questo argomento è discusso più dettagliatamente nella sezione [Opzioni di archiviazione](#storage-options).

- Usare più dischi con le stesse dimensioni ed eseguire lo striping dei dati tra questi dischi. Lo SKU delle VM determinerà il numero massimo di dischi dati che si possono collegare. Per altre informazioni, vedere la sezione [Requisiti del disco e del file system](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements).

- Usare uno SKU con CPU multicore, con almeno 2 core, ma preferibilmente 4 o più.

Per un nodo client:

- Non allocare spazio di archiviazione su disco per i dati di Elasticsearch, perché i client dedicati non archiviano dati su disco.

- Assicurarsi che sia disponibile una quantità di memoria adeguata per gestire i carichi di lavoro. Le richieste di inserimento in blocco vengono lette in memoria prima dell'invio dei dati ai diversi nodi dati e i risultati delle aggregazioni e delle query vengono accumulati in memoria prima di essere restituiti all'applicazione client. Effettuare un benchmark dei propri carichi di lavoro e monitorare l'uso della memoria con uno strumento come Marvel o le [informazioni su JVM](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_jvm_section) restituite tramite l'API *node/stats* (`GET _nodes/stats`) per valutare i requisiti ottimali: In particolare, monitorare la metrica *heap\_used\_percent* per ogni nodo allo scopo di mantenere le dimensioni dell'heap inferiori al 75% dello spazio disponibile.

- Assicurarsi che siano disponibili core CPU sufficienti per ricevere ed elaborare il volume di richieste previsto. Le richieste vengono accodate alla ricezione prima dell'elaborazione e il volume degli elementi che possono essere accodati è in funzione del numero di core CPU in ogni nodo. È possibile monitorare la lunghezza della coda usando i dati nelle [informazioni sui pool di thread ](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_threadpool_section) restituite con l'API node/stats.

    Se il numero di elementi *rifiutati* per una coda indica che le richieste vengono rifiutate, ciò significa che nel cluster iniziano a verificarsi colli di bottiglia. Questa condizione potrebbe essere dovuta alla larghezza di banda della CPU, ma anche ad altri fattori, come la mancanza di memoria o la riduzione delle prestazioni di I/O. Usare quindi queste informazioni in combinazione con altre statistiche per determinare la causa radice.

    I nodi client possono essere necessari o meno, a seconda dei carichi di lavoro. I carichi di lavoro di inserimento dei dati tendono a non trarre vantaggio dall'uso di client dedicati, dove alcune ricerche e aggregazioni possono essere eseguite più rapidamente. Potrà essere necessario effettuare un benchmark dei propri scenari.

    I nodi client sono sopratutto utili per le applicazioni che usano l'API Transport Client per connettersi al cluster. È anche possibile usare l'API Node Client che crea dinamicamente un client dedicato per l'applicazione usando le risorse dell'ambiente host dell'applicazione. Se le applicazioni usano l'API Node Client, può non essere necessario che il cluster contenga nodi client dedicati preconfigurati.
    
    Si tenga però presente che un nodo creato con l'API Client Node è un membro importante del cluster e come tale partecipa alla comunicazione di rete con altri nodi. L'avvio e l'arresto frequenti dei nodi client può creare un inutile disturbo nell'intero cluster.

Per un nodo master:

- Non allocare spazio di archiviazione su disco per i dati di Elasticsearch, perché i nodi master dedicati non archiviano dati su disco.

- I requisiti della CPU dovranno essere minimi.

- I requisiti di memoria dipendono dalle dimensioni del cluster. Le informazioni sullo stato del cluster vengono mantenute in memoria. Per i cluster di piccole dimensioni la quantità di memoria necessaria è minima, ma per un cluster di grandi dimensioni e molto attivo in cui vengono creati indici frequentemente e le partizioni vengono spostate, la quantità di informazioni sullo stato può aumentare significativamente. Monitorare le dimensioni dell'heap JVM per determinare se è necessario aggiungere altra memoria.

> [AZURE.NOTE]  Per l'affidabilità del cluster, è opportuno creare sempre più nodi master e configurare i nodi rimanenti per evitare la possibilità che si verifichi una condizione split brain. Idealmente, deve essere presente un numero dispari di nodi master. Questo argomento è descritto in dettaglio nel documento [Configurazione delle opzioni di resilienza e ripristino di Elasticsearch in Azure][].

### Opzioni di archiviazione

Sono disponibili numerose opzioni di archiviazione nelle VM di Azure, con diversi compromessi che interessano costi, prestazioni, disponibilità e ripristino e che occorre considerare con attenzione.

Si noti che è necessario memorizzare i dati di Elasticsearch su dischi dati dedicati. Si ridurrà così la contesa con il sistema operativo, assicurando che volumi elevati di I/O di Elasticsearch non entrino in conflitto con le funzioni del sistema operativo per le risorse di I/O.

I dischi di Azure sono soggetti a vincoli relativi alle prestazioni. Se si rileva che un cluster è soggetto periodicamente ad aumenti improvvisi di attività, è possibile limitare le richieste di I/O. Per evitare che ciò avvenga, ottimizzare la progettazione per bilanciare le dimensioni dei documenti in Elasticsearch rispetto al volume di richieste che sarà probabilmente ricevuto da ogni disco.

I dischi basati sull'archiviazione Standard supportano una frequenza massima di richieste di 500 IOPS, mentre i dischi basati sull'Archiviazione Premium possono funzionare a un massimo di 5.000 IOPS. I dischi di Archiviazione Premium sono disponibili solo per le VM serie DS e GS. Le frequenze massime di IOPS del disco per le [macchine virtuali di Azure sono documentate online](virtual-machines-size-specs/).

**Dischi dati persistenti**

I dischi dati persistenti sono dischi rigidi virtuali (VHD) supportati da Archiviazione di Azure. Se la VM deve essere ricreata dopo un errore grave, i VHD esistenti possono essere collegati facilmente alla nuova VM. È possibile creare VHD in base all'archiviazione Standard (supporti in rotazione) o all'Archiviazione Premium (unità SSD). Se si vogliono usare unità SSD, è necessario creare macchine virtuali serie DS o più avanzata. Il costo delle macchine virtuali serie DS è lo stesso delle VM serie D, ma viene addebitato un costo aggiuntivo per l'uso dell'Archiviazione Premium.

Nei casi in cui la velocità di trasferimento massima per ogni disco è insufficiente per supportare il carico di lavoro previsto, considerare la possibilità di creare più dischi dati e consentire a Elasticsearch di [eseguire lo striping dei dati tra questi dischi](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements) oppure implementare [striping RAID 0 con dischi virtuali](virtual-machines-linux-configure-raid/) a livello di sistema.

> [AZURE.NOTE] L'esperienza all'interno di Microsoft ha dimostrato che l'uso di RAID 0 è particolarmente utile per uniformare gli effetti dell'I/O dei carichi di lavoro di *picco* che generano aumenti improvvisi di attività.

Usare l'Archiviazione Premium con ridondanza locale o l'archiviazione con ridondanza locale per carichi di lavoro di fascia bassa o per il controllo di qualità per l'account di archiviazione che contiene i dischi. La replica tra aree geografiche e zone non è necessaria per la disponibilità elevata di Elasticsearch.

**Dischi temporanei**

L'uso di dischi persistenti basati su unità SSD richiede la creazione di VM che supportano l'Archiviazione Premium. Questa scelta influisce sul prezzo. L'uso del disco temporaneo locale per contenere dati di Elasticsearch può essere una soluzione conveniente per i nodi di dimensioni limitate che richiedono un massimo di circa 800 GB di spazio di archiviazione. Nelle VM serie Standard-D i dischi temporanei vengono implementati con unità SSD che forniscono prestazioni di gran lunga migliori e una latenza molto più bassa rispetto ai dischi normali.

Quando si usa Elasticsearch, le prestazioni possono essere equivalenti all'uso dell'Archiviazione Premium senza costi aggiuntivi. Per altre informazioni, vedere la sezione [Risoluzione dei problemi di latenza del disco](#addressing-disk-latency-issues).

Le dimensioni della VM limitano la quantità di spazio disponibile nell'archiviazione temporanea, come descritto nel documento relativo alle [aspettative in termini di prestazioni delle VM serie D](https://azure.microsoft.com/blog/d-series-performance-expectations/).

Ad esempio, una VM Standard\_D1 fornisce 50 GB di spazio di archiviazione temporaneo, una VM Standard\_D2 ha 100 GB di spazio di archiviazione temporaneo e una VM Standard\_D14 fornisce 800 GB di spazio temporaneo. Per i cluster in cui i nodi richiedono solo questa quantità di spazio, l'uso di una VM serie D con archiviazione temporanea può essere economicamente conveniente.

È necessario bilanciare la velocità effettiva maggiore disponibile con l'archiviazione temporanea rispetto al tempo e ai costi richiesti per il recupero di dati dopo il riavvio di una macchina virtuale. Il contenuto del disco temporaneo viene perso se si sposta la VM in un server host diverso, se l'host viene aggiornato o se nell'host si verifica un errore hardware. Se i dati stessi hanno una durata limitata, la perdita di dati può essere tollerabile. Per i dati di durata maggiore si potrebbe riuscire a ricompilare un indice o ripristinare le informazioni mancanti da un backup. È possibile ridurre al minimo le possibilità di perdita dei dati usando repliche mantenute in altre VM.

> [AZURE.NOTE] Non è consigliabile usare una **singola** VM per mantenere i dati di produzione critici. In caso di errore di un nodo, tutti i dati non saranno disponibili. Per le informazioni critiche, assicurarsi che i dati vengano replicati almeno in un altro nodo.

**File di Azure**

Il [Servizio File di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) fornisce l'accesso ai file condivisi tramite Archiviazione di Azure. È possibile creare condivisioni file che possono poi essere montate nelle VM di Azure. Più macchine virtuali possono montare la stessa condivisione file, abilitando quindi l'accesso agli stessi dati.

Per motivi di prestazioni, non è consigliabile usare condivisioni file per il mantenimento dei dati di Elasticsearch che non devono essere condivisi tra più nodi. I dischi dati normali sono più adatti a questo scopo. È possibile usare condivisioni file per la creazione di [indici di replica shadow](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-shadow-replicas.html) di Elasticsearch. Tuttavia, questa funzionalità è attualmente in fase sperimentale e al momento non deve essere implementata in un ambiente di produzione. Per questo motivo, gli indici shadow non saranno trattati oltre in questo documento.

**Opzioni di rete**

Azure implementa uno schema di rete condiviso. Le VM che usano gli stessi rack hardware si contendono le risorse di rete. La larghezza di banda disponibile può quindi variare in base all'ora del giorno e al ciclo di lavoro quotidiano in esecuzione nelle VM che condividono la stessa infrastruttura di rete fisica. Il controllo su questi fattori è molto limitato. È importante comprendere che le prestazioni della rete sono soggette a fluttuazioni nel tempo, quindi è opportuno stabilire di conseguenza le aspettative dell'utente.

## Aumento delle prestazioni dei nodi per supportare l'inserimento dei dati su larga scala

È possibile creare cluster Elasticsearch con hardware ragionevolmente limitato e quindi aumentare le prestazioni o aumentare le istanze in base alla crescita del volume di dati e all'aumento del numero di richieste. Con Azure è possibile aumentare le prestazioni con l'esecuzione su macchine virtuali più grandi e più costose oppure aumentare le istanze usando altre VM più piccole e più economiche.

È anche possibile eseguire una combinazione di entrambe le strategie. Non esiste alcuna soluzione universale per tutti gli scenari, quindi per valutare l'approccio migliore per qualsiasi tipo di situazione, è necessario essere pronti a intraprendere una serie di test delle prestazioni.

Questa sezione riguarda l'approccio relativo all'aumento delle prestazioni, mentre l'aumento delle istanze è descritto nella sezione [Aumento delle istanze: conclusioni](#scaling-out-conclusions). In questa sezione vengono descritti i risultati di una serie di benchmark eseguiti su un set di cluster Elasticsearch costituito da VM con diverse dimensioni. I cluster sono stati progettati con dimensioni piccole, medie e grandi. La tabella seguente riepiloga le risorse allocate alle VM in ogni cluster.

| Cluster | SKU di VM | Numero di core | Numero di dischi dati | RAM |
|---------|-------------|-----------------|----------------------|------|
| Piccolo | Standard D2 | 2 | 4 | 7 GB |
| Medio | Standard D3 | 4 | 8 | 14 GB |
| Grande | Standard D4 | 8 | 16 | 28 GB |

Ogni cluster Elasticsearch includeva 3 nodi dati. Questi nodi di dati hanno gestito le richieste dei client, nonché l'elaborazione dei dati. Non sono stati usati nodi client distinti, perché offrivano pochi vantaggi per lo scenario di inserimento di dati usato nei test. Il cluster includeva anche tre nodi master, uno dei quali è stato scelto da Elasticsearch per coordinare il cluster.

I test sono stati eseguiti con Elasticsearch 1.7.3. I test sono stati eseguiti inizialmente sui cluster che eseguono Ubuntu Linux 14.0.4 e quindi sono stati ripetuti con Windows Server 2012. I dettagli del carico di lavoro eseguito dai test sono descritti nell'[Appendice](#appendix-the-bulk-load-data-ingestion-performance-test).

### Prestazioni di inserimento dei dati: Ubuntu Linux 14.0.4

La tabella seguente riepiloga i risultati complessivi dell'esecuzione dei test per due ore per ogni configurazione:

| Configurazione | N. campioni | Tempo medio di risposta (ms) | Velocità effettiva (operazioni/sec) |
|---------------|-----------|----------------------------|---------------------------|
| Piccola | 67057 | 636 | 9,3 |
| Media | 123482 | 692 | 17,2 |
| Grande | 197085 | 839 | 27,4 |

La velocità effettiva e il numero di campioni elaborati per le tre configurazioni hanno un rapporto approssimativo 1:2:3. Tuttavia, le risorse disponibili in termini di memoria, core CPU e dischi hanno il rapporto 1:2:4. Si è ritenuto utile esaminare i dettagli delle prestazioni di basso livello dei nodi del cluster per valutare il motivo di questo risultato. Queste informazioni possono essere utili per determinare se esistono limiti per l'aumento delle prestazioni e quando può essere preferibile prendere in considerazione l'aumento delle istanze.

### Determinazione dei fattori limitanti: utilizzo della rete

Elasticsearch è dipendente dalla disponibilità di larghezza di banda sufficiente per supportare l'afflusso di richieste client, nonché il flusso di informazioni di sincronizzazione tra i nodi del cluster. Come evidenziato in precedenza, si ha un controllo limitato della disponibilità di larghezza di banda, che dipende da numerose variabili, ad esempio il data center in uso e l'attuale carico di rete di altre VM che condividono la stessa infrastruttura di rete. Vale comunque la pena esaminare l'attività di rete per ogni cluster anche solo per verificare che il volume di traffico non sia eccessivo. Il grafico seguente mostra un confronto del traffico di rete ricevuto dal nodo 2 in ogni cluster. I volumi per gli altri nodi in ogni cluster erano molto simili.

![](media/guidance-elasticsearch/data-ingestion-image1.png)

Ecco la media dei byte ricevuti al secondo per il nodo 2 in ogni configurazione di cluster nel periodo di due ore:

| Configurazione | Numero medio di byte ricevuti/sec |
|---------------|--------------------------------------|
| Piccola | 3993640,3 |
| Media | 7311689,9 |
| Grande | 11893874,2 |

I test sono stati eseguiti mentre lo stato di esecuzione del sistema era **stazionario**. Nelle situazioni in cui si verifica un ribilanciamento dell'indice o il ripristino dei nodi, le trasmissioni dei dati tra i nodi che includono partizioni primarie e di replica possono generare un traffico di rete significativo. Gli effetti di questo processo sono descritti anche nel documento [Configurazione delle opzioni di resilienza e ripristino di Elasticsearch in Azure][].

### Determinazione dei fattori limitanti: utilizzo della CPU

La frequenza con cui le richieste vengono gestite è disciplinata almeno in parte dalla capacità di elaborazione disponibile. Elasticsearch accetta le richieste di inserimento in blocco nella coda corrispondente. Ogni nodo ha un set code di inserimento in blocco che dipende dal numero di processori disponibili. Per impostazione predefinita, è disponibile una coda per ogni processore e ogni coda può contenere fino a 50 richieste in attesa prima di iniziare a rifiutarle.

Le applicazioni devono inviare le richieste con una frequenza che non causi l'overflow delle code. Il numero di elementi in ogni coda in un momento qualsiasi sarà in funzione della frequenza con cui le richieste vengono inviate dalle applicazioni client e della frequenza con cui le stesse richieste vengono recuperate ed elaborate da Elasticsearch. Per questo motivo, un'importante statistica acquisita riguarda la percentuale di errore riepilogata nella tabella seguente.

| Configurazione | Campioni totali | N. di errori | Percentuale di errore |
|---------------|---------------|-----------|------------|
| Piccola | 67057 | 0 | 0,00% |
| Media | 123483 | 1 | 0,0008% |
| Grande | 200702 | 3617 | 1,8 % |

Ognuno di questi errori è stato causato dall'eccezione Java seguente:

```
org.elasticsearch.action.support.replication.TransportShardReplicationOperationAction$PrimaryPhase$1@75a30c1b]; ]
[219]: index [systembase], type [logs], id [AVEAioKb2TRSNcPa_8YG], message [RemoteTransportException[[esdatavm2][inet[/10.0.1.5:9300]][indices:data/write/bulk[s]]]; nested: EsRejectedExecutionException[rejected execution (queue capacity 50)
```

L'aumento del numero di code e/o della lunghezza di ogni coda può ridurre il numero di errori, ma questo approccio può supportare solo picchi di breve durata. Procedendo in questo modo durante l'esecuzione di una serie sostenuta di attività di inserimento dei dati ritarderà semplicemente il punto in cui inizieranno a verificarsi errori. Inoltre, questa modifica non migliorerà la velocità effettiva e probabilmente danneggerà il tempo di risposta delle applicazioni client, perché le richieste vengono accodate più a lungo prima di essere elaborate.

La struttura dell'indice predefinito di 5 partizioni con 1 replica (10 partizioni in totale), genera un modesto sbilanciamento del carico tra i nodi in un cluster. Due nodi includeranno 3 partizioni, mentre l'altro nodo ne includerà 4. Il nodo più attivo è probabilmente l'elemento che limita maggiormente la velocità effettiva e questo è il motivo per cui questo nodo è stato selezionato in ogni caso.

Il set di grafici seguente illustra l'utilizzo della CPU per il nodo più attivo in ogni cluster.

![](media/guidance-elasticsearch/data-ingestion-image2.png)

![](media/guidance-elasticsearch/data-ingestion-image3.png)

![](media/guidance-elasticsearch/data-ingestion-image4.png)

Per i cluster piccolo, medio e grande l'utilizzo medio della CPU per questi nodi è stato di 75,01%, 64,93% e 64,64%. Raramente l'utilizzo raggiunge effettivamente il 100% e si riduce con l'aumento delle dimensioni dei nodi e della potenza della CPU disponibile. È quindi improbabile che la potenza della CPU sia un fattore limitante delle prestazioni del cluster di grandi dimensioni.

### Determinazione dei fattori limitanti: memoria

L'uso della memoria è un altro aspetto importante che può influenzare le prestazioni. Per i test, a Elasticsearch è stato allocato il 50% della memoria disponibile. Questa percentuale è in linea con le [raccomandazioni documentate](https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html#_give_half_your_memory_to_lucene). Durante l'esecuzione dei test è stata monitorata la JVM per un'attività di Garbage Collection eccessiva, un'indicazione che la memoria heap è insufficiente. In tutti i casi, le dimensioni dell'heap erano stabili e l'attività di Garbage Collection delle JVM è risultata limitata. La schermata nella figura seguente mostra uno snapshot di Marvel, che evidenzia le statistiche principali della JVM per un breve periodo durante l'esecuzione del test sul cluster di grandi dimensioni.

![](media/guidance-elasticsearch/data-ingestion-image5.png)

***Memoria e attività di Garbage Collection della JVM nel cluster di grandi dimensioni.***

### Determinazione dei fattori limitanti: velocità di I/O del disco

La funzionalità fisica rimanente sul lato server che potrebbe vincolare le prestazioni sono le prestazioni del sottosistema di I/O del disco. Il grafico seguente confronta l'attività del disco in termini di byte scritti per i nodi più attivi in ogni cluster.

![](media/guidance-elasticsearch/data-ingestion-image6.png)

La tabella seguente mostra la media dei byte scritti al secondo per il nodo 2 in ogni configurazione di cluster nel periodo di due ore:

| Configurazione | Numero medio di byte scritti/sec |
|---------------|-------------------------------------|
| Piccola | 25502361,94 |
| Media | 48856124,5 |
| Grande | 88137675,46 |

Il volume dei dati scritti aumenta con il numero di richieste elaborate da un cluster, ma le velocità di I/O rientrano nei limiti di Archiviazione di Azure, ovvero i dischi creati con l'Archiviazione di Azure possono supportare una velocità sostenuta da decine a centinaia di MB/s, a seconda che venga usata l'archiviazione Standard o Premium. L'analisi della quantità di tempo trascorso in attesa dell'I/O del disco consente di spiegare il motivo per cui la velocità effettiva del disco sia ben al di sotto del massimo teorico. I grafici e la tabella seguenti mostrano le statistiche per gli stessi tre nodi:

> [AZURE.NOTE] Il tempo di attesa del disco viene misurato monitorando la percentuale di tempo di CPU durante il quale i processori vengono bloccati in attesa del completamento delle operazioni di I/O.

![](media/guidance-elasticsearch/data-ingestion-image7.png)

![](media/guidance-elasticsearch/data-ingestion-image8.png)

![](media/guidance-elasticsearch/data-ingestion-image9.png)

| Configurazione | Tempo di CPU medio di attesa del disco (%) |
|---------------|--------------------------------|
| Piccola | 21,04 |
| Media | 14,48 |
| Grande | 15,84 |

Questi dati indicano che una percentuale significativa del tempo di CPU (tra circa 16% e 21%) viene impiegata in attesa del completamento dell'I/O del disco. Questo limita la capacità di Elasticsearch di elaborare le richieste e archiviare i dati.

Durante l'esecuzione dei test il cluster di grandi dimensioni ha inserito oltre **500 milioni di documenti**. Consentendo la prosecuzione del test, è risultato che i tempi di attesa sono aumentati in modo significativo quando nel database erano presenti oltre 600 milioni di documenti. Le cause di questo comportamento non sono state esaminate completamente, ma potrebbero essere dovute alla frammentazione del disco che causa l'aumento della latenza del disco.

L'aumento delle dimensioni del cluster su più nodi può contribuire a ridurre gli effetti di questo comportamento. In casi estremi potrebbe essere necessario deframmentare un disco che mostra tempi di I/O eccessivi. Tuttavia, la deframmentazione di un disco di grandi dimensioni può richiedere molto tempo, anche più di 48 ore per un'unità VHD di 2 TB. Riformattare semplicemente l'unità e consentire a Elasticsearch di ripristinare i dati mancanti da partizioni di replica possono diventare un approccio più conveniente.

### Risoluzione dei problemi di latenza del disco

I test sono stati eseguiti inizialmente con VM configurate con dischi Standard. Un disco Standard si basa su supporti in rotazione e di conseguenza è soggetto a latenza rotazionale e altri colli di bottiglia che possono vincolare le velocità di I/O. Azure offre anche l'Archiviazione Premium in cui i dischi vengono creati con dispositivi SSD. Questi dispositivi non hanno una latenza rotazionale e di conseguenza forniranno velocità di I/O migliori.

La tabella confronta i risultati della sostituzione di dischi Standard con dischi Premium nel cluster di grandi dimensioni. Le VM D4 Standard nel cluster di grandi dimensioni sono state sostituite con VM DS4 Standard. Il numero di core, la memoria e i dischi sono gli stessi in entrambi i casi. L'unica differenza riguarda l'uso di unità SSD con le VM DS4.

| Configurazione | N. campioni | Tempo medio di risposta (ms) | Velocità effettiva (operazioni/sec) |
|------------------|-----------|----------------------------|---------------------------|
| Grande - Standard | 197085 | 839 | 27,4 |
| Grande - Premium | 255985 | 581 | 35,6 |

I tempi di risposta sono stati decisamente migliori, determinando una velocità effettiva media molto simile a 4 volte quella del cluster di piccole dimensioni. Questo risultato è più in linea con le risorse disponibili in VM DS4 Standard. L'utilizzo medio della CPU nel nodo più attivo del cluster (il nodo 1 in questo caso) è aumentato, avendo impiegato meno tempo in attesa del completamento dell'I/O:

![](media/guidance-elasticsearch/data-ingestion-image10.png)

La riduzione del tempo di attesa del disco diventa evidente quando si considera il grafico seguente, che illustra come per il nodo più attivo questa statistica è scesa in media a circa l'1%:

![](media/guidance-elasticsearch/data-ingestion-image11.png)

Questo miglioramento ha tuttavia un prezzo. Il numero di errori di inserimento è aumentato di 10 volte a 35797 (12,3%). Anche in questo caso, la maggior parte degli errori è il risultato dell'overflow della coda di inserimenti in blocco. Dato che ora l'esecuzione dell'hardware sembra aver raggiunto quasi il massimo della capacità, potrebbe essere necessario aggiungere altri nodi o limitare di nuovo la frequenza degli inserimenti in blocco per ridurre il volume di errori. Questi problemi sono illustrati più avanti in questo documento.

### Test con l'archiviazione temporanea

Gli stessi test sono stati ripetuti in un cluster di VM D4 con l'archiviazione temporanea. Nelle VM D4 l'archiviazione temporanea viene implementata come una singola unità SSD da 400 GB. Il numero di campioni elaborati, il tempo di risposta e la velocità effettiva sono molto simili alle cifre indicate per il cluster basato su VM DS14 con Archiviazione Premium.

| Configurazione | N. campioni | Tempo medio di risposta (ms) | Velocità effettiva (operazioni/sec) |
|-----------------------------------|-----------|----------------------------|---------------------------|
| Grande - Premium | 255985 | 581 | 35,6 |
| Grande - Standard (disco temporaneo) | 255626 | 585 | 35,5 |

Anche la percentuale di errore è risultata simile (33862 errori su 289488 richieste totali: 11,7%).

I grafici seguenti illustrano le statistiche relative a utilizzo della CPU e attesa del disco per il nodo più attivo del cluster (nodo 2 questa volta):

![](media/guidance-elasticsearch/data-ingestion-image12.png)

![](media/guidance-elasticsearch/data-ingestion-image13.png)

In questo caso, in soli termini di prestazioni, l'uso dell'archiviazione temporanea può essere considerata una soluzione più conveniente rispetto all'uso dell'Archiviazione Premium.

### Prestazioni di inserimento dei dati: Windows Server 2012

Gli stessi test sono stati ripetuti con un set di cluster Elasticsearch con nodi che eseguono Windows Server 2012. Questi test sono stati eseguiti allo scopo di stabilire gli effetti, se presenti, che la scelta del sistema operativo può avere sulle prestazioni del cluster.

Per illustrare la scalabilità di Elasticsearch in Windows, la tabella seguente illustra i tempi di risposta e la velocità effettiva ottenuti per le configurazioni di cluster di piccole, medie e grandi dimensioni. Si noti che tutti questi test sono stati eseguiti con Elasticsearch configurato per l'uso dell'archiviazione temporanea su unità SSD, perché i test con Ubuntu hanno mostrato che la latenza del disco poteva essere un fattore critico per ottenere il livello massimo di prestazioni:

| Configurazione | N. campioni | Tempo medio di risposta (ms) | Velocità effettiva (operazioni/sec) |
|---------------|-----------|----------------------------|---------------------------|
| Piccola | 90295 | 476 | 12,5 |
| Media | 169243 | 508 | 23,5 |
| Grande | 257115 | 613 | 35,6 |

Questi risultati indicano il livello di scalabilità di Elasticsearch con dimensioni della VM e le risorse disponibili in Windows.

Le tabelle seguenti confrontano i risultati per il cluster di grandi dimensioni in Ubuntu e Windows:

| Sistema operativo | N. campioni | Tempo medio di risposta (ms) | Velocità effettiva (operazioni/sec) | Percentuale di errore (%) |
|------------------|-----------|----------------------------|---------------------------|----------------|
| Ubuntu | 255626 | 585 | 35,5 | 11,7 |
| Windows | 257115 | 613 | 35,6 | 7,2 |

La velocità effettiva è risultata coerente con quella per i cluster Ubuntu di grandi dimensioni e anche se il tempo di risposta è stato leggermente superiore, può essere compensato da una percentuale di errore inferiore. Gli errori vengono segnalati più rapidamente delle operazioni riuscite e quindi hanno un tempo di risposta inferiore.

L'utilizzo della CPU segnalato dagli strumenti di monitoraggio di Windows è stato leggermente superiore rispetto a quello di Ubuntu. È tuttavia consigliabile considerare con estrema cautela i confronti diretti delle misurazioni tra sistemi operativi come queste, per il modo in cui i diversi sistemi operativi segnalano queste statistiche. Le informazioni sulla latenza del disco in termini di tempo di CPU trascorso in attesa di I/O non sono inoltre disponibili nello stesso modo di Ubuntu. Il punto importante è che l'utilizzo della CPU è risultato elevato e indica che il tempo trascorso in attesa di I/O era ridotto:

![](media/guidance-elasticsearch/data-ingestion-image14.png)

### Aumento delle prestazioni: conclusioni

Le prestazioni di Elasticsearch per un cluster ottimizzato tendono a essere equivalenti in Windows e Ubuntu e aumentano secondo un modello simile in entrambi i sistemi operativi. Per prestazioni ottimali, **usare l'Archiviazione Premium per contenere i dati di Elasticsearch**.

## Aumento delle istanze dei cluster per supportare l'inserimento dei dati su larga scala

L'aumento delle istanze è l'approccio gratuito all'aumento delle prestazioni esaminato nella sezione precedente. Un'importante funzionalità di Elasticsearch è l'intrinseca scalabilità orizzontale incorporata nel software. L'aumento delle dimensioni di un cluster è un'operazione semplice che prevede solo l'aggiunta di più nodi. Non è necessario eseguire operazioni manuali per ridistribuire gli indici o le partizioni, perché queste attività vengono gestite automaticamente, anche se sono disponibili alcune opzioni di configurazione che è possibile usare per influire sul processo.

L'aggiunta di più nodi consente di migliorare le prestazioni distribuendo il carico tra più macchine. Quando si aggiungono più nodi, può anche essere necessario prendere in considerazione la reindicizzazione dei dati per aumentare il numero di partizioni disponibili. È possibile intervenire in questo processo in una certa misura con la creazione di indici con più partizioni di quanti sono i nodi disponibili inizialmente. Quando vengono aggiunti altri nodi, le partizioni possono essere distribuite.

Oltre a sfruttare la scalabilità orizzontale di Elasticsearch, esistono altri motivi per l'implementazione di indici con più partizioni che nodi. Ogni partizione viene implementata come una struttura di dati separata, ovvero un indice [Lucene](https://lucene.apache.org/), e ha i propri meccanismi interni per mantenere la coerenza e gestire la concorrenza. La creazione di più partizioni facilita l'aumento del parallelismo all'interno di un nodo, con la possibilità di migliorare le prestazioni.

Il mantenimento delle prestazioni durante il ridimensionamento è tuttavia un'azione di bilanciamento. Più sono i nodi e le partizioni presenti in un cluster, maggiore sarà l'impegno richiesto per sincronizzare le operazioni eseguite dal cluster, con una possibile riduzione della velocità effettiva. In qualsiasi carico di lavoro ci sarà una condizione ideale che consente di ottimizzare le prestazioni di inserimento, riducendo al minimo il sovraccarico di manutenzione. Questa condizione ideale dipenderà in larga misura dalla natura del carico di lavoro e dal cluster e, in particolare, dal volume, dalle dimensioni e dal contenuto dei documenti, nonché dalla frequenza con cui si verifica l'inserimento e dall'hardware in cui viene eseguito il sistema.

Questa sezione riepiloga i risultati delle ricerche sul ridimensionamento dei cluster, allo scopo di supportare il carico di lavoro usato dal test delle prestazioni descritti in precedenza. Lo stesso test è stato eseguito in cluster con VM di dimensioni elevate, ovvero D4 Standard con 8 core CPU, 16 dischi dati e 28 GB di RAM, che eseguono Ubuntu Linux 14.0.4, ma configurate con numeri di nodi e partizioni diversi. I risultati non devono essere considerati definitivi, perché si applicano solo a uno scenario specifico, ma possono essere un buon punto di partenza per analizzare la scalabilità orizzontale dei cluster e generare cifre che indicano il rapporto ottimale tra partizioni e nodi che meglio soddisfa le proprie esigenze.

### Risultati di base: 3 nodi

Per ottenere un'immagine di base, è stato eseguito il test delle prestazioni di inserimento dei dati in un cluster di 3 nodi con 5 partizioni e 1 replica. Questa è la configurazione predefinita per un indice di Elasticsearch. In questa configurazione Elasticsearch distribuisce 2 partizioni primarie a 2 dei nodi e la partizione primaria rimanente viene archiviata nel terzo nodo. La tabella seguente riepiloga la velocità effettiva in termini di operazioni di inserimento in blocco al secondo e il numero di documenti che sono stati archiviati correttamente dal test.

> [AZURE.NOTE] Nelle tabelle seguenti di questa sezione la distribuzione delle partizioni primarie viene presentata come un numero per ogni nodo separato da trattini. Ad esempio, il layout con 3 nodi e 5 partizioni è descritto come 2-2-1. Il layout delle partizioni di replica non è incluso. Seguono uno schema simile alle partizioni primarie.

| Configurazione | N. di documenti | Velocità effettiva (operazioni/sec) | Layout di partizione |
|---------------|--------------|-----------------------------|--------------|
| 5 partizioni | 200560412 | 27,86 | 2-2-1 |

### Risultati per 6 nodi

Il test è stato ripetuto in un cluster di 6 nodi. Lo scopo di questi test è stato quello di provare a verificare in modo più preciso gli effetti dell'archiviazione di più di una partizione in un nodo.

| Configurazione | N. di documenti | Velocità effettiva (operazioni/sec) | Layout di partizione |
|---------------|--------------|-----------------------------|--------------|
| 4 partizioni | 227360412 | 31,58 | 1-1-0-1-1-0 |
| 7 partizioni | 268013252 | 37,22 | 2-1-1-1-1-1 |
| 10 partizioni | 258065854 | 35,84 | 1-2-2-2-1-2 |
| 11 partizioni | 279788157 | 38,86 | 2-2-2-1-2-2 |
| 12 partizioni | 257628504 | 35,78 | 2-2-2-2-2-2 |
| 13 partizioni | 300126822 | 41,68 | 2-2-2-2-2-3 |

Tali risultati sembrano indicare le tendenze seguenti:

* Più partizioni per ogni nodo migliorano la velocità effettiva. Con il numero limitato di partizioni per ogni nodo create per questi test, questo fenomeno era previsto per i motivi descritti in precedenza.

* Un numero dispari di partizioni offre prestazioni migliori rispetto a un numero pari. I motivi di questo risultato sono meno chiari, ma *può* essere che l'algoritmo di routing usato da Elasticsearch sia in grado di distribuire meglio i dati tra le partizioni in questo caso, determinando un carico più uniforme per ogni nodo.

Per testare queste ipotesi, sono stati eseguiti diversi altri test con un numero elevato di partizioni. A seguito di un suggerimento verbale di Elasticsearch, si è deciso di usare un numero primo di partizioni per ogni test, perché forniscono una ragionevole distribuzione di numeri dispari per l'intervallo in questione.

| Configurazione | N. di documenti | Velocità effettiva (operazioni/sec) | Layout di partizione |
|---------------|--------------|-----------------------------|-------------------|
| 23 partizioni | 312844185 | 43,45 | 4-4-4-3-4-4 |
| 31 partizioni | 309930777 | 43,05 | 5-5-5-5-6-5 |
| 43 partizioni | 316357076 | 43,94 | 8-7-7-7-7-7 |
| 61 partizioni | 305072556 | 42,37 | 10-11-10-10-10-10 |
| 91 partizioni | 291073519 | 40,43 | 15-15-16-15-15-15 |
| 119 partizioni | 273596325 | 38,00 | 20-20-20-20-20-19 |

Questi risultati suggeriscono che è stato raggiunto il punto critico a circa 23 partizioni. Dopo questo punto, l'aumento del numero di partizioni ha causato una piccola riduzione delle prestazioni. La velocità effettiva di 43 partizioni è probabilmente un'anomalia.

### Risultati per 9 nodi

I test sono stati ripetuti con un cluster di 9 nodi, usando di nuovo un numero primo di partizioni.

| Configurazione | N. di documenti | Velocità effettiva (operazioni/sec) | Layout di partizione |
|---------------|--------------|-----------------------------|----------------------------|
| 17 partizioni | 325165364 | 45,16 | 2-2-2-2-2-2-2-2-1 |
| 19 partizioni | 331272619 | 46,01 | 2-2-2-2-2-2-2-2-3 |
| 29 partizioni | 349682551 | 48,57 | 3-3-3-4-3-3-3-4-3 |
| 37 partizioni | 352764546 | 49,00 | 4-4-4-4-4-4-4-4-5 |
| 47 partizioni | 343684074 | 47,73 | 5-5-5-6-5-5-5-6-5 |
| 89 partizioni | 336248667 | 46,70 | 10-10-10-10-10-10-10-10-9 |
| 181 partizioni | 297919131 | 41,38 | 20-20-20-20-20-20-20-20-21 |

Questi risultati hanno mostrato un modello simile, con un punto critico a circa 37 partizioni.

### Aumento delle istanze: conclusioni

Usando un'estrapolazione rudimentale, i risultati dei test per 6 nodi e 9 nodi indicano che, per questo specifico scenario, il numero ideale di partizioni per ottimizzare le prestazioni è 4n+/-1, dove n è il numero di nodi. Questo *può* essere in funzione del numero di thread di inserimento in blocco disponibili, che a sua volta dipende dal numero di core CPU, la cui spiegazione logica è riportata di seguito. Per altre informazioni, vedere [modello con documenti multipli](https://www.elastic.co/guide/en/elasticsearch/guide/current/distrib-multi-doc.html#distrib-multi-doc):

- Ogni richiesta di inserimento in blocco inviata dall'applicazione client viene ricevuta da un singolo nodo dati.

- Il nodo dati compila una nuova richiesta di inserimento in blocco per ogni partizione primaria interessata dalla richiesta originale e le inoltra agli altri nodi in parallelo.

- Durante la scrittura di ogni partizione primaria, un'altra richiesta viene inviata alla replica di tale partizione. La partizione primaria attende il completamento della richiesta inviata alla replica prima di terminare l'operazione.

Per impostazione predefinita, Elasticsearch crea un thread di inserimento in blocco per ogni core CPU disponibile in una macchina virtuale. Nel caso delle VM D4 usate da questo test, ogni CPU conteneva 8 core, quindi sono stati creati 8 thread di inserimento in blocco. L'indice ha usato 4 (in un caso 5) partizioni primarie estese in ogni nodo, ma sono presenti anche 4 (5) repliche in ogni nodo. L'inserimento di dati in queste partizioni e repliche può utilizzare fino a 8 thread in ogni nodo per ogni richiesta, corrispondente al numero disponibile. L'aumento o la riduzione del numero di partizioni può causare inefficienze a livello di threading, perché è possibile che i thread rimangano non occupati o le richieste siano accodate. Tuttavia, senza ulteriore sperimentazione questa è semplicemente una teoria e non è possibile trarre conclusioni definitive.

I test hanno illustrato anche un altro punto importante. In questo scenario l'aumento del numero di nodi può migliorare la velocità effettiva di inserimento dei dati, ma i risultati non vengono necessariamente ridimensionati in modo lineare. L'esecuzione di altri test con cluster di 12 e 15 nodi può mostrare a che punto l'aumento delle istanze offre un piccolo vantaggio aggiuntivo. Se questo numero di nodi non fornisce spazio di archiviazione sufficiente, può essere necessario tornare alla strategia di aumento delle prestazioni e iniziare a usare più dischi o dischi più grandi basati sull'Archiviazione Premium.

> [AZURE.IMPORTANT] Non considerare il rapporto 4n+/-1 come una formula magica che funzionerà sempre per tutti i cluster. Se sono disponibili più o meno core CPU, la configurazione ottimale delle partizioni potrebbe essere diversa. I risultati sono basati su un carico di lavoro specifico che ha eseguito solo l'inserimento dei dati. Per i carichi di lavoro che includono anche un insieme di query e aggregazioni i risultati potrebbero essere molto diversi.

> Il carico di lavoro di inserimento dei dati ha usato anche un singolo indice. In molte situazioni è probabile che i dati siano distribuiti in più indici, determinando l'uso di risorse o modelli diversi.

> Il punto importante di questo esercizio consiste nel comprendere il metodo usato piuttosto che i risultati ottenuti. È necessario essere pronti a eseguire una valutazione della scalabilità basata sui propri carichi di lavoro per ottenere informazioni applicabili con i migliori risultati al proprio scenario.

## Ottimizzazione dell'inserimento dei dati su larga scala

Elasticsearch è ampiamente configurabile, con molte opzioni e impostazioni che è possibile usare per ottimizzare le prestazioni per casi d'uso e scenari specifici. Questa sezione descrive alcuni esempi comuni. Tenere presente che la flessibilità fornita in questo senso da Elasticsearch è accompagnata da un avviso: è molto facile alterare Elasticsearch e causare un degrado delle prestazioni. Durante l'ottimizzazione, apportare una sola modifica alla volta e misurare sempre gli effetti delle modifiche per assicurarsi che non siano dannose per il sistema.

### Ottimizzazione delle risorse per le operazioni di indicizzazione

L'elenco seguente descrive alcuni punti che è opportuno considerare quando si ottimizza un cluster Elasticsearch per supportare l'inserimento di dati su larga scala. I primi due elementi hanno più probabilmente un effetto immediatamente percepibile sulle prestazioni, mentre gli altri sono più marginali, a seconda del carico di lavoro:

*  I nuovi documenti aggiunti a un indice diventano visibili per le ricerche solo quando l'indice viene aggiornato. L'aggiornamento di un indice è un'operazione dispendiosa, quindi viene eseguita solo periodicamente invece che alla creazione di ogni documento. L'intervallo di aggiornamento predefinito è 1 secondo. Se si eseguono operazioni in blocco, è consigliabile disattivare temporaneamente gli aggiornamenti dell'indice. Impostare il valore *refresh\_interval* dell'indice su -1.

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"refresh_interval": -1
		}
	}
	```

	Attivare un aggiornamento manualmente usando l'API [*\_refresh*](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-refresh.html) API alla fine dell'operazione per rendere visibili i dati. Per altre informazioni, vedere la sezione relativa all'[utilizzo dell'indicizzazione in blocco](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html#bulk). Più avanti sono disponibili altri dettagli sull'[impatto della modifica dell'intervallo di aggiornamento sull'inserimento dei dati](#the-impact-of-changing-the-index-refresh-interval-on-data-ingestion-performance).

* Se un indice viene replicato, ogni operazione di indicizzazione, ovvero creazione, aggiornamento o eliminazione di documenti, viene ripetuta in tutte le partizioni di replica appena si verificano nella partizione primaria. È consigliabile disabilitare la replica durante le operazioni di importazione in blocco e quindi riabilitarla una volta completata l'importazione:

    ```http
	PUT /my_busy_index
	{
		"settings" : {
			"number_of_replicas": 0
		}
	}
	```

	Quando si riabilita la replica, Elasticsearch esegue un trasferimento dei dati in rete byte per byte dall'indice a ogni replica. Questo approccio è più efficiente rispetto alla ripetizione del processo di indicizzazione documento per documento in ogni nodo. Il rischio è che può verificarsi una perdita di dati in caso di errore del nodo primario durante l'esecuzione dell'importazione in blocco, ma il ripristino può limitarsi semplicemente al riavvio dell'importazione. Più avanti è descritto in dettaglio l'[impatto della replica sulle prestazioni di inserimento dei dati](#the-impact-of-replicas-on-data-ingestion-performance).

* Elasticsearch prova a bilanciare le risorse disponibili tra quelle necessarie per l'esecuzione di query e quelle necessarie per l'inserimento di dati. Di conseguenza, può limitare le prestazioni di inserimento dei dati. Gli eventi di limitazione vengono registrati nel log di Elasticsearch. Questa restrizione è concepita per evitare che venga creato contemporaneamente un numero elevato di segmenti di indice che richiedono l'unione e il salvataggio su disco, un processo che può monopolizzare le risorse. Se attualmente il proprio sistema non esegue query, è possibile disabilitare la limitazione dell'inserimento di dati. In questo modo le prestazioni dell'indicizzazione saranno ottimizzare. È possibile disabilitare la limitazione per un intero cluster come indicato di seguito:

	```http
	PUT /_cluster/settings
	{
		"transient" : {
			"indices.store.throttle.type": "none"
		}
	}
	```

    Una volta completato l'inserimento, reimpostare il tipo di limitazione del cluster su *"merge"*. Si noti anche che la disabilitazione della limitazione può causare l'instabilità del cluster. Assicurarsi quindi che le procedure in atto consentano il ripristino del cluster, se necessario.

* Elasticsearch riserva una percentuale della memoria heap per le operazioni di indicizzazione. Il resto viene usato principalmente da ricerche e query. Lo scopo di questi buffer consiste nel ridurre il numero di operazioni di I/O su disco, allo scopo di eseguire meno scritture più grandi invece di più scritture più piccole. La percentuale predefinita di memoria heap allocata è 10%. Se si indicizza un grande volume di dati, questo valore può risultare insufficiente. Per i sistemi che supportano l'inserimento di volumi elevati di dati, è necessario consentire fino a 512MB di memoria per ogni partizione nel nodo attivo. Ad esempio, se si esegue Elasticsearch in VM D4 (28 GB di RAM) e viene allocato il 50% della memoria disponibile alla JVM (14 GB), saranno disponibili 1,4 GB per l'uso da parte delle operazioni di indicizzazione. Se un nodo contiene 3 partizioni attive, questa configurazione è probabilmente sufficiente. Tuttavia, se un nodo contiene più partizioni di queste, provare ad aumentare il valore del parametro *indices.memory.index\_buffer\_size* nel file di configurazione elasticsearch.yml. Per altre informazioni, vedere il documento relativo alle [considerazioni sulle prestazioni per l'indicizzazione con Elasticsearch](https://www.elastic.co/blog/performance-considerations-elasticsearch-indexing).

    L'allocazione di più di 512 MB per partizione attiva molto probabilmente non migliorerà le prestazioni di indicizzazione, ma può essere effettivamente sfavorevole, perché è disponibile una minore quantità di memoria per l'esecuzione di altre attività. Si consideri anche che allocare più spazio dell'heap per i buffer dell'indice rimuove memoria per altre operazioni, ad esempio ricerca e aggregazione dei dati, e può rallentare le prestazioni delle operazioni di query.

* Elasticsearch limita il numero di thread, il valore predefinito è 8, che possono eseguire contemporaneamente operazioni di indicizzazione in una partizione. Se un nodo contiene solo un numero ridotto di partizioni, provare ad aumentare l'impostazione *index\_concurrency* di un indice soggetto a un volume elevato di operazioni di indicizzazione o che è la destinazione di un inserimento in blocco, come indicato di seguito:

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"index_concurrency": 20
		}
	}
	```

* Se si esegue un numero elevato di operazioni in blocco e di indicizzazione per un breve periodo di tempo, è possibile aumentare il numero di thread *index* e *bulk* disponibili nel pool di thread ed estendere le dimensioni della coda *bulk insert* per ogni nodo dati. In questo modo più richieste potranno essere accodate invece che eliminate. Per altre informazioni, vedere il documento relativo al [pool di thread](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-threadpool.html). Se si eseguono livelli elevati e continui di inserimento dati, l'aumento del numero di thread in blocco non è consigliato. Creare invece nodi aggiuntivi e usare il partizionamento orizzontale per distribuire il carico di indicizzazione tra questi nodi. In alternativa, prendere in considerazione l'invio di batch di inserimento in blocco in serie piuttosto che in parallelo, perché questa impostazione verrà usata come meccanismo di limitazione naturale che può ridurre le probabilità di errori dovuti a un overflow della coda di inserimenti in blocco.

### Impatto della modifica dell'intervallo di aggiornamento dell'indice sulle prestazioni di inserimento dei dati

L'intervallo di aggiornamento determina la frequenza con cui i dati inseriti diventano visibili per le query e le aggregazioni, ma gli aggiornamenti frequenti possono influire sulle prestazioni delle operazioni di inserimento dati. L'intervallo di aggiornamento predefinito è 1 secondo. È possibile disabilitare completamente l'aggiornamento, ma questo potrebbe non essere appropriato per il carico di lavoro. È possibile provare diversi intervalli e stabilire un punto critico che bilanci le prestazioni di inserimento rispetto alla necessità di presentare informazioni aggiornate.

Per un esempio delle conseguenze, il test delle prestazioni di inserimento dati è stato ripetuto in un cluster Elasticsearch composto da 7 partizioni distribuite in 3 nodi dati. L'indice includeva una singola replica. Ogni nodo dati era basato su una VM D4 (28 GB di RAM, 8 core processore) con archiviazione temporanea basata su unità SSD per contenere i dati. Ogni test è stato eseguito per un'ora.

In questo test la frequenza di aggiornamento è stata impostata sul valore predefinito di 1 secondo. La tabella seguente mostra la velocità effettiva e i tempi di risposta per questo test rispetto a un'esecuzione separata in cui la frequenza di aggiornamento è stata ridotta a una volta ogni 30 secondi.

| Frequenza di aggiornamento | N. di campioni | Tempo medio di risposta: operazioni completate (ms) | Velocità effettiva: operazioni completate (operazioni/s) |
|--------------|------------|----------------------------------------------------|---------------------------------------------------|
| 1 secondo | 93755 | 460 | 26,0 |
| 30 secondi | 117758 | 365 | 32,7 |

In questo test la riduzione della frequenza di aggiornamento ha prodotto un miglioramento del 18% nella velocità effettiva e una riduzione del tempo medio di risposta pari al 21%. I grafici seguenti generati con Marvel illustrano il motivo principale di questa differenza. Le figure seguenti mostrano l'attività di unione dell'indice che si è verificata con l'intervallo di aggiornamento impostato su 1 secondo e 30 secondi.

Le unioni dell'indice vengono eseguite per evitare che il numero di segmenti di indice in memoria diventi troppo elevato. Un intervallo di aggiornamento di 1 secondo genera un numero elevato di segmenti di piccole dimensioni che devono essere uniti di frequente, mentre un intervallo di aggiornamento di 30 secondi genera meno segmenti di grandi dimensioni che possono essere uniti in modo ottimale.

![](media/guidance-elasticsearch/data-ingestion-image15.png)

***Attività di unione dell'indice per una frequenza di aggiornamento dell'indice di 1 secondo***

![](media/guidance-elasticsearch/data-ingestion-image16.png)

***Attività di unione dell'indice per una frequenza di aggiornamento dell'indice di 30 secondi***

### Impatto delle repliche sulle prestazioni di inserimento dei dati

Le repliche sono una funzionalità essenziale di qualsiasi cluster resiliente e, se non vengono usate, si rischia di perdere informazioni in caso di errore di un nodo. Le repliche aumentano tuttavia la quantità di operazioni di I/O eseguite su disco e in rete e possono avere effetti negativi sulla frequenza con cui vengono inseriti i dati. Per i motivi descritti in precedenza, può essere utile disabilitare temporaneamente le repliche per la durata delle operazioni di caricamento dati su larga scala.

I test delle prestazioni di inserimento dati sono stati ripetuti con tre configurazioni:

* Usando un cluster senza repliche.

* Usando un cluster con una replica.

* Usando un cluster con 2 repliche.

In tutti i casi, il cluster includeva 7 partizioni distribuite in 3 nodi, eseguite su VM configurate come descritto nel set di test precedente. L'indice di test usava un intervallo di aggiornamento di 30 secondi.

La tabella seguente riepiloga i tempi di risposta e la velocità effettiva di ogni test ai fini del confronto:

| Configurazione | N. di campioni | Tempo medio di risposta: operazioni completate (ms) | Velocità effettiva: operazioni completate (operazioni/s) | N. di errori di inserimento dati |
|---------------|------------|----------------------------------------------------|---------------------------------------------------|--------------------------|
| 0 repliche | 215451 | 200 | 59,8 | 0 |
| 1 replica. | 117758 | 365 | 32,7 | 0 |
| 2 repliche | 94218 | 453 | 26,1 | 194262 |


La riduzione delle prestazioni con l'aumento del numero di repliche è chiara, ma è anche opportuno sottolineare il volume elevato di errori di inserimento dati nel terzo test. I messaggi generati da questi errori indicano che sono dovuti all'overflow della coda di inserimento in blocco che ha causato il rifiuto delle richieste. Questi rifiuti si sono verificati molto rapidamente, determinando il numero elevato.

> [AZURE.NOTE] I risultati del terzo test sottolineano l'importanza dell'uso di una strategia di ripetizione dei tentativi intelligente quando si verificano errori temporanei come questo, ovvero interruzione per un breve intervallo per consentire lo svuotamento della coda di inserimento in blocco prima di riprovare a eseguire l'operazione.

Il set di grafici successivo confrontano i tempi di risposta durante i test. In ogni caso il primo grafico mostra i tempi di risposta complessivi, mentre il secondo grafico si concentra sui tempi di risposta per le operazioni più veloci. Si noti che la scala del primo grafico è dieci volte quella del secondo. È possibile vedere come il profilo dei tempi di risposta varia nei tre test.

Senza repliche la maggior parte delle operazioni ha richiesto tra 75ms e 750ms, con la risposta più rapida tempi intorno a 25 ms:

![](media/guidance-elasticsearch/data-ingestion-image17.png)

Con 1 replica il tempo di risposta operativa più popolato era compreso tra 125ms e 1250ms. Le risposte più rapide hanno impiegato circa 75ms, anche se le risposte rapide erano meno del caso con 0 repliche. Erano anche presenti molte più risposte che hanno richiesto tempi molto più lunghi rispetto ai casi più comuni, oltre 1250ms:

![](media/guidance-elasticsearch/data-ingestion-image18.png)

Con 2 repliche l'intervallo di tempo di risposta più popolato è risultato tra 200 ms e 1500 ms, ma con molti meno risultati al di sotto dell'intervallo minimo rispetto al test con 1 replica. Tuttavia, lo schema dei risultati che superano il limite massimo è stato molto simile a quello del test con 1 replica. Ciò è molto probabilmente dovuto agli effetti delll'overflow della coda di inserimento in blocco, con una lunghezza della coda di oltre 50. L'attività aggiuntiva necessaria per gestire 2 repliche fa sì che l'overflow della coda si verifichi più spesso, impedendo le operazioni di inserimento abbiano tempi di risposta eccessivi. Le operazioni vengono rifiutate rapidamente invece di attendere per un lungo intervallo di tempo, causando eventualmente eccezioni di timeout o influenzando la velocità di risposta delle applicazioni client, che corrisponde allo scopo del meccanismo delle code di inserimento in blocco:

![](media/guidance-elasticsearch/data-ingestion-image19.png)

<span id="_The_Impact_of_1" class="anchor"><span id="_Impact_of_Increasing" class="anchor"></span></span>Con Marvel è possibile vedere l'effetto del numero di repliche nella coda di inserimento nell'indice in blocco. La figura seguente mostra i dati di Marvel che illustrano il riempimento della coda di inserimento in blocco durante il test. La lunghezza media della coda è stata intorno a 40 richieste, ma picchi periodici hanno causato l'overflow e di conseguenza il rifiuto delle richieste:

![](media/guidance-elasticsearch/data-ingestion-image20.png)

***Dimensioni della coda di inserimento nell'indice in blocco e numero di richieste rifiutate con 2 repliche.***

È necessario confrontare questi dati con la figura seguente che mostra i risultati per una singola replica. Il motore di Elasticsearch ha potuto elaborare le richieste abbastanza rapidamente per mantenere la lunghezza media della coda a circa 25. La lunghezza della coda non ha mai superato 50 richieste e quindi nessuna attività è stata rifiutata.

![](media/guidance-elasticsearch/data-ingestion-image21.png)

***Dimensioni della coda di inserimento nell'indice in blocco e numero di richieste rifiutate con 1 replica.***

## Procedure consigliate per i client che inviano dati a Elasticsearch

Molti aspetti delle prestazioni riguardano non solo il sistema internamente, ma anche per il modo in cui le applicazioni client usano il sistema. Elasticsearch offre molte funzionalità che possono essere utilizzate dal processo di inserimento dati. Ad esempio, generando identificatori univoci per i documenti, eseguendo analisi dei documenti e anche usando script per trasformare i dati quando vengono archiviati. Queste funzioni che concorrono tuttavia al carico sul motore di Elasticsearch e in molti casi possono essere eseguite in modo più efficiente dalle applicazioni client prima della trasmissione.

> [AZURE.NOTE] Questo elenco di procedure consigliate riguarda principalmente l'inserimento di nuovi dati, invece della modifica dei dati esistenti già archiviati in un indice. I carichi di lavoro di inserimento vengono eseguiti come operazioni di aggiunta da Elasticsearch, mentre le modifiche dei dati sono eseguite come operazioni di eliminazione/aggiunta. Questo è dovuto al fatto che i documenti in un indice non sono modificabili, quindi la modifica di un documento implica la sostituzione dell'intero documento con una nuova versione. È possibile eseguire una richiesta HTTP PUT per sovrascrivere un documento esistente oppure usare l'API *update* di Elasticsearch che astrae una query per recuperare un documento esistente, unisce le modifiche e quindi esegue un'operazione PUT per archiviare il nuovo documento.

È anche consigliabile prendere in considerazione l'implementazione delle procedure seguenti, nei casi appropriati:

* Disabilitare l'analisi del testo per i campi dell'indice che non è necessario analizzare. Le analisi comportano la tokenizzazione del testo per abilitare query che possano cercare termini specifici. Questa può comunque essere un'attività che implica un uso intensivo della CPU, quindi occorre essere selettivi. Se si usa Elasticsearch per archiviare dati di log, può essere utile procedere alla tokenizzazione dei messaggi di log dettagliati per consentire ricerche complesse. Per altri campi, ad esempio quelli che contengono codici di errore o identificatori, non è probabilmente necessaria la tokenizzazione. Ad esempio, con quale frequenza è probabile che si richiedano i dettagli di tutti i messaggi il cui codice di errore contiene "3"? Il codice seguente disabilita l'analisi per i campi *name* e *hostip* nel tipo *logs* dell'indice *systembase*:

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			...
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			...
		}
	}
	```

* Disabilitare il campo *\_all* di un indice, se non è obbligatorio. Il campo *\_all* concatena i valori degli altri campi nel documento per l'analisi e l'indicizzazione. È utile per l'esecuzione di query che possono trovare una corrispondenza con qualsiasi campo in un documento. Se è previsto che i client trovino una corrispondenza con campi denominati, l'abilitazione di *\_all* comporta semplicemente un sovraccarico in termini di CPU e archiviazione. L'esempio seguente illustra come disabilitare il campo *\_all* per il tipo*logs* nell'indice *systembase*.

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			"_all": {
				"enabled" : false
			},
			...,
		...
		}
	}
	```

    Si noti che è possibile creare una versione selettiva di *\_all* contenente solo le informazioni di campi specifici. Per altre informazioni, vedere la sezione relativa alla [disabilitazione del campo \_all](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-all-field.html#disabling-all-field).

* Evitare i mapping dinamici negli indici. Il mapping dinamico è una funzionalità potente, ma l'aggiunta di nuovi campi a un indice esistente richiede il coordinamento delle modifiche alla struttura dell'indice tra i nodi, cosa che possono causare il blocco temporaneo dell'indice. Il mapping dinamico può anche causare un'esplosione del numero di campi e il conseguente volume di metadati dell'indice, se non viene usato con attenzione. A sua volta, causa un aumento dei requisiti di archiviazione maggiore e I/O, sia per l'inserimento di dati che durante l'esecuzione delle query. Entrambi questi problemi avranno un impatto sulle prestazioni. È consigliabile disabilitare il mapping dinamico e definire le strutture dell'indice in modo esplicito. Per altre informazioni, vedere il documento relativo al [mapping dinamico dei campi](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html#dynamic-field-mapping).

* Comprendere come bilanciare il carico di lavoro per soddisfare i requisiti in conflitto. È sempre consigliabile considerare che l'inserimento dei dati può avere un impatto significativo sulle prestazioni di altre operazioni simultanee, ad esempio le query eseguite dagli utenti. L'inserimento di dati può essere soggetto a picchi improvvisi e se il sistema prova a utilizzare immediatamente tutti i dati in arrivo, l'afflusso può causare un drastico rallentamento delle frequenze di query. Elasticsearch prova ad evitare che questa situazione si verifichi regolando la frequenza con cui elaborerà le richieste di inserimento tramite la coda di inserimento in blocco. Per altre informazioni, vedere [Determinazione dei fattori limitanti: utilizzo della CPU](#determining-limiting-factors-cpu-utilization). Questo meccanismo deve però essere considerato veramente come ultima risorsa, perché se il codice dell'applicazione non è predisposto per la gestione delle richieste rifiutate, si rischia di perdere dati. È consigliabile usare invece un modello, ad esempio quello descritto nell'articolo sul [livellamento del carico basato sulle code](https://msdn.microsoft.com/library/dn589783.aspx) per controllare la frequenza con cui i dati vengono passati a Elasticsearch.

* Assicurarsi che il cluster abbia risorse sufficienti per gestire il carico di lavoro, specialmente se gli indici sono configurati con più repliche.

* Usare l'API di inserimento in blocco per caricare batch di documenti di grandi dimensioni. Ridimensionare le richieste in blocco in modo appropriato. A volte i batch più grandi non sono i migliori ai fini delle prestazioni e possono causare il sovraccarico di thread e altre risorse di Elasticsearch, ritardando altre operazioni simultanee. I documenti in un batch di inserimento bulk vengono mantenuti in memoria nel nodo di coordinamento durante l'esecuzione dell'operazione. Le dimensioni fisiche di ogni batch sono più importanti del numero di documenti. Non esiste una regola precisa riguardante gli elementi che costituiscono le dimensioni del batch ideali, anche se nella documentazione di Elasticsearch viene suggerito di usare da 5 MB a 15 MB come base per le proprie analisi. Eseguire il test delle prestazioni per stabilire le dimensioni ottimali dei batch per la propria combinazione di scenari e carichi di lavoro.

* Assicurarsi che le richieste di inserimento in blocco vengano distribuite ai nodi invece di essere indirizzate a un singolo nodo. Indirizzare tutte le richieste a un singolo nodo può causare l'esaurimento della memoria, perché ogni richiesta di inserimento in blocco elaborata viene archiviata in memoria nel nodo. Il reindirizzamento delle richieste ad altri nodi può anche aumentare la latenza di rete.

* Elasticsearch usa un quorum costituito una maggioranza dei nodi primari e di replica durante la scrittura di dati. Un'operazione di scrittura non viene completata finché il quorum non segnala l'esito positivo. Questo approccio consente di assicurare che i dati non vengano scritti se la maggioranza dei nodi non è disponibile a causa di un evento (errore) delle partizioni di rete. L'uso di un quorum può rallentare le prestazioni delle operazioni di scrittura. È possibile disabilitare la scrittura basata su quorum impostando il parametro *consistency* su *one* durante la scrittura dei dati. L'esempio seguente aggiunge un nuovo documento, ma l'operazione viene completata subito dopo il completamento della scrittura nella partizione primaria.

	```http
	PUT /my_index/my_data/104?consistency=one
	{
		"name": "Bert",
		"age": 23
	}
	```

	Si noti che, con la replica asincrona, la disabilitazione della scrittura basata su quorum può causare incoerenze tra la partizione primaria e ognuna delle repliche.

* Quando si usano i quorum, Elasticsearch attenderà se non sono disponibili nodi sufficienti prima di stabilire che un'operazione di scrittura deve essere interrotta perché non è possibile raggiungere un quorum. Questo intervallo di attesa è determinato dal parametro di timeout nella query. Il valore predefinito è 1 minuto. È possibile modificare questa impostazione usando il parametro timeout nella query. L'esempio seguente crea un nuovo documento e attende al massimo per 5 secondi la risposta del quorum prima dell'interruzione:

	```http
	PUT /my_index/my_data/104?timeout=5s
	{
		"name": "Sid",
		"age": 27
	}
	```

	Elasticsearch consente anche di usare numeri di versione personalizzati [generati esternamente](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html#_version_types).

* È possibile disabilitare il campo *\_source* di un indice. Questo campo contiene una copia del documento JSON originale usato quando un documento viene archiviato. Il salvataggio di questo campo comporta operazioni di I/O su disco e costi di archiviazione aggiuntivi. Tuttavia, questi costi possono essere marginali a seconda della struttura del documento. Occorre anche tenere presente che la disattivazione del campo *\_source* impedisce a un client di eseguire le operazioni seguenti:

	* Uso dell'API Update per modificare un documento.
	* Esecuzione dell'evidenziazione in tempo reale quando si eseguono query.
	* Reindicizzazione dei dati.
	* Debug di query e aggregazioni visualizzando il documento originale.

	L'esempio seguente illustra come disabilitare il campo *\_source* per il tipo *logs* nell'indice *systembase*.

  ```http
  PUT /systembase
  {
		"settings" : {
			...
		},
		"logs" : {
			"_source": {
				"enabled": false
			},
			...,
		...
		}
  }
  ```

## Linee guida generali per l'esecuzione di test delle prestazioni di inserimento dati con Elasticsearch

Di seguito sono illustrati alcuni degli elementi che è necessario considerare quando si eseguono test delle prestazioni con Elasticsearch e si analizzano i risultati.

* I test delle prestazioni sono necessariamente costosi e dispendiosi in termini di tempo. Come minimo, raccogliere le statistiche che misurano la velocità di trasferimento su disco e in rete, l'utilizzo della CPU, i tempi di attesa della CPU e la latenza del disco, se possibile. Queste informazioni consentono di avere rapidamente commenti sull'attività di test, con un buon ritorno sugli investimenti.

* Sfruttare le funzionalità di script fornita dallo strumento di test di carico per raccogliere la metrica che altrimenti non sarebbe disponibile. Ad esempio Linux offre un'ampia gamma di statistiche affidabili sulle prestazioni che è possibile raccogliere con utilità come *vmstat* e *iostat*. È possibile usare script con JMeter per acquisire questi dati come parte di un piano di test.

* La progettazione delle prestazioni verte principalmente sull'analisi delle statistiche in base a dati affidabili e ripetibili. Non fermarsi alla metrica a livello generale che non fornirà le informazioni dettagliate necessarie. Usare i dati come base informativa e fare della progettazione delle prestazioni in un processo di sviluppo con un ciclo di feedback rapido. Esaminare sempre le statistiche che confrontano le tendenze con i risultati e le configurazioni precedenti. Procedendo in questo modo con regolarità, si potranno generare dati comprensibili, ripetibili con i carichi di lavoro e che consentiranno di valutare gli effetti delle modifiche alla configurazione e distribuzione.

* Usare uno strumento come Marvel per monitorare le prestazioni di cluster e nodi durante i test per ottenere altre informazioni dettagliate. JMeter può essere efficace per l'acquisizione di dati non elaborati per una successiva analisi, ma Marvel può fornire un'idea in tempo reale sull'andamento delle prestazioni e le possibili cause di anomalie e rallentamenti. Inoltre, molti strumenti di test di carico non forniscono visibilità sulla metrica interna di Elasticsearch. Usare e confrontare le velocità effettive di indicizzazione, unire conteggi dei segmenti, statistiche GC e tempi di limitazione disponibili nelle statistiche degli indici. Ripetere l'analisi a intervalli regolari.

* Confrontare le statistiche dello strumento di test di carico con le statistiche dei nodi in Marvel, ad esempio traffico del disco e di rete, utilizzo della CPU, utilizzo della memoria e del pool di thread, per comprendere il modello di correlazione tra le cifre segnalate dall'infrastruttura e le statistiche specifiche di Elasticsearch.

* Come regola generale è consigliabile usare *una partizione, un nodo* come base per il test delle prestazioni e valutare i costi dell'applicazione aggiungendo nodi. Evitare tuttavia di dipendere completamente dall'estrapolazione delle prestazioni sulla base di un numero ridotto di nodi e partizioni. I costi di sincronizzazione e comunicazione del cluster possono avere effetti imprevisti in presenza di un numero maggiore di nodi e partizioni.

* Esaminare l'allocazione di partizioni tra i nodi per confrontare le statistiche. Alcuni nodi avranno un minor numero di repliche e partizioni, creando uno squilibrio nell'utilizzo delle risorse.

* Se si eseguono test di carico, aumentare il numero di thread usato dallo strumento di test per inviare operazioni al cluster finché non si verificano errori. Per i test della velocità effettiva sostenibile è consigliabile mantenere il livello di test sotto una *barriera* concordata. Se la percentuale di errore supera questa barriera, gli errori comporteranno un costo per le risorse back-end a causa della capacità di ripristino. In queste situazioni, la velocità effettiva diminuirà inevitabilmente.

* Per simulare la reazione del sistema a picchi di attività inaspettatamente estesi, è consigliabile eseguire test che generino una percentuale di errore oltre la barriera definita. Si otterranno dati sulla velocità effettiva non solo in termini di capacità, ma anche di costo di ripristino.

* Usare un numero di documenti per valutare il profilo delle prestazioni e riciclare i documenti in base ai propri modelli di carico di lavoro. Si noti che l'aggiunta di più documenti può modificare il profilo delle prestazioni.

* Tenere presente i contratti di servizio per IOPS e i limiti di velocità di trasferimento per l'archiviazione in uso. La velocità di trasferimento è diversa a seconda dei diversi tipi di archiviazione, ad esempio unità SSD e supporti in rotazione.

* Tenere presente che le prestazioni della CPU possono ridursi non solo a causa di attività del disco e di rete, ma anche perché le applicazioni back-end possono usare meccanismi di blocco e comunicazione con elaborazione distribuita, che possono causare un sottoutilizzo del processore.

* Eseguire test delle prestazioni per almeno due ore, non pochi minuti. L'indicizzazione può influire sulle prestazioni in modi che possono non essere immediatamente visibili. Ad esempio, le statistiche di Garbage Collection della JVM e le unioni per l'indicizzazione possono modificare il profilo delle prestazioni nel tempo.

* Considerare come gli aggiornamenti dell'indice possano avere un impatto significativo sulla velocità effettiva di inserimento dei dati e sulla limitazione con un cluster.

## Riepilogo

È importante comprendere come ridimensionare la soluzione man mano che aumentano i volumi di dati e il numero di richieste. Elasticsearch in esecuzione in Azure consente la scalabilità orizzontale e verticale. È possibile eseguirlo su VM con più risorse e distribuire un cluster Elasticsearch in una rete di VM. La gamma di opzioni può essere sorprendente. È più economico implementare un cluster su un numero elevato di VM piccole, in un cluster con un numero limitato di VM di grandi dimensioni o scegliere una configurazione intermedia? Inoltre, quante partizioni deve contenere ogni indice e quali sono i compromessi tra inserimento dei dati e prestazioni delle query? Il modo in cui le partizioni vengono distribuite tra i nodi può avere un impatto significativo sulla velocità effettiva di inserimento dei dati. L'uso di più partizioni può ridurre il livello di contesa interno che si verifica in una partizione, ma è necessario bilanciare questo vantaggio con il sovraccarico che l'uso di molte partizioni può imporre in un cluster. Per rispondere a queste domande in modo efficace, è necessario essere pronti per eseguire il test del sistema per determinare la strategia più appropriata.

Per i carichi di lavoro di inserimento dei dati, le prestazioni del sottosistema di I/O del disco sono un fattore critico. L'uso di unità SSD può migliorare la velocità effettiva, riducendo la latenza delle operazioni di scrittura del disco. Se non è necessarie grandi quantità di spazio su disco in un nodo, considerare l'uso di VM Standard con archiviazione temporanea al posto di VM più costose che supportano l'Archiviazione Premium.

## Appendice: Test delle prestazioni di inserimento dati con caricamento bulk

Questa appendice descrive il test delle prestazioni eseguito sul cluster Elasticsearch. I test sono stati eseguiti con JMeter in esecuzione in un set di VM separato. Per informazioni dettagliate sull'ambiente di test, vedere il documento [Creazione di un ambiente di test delle prestazioni per Elasticsearch in Azure][]. Per eseguire il test, è possibile creare manualmente il proprio piano di test con JMeter o usare gli script di test automatici disponibili separatamente. Per altre informazioni, vedere [Esecuzione dei test automatizzati delle prestazioni di Elasticsearch][].

Il carico di lavoro di inserimento dati ha eseguito un caricamento su larga scala di documenti tramite l'API di inserimento in blocco. Lo scopo di questo indice è stata la simulazione di un repository che riceve dati di log che rappresentano gli eventi di sistema per la ricerca e l'analisi successive. Ogni documento è stato archiviato in un singolo indice denominato *systembase* con il tipo *logs*. Tutti i documenti ha lo stesso schema predefinito descritto nella tabella seguente:

| Campo | Datatype | Esempio |
|---------------|---------------------|-----------------------------------|
| @timestamp | datetime | 2013-12-11T08:01:45.000Z |
| name | string | checkout.payment |
| message | string | Messaggio di richiesta in ingresso |
| severityCode | integer | 1 |
| gravità | string | info |
| hostname | string | sixshot |
| hostip | string (ip address) | 10\.0.0.4 |
| pid | int | 123 |
| tid | int | 4325 |
| appId | string (uuid) | {00000000-0000-0000-000000000000} |
| appName | string | mytestapp |
| appVersion | string | 0\.1.0.1234 |
| type | int | 5 |
| subtype | int | 1 |
| correlationId | guid | {00000000-0000-0000-000000000000} |
| OS | string | Linux |
| osVersion | string | 4\.1.1 |
| parameters | [ ] | {key:value,key:value} |

È possibile usare la richiesta seguente per creare l'indice. In molti test le impostazioni *number\_of\_replicas*, *refresh\_interval* e *number\_of\_shards* erano diverse rispetto ai valori illustrati di seguito.

> [AZURE.IMPORTANT] L'indice è stato eliminato e ricreato prima di ogni esecuzione di test.

```http
PUT /systembase
{
	"settings" : {
		"number_of_replicas": 1,
		"refresh_interval": "30s",
		"number_of_shards": "5"
	},
	"logs" : {
		"properties" : {
			"@timestamp": {
			"type": "date",
			"index" : "not_analyzed"
			},
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"message": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"severityCode": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"severity": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostname": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"pid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"tid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"appId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appName": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appVersion": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"type": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"subtype": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"correlationId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"os": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"osVersion": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"parameters": {
				"type": "string",     
				"index" : "not_analyzed"
			}
		}
	}
}
```

Ogni batch di inserimento in blocco era composto da 1000 documenti. Ogni documento è stato generato in base a una combinazione di valori casuali per i campi *severityCode*, *hostname*, *hostip*, *pid*, *tid*, *appName*, *appVersion*, *tipo*, *subtype* e *correlationId* e una selezione casuale di testo in un set fisso di termini per i campi *name*, *message*, *severity*, *os*, *osVersion*, *parameters*, *data1* e *data2*. Il numero di istanze dell'applicazione client usate per caricare i dati è stato selezionato attentamente per ottimizzare il volume di input riusciti. I test sono stati eseguiti per due ore per consentire al cluster di stabilizzarsi e ridurre l'influenza di qualsiasi anomalia temporanea nei risultati complessivi. In questo intervallo alcuni test hanno caricato quasi 1,5 milioni di documenti.

I dati sono stati generati in modo dinamico usando un campionatore JUnit Request personalizzato che è stato aggiunto a un gruppo di thread in un piano di test di JMeter. Il codice JUnit è stato creato con il modello JUnit Test Case nell'IDE di Eclipse.

> [AZURE.NOTE] Per informazioni su come creare un test JUnit per JMeter, vedere il documento [Distribuzione di un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch][].

Il frammento di codice seguente mostra il codice Java per il test di Elasticsearch 1.7.3. Si noti che la classe di test JUnit in questo esempio è denominata *ElasticsearchLoadTest2*:

```java
/* Java */
package elasticsearchtest2;

	import static org.junit.Assert.*;

	import org.junit.*;

	import java.util.*;

	import java.io.*;

	import org.elasticsearch.action.bulk.*;
	import org.elasticsearch.common.transport.*;
	import org.elasticsearch.client.transport.*;
	import org.elasticsearch.common.settings.*;
	import org.elasticsearch.common.xcontent.*;

	public class ElasticsearchLoadTest2 {

		private String [] names={"checkout","order","search","payment"};
		private String [] messages={"Incoming request from code","incoming operation succeeded with code","Operation completed time","transaction performed"};
		private String [] severity={"info","warning","transaction","verbose"};
		private String [] apps={"4D24BD62-20BF-4D74-B6DC-31313ABADB82","5D24BD62-20BF-4D74-B6DC-31313ABADB82","6D24BD62-20BF-4D74-B6DC-31313ABADB82","7D24BD62-20BF-4D74-B6DC-31313ABADB82"};

		private String hostname = "";
		private String indexstr = "";
		private String typestr = "";
		private int port = 0;
		private int itemsPerInsert = 0;
		private String clustername = "";
		private static Random rand=new Random();

		@Before
		public void setUp() throws Exception {
		}

		public ElasticsearchLoadTest2(String paras) {
		* Paras is a string containing a set of comma separated values for:
			hostname
			indexstr
			typestr
			port
			clustername
			node
			itemsPerInsert
		*/

			// Note: No checking/validation is performed

			String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
			String[] items = paras.split(delims);

			hostname = items[0];
			indexstr = items[1];
			typestr = items[2];
			port = Integer.parseInt(items[3]);
			clustername = items[4];
			itemsPerInsert = Integer.parseInt(items[5]);

			if (itemsPerInsert == 0)
				itemsPerInsert = 1000;
			}

		@After
		public void tearDown() throws Exception {
		}

		@Test
		public void BulkBigInsertTest() throws IOException {

			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();
				Random random = new Random();
				char[] exmarks = new char[12000];
				Arrays.fill(exmarks, 'x');
				String dataString = new String(exmarks);

				for(int i=1; i &lt; itemsPerInsert; i++){
					random.nextInt(10);
					int host=random.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[random.nextInt(names.length)])
						.field("message", messages[random.nextInt(messages.length)])
						.field("severityCode", random.nextInt(10))
						.field("severity", severity[random.nextInt(severity.length)])
						.field("hostname", "Hostname"+host)
						.field("hostip", "10.1.0."+host)
						.field("pid",random.nextInt(10))
						.field("tid",random.nextInt(10))
						.field("appId", apps[random.nextInt(apps.length)])
						.field("appName", "application" + host)
						.field("appVersion", random.nextInt(5))
						.field("type", random.nextInt(6))
						.field("subtype", random.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
						.field("data1",dataString)
						.field("data2",dataString)
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}

		@Test
		public void BulkDataInsertTest() throws IOException {
			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();

				for(int i=1; i&lt; itemsPerInsert; i++){
					rand.nextInt(10);
					int host=rand.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[rand.nextInt(names.length)])
						.field("message", messages[rand.nextInt(messages.length)])
						.field("severityCode", rand.nextInt(10))
						.field("severity", severity[rand.nextInt(severity.length)])
						.field("hostname", "Hostname" + host)
						.field("hostip", "10.1.0."+host)
						.field("pid",rand.nextInt(10))
						.field("tid",rand.nextInt(10))
						.field("appId", apps[rand.nextInt(apps.length)])
						.field("appName", "application"+host)
						.field("appVersion", rand.nextInt(5))
						.field("type", rand.nextInt(6))
						.field("subtype", rand.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}
	}
```

Le matrici *String* private *names*, *messages*, *severity* e *apps* contengono un set limitato di valori da cui vengono selezionati elementi in modo casuale. Gli elementi di dati rimanenti per ogni documento vengono generati in fase di esecuzione.

Il costruttore che accetta il parametro *String* viene richiamato da JMeter e i valori passati nella stringa vengono specificati come parte della configurazione del campionatore JUnit Request. Per questo test JUnit il parametro *String* deve contenere le informazioni seguenti:

* **Hostname**. È il nome o l'indirizzo IP del servizio di bilanciamento del carico di Azure. Il servizio di bilanciamento del carico prova a distribuire la richiesta tra i nodi dati nel cluster. Se non si usa un servizio di bilanciamento del carico è possibile specificare l'indirizzo di un nodo del cluster, ma tutte le richieste saranno indirizzate a quel nodo che potrebbe diventare un collo di bottiglia.

* **Indexstr**. È il nome dell'indice in cui vengono aggiunti i dati generati dal test JUnit. Se l'indice è stato creato come descritto sopra, questo valore deve essere *systembase*.

* **Typestr**. È il tipo dell'indice in cui vengono archiviati i dati. Se l'indice è stato creato come descritto sopra, questo valore deve essere *logs*.

* **Port**. È la porta a cui connettersi nell'host. Nella maggior parte dei casi questo valore deve essere impostato su 9300. È la porta usata da Elasticsearch per ascoltare le richieste dell'API client. La porta 9200 viene usata solo per le richieste HTTP.

* **Clustername**. È il nome del cluster Elasticsearch contenente l'indice.

* **ItemsPerInsert**. È un parametro numerico che indica il numero di documenti da aggiungere in ogni batch di inserimento in blocco. Il valore delle dimensioni del batch predefinito è 1000.

I dati per la stringa del costruttore vengono specificati nella pagina di JUnit Request usata per configurare il campionatore JUnit in JMeter. La figura seguente mostra un esempio:

![](media/guidance-elasticsearch/data-ingestion-image22.png)

I metodi *BulkInsertTest* e *BigBulkInsertTest* eseguono l'effettiva operazione di generazione e caricamento dei dati. Entrambi i metodi sono molto simili, possono connettersi al cluster Elasticsearch e quindi creare un batch di documenti, come determinato dal parametro della stringa del costruttore *ItemsPerInsert*. I documenti vengono aggiunti all'indice tramite l'API Bulk di Elasticsearch. La differenza tra i due metodi è che i campi stringa *data1* e *data2* in ogni documento vengono omessi dal caricamento nel metodo *BulkInsertTest*, ma sono riempiti con stringhe di 12000 caratteri nel metodo *BigBulkInsertTest*. Si noti che è possibile scegliere quali di questi metodi eseguire tramite la casella *Test Method*, evidenziata nella figura precedente, nella pagina JUnit Request in JMeter.

> [AZURE.NOTE] Il codice di esempio presentato qui usa la libreria Transport Client di Elasticsearch 1.7.3. Se si usa Elasticsearch 2.0.0 o versione successiva, è necessario usare la libreria appropriata per la versione selezionata. Per altre informazioni sulla libreria Transport Client di Elasticsearch 2.0.0, vedere la pagina [Transport Client](https://www.elastic.co/guide/en/elasticsearch/client/java-api/2.0/transport-client.html) nel sito Web Elasticsearch.

[Configurazione delle opzioni di resilienza e ripristino di Elasticsearch in Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Creazione di un ambiente di test delle prestazioni per Elasticsearch in Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Esecuzione dei test automatizzati delle prestazioni di Elasticsearch]: guidance-elasticsearch-running-automated-performance-tests.md
[Distribuzione di un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->