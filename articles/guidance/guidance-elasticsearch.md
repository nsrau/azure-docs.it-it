<properties
   pageTitle="Esecuzione di Elasticsearch in Azure | Microsoft Azure"
   description="Come installare, configurare ed eseguire Elasticsearch in Azure."
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
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Esecuzione di Elasticsearch in Azure

Questo articolo fa [parte di una serie](guidance-elasticsearch-introduction.md).

## Panoramica

Elasticsearch è un motore di ricerca e un database open source altamente scalabile. È adatto a situazioni che richiedono velocità nell'analisi e nell'individuazione di informazioni in set di dati di grandi dimensioni. Gli scenari comuni includono:

- Ricerca di testo libero su larga scala, in cui i documenti che corrispondono a combinazioni di termini di ricerca possono essere trovati e recuperati rapidamente.
- Registrazione di eventi, in cui le informazioni possono arrivare da varie origini. Potrebbe essere necessario analizzare i dati per determinare come una catena di eventi abbia portato a una conclusione specifica.
- Archiviazione di dati acquisiti da dispositivi remoti e altre origini. I dati possono contenere informazioni diverse, ma un requisito comune è presentare queste informazioni in una serie di dashboard per consentire a un operatore di comprendere lo stato generale del sistema. Le applicazioni possono usare tali informazioni anche per prendere rapidamente decisioni relative al flusso delle operazioni aziendali e sui dati da eseguire.
- Controllo delle scorte, in cui le modifiche vengono registrate all'inventario man mano che i prodotti vengono venduti. I sistemi aziendali possono usare queste informazioni per segnalare i livelli di scorte agli utenti e riordinare le scorte se il livello di un prodotto scende troppo. Gli analisti possono esaminare i dati per ricavarne tendenze che permettono di determinare quali circostanze fanno registrare le vendite migliori.
- Analisi finanziaria, in cui le informazioni relative al mercato vengono ricevute quasi in tempo reale. È possibile generare dashboard che riportano aggiornamenti continui sulle prestazioni di vari strumenti finanziari, da usare successivamente nelle decisioni di acquisto e di vendita.

Questo documento fornisce una breve introduzione alla struttura generale di Elasticsearch e descrive quindi come implementare un cluster Elasticsearch con Azure. Il documento è incentrato sulle procedure consigliate per la distribuzione di un cluster Elasticsearch, con particolare attenzione ai vari requisiti di gestione e di prestazioni funzionali del sistema, e illustra come la configurazione e la topologia dovrebbero essere selezionate in base ai requisiti.

> [AZURE.NOTE] Questa guida presuppone una certa conoscenza di base di [Elasticsearch][].

## Struttura di Elasticsearch

Elasticsearch è un database di documenti altamente ottimizzato per funzionare come un motore di ricerca. I documenti vengono serializzati in formato JSON. I dati sono contenuti in indici, implementati tramite [Apache Lucene][], ma i dettagli vengono ricavati dalla visualizzazione e non è necessario conoscere a fondo Lucene per poter usare Elasticsearch.

### Cluster, nodi, indici e partizioni

Elasticsearch implementa un'architettura cluster che usa il partizionamento orizzontale per distribuire dati tra più nodi e la replica per garantire un'elevata disponibilità.

I documenti vengono archiviati in indici. L'utente può specificare quali campi vengono usati in un documento per identificarlo in modo univoco all'interno di un indice oppure il sistema può generare automaticamente un campo chiave e valori chiave. L'indice viene usato per organizzare fisicamente i documenti e costituisce il mezzo principale per l'individuazione di documenti. Elasticsearch crea automaticamente un set di strutture aggiuntive che funzionano come indici invertiti sui campi rimanenti per abilitare la ricerca veloce ed eseguire analisi in una raccolta.

Un indice è costituito da un set di partizioni. I documenti vengono distribuiti in modo uniforme tra le partizioni usando un meccanismo di hash basato sui valori delle chiavi di indice e sul numero di partizioni nell'indice. Quando un documento viene allocato a una partizione non verrà più spostato a meno che non venga modificata la relativa chiave di indice. Elasticsearch distribuisce le partizioni in tutti i nodi dati disponibili in un cluster. Un singolo nodo inizialmente può contenere uno o più partizioni appartenenti allo stesso indice. Man mano che nuovi nodi vengono aggiunti al cluster, tuttavia, Elasticsearch sposta le partizioni per garantire il bilanciamento del carico nel sistema. Lo stesso ribilanciamento viene eseguito quando i nodi vengono rimossi.

Gli indici possono essere replicati. In tal caso ogni partizione nell'indice viene copiata. Elasticsearch fa in modo che ogni partizione originale di un indice, definita "partizione primaria", e la relativa replica risiedano sempre in nodi diversi.

> [AZURE.NOTE] Il numero di partizioni in un indice non può essere modificato facilmente dopo che l'indice è stato creato, ma è possibile aggiungere repliche.

Quando un documento viene aggiunto o modificato, tutte operazioni di scrittura vengono eseguite prima sulla partizione primaria e successivamente su ogni replica. Per impostazione predefinita, questo processo viene eseguito in modo sincrono per assicurare la coerenza. Elasticsearch usa la concorrenza ottimistica con controllo delle versioni durante la scrittura di dati. Le operazioni di lettura possono essere eseguite usando la partizione primaria o una delle relative repliche.

La figura 1 mostra gli aspetti essenziali di un cluster Elasticsearch costituito da tre nodi. È stato creato un indice costituito da due partizioni primarie con due repliche per ogni partizione, sei partizioni in tutto.

![](media/guidance-elasticsearch-general-cluster1.png)

**Figura 1.**
Cluster Elasticsearch semplice costituito da due nodi primari e due set di repliche

In questo cluster la partizione primaria 1 e la partizione primaria 2 si trovano in nodi distinti per bilanciare il carico tra di essi. Le repliche sono distribuite in modo analogo. In caso di errore di un singolo nodo, i nodi rimanenti hanno informazioni sufficienti per consentire al sistema di continuare a funzionare normalmente. Se necessario, Elasticsearch promuoverà una partizione di replica a partizione primaria, se la partizione primaria corrispondente non è disponibile.
Quando viene avviata l'esecuzione di un nodo, questo può avviare un nuovo cluster, se si tratta del primo nodo del cluster, o unirsi a un cluster esistente. Il cluster a cui appartiene un nodo è determinato dall'impostazione *cluster.name* nel file elasticsearch.yml.

### Ruoli dei nodi

I nodi in un cluster Elasticsearch possono eseguire i ruoli seguenti:

- Nodo dati, che può includere una o più partizioni contenenti dati di indice.
- Nodo client, che non conserva i dati dell'indice ma gestisce le richieste in ingresso inviate dalle applicazioni client al nodo dati appropriato.
- Nodo master, che non conserva i dati dell'indice ma esegue operazioni di gestione del cluster, ad esempio la gestione e la distribuzione di informazioni di routing per il cluster come l'elenco dei nodi che contengono partizioni, l'individuazione dei nodi disponibili, lo spostamento delle partizioni nei nodi di volta in volta disponibili e il coordinamento del ripristino dopo un errore del nodo. Più nodi possono essere configurati come master, ma solo uno verrà effettivamente eletto per svolgere tali funzioni. In caso di errore di questo nodo, ha luogo un'altra elezione e uno degli altri nodi master idonei prenderà il posto del primo.

Per impostazione predefinita, i nodi Elasticsearch eseguono tutti e tre i ruoli, per consentire all'utente di compilare un cluster di lavoro completo in un singolo computer a scopo di sviluppo e prova, ma è possibile modificarne il funzionamento usando le impostazioni *node.data* e *node.master* nel file *elasticsearch.yml*, come indicato di seguito:

```yaml
node.data: true
node.master: false
```

**Configurazione per un nodo dati**

```yaml
node.data: false
node.master: false
```

**Configurazione per un nodo client**

```yaml
node.data: false
node.master: true
```

**Configurazione per un nodo master**

> [AZURE.NOTE] Il nodo master eletto è fondamentale per l'integrità del cluster. Gli altri nodi effettuano regolarmente il ping del nodo master per verificarne la disponibilità. Se il nodo master eletto funge anche da nodo dati, potrebbe diventare occupato e non rispondere ai ping. Questa situazione viene considerata un errore del nodo master e viene eletto al suo posto uno degli altri nodi master. Se il nodo master originale è effettivamente ancora disponibile, ciò può dare luogo a un cluster con due master eletti e generare un problema di "split brain", con il rischio di danneggiamento dei dati e altri problemi. Il documento relativo alla configurazione, al test e all'analisi di resilienza e ripristino di Elasticsearch descrive come configurare il cluster per ridurre le probabilità che ciò accada. In ultima analisi, nei cluster di medie e grandi dimensioni è consigliabile usare nodi master dedicati che non accettano responsabilità di gestione dei dati.

I nodi in un cluster condividono le informazioni relative agli altri nodi del cluster e alle relative partizioni usando un [protocollo di gossip][]. Le applicazioni client che eseguono l'archiviazione e il recupero di dati possono connettersi a qualsiasi nodo di un cluster. Le richieste vengono indirizzate in modo trasparente al nodo appropriato. Quando un'applicazione client richiede dati al cluster, il nodo che riceve per primo la richiesta si occupa di indirizzare l'operazione, di comunicare con i nodi pertinenti per il recupero dei dati e quindi di aggregare i risultati prima di restituirli all'applicazione client. L'uso dei nodi client per la gestione delle richieste libera i nodi dati dall'esecuzione di operazioni scatter-gather. In questo modo i nodi dati possono continuare a gestire i dati. Disabilitando il trasporto HTTP per i nodi dati è possibile impedire alle applicazioni client di comunicare accidentalmente con i nodi dati e costringerli così a funzionare come nodi client:

```yaml
http.enabled: false
```

I nodi dati possono comunque comunicare con altri nodi dati, nodi client e nodi master dedicati nella stessa rete attraverso il modulo di trasporto di Elasticsearch, che usa i socket TCP per la connessione diretta tra i nodi, ma le applicazioni client possono connettersi soltanto a nodi client tramite HTTP. La figura 2 mostra una topologia che include una combinazione di nodi master dedicati, nodi client e nodi dati in un cluster Elasticsearch.

![](media/guidance-elasticsearch-general-cluster2.png)

**Figura 2.**
Cluster Elasticsearch con diversi tipi di nodi

### Costi e vantaggi legati all'uso dei nodi client

Quando un'applicazione invia una query a un cluster Elasticsearch, il nodo a cui l'applicazione si connette è responsabile dell'indirizzamento del processo di query. Il nodo inoltra la richiesta a tutti i nodi dati e raccoglie i risultati per poi restituire le informazioni all'applicazione. Se una query prevede aggregazioni e altri calcoli, il nodo a cui si connette l'applicazione esegue le operazioni necessarie dopo aver recuperato i dati da ognuno degli altri nodi. Questo processo scatter-gather può richiedere l'utilizzo di una notevole quantità di risorse di elaborazione e di memoria.

La presenza di nodi client dedicati per l'esecuzione di queste attività permette di usare i nodi dati per la sola gestione e archiviazione dei dati. Molti scenari che prevedono aggregazioni e query complesse possono trarre vantaggio dall'uso di nodi client dedicati. L'impatto, tuttavia, varia a seconda dello scenario, del carico di lavoro e delle dimensioni del cluster. Ad esempio, i carichi di lavoro di inserimento di dati potrebbero rivelarsi meno efficienti con l'uso dei nodi client a causa dell'hop di rete aggiuntivo necessario durante l'archiviazione dei dati. In un cluster a tre nodi con sei partizioni se il sistema non è configurato con nodi client dedicati, a parità di fattori ambientali e carichi dei nodi, esiste una probabilità su tre che un'applicazione che archivia o modifica i dati si connetta direttamente alla partizione appropriata, senza che sia necessario eseguire un passaggio di rete aggiuntivo in un terzo dei casi. D'altra parte, i carichi di lavoro che eseguono aggregazioni complesse possono trarre vantaggio dall'uso di client dedicati dal momento che un singolo nodo sarà responsabile per ogni set di operazioni scatter-gather eseguito da tali operazioni. In un ambiente misto è consigliabile essere pronti a eseguire test delle prestazioni per valutare l'impatto legato all'uso di nodi client per i carichi di lavoro specifici.

> [AZURE.NOTE] Il documento relativo all'ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query con Elasticsearch in Azure riepiloga una serie di benchmark eseguiti dal team di sviluppo Microsoft Patterns & Practices, in parte a questo scopo.

### Connessione a un cluster

Elasticsearch espone una serie di API REST per la compilazione di applicazioni client e l'invio di richieste a un cluster. Se si sviluppano applicazioni con .NET Framework, sono disponibili due API di livello superiore: [Elasticsearch.Net e NEST][].

Se si compilano applicazioni client con Java, è possibile usare l'[API Node Client][] per creare nodi client in modo dinamico e aggiungerli al cluster. La creazione dinamica di nodi client risulta utile se il sistema usa un numero relativamente ridotto di connessioni di lunga durata. I nodi client creati usando l'API Node includono una mappa di routing dei cluster fornita dal nodo master, con i dettagli dei nodi e delle relative partizioni. Queste informazioni consentono all'applicazione Java di connettersi direttamente ai nodi appropriati quando esegue l'indicizzazione o query sui dati, riducendo così il numero di hop necessari rispetto all'uso di altre API. Il costo di questo approccio è costituito dal costo iniziale di registrazione del nodo client nel cluster. Se un gran numero di nodi client compare e scompare rapidamente, la manutenzione e la distribuzione della mappa di routing dei cluster possono avere un impatto notevole.

**Bilanciamento del carico di connessione**

Elasticsearch consente diversi meccanismi per l'implementazione del bilanciamento del carico di connessione. L'elenco seguente fornisce un riepilogo di alcuni approcci comuni:

**Bilanciamento del carico basato su client**: se si compilano applicazioni client usando le API Elasticsearch.Net o NEST, è possibile usare un pool di connessioni per eseguire il round robin delle richieste di connessione tra i nodi, per bilanciare il carico delle richieste senza dover ricorrere a un servizio di bilanciamento del carico esterno. Il frammento di codice seguente crea un oggetto *ElasticsearchClient* configurato con gli indirizzi di tre nodi. Le richieste provenienti dall'applicazione client verranno distribuite tra i nodi:

```csharp
// C#
var node1 = new Uri("http://node1.example.com:9200");
var node2 = new Uri("http://node2.example.com:9200");
var node3 = new Uri("http://node3.example.com:9200");

var connectionPool = new SniffingConnectionPool(new[] {node1, node2, node3});
var config = new ConnectionConfiguration(connectionPool);
var client = new ElasticsearchClient(config);
```

> [AZURE.NOTE] Una funzionalità simile è disponibile per le applicazioni Java tramite l'[API Transport Client][].

**Bilanciamento del carico basato su server**: è possibile usare un servizio di bilanciamento del carico separato per la distribuzione delle richieste ai nodi. Il vantaggio di questo approccio è la trasparenza dell'indirizzo. Non è necessario configurare le applicazioni client con i dettagli di ogni nodo, in questo modo è più semplice aggiungere, rimuovere o spostare i nodi senza modificare il codice client. La figura 3 mostra una configurazione che usa un servizio di bilanciamento del carico per instradare le richieste a un set di nodi client, ma la stessa strategia può essere usata per connettersi direttamente ai nodi dati se i nodi client non vengono usati.

> [AZURE.NOTE] Il [servizio di bilanciamento del carico di Azure][] può essere usato per esporre il cluster a Internet pubblico. In alternativa, è possibile usare un [servizio di bilanciamento del carico interno][] se le applicazioni client e i cluster sono contenuti interamente nella stessa rete privata virtuale (VNET).

![](media/guidance-elasticsearch-general-clientappinstances.png)

**Figura 3.**
Istanze dell'applicazione client che si connettono a un cluster Elasticsearch tramite il servizio di bilanciamento del carico di Azure

**Bilanciamento del carico personalizzato**: è possibile usare [nginx][] come server proxy inverso anziché il servizio di bilanciamento del carico di Azure. nginx offre diversi metodi di bilanciamento del carico, inclusi round robin, hash in base all'indirizzo IP del client e instradamento delle richieste alla destinazione con il minor numero di connessioni correnti.

> [AZURE.NOTE] È possibile distribuire un server nginx come una macchina virtuale di Azure. Per mantenere la disponibilità, è necessario creare almeno due server nginx nello stesso set di disponibilità di Azure.

Nella decisione relativa all'uso del bilanciamento del carico e dell'implementazione da usare, prendere in considerazione i punti seguenti:

- La connessione allo stesso nodo per gestire tutte le richieste per tutte le istanze di un'applicazione può trasformare il nodo in un collo di bottiglia. Man mano che il numero di thread disponibili nel nodo si esaurisce, le richieste vengono accodate e possono essere rifiutate se la lunghezza della coda diventa eccessiva. Non impostare come hardcoded i dettagli di connessione di un singolo nodo nel codice di un'applicazione che potrebbe essere distribuito a molti utenti.
- Il meccanismo di round robin delle API Elasticsearch.Net, NEST e Transport Client gestisce le richieste di connessione con esito negativo provando nuovamente a eseguire la connessione con il nodo disponibile successivo nel pool di connessioni. Una connessione a un nodo che non risponde nel pool può essere contrassegnata temporaneamente come *inattiva* e tornare attiva in un secondo momento. Il pool può quindi effettuare il ping del nodo per determinare se è tornato attivo.
- Il servizio di bilanciamento del carico di Azure può reindirizzare in modo trasparente le richieste ai nodi in base a una serie di fattori quali, indirizzo IP del client, porta del client, indirizzo IP di destinazione, porta di destinazione e tipo di protocollo. Seguendo questa strategia, un'istanza di un'applicazione client in esecuzione in un determinato computer verrà indirizzata molto probabilmente allo stesso nodo Elasticsearch. A seconda della configurazione del probe del servizio di bilanciamento del carico, se il servizio Elasticsearch ha esito negativo in questo nodo ma la macchina virtuale continua a essere in esecuzione, tutte le connessioni a questo nodo andranno in timeout mentre le connessioni ad altri nodi da altre istanze client potranno continuare ad avere esito positivo.
- È possibile configurare il servizio di bilanciamento del carico di Azure perché rimuova dalla rotazione i nodi che non riescono a rispondere in modo appropriato alle richieste del probe di integrità eseguite dal servizio di bilanciamento del carico.

### Individuazione di nodi

Elasticsearch è basato sulla comunicazione peer-to-peer, quindi l'individuazione di altri nodi in un cluster è una parte importante del ciclo di vita di un nodo. L'individuazione di nodi permette di aggiungere in modo dinamico nuovi nodi dati a un cluster, che a sua volta può aumentare il numero di istanze in modo trasparente. Se un nodo dati non riesce a rispondere alle richieste di comunicazione da altri nodi, un nodo master può determinarne lo stato di errore ed eseguire i passaggi necessari per riallocare le relative partizioni ad altri nodi dati operativi.

L'individuazione di nodi in Elasticsearch viene gestita con un modulo di individuazione, un plug-in che può essere impostato per l'uso di un meccanismo di individuazione diverso. Il modulo di individuazione predefinito, [Zen][], fa sì che un nodo invii richieste di ping per trovare altri nodi nella stessa rete. Se altri nodi rispondono, viene usato il protocollo gossip per lo scambio di informazioni. Un nodo master può quindi distribuire le partizioni al nuovo nodo, se si tratta di un nodo dati, e ribilanciare il cluster.

Il modulo di individuazione Zen gestisce anche il processo di elezione del master e il protocollo per il rilevamento di errori dei nodi.

Nelle versioni di Elasticsearch precedenti alla 2.0 il modulo di individuazione Zen usa le comunicazioni multicast per consentire i contatti tra i nodi. Questo semplifica molto l'introduzione di un nuovo nodo in un cluster, ma può anche causare problemi di sicurezza nel caso in cui lo stesso nome del cluster venga usato da un'altra installazione di Elasticsearch nella stessa rete. La nuova installazione viene considerata parte dello stesso cluster e alcune partizioni potrebbero essere indirizzate ai nodi di questa installazione. Se si eseguono i nodi di Elasticsearch come macchine virtuali di Azure, la messaggistica multicast non è supportata. Per questi motivi, è necessario configurare l'individuazione Zen per l'uso della messaggistica unicast e perché indichi un elenco di nodi di contatto validi nel file di configurazione elasticsearch.yml.

> [AZURE.NOTE] Con Elasticsearch 2.0 e versioni successive il multicast non è più il meccanismo di individuazione predefinito.

Se si ospita un cluster Elasticsearch all'interno di una rete virtuale di Azure, è possibile specificare che l'indirizzo IP privato assegnato da DHCP a ogni macchina virtuale del cluster deve rimanere allocato, ovvero statico. È possibile configurare la messaggistica unicast dell'individuazione Zen con questi indirizzi IP statici. Se si usano macchine virtuali con indirizzi IP dinamici, tenere presente che l'arresto di una macchina virtuale può comportare l'allocazione di un nuovo indirizzo IP, che renderà più difficile l'individuazione. Per gestire questo scenario, è possibile scambiare il modulo di individuazione Zen con il [plug-in Azure Cloud][]. Questo plug-in usa l'API di Azure per implementare il meccanismo di individuazione, basato sulle informazioni di sottoscrizione di Azure.

> [AZURE.NOTE] La versione corrente del plug-in Azure Cloud richiede l'installazione del certificato di gestione per la sottoscrizione di Azure nell'archivio chiavi Java nel nodo Elasticsearch, nonché il percorso e le credenziali per l'accesso all'archivio chiavi nel file elasticsearch.yml. Questo file viene mantenuto in testo non crittografato, è quindi estremamente importante fare in modo che sia accessibile unicamente per l'account che esegue il servizio Elasticsearch. Questo approccio potrebbe non essere compatibile con le distribuzioni di Gestione risorse di Azure. Per questi motivi, è consigliabile usare indirizzi IP statici per i nodi master e usare questi nodi per implementare la messaggistica unicast dell'individuazione Zen all'interno del cluster. Nella configurazione seguente, tratta dal file elasticsearch.yml per un nodo dati di esempio, gli indirizzi IP host fanno riferimento a nodi master nel cluster:

>`discovery.zen.ping.multicast.enabled: false`  
`discovery.zen.ping.unicast.hosts: ["10.0.0.10","10.0.0.11","10.0.0.12"]`

## Indicazioni generali di sistema

È possibile eseguire Elasticsearch in vari tipi di computer, dal singolo computer portatile al cluster di server avanzati. Tuttavia, una maggiore disponibilità di risorse in termini di memoria, potenza di calcolo e velocità dei dischi consente di ottenere prestazioni migliori. Le sezioni seguenti forniscono un riepilogo dei requisiti hardware e software di base per l'esecuzione di Elasticsearch.

### Requisiti di memoria
Elasticsearch prova ad archiviare dati in memoria per rapidità. Un server di produzione che ospita un nodo per una tipica distribuzione commerciale di medie dimensioni o enterprise in Azure deve avere tra 14 GB e 28 GB di RAM (macchine virtuali D3 o D4). È preferibile distribuire il carico tra più nodi anziché creare nodi con più memoria. Esperimenti hanno dimostrato che l'uso di nodi più grandi con più memoria può allungare i tempi di recupero in caso di errore. La creazione di cluster con un numero molto elevato di nodi di piccole dimensioni, tuttavia, può aumentare la velocità effettiva e la disponibilità ma aumenta anche l'impegno richiesto per la gestione di un sistema di questo tipo.

Allocare il 50% della memoria disponibile in un server all'heap di Elasticsearch. Se si usa Linux, impostare la variabile di ambiente ES\_HEAP\_SIZE prima di eseguire Elasticsearch. In alternativa, se si usa Windows o Linux, è possibile specificare le dimensioni della memoria nei parametri *Xmx* e *Xms* quando si avvia Elasticseach. Impostare entrambi questi parametri sullo stesso valore per evitare il ridimensionamento dell'heap in fase di esecuzione da parte di Java Virtual Machine (JVM). Non allocare più di 30 GB. Usare la memoria rimanente per la cache del file del sistema operativo.

> [AZURE.NOTE] Elasticsearch utilizza la libreria Lucene per creare e gestire gli indici. Le strutture Lucene usano un formato basato su disco e la memorizzazione di queste strutture nella cache del file system permette di migliorare notevolmente le prestazioni.

> Elasticsearch è scritto in Java. La dimensione massima dell'heap ottimale per Java in un computer a 64 bit è appena superiore a 30 GB. Al di sopra di questa dimensione Java passa all'uso di un meccanismo esteso per fare riferimento agli oggetti sull'heap. Questo aumenta i requisiti di memoria per ogni oggetto e riduce le prestazioni. Il Garbage Collector Java predefinito, Concurrent Mark and Sweep (CMS), può registrare prestazioni non ottimali anche quando la dimensione dell'heap è superiore a 30 GB. Attualmente non è consigliabile passare a un Garbage Collector diverso, perché Elasticsearch e Lucene sono stati testati solo con quello predefinito.

Evitare l'overcommit della memoria, perché lo scambio della memoria principale su disco incide molto negativamente sulle prestazioni. Se possibile, disabilitare completamente lo scambio. I dettagli variano a seconda del sistema operativo. Se non è possibile, abilitare l'impostazione *mlockall* nel file di configurazione di Elasticsearch, elasticsearch.yml, come indicato di seguito:

```yaml
bootstrap.mlockall: true
```

Questa impostazione di configurazione determina il blocco della memoria della JVM e ne impedisce lo scambio da parte del sistema operativo.

### Requisiti del disco e del file system

Usare unità SSD per l'archiviazione di partizioni. Le dimensioni dei dischi devono poter contenere la quantità massima di dati prevista per le partizioni, ma è possibile aggiungere altri dischi in un secondo momento. È possibile estendere una partizione su più dischi in un nodo.

> [AZURE.NOTE] Elasticsearch comprime i dati per i campi archiviati usando l'algoritmo LZ4. A partire da Elasticsearch 2.0 è possibile modificare il tipo di compressione. È possibile impostare l'algoritmo di compressione su DEFLATE come richiesto dalle utilità *zip* e *gzip*. Questa tecnica di compressione può comportare un maggiore utilizzo delle risorse, ma è indicata per gli indici a freddo, ad esempio i dati di log archiviati. Questo approccio può contribuire a ridurre le dimensioni dell'indice.

Non è essenziale che tutti i nodi di un cluster abbiano la stessa capacità e lo stesso layout del disco. Tuttavia, un nodo con una capacità del disco molto grande rispetto agli altri nodi del cluster attira più dati e richiede una maggiore potenza di elaborazione per gestirli. Di conseguenza, il nodo può diventare "caldo" rispetto agli altri e questo può influire sulle prestazioni.

> [AZURE.NOTE] Azure offre diverse opzioni di archiviazione su disco, incluse l'archiviazione standard, l'archiviazione premium e l'archiviazione temporanea. L'archiviazione standard si basa su supporti in rotazione mentre l'archiviazione premium fa uso di unità SSD. A seconda dello SKU usato per implementare una macchina virtuale, l'archiviazione temporanea può essere implementata come supporto in rotazione, per le macchine virtuali della serie A, o come unità SSD, per le macchine virtuali della serie D e versioni successive. Quando si prendono in considerazione le opzioni di archiviazione è necessario trovare un equilibrio tra vari compromessi a livello finanziario e tecnico. Tali compromessi sono descritti in dettaglio nel documento Ottimizzazione delle prestazioni di inserimento dei dati con Elasticsearch in Azure.

Se possibile, usare RAID 0 (striping). Non sono necessarie altre forme di RAID che implementano il mirroring e la parità, dal momento che Elasticsearch fornisce una soluzione a disponibilità elevata sotto forma di repliche e i dischi di Azure salvano comunque tre copie dei dati del disco.

> [AZURE.NOTE] Nelle versioni di Elasticsearch precedenti alla 2.0.0 è anche possibile implementare lo striping a livello di software specificando più directory nell'impostazione di configurazione *path.data*. In Elasticsearch 2.0.0 questa forma di striping non è più supportata. È invece possibile allocare diverse partizioni a percorsi diversi, ma tutti i file in una singola partizione vengono scritti nello stesso percorso. Se lo striping dei dati è necessario, dovrebbe essere eseguito a livello di hardware o di sistema operativo.

Si noti anche che la velocità effettiva delle operazioni di I/O per i dischi di Azure collegati a una macchina virtuale è limitata dal gruppo di archiviazione a cui appartengono. Un singolo account di archiviazione standard può gestire una frequenza delle richieste massima di 20.000 IOPS. Questa limitazione non si applica all'archiviazione premium. Se questo valore è inferiore al traffico di I/O previsto per il disco, distribuire i dischi delle macchine virtuali del cluster in più account di archiviazione, tenendo presente che una singola sottoscrizione può creare fino a 100 account di archiviazione.

La libreria Lucene può usare un numero elevato di file per l'archiviazione dei dati di indice ed Elasticsearch può aprire un numero notevole di socket per la comunicazione tra i nodi e con i client. Assicurarsi che il sistema operativo sia configurato per supportare un numero sufficiente di descrittori di file aperti, fino a 64.000 se è disponibile una quantità di memoria sufficiente. Si noti che la configurazione predefinita per molte distribuzioni di Linux limita il numero di descrittori di file aperti a 1024, che non è un numero sufficiente.

Elasticsearch usa una combinazione di I/O mappato alla memoria (mmap) e Java New I/O (NIO) per ottimizzare l'accesso simultaneo ai file di dati e agli indici. Se si usa Linux, è necessario configurare il sistema operativo in modo che la memoria virtuale disponibile sia sufficiente e ci sia spazio per aree della mappa di memoria da 256 K.

> [AZURE.NOTE] Molte distribuzioni Linux usano per impostazione predefinita l'utilità di pianificazione Completely Fair Queuing (CFQ) per la disposizione della scrittura dei dati su disco. Questa utilità di pianificazione non è ottimizzata per le unità SSD. È consigliabile riconfigurare il sistema operativo per usare l'utilità di pianificazione NOOP o l'utilità di pianificazione Deadline, entrambe più efficaci per le unità SSD.

### Requisiti della CPU

Usare CPU con più core. Un numero maggiore di core è preferibile all'uso di CPU più veloci con un minor numero di core. Questo perché le dimensioni del pool di thread predefinito di Elasticsearch vengono configurate in base al numero di core della CPU disponibili. Gli algoritmi usati da Elasticsearch sono ottimizzati in base a questi calcoli ed è consigliabile non modificare le impostazioni del pool di thread predefinito di Elasticsearch.

> [AZURE.NOTE] Le macchine virtuali di Azure sono disponibili in diverse configurazioni della CPU e supportano da 1 a 32 core. Per un nodo dati è consigliabile partire da una macchina virtuale serie D standard e selezionare lo SKU D3, a 4 core, oppure il D4, a 8 core. Il D3 include anche 14 GB di RAM, mentre il D4 include 28 GB di RAM. Se è necessaria l'archiviazione premium su disco, è possibile usare una macchina virtuale serie DS, ad esempio un computer DS3 o DS4. Se si ritiene che le prestazioni della CPU possano limitare in modo determinante la velocità effettiva, la serie Dv2 offre processori Intel Xeon da 2,4 GHz più potenti. La serie G, per l'archiviazione standard, e la serie GS, per l'archiviazione premium, usano processori Xeon E5 V3 utili per i carichi di lavoro a elevato utilizzo di calcolo, ad esempio le aggregazioni su larga scala. Per informazioni aggiornate, vedere [Dimensioni delle macchine virtuali][].

### Requisiti di rete

Elasticsearch richiede una larghezza di banda compresa tra 1 e 10 Gbps, a seconda delle dimensioni e della volatilità dei cluster che implementa. Man mano che vengono aggiunti più nodi a un cluster, Elasticsearch esegue la migrazione delle partizioni tra i nodi. Elasticsearch presuppone che il tempo di comunicazione tra tutti i nodi sia quasi equivalente e non considera i percorsi relativi delle partizioni contenute nei nodi. La replica può anche incontrare un gran numero di operazioni di I/O di rete tra le partizioni. Per questi motivi, si sconsiglia di creare cluster in nodi situati in località geografiche diverse.

Si noti che la larghezza di banda di rete nelle macchine virtuali di Azure è limitata sia dallo SKU che dall'utilizzo totale della rete da parte dell'hardware fisico in cui vengono eseguite le macchine virtuali. Le macchine virtuali che condividono hardware avranno una larghezza di banda minore rispetto a quelle eseguite in computer dedicati, ma la loro distribuzione ha un costo inferiore. Ciò significa che, a parte la scelta della serie e dello SKU per le macchine virtuali, non è possibile controllare la larghezza di banda di rete disponibile. Ad esempio, anche se è possibile creare macchine virtuali con più schede di interfaccia di rete virtuali, la larghezza di banda complessiva disponibile per le macchine virtuali non aumenta e ci saranno più schede di interfaccia di rete virtuali a contendersi le stesse risorse di rete fisiche.

### Requisiti software

È possibile eseguire Elasticsearch in Windows o in Linux. Il servizio Elasticsearch viene distribuito come libreria JAR di Java e presenta dipendenze da altre librerie Java incluse nel pacchetto Elasticsearch. Per eseguire Elasticsearch è necessario installare la JVM di Java 7, aggiornamento 55 o versione successiva, o Java 8, aggiornamento 20 o versione successiva.

> [AZURE.NOTE] Non modificare le impostazioni di configurazione predefinite della JVM a parte i parametri della memoria *Xmx* e *Xms*, specificati come opzioni della riga di comando per il motore Elasticsearch. Vedere in proposito la sezione [Requisiti di memoria][]. Elasticsearch è progettato in base ai valori predefiniti, la loro modifica può influire negativamente sulle prestazioni del servizio.

### Distribuzione di Elasticsearch in Azure

Anche se non è difficile distribuire una singola istanza di Elasticsearch, la creazione di vari nodi e l'installazione e configurazione di Elasticsearch in ogni nodo richiedono molto tempo e sono soggette a errori. Se si intende eseguire Elasticsearch in macchine virtuali di Azure, sono disponibili due opzioni che permettono di ridurre le probabilità di errore.
- La prima è l'uso del [modello di Gestione risorse di Azure](http://azure.microsoft.com/documentation/templates/elasticsearch/) per la creazione del cluster. I parametri di questo modello consentono di specificare il livello di prestazioni e le dimensioni delle macchine virtuali che implementano i nodi, il numero di dischi da usare e altri fattori comuni. Il modello può creare un cluster basato su Windows Server 2012 o Ubuntu Linux 14.0.4.
- La seconda è l'uso di script che possono essere automatizzati o eseguiti in modo automatico. Gli script che possono creare e distribuire un cluster Elasticsearch sono disponibili sul sito [Modelli di avvio rapido di Azure][].

## Considerazioni su cluster, ridimensionamento di nodi e scalabilità

Elasticsearch consente l'uso di varie topologie di distribuzione, progettate per supportare requisiti e livelli di scalabilità differenti. Questa sezione illustra alcune topologie comuni e descrive le considerazioni per l'implementazione di cluster basati su tali topologie.

### Topologie di Elasticsearch

La figura 4 illustra un punto di partenza per la progettazione di una topologia di Elasticsearch per il cloud basata su macchine virtuali di Azure.

![](media/guidance-elasticsearch-general-startingpoint.png)

**Figura 4.**
Punto di partenza suggerito per la compilazione di un cluster Elasticsearch con Azure

Questa topologia è costituita da sei nodi dati con tre nodi client e tre nodi master. Un solo nodo master viene eletto, mentre gli altri due rimangono disponibili per l'elezione in caso di errore del master eletto. Ogni nodo viene implementato come una macchina virtuale separata. Le applicazioni Web di Azure vengono indirizzate ai nodi client attraverso un servizio di bilanciamento del carico. In questo esempio tutti i nodi e le applicazioni Web si trovano nella stessa rete virtuale di Azure, che li isola dal tutto il resto. Se il cluster deve essere disponibile all'esterno, ad esempio nell'ambito di una soluzione ibrida che include client locali, è possibile usare il servizio di bilanciamento del carico di Azure per fornire un indirizzo IP pubblico, ma è necessario adottare precauzioni di sicurezza aggiuntive per impedire accessi non autorizzati al cluster. Il "jumpbox" facoltativo è una macchina virtuale disponibile solo per gli amministratori. Questa macchina virtuale ha una connessione di rete alla rete virtuale di Azure, ma ha anche una connessione di rete esterna per consentire l'accesso come amministratore. Questo accesso deve essere protetto con una password complessa o un certificato. Un amministratore può accedere al jumpbox e connettersi direttamente a uno dei nodi del cluster. In alternativa è possibile usare una VPN da sito a sito tra l'azienda e la rete virtuale o circuiti [ExpressRoute][] per la connessione alla rete virtuale. Questi meccanismi consentono l'accesso amministrativo al cluster senza esporlo a Internet pubblico.

Per mantenere la disponibilità delle macchine Virtuali, i nodi dati sono raggruppati nello stesso set di disponibilità di Azure. Analogamente, i nodi client vengono mantenuti in un altro set di disponibilità e i nodi master sono archiviati in un terzo set di disponibilità.

Questa topologia è relativamente facile da ridimensionare. È sufficiente aggiungere altri nodi del tipo appropriato e assicurarsi che siano configurati con lo stesso nome di cluster specificato nel file elasticsearch.yml. Anche i nodi client devono essere aggiunti al pool back-end per il servizio di bilanciamento del carico di Azure.

**Posizionamento geografico dei cluster**

Non è consigliabile distribuire i nodi di un cluster in aree diverse, perché questo può influire sulle prestazioni di comunicazione tra i nodi. Vedere in proposito la sezione [Requisiti di rete][]. Il posizionamento geografico dei dati vicino agli utenti in aree diverse richiede la creazione di più cluster. In una situazione di questo tipo è necessario decidere se e come sincronizzare i cluster. Di seguito sono illustrate alcune delle possibili soluzioni:

**Connessione al cluster tramite [nodi tribe][].** Un nodo tribe è simile a un nodo client, ma può partecipare a più cluster Elasticsearch e visualizzarli tutti come un unico cluster di grandi dimensioni. I dati vengono gestiti localmente da ogni cluster e gli aggiornamenti non vengono propagati oltre i limiti del cluster, ma tutti i dati sono visibili. Un nodo tribe può eseguire query, creare e gestire documenti in qualsiasi cluster. Il limite principale è dato dal fatto che non è possibile usare un nodo tribe per creare un nuovo indice e i nomi di indice devono essere univoci in tutti i cluster. È quindi importante scegliere con cura come denominare gli indici durante la progettazione di cluster a cui i nodi tribe dovranno accedere.

Usando questo meccanismo, ogni cluster può contenere i dati a cui probabilmente accederanno le applicazioni client locali. Tali client possono comunque accedere ai dati remoti e modificarli, anche se ciò può comportare una latenza estesa. La figura 5 illustra un esempio di questa topologia. Il nodo tribe nel cluster 1 è evidenziato. Anche gli altri cluster possono avere nodi tribe, anche se non sono mostrati nel diagramma:

![](media/guidance-elasticsearch-general-tribenode.png)

**Figura 5.**
Applicazione client che accede a più cluster attraverso un nodo tribe

In questo esempio l'applicazione client si connette al nodo tribe nel cluster 1, situato nella stessa area, ma questo nodo è configurato per accedere al cluster 2 e al cluster 3, che potrebbero trovarsi in aree diverse. L'applicazione client può inviare richieste per il recupero o la modifica di dati in tutti i cluster.

> [AZURE.NOTE] I nodi tribe richiedono l'individuazione multicast per la connessione ai cluster e questo può presentare un problema di sicurezza. Per altri dettagli, vedere la sezione [Individuazione di nodi][].

*	Implementazione della replica geografica tra cluster. In questo approccio, le modifiche apportate a ogni cluster vengono propagate quasi in tempo reale ai cluster situati in altri data center. Sono disponibili plug-in di terze parti per Elasticsearch che supportano questa funzionalità, ad esempio il [plug-in PubNub Changes][].
*	Uso del [modulo di snapshot e ripristino di Elasticsearch][]. Se i dati si muovono in modo molto lento e vengono modificati da un singolo cluster, è possibile usare gli snapshot per eseguire una copia periodica dei dati e quindi ripristinare tali snapshot in altri cluster. È possibile archiviare gli snapshot nell'archivio BLOB di Azure, se è stato installato il [plug-in Azure Cloud][]. Tuttavia, questa soluzione non è adatta nel caso di dati che cambiano rapidamente o di dati che possono essere modificati in più di un cluster.

**Topologie su scala ridotta**

Le topologie su larga scala con cluster composti da nodi master, nodi client e nodi dati dedicati potrebbero non essere appropriate per tutti i tipi di scenario. Se si compila un sistema di sviluppo o produzione su scala ridotta, è possibile usare il cluster a tre nodi illustrato nella figura 6. Le applicazioni client si connettono direttamente a qualsiasi nodo dati disponibile nel cluster. Il cluster contiene tre partizioni etichettate come P1-P3, per consentire la crescita, e repliche etichettate come R1-R3. L'uso di tre nodi consente a Elasticsearch di distribuire le partizioni e le repliche in modo che non vadano persi dati in caso di errore di un singolo nodo.

![](media/guidance-elasticsearch-general-threenodecluster.png)

**Figura 6.**
Cluster a tre nodi con tre partizioni e repliche

Se si esegue un'installazione di sviluppo in un computer autonomo è possibile configurare un cluster con un singolo nodo che funge da master, client e risorsa di archiviazione dati. In alternativa, è possibile avviare più nodi in esecuzione come un cluster nello stesso computer avviando più di un'istanza di Elasticsearch. La figura 7 mostra un esempio.

![](media/guidance-elasticsearch-general-developmentconfiguration.png)

**Figura 7.**
Configurazione di sviluppo con più nodi Elasticsearch in esecuzione nello stesso computer

Si noti che nessuna di queste configurazioni autonome è consigliata per un ambiente di produzione, perché possono causare conflitti, a meno che il computer di sviluppo non abbia a disposizione una notevole quantità di memoria e diversi dischi veloci. Non forniscono alcuna garanzia di disponibilità elevata. In caso di errore del computer, tutti i nodi vengono persi.

### Considerazioni sulla scalabilità di un cluster e dei nodi dati

Elasticsearch può eseguire il ridimensionamento in base a due fattori: verticalmente, usando computer più grandi e più potenti, e orizzontalmente, distribuendo il carico tra più computer.

**Scalabilità verticale dei nodi dati di Elasticsearch**

Se si ospita un cluster Elasticsearch in macchine virtuali di Azure, ogni nodo può corrispondere a una macchina virtuale. Il limite di scalabilità verticale per un nodo dipende in gran parte dallo SKU della macchina virtuale e dalle restrizioni generali applicate ai singoli account di archiviazione e alle sottoscrizioni di Azure. La pagina [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits/) descrive in dettaglio questi limiti, ma per quanto riguarda la compilazione di un cluster Elasticsearch sono più rilevanti gli elementi nell'elenco seguente. È anche consigliabile evitare l'uso di macchine virtuali quando sono disponibili meno di 64 GB di memoria, in assenza di un motivo concreto. Come descritto nella sezione [Requisiti di memoria][], non si devono allocare più di 30 GB di RAM per ogni macchina virtuale nella JVM ed è necessario consentire al sistema operativo di utilizzare la memoria rimanente per il buffering di I/O. Ogni account di archiviazione ha un limite in relazione ai punti elencati di seguito:
- 20.000 IOPS. Usare lo stesso account di archiviazione per mantenere vari dischi rigidi virtuali può limitarne le prestazioni.
- Numero di nodi dati in una rete virtuale. Se non si usa Gestione risorse di Azure, è previsto un limite di 2048 istanze della macchina virtuale per ogni rete virtuale. In molti casi ciò risulta sufficiente, ma con una configurazione molto grande con migliaia di nodi potrebbe rappresentare una limitazione.
- Numero di account di archiviazione per sottoscrizione per ogni area. È possibile creare fino a 100 account di archiviazione per sottoscrizione di Azure in ogni area. Gli account di archiviazione vengono usati per contenere i dischi virtuali e ogni account di archiviazione ha un limite di 500 TB di spazio disponibile.
- Numero di core per sottoscrizione. Il limite predefinito è 20 core per sottoscrizione, ma può essere aumentato da Microsoft fino a 10.000 core. Tenere presente che alcune dimensioni della macchina virtuale, ad esempio A9, A11, D14 e DS14, possono contenere 16 core, mentre una macchina virtuale G5 contiene 32 core.
- Quantità di memoria per dimensioni della macchina virtuale. Macchine virtuali di dimensioni più piccole hanno una quantità limitata di memoria disponibile, ad esempio i computer D1 hanno 3,5 GB e i computer D2 hanno 7 GB. Queste macchine virtuali potrebbero non essere adatte per scenari che richiedono la memorizzazione nella cache di grandi quantità di dati da parte di Elasticsearch per raggiungere prestazioni ottimali, ad esempio nell'aggregazione dei dati o nell'analisi di un numero elevato di documenti durante l'inserimento di dati.
- Numero massimo di dischi per dimensioni della macchina virtuale. Questa limitazione può vincolare le dimensioni e le prestazioni di un cluster. Un numero inferiore di dischi permette di contenere meno dati e può ridurre le prestazioni a causa del minor numero di dischi disponibili per lo striping.
- Numero di domini di aggiornamento/domini di errore per set di disponibilità. Se si creano macchine virtuali con Gestione risorse di Azure, è possibile allocare fino a tre domini di errore e 20 domini di aggiornamento per ogni set di disponibilità. Questa limitazione può influire sulla resilienza di un cluster di grandi dimensioni sottoposto di frequente ad aggiornamenti in sequenza.

Tenendo presenti queste restrizioni, è sempre consigliabile distribuire i dischi virtuali per le macchine virtuali di un cluster tra gli account di archiviazione, per ridurre le probabilità di limitazione delle operazioni di I/O. Nel caso di un cluster di dimensioni molto grandi potrebbe essere necessario riprogettare l'infrastruttura logica e suddividerlo in partizioni funzionali separate. Ad esempio, potrebbe essere necessario suddividere il cluster tra le sottoscrizioni, anche se questo processo può portare ad altre complicazioni a causa della necessità di connettere le reti virtuali.

>	[AZURE.NOTE] Tenere presente che con Azure gli account di archiviazione vengono aggiunti a uno stamp di archiviazione specifico. Si tratta di un meccanismo interno usato per mantenere la coerenza e la disponibilità. Per informazioni dettagliate sul funzionamento, vedere il documento [A Highly Available Cloud Storage Service with Strong Consistency][] che descrive il servizio di archiviazione cloud a disponibilità elevata con coerenza assoluta. Nel caso di un'interruzione dell'archiviazione individuata in uno stamp specifico, si verificheranno errori in tutte le unità create con tale account. In questo caso, è possibile che tutte le VM che usano queste unità siano interessate dall'errore. L'uso di più account di archiviazione per ospitare le diverse unità di una VM può quindi aumentare il rischio di errore per tale VM. Per questo motivo, è consigliabile usare un singolo account di archiviazione per ogni nodo e archiviare l'unità di sistema e tutte le unità dati in quell'account.

**Scalabilità orizzontale di un cluster Elasticsearch**

All'interno di Elasticsearch il limite di scalabilità orizzontale è determinato dal numero di partizioni definite per ogni indice. Inizialmente è possibile allocare molte partizioni allo stesso nodo in un cluster. Man mano che il volume di dati aumenta è possibile aggiungere altri nodi e distribuire le partizioni tra di essi. Teoricamente, la scalabilità orizzontale del sistema cessa solo quando il numero dei nodi raggiunge il numero delle partizioni.

Come per la scalabilità verticale, esistono alcuni problemi da prendere in considerazione quando si valuta la possibilità di implementare la scalabilità orizzontale, tra cui:

- Numero massimo di macchine virtuali che è possibile connettere in una rete virtuale di Azure. Questo può limitare la scalabilità orizzontale per un cluster di dimensioni molto grandi. Per aggirare questo limite è possibile creare un cluster di nodi che copre più di una rete virtuale, ma questo approccio può portare a una riduzione delle prestazioni a causa della distanza di ogni nodo dai relativi peer.
- Numero di dischi per dimensioni della macchina virtuale. Il numero di dischi collegati supportati varia a seconda della serie e dello SKU. È anche possibile usare l'archiviazione temporanea disponibile nella macchina virtuale per fornire una quantità limite di archiviazione dati più veloce, ma per farlo è necessario prendere in considerazione le implicazioni relative alla resilienza e al ripristino. Per altre informazioni, vedere il documento relativo alla configurazione, al test e all'analisi di resilienza e ripristino di Elasticsearch. Le macchine virtuali serie D, serie DS, serie Dv2 e serie GS usano unità SSD per l'archiviazione temporanea.
- Numero massimo di IOPS per disco virtuale. I normali dischi collegati creati usando una risorsa di archiviazione standard hanno un limite di 500 IOPS per disco. Le unità SSD collegate possono supportare fino a 5000 IOPS per disco. Per usare le unità SSD per i dischi collegati, per l'archiviazione non temporanea, è necessario creare una macchina virtuale che supporta l'archiviazione premium, ad esempio macchine della serie DS o GS.

È possibile usare i set di scalabilità di Azure per avviare e arrestare le macchine virtuali in base alle esigenze. Per informazioni dettagliate, vedere [Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali][]. Tuttavia, questo approccio potrebbe non essere appropriato per un cluster Elasticsearch. I motivi sono indicati di seguito.

- Questo approccio è più adatto a macchine virtuali senza stato. Ogni volta che si aggiunge o si rimuove un nodo da un cluster Elasticsearch, le partizioni vengono riallocate per bilanciare il carico. Questo processo può generare una notevole quantità di traffico di rete e di operazioni di I/O su disco e può compromettere la velocità di inserimento dei dati. È necessario valutare questo sovraccarico rispetto ai vantaggi dati dall'elaborazione aggiuntiva e dalle risorse di memoria rese disponibili avviando in modo dinamico più macchine virtuali.
- L'avvio di una macchina virtuale non avviene immediatamente e possono essere necessari diversi minuti prima che le macchine virtuali aggiuntive diventino disponibili o vengano arrestate. La scalabilità di questo tipo è consigliata solo in caso di cambiamento prolungato delle esigenze.
- Dopo un aumento delle istanze, è consigliabile valutare con cura l'effettiva necessità di ridurle. La rimozione di una macchina virtuale da un cluster Elasticsearch è un processo a elevato utilizzo di risorse, dal momento che Elasticsearch deve recuperare le partizioni e le repliche posizionate nella macchina virtuale da rimuovere e ricrearle in uno o più dei nodi rimanenti. La rimozione di più macchine virtuali nello stesso momento può compromettere l'integrità del cluster rendendone difficile il ripristino. Molte implementazioni di Elasticsearch crescono nel tempo, ma la natura dei dati è tale che il volume tende a non diminuire. È possibile eliminare manualmente i documenti o configurarli con un valore di durata (TTL) dopo il quale scadono e vengono rimossi, ma nella maggior parte dei casi è probabile che lo spazio allocato in precedenza verrà riutilizzato rapidamente da documenti nuovi o modificati. Quando i documenti vengono rimossi o modificati, l'indice può diventare frammentato. In tal caso è possibile usare l'API [Optimize][] HTTP di Elasticsearch 2.0.0 e versioni precedenti oppure l'API [Force Merge][] di Elasticsearch 2.1.0 e versioni successive per eseguire la deframmentazione.

> [AZURE.NOTE] L'ottimizzazione è un'operazione molto dispendiosa non consigliata su un indice altamente attivo. L'ottimizzazione di un indice inattivo è invece consigliata perché contribuisce a ridurre le risorse necessarie per eseguire la ricerca.

### Determinazione del numero di partizioni per un indice

Il numero di nodi in un cluster può variare nel tempo, ma il numero di partizioni in un indice è fisso dopo la creazione dell'indice. Per aggiungere o rimuovere partizioni è necessario eseguire la reindicizzazione dei dati, un processo di creazione di un nuovo indice con il numero di partizioni richiesto e successiva copia dei dati dall'indice precedente al nuovo. È possibile usare gli alias per isolare gli utenti dalla reindicizzazione dei dati. Per informazioni dettagliate, vedere il documento relativo all'ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query con Elasticsearch in Azure. È quindi importante determinare il numero di partizioni che potrebbero essere necessarie prima di creare il primo indice del cluster. Per determinare questo numero è possibile seguire questa procedura:

1. Creare un cluster a nodo singolo usando la stessa configurazione hardware che si intende distribuire nell'ambiente di produzione.
2. Creare un indice che corrisponde alla struttura da usare nell'ambiente di produzione. Assegnare a questo indice una singola partizione e nessuna replica.
3. Aggiungere una specifica quantità di dati di produzione realistici all'indice.
4. Eseguire query e aggregazioni tipiche e altri carichi di lavoro basati sull'indice e misurare il tempo di risposta e la velocità effettiva.
5. Se il tempo di risposta e la velocità effettiva rientrano nei limiti accettabili, ripetere il processo dal passaggio 3 aggiungendo altri dati.
6. Quando sembra essere stata raggiunta la capacità della partizione, perché i tempi di risposta e la velocità effettiva iniziano a diventare inaccettabili, prendere nota del volume di documenti.
7. Estrapolare i dati relativi alla capacità di una singola partizione rispetto al numero previsto di documenti nell'ambiente di produzione per calcolare il numero di partizioni necessarie. È consigliabile prevedere un margine di errore in questi calcoli, perché l'estrapolazione non è una scienza esatta.

> [AZURE.NOTE] Tenere presente che ogni partizione viene implementata come un indice di Lucene che utilizza memoria, potenza della CPU e handle di file. Maggiore è il numero di partizioni, maggiore sarà la quantità di risorse necessarie.

La creazione di più partizioni può aumentare la scalabilità, a seconda dei carichi di lavoro e degli scenari, e può aumentare la velocità di inserimento dei dati, ma può anche ridurre l'efficienza di molte query. Per impostazione predefinita, una query interroga ogni partizione usata da un indice. Se si conosce già la partizione in cui si trovano i dati necessari, è possibile usare il [routing personalizzato][] per modificare questo comportamento.

Questo processo permette unicamente di generare una stima del numero di partizioni, ma il volume di documenti previsto nell'ambiente di produzione potrebbe non essere noto. In tal caso, è necessario determinare il volume iniziale, come indicato in precedenza, e il tasso di crescita previsto. Creare un numero appropriato di partizioni per gestire la crescita dei dati fino a quando non si decide di reindicizzare il database. Una delle strategie adottate per scenari quali la gestione e la registrazione di eventi è l'uso di indici in sequenza. Per i dati inseriti ogni giorno viene creato un nuovo indice a cui è possibile accedere con un alias che viene cambiato ogni giorno in modo da puntare all'indice più recente. Questo approccio permette di eliminare più facilmente i dati meno aggiornati e gli indici con informazioni non più necessarie e di mantenere gestibile il volume dei dati.

Tenere presente che il numero di nodi non deve corrispondere al numero di partizioni. Ad esempio, se si creano 50 partizioni, è possibile distribuirle inizialmente in 10 nodi e poi aggiungere altri nodi per ridimensionare il sistema all'aumentare del volume di lavoro. Evitare, tuttavia, di creare un numero estremamente elevato di partizioni in un numero ridotto di nodi, ad esempio 1000 partizioni distribuite in 2 nodi. Anche se, in linea teorica, il sistema può essere ridimensionato fino a 1000 nodi con questa configurazione, l'esecuzione di 500 partizioni in un singolo nodo rischia di comprometterne le prestazioni.

> [AZURE.NOTE] Per i sistemi con un'intensa attività di inserimento di dati è consigliabile usare un numero primo di partizioni. In questo modo, l'algoritmo predefinito usato da Elasticsearch per indirizzare i documenti alle partizioni produce una distribuzione ancora più uniforme.

### Considerazioni relative alla sicurezza

Per impostazione predefinita, Elasticsearch implementa il livello minimo di sicurezza e non prevede metodi di autenticazione e autorizzazione. Per questi aspetti è necessario configurare la rete e il sistema operativo sottostante e usare plug-in e utilità di terze parti, come ad esempio [Shield][] e [Search Guard][].

> [AZURE.NOTE] Shield è un plug-in fornito da Elastic per l'autenticazione utente, la crittografia dei dati, il controllo degli accessi in base al ruolo, il filtro IP e il controllo. Potrebbe essere necessario configurare il sistema operativo sottostante per implementare altre misure di sicurezza, ad esempio la crittografia del disco.

In un sistema di produzione, è necessario valutare come:

- Prevenire accessi non autorizzati al cluster.
- Identificare e autenticare gli utenti.
- Autorizzare le operazioni che gli utenti autenticati possono eseguire.
- Proteggere il cluster da operazioni dannose o inaffidabili.
- Proteggere i dati dall'accesso non autorizzato.
- Soddisfare i requisiti normativi per la sicurezza dei dati a pagamento, se appropriato.

### Protezione dell'accesso al cluster

Elasticsearch è un servizio di rete. I nodi di un cluster Elasticsearch rimangono in ascolto delle richieste client in ingresso tramite HTTP e comunicano tra loro usando un canale TCP. Adottare le misure necessarie per evitare che client o servizi non autorizzati possano inviare richieste tramite HTTP e TCP. Prendere in considerazione quanto segue:

- Definire gruppi di sicurezza di rete per limitare il traffico di rete in ingresso e in uscita per una macchina virtuale o una rete virtuale soltanto verso porte specifiche.
- Modificare le porte predefinite usate per l'accesso Web client (9200) e l'accesso alla rete a livello di codice (9300). Usare un firewall per proteggere tutti i nodi dal traffico Internet dannoso.
- A seconda della località e della connettività dei client, posizionare il cluster in una subnet privata senza accesso diretto a Internet. Se il cluster deve essere esposto all'esterno della subnet, indirizzare tutte le richieste attraverso un server bastion o un proxy sufficientemente avanzato per proteggere il cluster.
- Se è necessario fornire l'accesso diretto ai nodi, usare il plug-in Jetty di Elasticsearch per garantire la connettività SSL, l'autenticazione e la registrazione delle connessioni. In alternativa, configurare un server proxy nginx e l'autenticazione HTTPS.

> [AZURE.NOTE] L'uso di un server proxy come nginx consente anche di limitare l'accesso alle funzionalità. Ad esempio, se è necessario impedire ai client di eseguire altre operazioni è possibile configurare nginx per consentire solo le richieste all'endpoint \_search.

- Se è richiesta una sicurezza dell'accesso di rete più completa, usare i plug-in Shield o Search Guard.

### Identificazione e autenticazione degli utenti

Tutte le richieste inviate dai client al cluster devono essere autenticate. È anche preferibile impedire l'accesso al cluster da parte di nodi non autorizzati, perché potrebbero fornire una backdoor nel sistema che ignora l'autenticazione.

Sono disponibili plug-in di Elasticsearch per eseguire diversi tipi di autenticazione, tra cui:
- Autenticazione di base HTTP. Nomi utente e password sono sempre inclusi. Tutte le richieste devono essere crittografate tramite SSL/TLS o un livello di protezione equivalente.
- Integrazione tra LDAP e Active Directory. Questo approccio richiede l'assegnazione di ruoli ai client in gruppi Active Directory o LDAP.
- Autenticazione nativa per mezzo di identità definite all'interno del cluster Elasticsearch stesso.
- Autenticazione TLS all'interno di un cluster per l'autenticazione di tutti i nodi.
- Filtro IP, per impedire ai client di subnet non autorizzate di connettersi e impedire ai nodi di tali subnet di accedere al cluster.

### Autorizzazione delle richieste client

L'autorizzazione dipende dal plug-in di Elasticsearch usato per fornire il servizio. Ad esempio, un plug-in che fornisce l'autenticazione di base in genere fornisce funzionalità che definiscono il livello di autenticazione, mentre un plug-in che usa Active Directory o LDAP in genere associa i client ai ruoli e poi assegna diritti di accesso a tali ruoli. Quando si usa un plug-in specifico, valutare i punti seguenti:
- È necessario limitare le operazioni che un client può eseguire? Ad esempio, un client deve poter monitorare lo stato del cluster oppure creare ed eliminare gli indici?
- È necessario limitare l'accesso del client a indici specifici? Questo risulta utile in una situazione multi-tenant in cui ai tenant potrebbero essere assegnati set di indici specifici, a cui altri tenant non devono avere accesso.
- Il client deve poter leggere e scrivere dati in un indice? Ad esempio, è possibile permettere a un client di eseguire ricerche che recuperano dati tramite un indice ma è necessario impedire che aggiunga o elimini dati da tale indice.

Attualmente, la maggior parte dei plug-in di sicurezza definisce l'ambito delle operazioni a livello di cluster o di indice e non nei subset di documenti all'interno degli indici. Questo avviene per motivi di efficienza. Non è quindi semplice limitare le richieste a documenti specifici all'interno di un singolo indice. Per ottenere questo livello di granularità, salvare i documenti in indici separati e usare alias per raggruppare gli indici. Prendendo ad esempio un sistema del personale, se l'utente A richiede l'accesso a tutti i documenti che contengono informazioni sui dipendenti nel reparto X, l'utente B richiede l'accesso a tutti i documenti che contengono informazioni sui dipendenti nel reparto Y e l'utente C richiede l'accesso a tutti i documenti che contengono informazioni sui dipendenti in entrambi i reparti, è necessario creare due indici, per il reparto X e per il reparto Y, e un alias che fa riferimento a entrambi gli indici. Concedere all'utente A l'accesso in lettura al primo indice, concedere all'utente B l'accesso in lettura al secondo indice e concedere all'utente C l'accesso in lettura a entrambi gli indici attraverso l'alias. Per altre informazioni, vedere l'articolo relativo alla [simulazione di indici per ogni utente con gli alias][].

### Protezione del cluster

Se non è protetto in modo adeguato il cluster può essere soggetto a un uso improprio, come descritto di seguito:

- Disabilitare lo scripting di query dinamiche nelle query di Elasticsearch perché possono portare a vulnerabilità della sicurezza. Usare script nativi anziché script di query. Uno script nativo è un plug-in di Elasticsearch scritto in Java e compilato in un file JAR.

> [AZURE.NOTE] Lo scripting di query dinamiche è stato disabilitato per impostazione predefinita. Evitare di abilitarlo nuovamente a meno che non esista un motivo concreto.

- Evitare di esporre ricerche di stringhe di query agli utenti. La ricerca di stringhe di query consente agli utenti di eseguire liberamente query a elevato utilizzo di risorse. Queste ricerche potrebbero influire negativamente sulle prestazioni del cluster e possono rendere il sistema vulnerabile ad attacchi Denial of Service. La ricerca di stringhe di query può anche esporre informazioni potenzialmente riservate.
- Impedire alle operazioni di utilizzare una grande quantità di memoria perché possono verificarsi eccezioni di memoria esaurita e conseguenti errori dei nodi di Elasticsearch. Le operazioni a elevato utilizzo di risorse con esecuzione prolungata possono anche essere usate per implementare attacchi Denial of Service. Tra gli esempi sono inclusi:
- Richieste di ricerca che provano a caricare campi di dimensioni molto grandi in memoria, se una query esegue ordinamenti, script o facet su questi campi.
  
> [AZURE.NOTE] L'uso di [Doc Values][] può ridurre i requisiti di memoria degli indici salvando i dati dei campi su disco anziché caricarli in memoria. Questo può contribuire a ridurre il rischio di esaurire la memoria in un nodo, ma risulta ridotta anche la velocità.

- Ricerche che eseguono query su più indici contemporaneamente.
- Ricerche che recuperano un numero elevato di campi. Le ricerche di questo tipo possono esaurire la memoria a causa della memorizzazione nella cache di una grande quantità di dati dei campi. Per impostazione predefinita, la cache dei dati dei campi ha dimensioni illimitate, ma è possibile impostare le proprietà [indices.fielddata.cache.*](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html) nel file di configurazione elasticsearch.yml per limitare le risorse disponibili. È anche possibile configurare l'[interruttore di dati del campo][] per impedire che i dati memorizzati nella cache da un singolo campo possano esaurire la memoria e l'[interruttore di richieste][] per impedire a singole query di monopolizzare la memoria. L'impostazione di questi parametri può comportare una maggiore probabilità che si verifichino errori o timeout delle query.
  
> [AZURE.NOTE] Elasticsearch presuppone sempre che sia disponibile memoria sufficiente per eseguire il carico di lavoro corrente. In caso contrario, il servizio Elasticsearch può subire un arresto anomalo. Elasticsearch fornisce endpoint che restituiscono informazioni sull'utilizzo delle risorse, ovvero le [API cat][] HTTP, ed è necessario monitorare attentamente queste informazioni.

- Attesa prolungata prima di scaricare un segmento di memoria in corso. Questo può esaurire lo spazio di buffer in memoria. Se necessario, [configurare il translog][] per ridurre le soglie di scaricamento dei dati su disco.

- Creazione di indici con grandi quantità di metadati. Un indice che contiene documenti con un gran numero di varianti dei nomi dei campi può utilizzare una grande quantità di memoria. Per altre informazioni, vedere l'articolo relativo all'[esplosione del mapping][].
  
> [AZURE.NOTE] La definizione di operazione a esecuzione prolungata oppure a elevato utilizzo di query è specifica dello scenario. Il carico di lavoro previsto in genere da un cluster può avere un profilo completamente diverso rispetto al carico di lavoro in un altro cluster. L'individuazione delle operazioni inaccettabili richiede attività di ricerca approfondite e il test delle applicazioni.

È consigliabile rilevare e arrestare le attività dannose prima che possano causare danni rilevanti o perdita di dati. Si consiglia di usare un sistema di monitoraggio e notifiche di sicurezza che possa rilevare rapidamente modelli insoliti di accesso ai dati e generare avvisi quando, ad esempio, le richieste di accesso utente hanno esito negativo, nodi imprevisti entrano o escono dal cluster oppure le operazioni richiedono più tempo del previsto. Uno degli strumenti che possono eseguire queste attività è Elasticearch [Watcher][].

### Protezione dei dati

È possibile proteggere i dati in esecuzione tramite SSL/TLS, ma Elasticsearch non fornisce alcuna forma predefinita di crittografia dei dati per le informazioni archiviate su disco. Tenere presente che queste informazioni sono memorizzate in normali file su disco e qualsiasi utente con accesso a tali file potrebbe compromettere i dati in essi contenuti, ad esempio copiandoli nel proprio cluster. Prendere in considerazione quanto segue:
- Proteggere i file usati da Elasticsearch per contenere i dati. Non consentire l'accesso arbitrario in lettura o scrittura a identità diverse dal servizio Elasticsearch.

- Crittografare i dati contenuti in questi file usando un file system di crittografia.

> [AZURE.NOTE] Azure ora supporta la crittografia del disco per le macchine virtuali di Windows e Linux. Per altre informazioni, vedere [Crittografia dischi di Azure per l'anteprima di macchine virtuali IaaS Windows e Linux][].

### Requisiti normativi

I requisiti normativi riguardano principalmente il controllo delle operazioni, per mantenere una cronologia degli eventi, e la garanzia della riservatezza di tali operazioni, per evitare che vengano monitorate e riprodotti da un'entità esterna. In particolare, è necessario valutare come:

- Tenere traccia di tutte le richieste, con esito positivo o negativo, e di tutti i tentativi di accesso al sistema.
- Crittografare le comunicazioni inviate dai client al cluster, nonché le comunicazioni da nodo a nodo eseguite dal cluster. Il protocollo SSL/TLS deve essere implementato per tutte le comunicazioni del cluster. Elasticsearch supporta anche crittografie modulari per le aziende con requisiti diversi da quelli disponibili tramite SSL/TLS.
- Archiviare tutti i dati di controllo in modo sicuro. Il volume delle informazioni di controllo può aumentare molto rapidamente e le informazioni devono essere protette in modo affidabile per evitarne la manomissione.
- Archiviare i dati di controllo in modo sicuro.

### Considerazioni sul monitoraggio

Il monitoraggio è importante sia a livello di sistema operativo che a livello di Elasticsearch.

Per il monitoraggio a livello di sistema operativo è possibile usare strumenti specifici del sistema operativo. In Windows è possibile usare strumenti come Performance Monitor, con i contatori delle prestazioni appropriati, mentre in Linux è possibile usare strumenti come *vmstat*, *iostat* e *top*. Gli elementi chiave per il monitoraggio a livello di sistema operativo includono l'utilizzo della CPU, i volumi di I/O su disco, i tempi di attesa di I/O su disco e il traffico di rete. In un cluster Elasticsearch ottimizzato l'utilizzo della CPU da parte del processo Elasticsearch deve essere elevato e i tempi di attesa di I/O su disco devono essere ridotti al minimo.

A livello di software, è necessario monitorare la velocità effettiva e i tempi di risposta delle richieste, insieme ai dettagli relativi alle richieste non riuscite. Elasticsearch fornisce una serie di API che permettono di esaminare le prestazioni di diversi aspetti di un cluster. Le due API più importanti sono *\_cluster/health* e *\_nodes/stats*. L'API *\_cluster/health* può essere usata per ottenere un'istantanea dello stato complessivo del cluster, oltre a informazioni dettagliate per ogni indice, come illustrato nell'esempio seguente:

`GET _cluster/health?level=indices`

L'output di esempio illustrato di seguito è stato generato usando questa API:

```json
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 6,
    "number_of_data_nodes": 3,
    "active_primary_shards": 10,
    "active_shards": 20,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "indices": {
        "systwo": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        },
        "sysfour": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        }
    }
}
```

Questo cluster contiene due indici denominati *systwo* e *sysfour*. Le statistiche principali che devono essere monitorate per ogni indice sono status, active\_shards e unassigned\_shards. Il valore di status deve essere "green", il valore di active\_shards deve riflettere i valori di number\_of\_shards e number\_of\_replicas, mentre il valore di unassigned\_shards deve essere zero. Se il valore di status è "red", parte dell'indice è mancante o danneggiata. In tal caso, il valore di *active\_shards* è minore di *number\_of\_shards* - (*number\_of\_replicas* + 1) e il valore di unassigned\_shards è diverso da zero. Se il valore di status è "yellow", un indice è in uno stato di transizione. Ciò può essere dovuto all'aggiunta di più repliche o allo spostamento di partizioni. Al termine della transizione il valore di status deve diventare "green". Se rimane "yellow" per tempo prolungato o diventa "red", è necessario controllare se si sono verificati eventi di I/O significativi, ad esempio un errore di rete o del disco, a livello di sistema operativo.
L'API \_nodes/stats genera informazioni dettagliate su ogni nodo del cluster:

`GET _nodes/stats`

L'output generato contiene informazioni dettagliate su come gli indici vengono archiviati in ogni nodo, incluse le dimensioni e il numero di documenti, sul tempo impiegato per eseguire l'indicizzazione, le query, le ricerche, l'unione e la memorizzazione nella cache, contiene anche informazioni sui processi e sul sistema operativo, statistiche relative alla JVM, incluse le prestazioni di Garbage Collection, e pool di thread. Per altre informazioni, vedere l'articolo relativo al [monitoraggio di singoli nodi][].

Se una parte significativa delle richieste di Elasticsearch ha esito negativo con messaggi di errore *EsRejectedExecutionException*, Elasticsearch non riesce a gestire il carico di lavoro. In tal caso è necessario identificare il collo di bottiglia che impedisce a Elasticsearch di funzionare correttamente. Prendere in considerazione quanto segue:

- Se il collo di bottiglia è dovuto a risorse limitate, ad esempio nel caso in cui la memoria allocata alla JVM non sia sufficiente e dia luogo a un numero eccessivo di operazioni di Garbage Collection, è consigliabile allocare risorse aggiuntive. In questo caso, configurare la JVM per un uso maggiore della memoria, fino al 50% dello spazio di archiviazione disponibile nel nodo. Vedere in proposito la sezione [Requisiti di memoria][].
- Se il cluster mostra tempi di attesa di I/O eccessivi e le statistiche di unione raccolte per un indice con l'API \_nodes/stats contengono valori elevati, l'indice ha un numero elevato di operazioni di scrittura. Rivedere quanto indicato nella sezione [Ottimizzazione delle risorse per operazioni di indicizzazione](Ottimizzazione delle risorse per operazioni di indicizzazione) per ottimizzare le prestazioni di indicizzazione.
- Limitare le applicazioni client che eseguono operazioni di inserimento di dati e determinarne l'effetto sulle prestazioni. Se questo approccio mostra miglioramenti significativi, valutare se mantenere la limitazione o aumentare le istanze distribuendo il carico per gli indici con un numero elevato di operazioni di scrittura tra più nodi. Per altre informazioni, vedere il documento Ottimizzazione delle prestazioni di inserimento dei dati con Elasticsearch in Azure.
- Se le statistiche di ricerca relative a un indice indicano che l'esecuzione di query richiede troppo tempo, valutare come ottimizzare le query. Per altre informazioni, vedere la sezione [Ottimizzazione delle query][]. Si noti che è possibile usare i valori *query\_time\_in\_millis* e *query\_total* segnalati dalle statistiche di ricerca per calcolare in modo approssimativo come migliorare l'efficienza delle query. L'equazione *query\_time\_in\_millis* / *query\_total* fornisce un tempo medio per ogni query.

### Strumenti per il monitoraggio di Elasticsearch

È disponibile un'ampia gamma di strumenti per il regolare monitoraggio di Elasticsearch nell'ambiente di produzione. Questi strumenti usano in genere le API sottostanti di Elasticsearch per raccogliere informazioni e presentare i dettagli in un modo più leggibile rispetto ai dati non elaborati. Si tratta ad esempio di [Elasticsearch-Head][], [Bigdesk][], [Kopf][] e [Marvel][].

Head Elasticsearch, Bigdesk e Kopf vengono eseguiti come plug-in per il software Elasticsearch. Le versioni più recenti di Marvel possono essere eseguite in modo indipendente, ma richiedono [Kibana][] per fornire un ambiente di hosting e acquisizione dei dati. L'uso di Marvel con Kibana permette di implementare il monitoraggio in un ambiente separato dal cluster Elasticsearch. In questo modo è possibile esplorare molto più a fondo i problemi di Elasticsearch rispetto all'esecuzione degli strumenti di monitoraggio come parte del software Elasticsearch. Ad esempio, in caso di estrema lentezza o di errore ripetuto di Elasticsearch, questo influisce anche sugli strumenti eseguiti come plug-in di Elasticsearch e rende molto più difficili il monitoraggio e la diagnostica.

A livello di sistema operativo è possibile usare strumenti come la funzionalità Analisi dei log di [Azure Operations Management Suite][] o [Diagnostica di Azure con il portale di Azure][] per acquisire i dati sulle prestazioni delle macchine virtuali che ospitano nodi di Elasticsearch. Un altro approccio consiste nell'uso di [Logstash][] per acquisire i dati relativi a log e prestazioni, archiviare le informazioni in un cluster Elasticsearch separato, diverso dal cluster usato per l'applicazione, e infine usare Kibana per visualizzare i dati. Per altre informazioni, vedere l'articolo relativo a [Diagnostica di Microsoft Azure con ELK][].

### Strumenti per il test delle prestazioni di Elasticsearch

Sono disponibili altri strumenti per il benchmarking di Elasticsearch e il test delle prestazioni dei cluster. Gli strumenti sono progettati per l'uso in un ambiente di sviluppo o di test anziché di produzione. Uno strumento di uso frequente è [Apache JMeter][].

JMeter è stato usato per eseguire il benchmarking e altri test di carico descritti nei documenti correlati a questa guida. Il documento relativo all'esecuzione di test delle prestazioni in Elasticsearch con JMeter descrive in dettaglio la configurazione e l'uso di JMeter.

## Passaggi successivi
- [Articolo relativo alla guida completa di Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/guide/master/index.html)

[Apache JMeter]: http://jmeter.apache.org/
[Apache Lucene]: https://lucene.apache.org/
[Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali]: virtual-machines-vmss-walkthrough/
[Crittografia dischi di Azure per l'anteprima di macchine virtuali IaaS Windows e Linux]: azure-security-disk-encryption/
[servizio di bilanciamento del carico di Azure]: load-balancer-overview/
[ExpressRoute]: expressroute-introduction/
[servizio di bilanciamento del carico interno]: load-balancer-internal-overview/
[Dimensioni delle macchine virtuali]: virtual-machines-size-specs/

[Requisiti di memoria]: #memory-requirements
[Requisiti di rete]: #network-requirements
[Individuazione di nodi]: #node-discovery
[Ottimizzazione delle query]: #query-tuning

[A Highly Available Cloud Storage Service with Strong Consistency]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx
[plug-in Azure Cloud]: https://www.elastic.co/blog/azure-cloud-plugin-for-elasticsearch
[Diagnostica di Azure con il portale di Azure]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[Azure Operations Management Suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[Modelli di avvio rapido di Azure]: https://azure.microsoft.com/documentation/templates/
[Bigdesk]: http://bigdesk.org/
[API cat]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/cat.html
[configurare il translog]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-translog.html
[routing personalizzato]: https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-routing-field.html
[Doc Values]: https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html
[Elasticsearch]: https://www.elastic.co/products/elasticsearch
[Elasticsearch-Head]: https://mobz.github.io/elasticsearch-head/
[Elasticsearch.Net e NEST]: http://nest.azurewebsites.net/
[modulo di snapshot e ripristino di Elasticsearch]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html
[simulazione di indici per ogni utente con gli alias]: https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html
[interruttore di dati del campo]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#fielddata-circuit-breaker
[Force Merge]: https://www.elastic.co/guide/en/elasticsearch/reference/2.1/indices-forcemerge.html
[protocollo di gossip]: https://en.wikipedia.org/wiki/Gossip_protocol
[Kibana]: https://www.elastic.co/downloads/kibana
[Kopf]: https://github.com/lmenezes/elasticsearch-kopf
[Logstash]: https://www.elastic.co/products/logstash
[esplosione del mapping]: https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion
[Marvel]: https://www.elastic.co/products/marvel
[Diagnostica di Microsoft Azure con ELK]: https://github.com/mspnp/semantic-logging/tree/elk
[monitoraggio di singoli nodi]: https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_monitoring_individual_nodes
[nginx]: http://nginx.org/en/
[API Node Client]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/node-client.html
[Optimize]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/indices-optimize.html
[plug-in PubNub Changes]: http://www.pubnub.com/blog/quick-start-realtime-geo-replication-for-elasticsearch/
[interruttore di richieste]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#request-circuit-breaker
[Search Guard]: https://github.com/floragunncom/search-guard
[Shield]: https://www.elastic.co/products/shield
[API Transport Client]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/transport-client.html
[nodi tribe]: https://www.elastic.co/blog/tribe-node
[Watcher]: https://www.elastic.co/products/watcher
[Zen]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html

<!----HONumber=AcomDC_0211_2016-->
