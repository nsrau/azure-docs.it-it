<properties
   pageTitle="Indicazioni sulla memorizzazione nella cache | Microsoft Azure"
   description="Indicazioni per la memorizzazione nella cache per migliorare le prestazioni e la scalabilità."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Informazioni aggiuntive sulla memorizzazione nella cache

![](media/best-practices-caching/pnp-logo.png)

La memorizzazione nella cache è una tecnica comune che ha lo scopo di migliorare le prestazioni e la scalabilità di un sistema tramite la copia temporanea dei dati a cui si accede con maggiore frequenza nella risorsa di archiviazione rapida situata accanto all'applicazione. Se la risorsa di archiviazione rapida dei dati si trova più vicina all'applicazione rispetto all'origine iniziale, la memorizzazione nella cache potrà migliorare significativamente i tempi di risposta per le applicazioni client fornendo i dati più rapidamente. La memorizzazione nella cache è più efficace quando un'istanza di un client legge ripetutamente gli stessi dati (specialmente se i dati rimangono relativamente statici e l'archivio dei dati originale è lento rispetto alla rapidità della cache) è soggetto a un elevato livello di conflitti o se si trova lontano a tal punto che una latenza di rete potrebbe comportare lentezza nell'accesso.

## Memorizzazione nella cache di applicazioni distribuite

Le applicazioni distribuite solitamente implementano una o entrambe le due strategie seguenti durante la memorizzazione nella cache dei dati:

- L'uso di una cache privata, in cui i dati vengono conservati in locale nel computer su cui è in esecuzione un'istanza di un'applicazione o di un servizio.
- L'uso di una cache condivisa, che funga da origine comune a cui possono accedere più processi o macchine.

In entrambi i casi, la memorizzazione nella cache può essere eseguita lato client (da parte del processo che ha fornito l'interfaccia utente per un sistema, quale un browser Web o un'applicazione desktop) e/o lato server (da parte del processo che fornisce servizi aziendali in esecuzione in remoto).

### Memorizzazione nella cache privata

Il tipo più semplice di cache è un archivio in memoria, contenuto nello spazio di indirizzi di un singolo processo e accessibile direttamente dal codice in esecuzione nel processo. Questo tipo di cache è accessibile molto rapidamente e può fornire una strategia estremamente efficace per l'archiviazione di modeste quantità di dati statici poiché la dimensione della cache è solitamente limitata dal volume della memoria disponibile nella macchina che ospita il processo. Se è necessario memorizzare nella cache un maggior numero di informazioni in memoria rispetto a quanto fisicamente possibile, sarà possibile scrivere i dati memorizzati nella cache nel file system locale. L'operazione di accesso ai dati sarà necessariamente più lenta rispetto a quanto non avvenga con i dati contenuti in memoria, ma dovrebbe comunque essere più rapida e affidabile rispetto al recupero di dati attraverso una rete.

Se si dispone di più istanze di un'applicazione che usa questo modello in esecuzione in modo simultaneo, ciascuna istanza dell'applicazione disporrà della propria cache indipendente che conterrà una copia personalizzata dei dati.

Occorre considerare una cache come uno snapshot dei dati originali in un determinato momento nel passato. Se i dati non sono statici, è probabile che istanze diverse di applicazioni conterranno versioni diverse dei dati nelle proprie cache. Pertanto, la stessa query eseguita da tali istanze potrebbe restituire risultati diversi, come mostrato nella Figura 1.

![uso di una cache in memoria in diverse istanze di un'applicazione](media/best-practices-caching/Figure1.png)

_Figura 1: uso di una cache in memoria in diverse istanze di un'applicazione_

### Memorizzazione nella cache condivisa

L'uso di una cache condivisa consente di ridurre il problema di differenziazione dei dati in ciascuna cache, come può accadere con la memorizzazione nella cache in memoria. La memorizzazione nella cache condivisa garantisce che diverse istanze di applicazioni abbiano accesso alla stessa visualizzazione di dati memorizzati nella cache individuando quest'ultima in una posizione separata, solitamente ospitata come parte di un servizio separato, in base a quanto mostrato nella Figura 2.

![uso di una cache condivisa_](media/best-practices-caching/Figure2.png)

_Figura 2: uso di una cache condivisa_

Un vantaggio importante di usare un approccio basato su memorizzazione nella cache condivisa è rappresentato dalla scalabilità che è in grado di offrire. Molti servizi di cache condivisa vengono implementati mediante l'uso di un cluster di server e usano software che distribuiscono i dati nel cluster in modo trasparente. Un'istanza dell'applicazione invia semplicemente una richiesta al servizio cache e l'infrastruttura sottostante è responsabile nel determinare la posizione dei dati memorizzati nella cache del cluster. È possibile aumentare la quantità di cache aggiungendo più server.

Gli svantaggi dell'approccio basato sulla memorizzazione nella cache condivisa sono rappresentati dal fatto che l'accesso alla cache è più lento perché non viene più mantenuta a livello locale per ciascuna istanza di un'applicazione e l'esigenza di implementare un servizio cache separato potrebbe comportare maggiore complessità alla soluzione.

## Considerazioni sull'uso della memorizzazione della cache

Nelle sezioni seguenti vengono descritte considerazioni in modo più dettagliato per la progettazione e l'uso di una cache.

### Quando è necessario memorizzare i dati nella cache?

La memorizzazione nella cache può migliorare significativamente le prestazioni, la scalabilità e la disponibilità. Maggiore sarà la quantità di dati a disposizione e maggiore sarà il numero di utenti che dovranno accedere a tali dati, maggiori saranno i vantaggi della memorizzazione nella cache, grazie alla riduzione della latenza e dei conflitti associati alla gestione di un'elevata quantità di richieste simultanee nell'archivio dati originale. Ad esempio, un database può supportare un numero limitato di connessioni simultanee, ma il recupero di dati da una cache condivisa piuttosto che da un database sottostante consente a un'applicazione client di accedere ai dati anche se il numero di connessioni attualmente disponibili si è esaurito. Inoltre, se il database diventa non disponibile, è possibile che l'esecuzione delle applicazioni client possa continuare usando i dati contenuti nella cache.

Occorre tenere presente la memorizzazione nella cache dei dati letti di frequente, ma modificati raramente (i dati presentano un'elevata proporzione di operazioni di lettura rispetto a quelle di scrittura). Tuttavia, non si deve usare la cache come archivio autorevole di informazioni critiche; è necessario assicurarsi che tutte le modifiche che l'applicazione non può permettersi di perdere siano sempre salvate in un archivio dati permanente. In questo modo, se la cache non è disponibile, l'applicazione potrà comunque continuare a funzionare usando l'archivio dati e non verranno perse informazioni importanti.

### Tipi di dati e strategie di popolamento della cache

Il modo fondamentale in cui usare una cache in modo efficiente consiste nello stabilire i dati più importanti da memorizzare nella cache e l'orario più adatto. I dati possono essere aggiunti alla cache su richiesta la prima volta che vengono recuperati da un'applicazione, in modo tale che l'applicazione debba recuperare i dati solo una volta dall'archivio dati e possa effettuare gli accessi successivi usando la cache.

In alternativa, una cache può essere parzialmente o completamente popolata con i dati in anticipo, tipicamente all'avvio dell'applicazione (approccio noto come seeding). Tuttavia, potrebbe non essere consigliabile implementare il seeding per una cache di grosse dimensioni poiché tale approccio potrebbe imporre un carico elevato e improvviso sull'archivio dati originale quando inizia l'esecuzione dell'applicazione.

Spesso un'analisi dei modelli di uso consentono di decidere se prepopolare completamente o parzialmente una cache e scegliere i dati che è necessario memorizzare nella cache. Ad esempio, potrebbe essere utile effettuare il seeding della cache quando i dati statici del profilo utente per i clienti che usando l'applicazione regolarmente (forse ogni giorno), ma non per i clienti che usano l'applicazione solo una volta a settimana.

La memorizzazione nella cache solitamente funziona bene con i dati non modificabili o che non vengono modificati di frequente. Esempi includono informazioni di riferimento, quali informazioni sui prodotti e sui prezzi in un'applicazione di e-commerce o in risorse statiche condivise costose da costruire. A|cuni o tutti questi dati possono essere caricati nella cache all'avvio dell'applicazione per ridurre al minimo la richiesta di risorse e per migliorare le prestazioni. Potrebbe inoltre essere opportuno disporre di un processo in background che aggiorni periodicamente i dati di riferimento nella cache per garantire che siano aggiornati o che aggiorni la cache quando i dati di riferimento vengono modificati.

La memorizzazione nella cache potrebbe rivelarsi meno utile per i dati dinamici, benché vi siano delle eccezioni a tale considerazione (per ulteriori informazioni, vedere la sezione relativa alla memorizzazione nella cache di dati altamente dinamici più avanti in queste linee guida). Quando i dati originali vengono regolarmente modificati, le informazioni memorizzate nella cache possono diventare obsoleti molto rapidamente o il sovraccarico dovuto al fatto che la cache viene mantenuta sincronizzata con l'archivio dati originale riduce l'efficienza della memorizzazione nella cache. Si noti che la cache non deve includere i dati completi relativi a un'entità. Ad esempio, se un elemento di dati rappresenta un oggetto a più valori quale il cliente di una banca con un nome, un indirizzo e un saldo di conto corrente, alcuni di tali elementi potrebbero rimanere statici (il nome e l'indirizzo), mentre altri (ad esempio, il saldo di conto corrente) potrebbero essere più dinamici. In tali situazioni, potrebbe essere utile memorizzare nella cache le parti statiche dei dati e recuperare solo (o calcolare) le informazioni rimanenti in base alle necessità.

È necessario effettuare il test delle prestazioni e l'analisi dell'uso per stabilire se sia opportuno il prepopolamento o il caricamento su richiesta della cache oppure una combinazione di entrambi. La decisione dovrebbe basarsi su una combinazione della volatilità e del modello di uso dei dati. L'analisi dell'uso e delle prestazioni della cache è particolarmente importante nelle applicazioni che riscontrano carichi elevati e che devono essere altamente scalabili. Ad esempio, negli scenari a elevata scalabilità potrebbe avere senso effettuare il seeding della cache per ridurre il carico sull'archivio dati nei momenti di massima attività.

La memorizzazione nella cache può essere inoltre usata per evitare la ripetizione di calcoli durante l'esecuzione dell'applicazione. Se un'operazione trasforma i dati o esegue un calcolo complicato, potrebbe salvare i risultati dell'operazione nella cache. Se lo stesso calcolo è necessario successivamente, l'applicazione potrà semplicemente richiamare i risultati dalla cache.

Un'applicazione può modificare i dati conservati nella cache, ma è necessario considerare la cache come un archivio dati temporaneo che potrebbe scomparire in qualsiasi momento. Non archiviare dati preziosi solo nella cache, ma assicurarsi di mantenere le informazioni anche nell'archivio dati originale. In questo modo, se la cache dovesse diventare non disponibile, è possibile ridurre al minimo le possibilità di perdere i dati.

### Memorizzazione nella cache di dati altamente dinamici

L'archiviazione di informazioni che cambiano rapidamente in un archivio dati permanente possono comportare un sovraccarico nel sistema. Ad esempio, si consideri un dispositivo che segnali continuamente uno stato o un altro tipo di misurazione. Se un'applicazione sceglie di non memorizzare nella cache i dati sulla base del fatto che le informazioni memorizzate nella cache saranno quasi sempre scadute, pertanto le stesse considerazioni potrebbero rivelarsi vere durante l'archiviazione e il richiamo di tali informazioni dall'archivio dati; nel tempo impiegato per salvare e recuperare i dati le informazioni potrebbero essere cambiate. In una situazione simile, considerare i vantaggi di archiviare le informazioni dinamiche direttamente nella cache invece che nell'archivio dati permanente. Se i dati non sono critici e non richiedono un controllo, non importa allora se la modifica occasionale andasse persa.

### Gestione della scadenza dei dati nella cache

Nella maggior parte dei casi, i dati conservati in una cache sono una copia dei dati conservati nell'archivio dati originale. I dati nell'archivio dati originale potrebbero cambiare dopo essere memorizzati nella cache, provocando l'obsolescenza dei dati memorizzati nella cache. Molti sistemi di memorizzazione nella cache consentono di configurare la cache per la scadenza dei dati e per la riduzione del periodo di tempo per cui i dati potrebbero risultare scaduti.

Quando i dati memorizzati nella cache scadono, vengono rimossi dalla cache e l'applicazione deve recuperare i dati dall'archivio dati originale (potrebbe riporre le informazioni appena recuperate nella cache). Successivamente, sarà possibile impostare un criterio di scadenza predefinito durante la configurazione della cache. In molti servizi cache è possibile inoltre impostare il periodo di scadenza di singoli oggetti quando si archiviano a livello di codice nella cache (alcune cache consentono di specificare il periodo di scadenza come un valore assoluto o come un valore scorrevole che comporta la rimozione dell'elemento dalla cache si non si accede a esso entro il periodo di tempo prestabilito). Questa impostazione esegue l'override di qualsiasi criterio di scadenza relativo alla cache, ma solo per gli oggetti specificati.

> [AZURE.NOTE]Si consideri attentamente il periodo di scadenza della cache e degli oggetti in essa contenuti. Se si imposta un periodo troppo ridotto, gli oggetti scadranno troppo rapidamente e si ridurranno i vantaggi dell'uso della cache. Se si imposta il periodo in modo troppo esteso, si rischi l'obsolescenza dei dati.

È inoltre possibile che la cache si riempia se ai dati è consentita una permanenza prolungata. In tal caso, qualsiasi richiesta di aggiungere nuovi elementi alla cache potrebbe comportare la rimozione forzata di alcuni elementi in un processo noto come rimozione. I servizi cache tipicamente rimuovono i dati usati meno di recente, ma è solitamente possibile eseguire l'override di questo criterio e impedire la rimozione di elementi. Tuttavia, se si adotta questo approccio si rischia che la memoria cache superi la memoria disponibile e un'applicazione che prova ad aggiungere un elemento alla cache non completerà l'operazione con un'eccezione.

Alcune implementazione di caching possono fornire altri criteri di rimozione. Solitamente è incluso il criterio usato più di recente (in previsione che i dati non verranno nuovamente richiesti), il criterio FIFO (First-In-First-Out) (i dati meno recenti vengono rimossi per primi) o rimozione esplicita basata su un evento attivato (quali i dati modificati).

### Annullamento dei dati in una cache lato client

I dati contenuti in una cache lato client non sono generalmente considerati sotto la protezione del servizio che fornisce i dati al client; un servizio non può direttamente imporre a un client di aggiungere o rimuovere informazioni da una cache lato client. Ciò significa che è possibile per un client che usa una cache configurata in modo non corretto (ad esempio, i criteri di scadenza non sono implementati correttamente) di continuare a usare informazioni scadute memorizzate nella cache locale quando le informazioni nell'origine dati originale sono state modificate.

Se si compila un'applicazione Web che fornisce dati su una connessione HTTP, è possibile forzare un client Web (ad esempio un browser o un proxy Web) in modo implicito per recuperare le informazioni più recenti, se una risorsa viene aggiornata modificando l'URI della risorsa. I client Web solitamente usano l'URI di una risorsa come chiave nella cache lato client, pertanto la modifica dell'URI comporta al client Web di ignorare eventuali versioni memorizzate in precedenza nella cache di una risorsa e di recuperare invece la nuova versione.

## Gestione della concorrenza in una cache

Le cache sono spesso progettate per essere condivise da più istanze di un'applicazione. Ciascuna istanza dell'applicazione può leggere e modificare i dati nella cache. Di conseguenza, gli stessi problemi di concorrenza che si verificano con qualsiasi archivio dati condiviso valgono anche per una cache. In una situazione in cui un'applicazione deve modificare i dati contenuti nella cache, potrebbe essere necessario garantire che gli aggiornamenti effettuati da un'istanza dell'applicazione non sovrascrivano alla cieca le modifiche effettuate da un'altra istanza.

A seconda della natura dei dati e della probabilità delle collisioni, è possibile uno dei due seguenti approcci alla concorrenza:

- __Ottimistico.__ L'applicazione controlla se i dati nella cache sono stati modificati dal momento che è stata recuperata, immediatamente prima dell'aggiornamento. Se i dati sono ancora gli stessi, sarà possibile apportare la modifica. In caso contrario, l'applicazione deve decidere se aggiornarli (la logica di business alla base di questa decisione sarà specifica dell'applicazione). Questo approccio è adatto a situazioni in cui sono frequenti aggiornamenti o in cui sia improbabile che si verifichino conflitti.
- __Pessimistico.__ L'applicazione blocca i dati nella cache durante il recupero per impedire la modifica dei dati da parte di un'altra istanza. Questo processo assicura che non possano verificarsi conflitti, ma potrebbe bloccare altre istanze che devono elaborare gli stessi dati. La concorrenza pessimistica può influire sulla scalabilità della soluzione e deve essere usata solo per le operazioni di breve durata. Questo approccio potrebbe essere appropriato per situazioni in cui conflitti hanno maggiori probabilità, specialmente se un'applicazione aggiorna più elementi nella cache e deve assicurarsi che queste modifiche vengano applicate in modo coerente.

### Implementazione di scalabilità e disponibilità elevata e miglioramento delle prestazioni

Una cache non deve essere il repository principale dei dati; questo è il ruolo dell'archivio dati originale da cui la cache viene popolata. L'archivio dati originale è responsabile di garantire la persistenza dei dati.

Prestare attenzione a non introdurre dipendenze critiche per la disponibilità di un servizio di cache condivisa nelle soluzioni. Un'applicazione deve essere in grado di continuare a funzionare se il servizio che fornisce la cache condivisa è disponibile; l'applicazione non deve bloccarsi o non essere completata durante l'attesa che il servizio cache venga ripristinato. Pertanto, l'applicazione deve essere preparata per rilevare la disponibilità del servizio cache e il fallback all'archivio dati originale se la cache non è accessibile. Il [Modello a interruttore](http://msdn.microsoft.com/library/dn589784.aspx) è utile per la gestione di questo scenario. È possibile ripristinare il servizio che fornisce la cache e non appena torna disponibile della cache può essere ripopolata come i dati letti formano l'archivio dati originale, seguendo una strategia di come [modello cache-aside](http://msdn.microsoft.com/library/dn589799.aspx).

Tuttavia, eseguire il fallback all'archivio dati originale se la cache è temporaneamente non disponibile potrebbe avere un impatto sulla scalabilità del sistema; mentre viene recuperato l'archivio dati, l'archivio dati originale potrebbe essere sovraccaricato con richieste per i dati risultanti in timeout e connessioni non riuscite. Una strategia da considerare consiste nell'implementare una cache locale e privata in ogni istanza di un'applicazione con la cache condivisa a cui accedono tutte le istanze dell'applicazione. Quando l'applicazione recupera un elemento, è possibile verificare innanzitutto nella relativa cache locale, quindi la cache condivisa e infine archiviare i dati originali. La cache locale può essere popolata usando i dati nella cache condivisa o il database se la cache condivisa è disponibile. Questo approccio richiede una configurazione accurata per evitare che la cache locale diventi obsoleta rispetto alla cache condivisa, ma funge da buffer, se la cache condivisa è raggiungibile. Nella Figura 3 viene illustrata questa struttura.

![uso di una cache locale, privata con una cache condivisa_](media/best-practices-caching/Caching3.png) _Figura 3: uso di una cache locale, privata con una cache condivisa_

Per supportare la cache di grandi dimensioni che contengono dati di durata relativamente lunga, alcuni servizi cache forniscono un'opzione di disponibilità elevata che implementa il failover automatico, se la cache non è più disponibile. Questo approccio implica in genere la replica dei dati memorizzati nella cache archiviati in un server primario della cache verso un server secondario della cache e il passaggio al server secondario se il server primario presenta problemi o se viene persa la connessione. Per ridurre la latenza associata a più destinazioni, quando i dati vengono scritti nella cache sul server primario, la replica nel server secondario può verificarsi in modo asincrono. Questo approccio comporta la possibilità che alcune informazioni memorizzate nella cache vengano perse in caso di errore, ma la proporzione di tali eventauli perdite deve essere ridotta rispetto alle dimensioni complessive della cache.

Se una cache condivisa è grande, è consigliabile partizionare i dati memorizzati nella cache tra i nodi per ridurre le probabilità di conflitti e migliorare la scalabilità. Molte cache condivise supportano la possibilità di aggiungere in modo dinamico (e rimuovere) i nodi, ribilanciando i dati tra le partizioni. Questo approccio può comportare il clustering in base al quale l'insieme di nodi viene presentato alle applicazioni client come una singola cache trasparente, ma internamente i dati vengono suddivisi tra i nodi in base a qualche strategia di distribuzione predefinita che ripartisce equamente il carico in modo uniforme. Il [documento Data Partitioning Guidance](http://msdn.microsoft.com/library/dn589795.aspx) sul sito Web di Microsoft fornisce ulteriori informazioni sulle possibili strategie di partizionamento.

La funzionalità di clustering consente inoltre di aggiungere ulteriore disponibilità della cache. Se un nodo presenta errori, il resto della cache sarà ancora accessibile. La funzionalità di clustering viene spesso usata in combinazione con la replica e il failover. Ogni nodo può essere replicato e la replica può essere rapidamente collegata se il nodo presenta errori.

Molte operazioni di lettura e di scrittura probabilmente coinvolgeranno probabilmente singoli valori di dati o oggetti. Tuttavia, talvolta è possibile, quando necessario, archiviare o recuperare rapidamente elevati volumi di dati. Ad esempio, il seeding di una cache può comportare la scrittura di centinaia o migliaia di elementi nella cache o un'applicazione potrebbe avere la necessità di recuperare un numero elevato di elementi correlati dalla cache come parte della medesima richiesta. Molte cache su larga scala forniscono operazioni batch per tali scopi, consentendo a un'applicazione client di creare il pacchetto di un volume elevato di elementi in una singola richiesta e riducendo il sovraccarico associato all'esecuzione di un numero elevato di richieste di ridotte dimensioni.

## Memorizzazione nella cache e coerenza finale

Il modello cache-aside dipende dall'istanza dell'applicazione di popolamento della cache che può accedere alla versione più recente e coerente dei dati. In un sistema che implementa la coerenza finale (ad esempio, un archivio di dati replicati), ciò potrebbe non verificarsi. Un'istanza di un'applicazione può modificare un elemento di dati e invalidare la versione memorizzata nella cache dell'elemento. Un'altra istanza dell'applicazione potrebbe tentare di leggere l'elemento dalla cache provocando un mancato riscontro nella cache, in modo da leggere i dati dall'archivio dati e aggiungerli alla cache. Tuttavia, se l'archivio dati non è stato completamente sincronizzato con le altre repliche, l'istanza dell'applicazione può leggere e popolare la cache con il valore precedente.

Per ulteriori informazioni sulla gestione di coerenza dei dati, vedere la pagina di Data Consistency Guidance sul sito Web di Microsoft.

### Protezione dei dati memorizzati nella cache

Qualunque sia il servizio cache in uso, è necessario considerare il modo in cui proteggere i dati contenuti nella cache da accessi non autorizzati. Esistono due problemi principali:

- La riservatezza dei dati nella cache.
- La riservatezza del flusso dei dati tra la cache e l'applicazione che usa la cache.

Per proteggere i dati nella cache, il servizio cache può implementare un meccanismo di autenticazione che richiede alle applicazioni di identificarsi e uno schema di autorizzazione che specifica quali identità possono accedere ai dati nella cache e le operazioni (lettura e scrittura) che possono essere eseguite da tali identità. Per ridurre il sovraccarico associato alla lettura e alla scrittura dei dati, dopo che a un'identità è stato concesso l'accesso in scrittura e/o lettura alla cache, l'identità potrà usare tutti i dati nella cache. Se è necessario limitare l'accesso a subset di dati memorizzati nella cache, è possibile effettuare quanto segue:

- Suddividere la cache in partizioni (usando diversi server di cache) e concedere solo l'accesso alle identità per le partizioni che possono usare oppure
- Crittografare i dati in ogni subset con chiavi diverse e fornire solo le chiavi di crittografia per le identità che devono disporre dell'accesso a ogni subset. Un'applicazione client può comunque essere in grado di recuperare tutti i dati nella cache, ma sarà in grado di decrittografare i dati per il quale dispone le chiavi.

Per proteggere i dati trasmessi nella e dalla cache si dipende dalle funzionalità di sicurezza fornite dall'infrastruttura di rete usata dalle applicazioni client per connettersi alla cache. Se la cache viene implementata usando un server locale all'interno della stessa organizzazione che ospita le applicazioni client, grazie all'isolamento della rete stessa potrebbe non essere necessario eseguire ulteriori operazioni. Se la cache si trova in modalità remota e richiede una connessione TCP o HTTP in una rete pubblica (ad esempio Internet), è necessario considerare l'implementazione del protocollo SSL.

## Considerazioni per l'implementazione della cache con Microsoft Azure

Azure offre Cache Redis di Azure. Si tratta di un'implementazione della cache Redis Open Source che viene eseguita come servizio in un data center di Azure. Se l'applicazione viene implementata come un servizio cloud, un sito Web o all'interno di una macchina virtuale di Azure fornisce un servizio di caching a cui è possibile accedere da qualsiasi applicazione Azure. Le cache possono essere condivise da applicazioni client che dispongono della chiave di accesso appropriato.

Redis è una soluzione di memorizzazione nella cache ad alte prestazioni che fornisce disponibilità, scalabilità e sicurezza. In genere, viene eseguita come servizio distribuito in uno o più macchine dedicate e tenta di archiviare quante più informazioni possibile in memoria per garantire un rapido accesso. Questa architettura è destinata a fornire bassa latenza e velocità effettiva elevata, riducendo la necessità di eseguire lente operazioni di I/O.

La cache Redis di Azure è compatibile con molte delle varie API usate dalle applicazioni client. Se si dispone di applicazioni che usano già Redis in esecuzione in locale, la cache Redis di Azure fornisce un percorso di migrazione rapido per la memorizzazione nella cache nel cloud.

> [AZURE.NOTE]Azure offre inoltre il servizio cache gestita. Questo servizio si basa sul motore di cache di AppFabric Microsoft. Consente di creare una cache distribuita che può essere condivisa da applicazioni collegate in modo flessibile. La cache è ospitata su server ad alte prestazioni in esecuzione in un data center di Azure. Tuttavia, questa opzione non è più consigliata e viene fornita solo per supportare le applicazioni esistenti che sono stata realizzate per usarla. Per tutte le nuove applicazioni sviluppate, usare invece la cache Redis di Azure.
>
> Inoltre, Azure supporta la memorizzazione nella cache nel ruolo. Questa funzionalità consente di creare una cache specifica per un servizio cloud. La cache ospitata da istanze di un ruolo Web o di lavoro e accessibile solo dai ruoli funziona come parte della stessa unità di distribuzione servizio cloud (un'unità di distribuzione è il set di istanze del ruolo distribuito come servizio cloud in un'area specifica). La cache è di tipo cluster e tutte le istanze del ruolo all'interno della stessa unità di distribuzione che ospitano la cache entrano a far parte dello stesso cluster di cache. Le applicazioni esistenti che usano la memorizzazione nella cache nel ruolo possono continuare a farlo, ma la migrazione alla cache Redis di Azure può offrire ulteriori vantaggi. Per ulteriori informazioni sull'uso della cache Redis di Azure o di una cache nel ruolo, visitare la pagina [Qual è l'offerta di Cache di Azure più adatta alle mie esigenze?](http://msdn.microsoft.com/library/azure/dn766201.aspx) sul sito Web di Microsoft.


### Funzionalità di Redis

Redis è molto di più di un server di cache semplice. Fornisce un database in memoria distribuito con un set di comandi completo che supporta molti scenari comuni, come descritto nella sezione Casi di uso per la memorizzazione nella cache Redis più avanti in questo documento. In questa sezione vengono riepilogate alcune delle funzionalità chiave fornite da Redis.

### Redis come un database in memoria

Redis supporta la lettura e le operazioni di scrittura. A differenza di molte cache (che devono essere considerate come archivi dati transitori), le operazioni di scrittura possono essere protette da un errore di sistema mediante memorizzazione periodica in un file di snapshot locale o in un file di log di solo accodamento. Tutte le operazioni di scrittura sono asincrone e non impediscono ai client di leggere e scrivere dati. Quando viene avviata l'esecuzione di Redis, verranno letti i dati dal file di snapshot o di log e verranno usati per costruire la cache in memoria. Per ulteriori informazioni, vedere la sezione relativa alla [persistenza di Redis](http://redis.io/topics/persistence) sul relativo sito Web.

> [AZURE.NOTE]Redis non garantisce che tutte le scritture verranno salvate in caso di errore, ma nel peggiore dei casi verranno persi solo il corrispettivo dei dati corrispondenti ad alcuni secondi. Tenere presente che una cache non è destinata a fungere da origine dati autorevole e spetta alle applicazioni che usano la cache garantire che i dati critici vengano salvati correttamente in un archivio dati appropriato. Per ulteriori informazioni, vedere il modello cache-aside.

#### Tipi di dati Redis

Redis è un archivio chiave-valore, dove i valori possono contenere tipi semplici o complesse strutture di dati, ad esempio gli hash, gli elenchi e i set. Redis supporta una serie di operazioni atomiche su questi tipi di dati. Le chiavi possono essere permanenti o contrassegnate con un periodo di durata limitato dopo il quale la chiave e il valore corrispondente vengono automaticamente rimossi dalla cache. Per ulteriori informazioni sulle chiavi e sui valori di Redis, visitare la pagina [An Introduction to Redis data types and abstractions](http://redis.io/topics/data-types-intro) sul sito Web di Redis.

#### Replica e cluster di Redis

Redis supporta la replica primaria o secondaria per garantire la disponibilità e gestire la velocità effettiva; le operazioni di scrittura per un nodo master di Redis vengono replicate su uno o più nodi subordinati e le operazioni di lettura possono essere fornite dal nodo master o tutti i nodi secondari. In caso di una partizione di rete, le forme subordinate possono continuare a fornire i dati e in modo trasparente risincronizzarsi con il nodo master quando viene ristabilita la connessione. Per ulteriori informazioni, visitare la pagina relativa alla [replica](http://redis.io/topics/replication) sul sito Web di Redis.

Redis fornisce inoltre il clustering, consentendo di partizionare i dati in partizioni tra server e distribuire il carico in modo trasparente. Questa funzionalità migliora la scalabilità poiché è possibile aggiungere nuovi server Redis e ripartizionare i dati con l'incremento della dimensione della cache. Inoltre, ogni server del cluster può essere replicato usando la replica primaria o secondaria per garantire la disponibilità in ogni nodo del cluster. Per ulteriori informazioni sul servizio cluster e sul partizionamento orizzontale, visitare la [pagina relativa all'esercitazione del cluster Redis](http://redis.io/topics/cluster-tutorial) sul relativo sito Web.

> [AZURE.NOTE]La cache Redis di Azure non supporta attualmente il clustering. Se si desidera creare un cluster di Redis, è possibile creare un server Redis personalizzato. Per ulteriori informazioni, vedere la sezione relativa alla creazione di una cache Redis personalizzata più avanti in questo documento.

### uso della memoria di Redis

La cache Redis ha una dimensione limitata a seconda delle risorse disponibili nel computer host. Quando si configura un server Redis, è possibile specificare la quantità massima di memoria usabile. Una chiave in una cache Redis può essere configurata con una scadenza, dopo il quale viene automaticamente rimossa dalla cache. Questa funzionalità può aiutare a impedire che la cache in memoria venga riempita con dati obsoleti o non aggiornati.

Come la memoria si riempie, Redis automaticamente provvedere alla rimozione di chiavi e dei relativi valori seguendo un determinato numero di criteri. L'impostazione predefinita è la rimozione degli elementi usati meno di recente, ma è possibile inoltre selezionare altri criteri quale la rimozione di chiavi casualmente o la disattivazione complessiva della rimozione (in tal caso, i tentativi di aggiungere elementi alla cache avranno esito negativo se questa sarà piena). Ulteriori informazioni vengono fornite nella pagina relativa all'[uso di Redis come cache LRU](http://redis.io/topics/lru-cache).

### Transazioni e batch Redis

Redis consente a un'applicazione client di inviare una serie di operazioni che consentono di leggere e scrivere dati nella cache come una transazione atomica. L'esecuzione di tutti i comandi della transazione sono garantiti in modo sequenziale e nessun altro comando emesso da altri client simultaneamente verrà interconnesso. Tuttavia, non si tratta di transazioni valide sarebbero eseguite da un database relazionale. L'elaborazione delle transazioni è costituita da due fasi: l'accodamento dei comandi e l'esecuzione dei comandi. Durante la fase di accodamento messaggi del comando, i comandi che costituiscono la transazione vengono inviati dal client. Se si verifica una sorta di errore a questo punto (ad esempio un errore di sintassi o un numero errato di parametri), Redis rifiuterà di elaborare l'intera transazione e annullerà le modifiche. Durante la fase di esecuzione, Redis esegue ogni comando in coda in sequenza. Se un comando ha esito negativo durante questa fase, Redis continuerà con il comando successivo in coda e non ripristinare gli effetti di qualsiasi comando che è già stato eseguito. Questo tipo di transazione semplificata consente di mantenere le prestazioni ed evitare problemi di prestazioni causati da conflitti. Redis implementa una forma di blocco ottimistico per facilitare la gestione della coerenza. Per informazioni dettagliate sulle transazioni e sul blocco di Redis, visitare la [pagina relativa alle transazioni](http://redis.io/topics/transactions) sul sito Web di Redis.

Redis supporta inoltre l'invio in batch non transazionale di richieste. Il protocollo di Redis usato dai client per inviare comandi a un server di Redis consente ai client di inviare una serie di operazioni come parte della richiesta stessa. Ciò può contribuire a ridurre la frammentazione del pacchetto in rete. Quando viene elaborato il batch, viene eseguito ogni comando. A differenza di una transazione, se uno di questi comandi sono in formato non valido verranno rifiutati ma i comandi rimanenti verranno eseguiti. Non esiste inoltre alcuna garanzia sull'ordine in cui verranno elaborati i comandi nel batch.

### Sicurezza di Redis

Redis è progettato esclusivamente per fornire accesso rapido ai dati e per l'esecuzione in un ambiente attendibile a cui possano accedervi solo client attendibili. Redis supporta solo un modello di sicurezza limitato in base all'autenticazione di password (è possibile rimuovere completamente l'autenticazione, anche se questo non è consigliato). Tutti i client autenticati hanno la stessa password globale e hanno accesso alle stesse risorse. Se è necessaria una sicurezza degli accessi più completa, occorre implementare il proprio livello di sicurezza prima del server Redis e tutte le richieste client devono passare tramite questo livello aggiuntivo; Redis non deve essere esposto direttamente ai client non attendibili o non autenticati.

È possibile limitare l'accesso ai comandi disabilitandoli o rinominandoli (e solo fornendo ai client privilegiati i nuovi nomi).

Redis non supporta direttamente alcuna forma di crittografia dei dati, in modo che i tipi di codifica devono essere eseguiti da applicazioni client. Inoltre, Redis non fornisce alcuna forma di sicurezza del trasporto, in modo che se si desidera proteggere i dati trasmessi attraverso la rete è necessario implementare un proxy SSL.

Per ulteriori informazioni, visitare la pagina relativa alla [protezione Redis](http://redis.io/topics/security) sul relativo sito Web.

> [AZURE.NOTE]La cache Redis di Azure fornisce il proprio livello di protezione attraverso il quale i client si connettono; i server Redis sottostanti non sono esposti alla rete pubblica.

### Come usare la cache Redis di Azure

La cache Redis di Azure fornisce l'accesso ai server Redis in esecuzione su server ospitati in un data center di Azure e agisce come un'interfaccia che fornisce controllo e sicurezza degli accessi. È possibile eseguire il provisioning di una cache tramite il portale di gestione di Azure. Il portale fornisce una serie di configurazioni predefinite, che vanno da una cache di 53 GB in esecuzione come servizio dedicato che supporta le comunicazioni SSL (per la privacy) e replica principale/secondario con un contratto di servizio di disponibilità del 99,9%, fino a 250 MB di cache senza replica (nessuna garanzia di disponibilità) in esecuzione su hardware condiviso.

Tramite il portale di gestione di Azure è possibile anche configurare il criterio di eliminazione della cache e controllare l'accesso alla cache mediante l'aggiunta di utenti ai ruoli forniti: proprietario, collaboratore, lettore. Questi ruoli definiscono le operazioni che i membri possono eseguire. Ad esempio, i membri del ruolo Proprietario dispongono di controllo completo sulla cache (inclusa la sicurezza) e il relativo contenuto, i membri del ruolo Collaboratore sono in grado di leggere e scrivere informazioni nella cache e i membri del ruolo Lettore possono recuperare solo i dati dalla cache.

La maggior parte delle attività amministrative vengono eseguite tramite il portale di gestione di Azure e per questo motivo, molti dei comandi amministrativi disponibili nella versione standard di Redis non sono disponibili, inclusa la possibilità di modificare la configurazione a livello di codice, di arrestare il server Redis, di configurare ulteriori server secondari o forzatamente salvare i dati su disco.

Il portale di gestione di Azure include una visualizzazione grafica pratica che consente di monitorare le prestazioni della cache. Ad esempio, è possibile visualizzare il numero di connessioni stabilite, il numero di richieste eseguite, il volume di letture e scritture e il numero di riscontri nella cache rispetto ai mancati riscontri nella cache. Queste informazioni consentono di determinare l'efficacia della cache e se necessario passare a una configurazione diversa o modificare il criterio di rimozione. Inoltre, è possibile creare avvisi che inviano messaggi di posta elettronica a un amministratore se uno o più metriche scende al di sotto di un intervallo previsto. Ad esempio, se il numero di mancati riscontri nella cache supera il valore specificato durante l'ultima ora, un amministratore potrebbe ricevere un avviso quando la cache dovesse risultare troppo piccola o i dati dovessero essere rimossi troppo rapidamente.

È inoltre possibile monitorare la CPU, la memoria e l'uso della rete per la cache.

> [AZURE.NOTE]La cache Redis di Azure è destinata a fungere esclusivamente da cache piuttosto che da database. Di conseguenza, attualmente non viene implementata la persistenza Redis.

Per ulteriori informazioni ed esempi che illustrano come creare e configurare una cache Redis di Azure, visitare la pagina relativa all'[Introduzione alla Cache Redis di Azure](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sul blog di Azure.

## Memorizzazione nella cache dello stato della sessione e dell'output HTML

Se la compilazione di applicazioni Web ASP.NET eseguite tramite i ruoli Web di Azure, è possibile salvare informazioni sullo stato della sessione e sull'output HTML in una cache Redis di Azure. Il Provider di stato della sessione per la cache Redis di Azure consente di condividere le informazioni di sessione tra istanze diverse di un'applicazione Web ASP.NET e risulta particolarmente utile nelle situazioni di Web farm in cui l'affinità client-server non è disponibile e la memorizzazione nella cache dati sessione in memoria potrebbe non essere indicata.

L'uso del provider di stato della sessione cache Redis di Azure offre diversi vantaggi, tra cui:

- Consente di condividere lo stato della sessione tra un numero elevato di istanze di un'applicazione Web ASP.NET, che fornisce scalabilità
- Supporta l'accesso controllato e simultaneo agli stessi dati di stato sessione per più lettori e un unico scrittore e
- Consente di usare la compressione per risparmiare memoria e migliorare le prestazioni della rete.

Per ulteriori informazioni visitare la pagina [Provider di stato della sessione ASP.NET per Cache Redis di Azure](http://msdn.microsoft.com/library/azure/dn690522.aspx) del sito Web di Microsoft.

> [AZURE.NOTE]Non usare il Provider di stato sessione per Cache Redis di Azure per le applicazioni ASP.NET eseguite all'esterno dell'ambiente Azure. La latenza di accesso alla cache dall'esterno di Azure consente di eliminare le prestazioni di memorizzazione nella cache di dati.

Analogamente, il Provider di Cache di Output per Cache Redis di Azure consente di salvare le risposte HTTP generate da un'applicazione Web ASP.NET. L'uso del Provider di Cache di Output con Cache Redis di Azure può migliorare i tempi di risposta delle applicazioni che eseguono il rendering di output HTML complesso. Le istanze dell'applicazione che semplificano la generazione di risposte simili usano dei frammenti condivisi di output nella cache anziché generare il modello dell'output HTML. Per ulteriori informazioni visitare la pagina [Provider di cache di output ASP.NET per Cache Redis di Azure](http://msdn.microsoft.com/library/azure/dn798898.aspx) sul sito Web di Microsoft.

## Creazione di una Cache Redis personalizzata

La Cache Redis di Azure agisce come un'interfaccia per i server Redis sottostanti. Attualmente supporta un set fisso di configurazioni ma non è disponibile per il clustering di Redis. Se è necessaria una configurazione avanzata che non rientra nella Cache Redis di Azure (ad esempio una cache di dimensioni superiore a 53 GB) è possibile creare e ospitare i propri server Redis usando macchine virtuali di Azure. Si tratta di un processo potenzialmente complesso come potrebbe essere necessario creare più macchine virtuali di agire come nodi secondari se si desidera implementare la replica. Inoltre, se si desidera creare un cluster, è necessario disporre di più server master e secondari; una topologia di replica in cluster minima che fornisce un elevato livello di disponibilità e scalabilità è costituita da almeno 6 macchine virtuali organizzate come 3 coppie di server master/secondario (un cluster deve contenere almeno 3 nodi master). Ogni coppia master/secondario deve trovarsi in posizione ravvicinata per ridurre al minimo la latenza, ma ogni set di coppie possono essere in esecuzione nei data center di Azure diversi ubicati in aree diverse se si desidera individuare i dati memorizzati nella cache vicino alle applicazioni che più probabilmente la useranno. La pagina [Running Redis on a CentOS Linux VM in Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) sul sito Web di Microsoft illustra un esempio che mostra come creare e configurare un nodo Redis in esecuzione come una macchina virtuale di Azure.

Si noti che se si implementa la propria cache Redis in questo modo, si sarà responsabili del monitoraggio, della gestione e della protezione del servizio.

## Partizionamento di una cache Redis

Il partizionamento della cache comporta la suddivisione della cache tra più computer. Questa struttura offre diversi vantaggi rispetto all'uso di un singolo server di cache, tra cui:

- La creazione di una cache più grande di quanto non possa essere archiviata in un singolo server.
- Distribuzione dei dati tra server, migliorando la disponibilità. Se un server presenta errori o non è accessibile, solo i dati che contiene sono inaccessibili; i dati sui server rimanenti sono ancora accessibili. Per una cache, ciò non è fondamentale poiché i dati memorizzati nella cache sono solo una copia temporanea dei dati contenuti in un database e i dati memorizzati nella cache in un server che non è accessibile possono essere invece memorizzati in un altro server.
- Estensione del carico tra server, con conseguente miglioramento delle prestazioni e della scalabilità.
- Attivazione della geolocalizzazione dei dati vicini agli utenti che li usando, per ridurre la latenza.

Per una cache, la forma più comune di partizionamento è il partizionamento orizzontale. In questa strategia ogni partizione è una cache Redis in sé. I dati vengono indirizzati a una partizione specifica tramite la logica di partizionamento orizzontale, che consente di usare un'ampia gamma di approcci per distribuire i dati. Il [modello di partizionamento orizzontale](http://msdn.microsoft.com/library/dn589797.aspx) fornisce ulteriori informazioni sull'implementazione del partizionamento orizzontale.

Per implementare il partizionamento in una cache Redis, è possibile adottare uno degli approcci seguenti:

- _Routing di query lato server._ Con questa tecnica, un'applicazione client invia una richiesta a un qualsiasi server Redis che costituisca la cache (probabilmente il server più vicino). Ogni server Redis archivia i metadati che descrivono la partizione contenuta e contiene inoltre informazioni sulle partizioni che si trovano su altri server. Il server Redis esamina la richiesta del client e se può risolverla in locale eseguirà l'operazione richiesta, in caso contrario inoltrerà la richiesta al server appropriato. Questo modello viene implementato dal clustering Redis e viene descritto più dettagliatamente nell'[esercitazione relativa al cluster Redis](http://redis.io/topics/cluster-tutorial) sulla relativa pagina Web. Il clustering di Redis è trasparente alle applicazioni client e gli ulteriori server Redis possono essere aggiunti al cluster (e i dati partizionati nuovamente) senza dover riconfigurare i client.

  >[AZURE.IMPORTANT]La Cache Redis di Azure non supporta attualmente il clustering. Se si desidera implementare questo approccio è necessario compilare una cache Redis personalizzata come descritto in precedenza.

- _Partizionamento lato client._ In questo modello, l'applicazione client contiene la logica (possibilmente in forma di una libreria) le richieste vengono indirizzate al server Redis appropriato. Questo approccio può essere usato con la Cache Redis di Azure. È possibile creare più Cache Redis di Azure (una per ogni partizione di dati) e implementare la logica lato client che effettua il routing delle richieste verso la cache corretta. Se lo schema di partizionamento viene modificato (se ulteriori Cache Redis di Azure vengono create, ad esempio), potrebbe essere necessario riconfigurare le applicazioni client.

- _Partizionamento assistito dal proxy._ In questo schema, il client invia le applicazioni richieste al servizio proxy intermediario che comprende il modo in cui i dati sono partizionati e instrada la richiesta appropriata Redis server. Questo approccio può essere usato anche con Cache Redis di Azure; il servizio proxy è implementato come servizio cloud di Azure. Questo approccio richiede un ulteriore livello di complessità per implementare il servizio e le richieste potrebbero richiedere più tempo rispetto all'uso del partizionamento lato client.

La pagina relativa al [partizionamento e su come suddividere i dati tra più istanze di Redis](http://redis.io/topics/partitioning) sul relativo sito Web fornisce ulteriori informazioni sull'implementazione del partizionamento con Redis.

### Implementazione di applicazioni client della cache Redis

Redis supporta le applicazioni client scritte in numerosi linguaggi di programmazione. Se si creano nuove applicazioni usando .NET Framework, l'approccio consigliato consiste nell'usare la libreria client Stackexchange.Redis. Questa libreria fornisce un modello a oggetti .NET Framework che astrae i dettagli per la connessione a un server di Redis, l'invio di comandi e la ricezione delle risposte. È disponibile in Visual Studio come pacchetto NuGet. Per connettersi a una cache Redis di Azure o una cache Redis personalizzata ospitata in una macchina virtuale, è possibile usare la stessa raccolta.

Per connettersi a un server Redis è possibile usare il metodo statico `Connect` della classe `ConnectionMultiplexer`. La connessione creata da questo metodo è progettata per essere usata per tutta la durata dell'applicazione client e la stessa connessione può essere usata da più thread simultanei; si sconsiglia di non riconnettersi e disconnettersi ogni volta che si esegue un'operazione di Redis poiché ciò potrebbe ridurre le prestazioni. È possibile specificare i parametri di connessione, ad esempio l'indirizzo dell'host Redis e la password. Se si usa la cache Redis di Azure, la password è la chiave primaria o secondaria generato per la Cache Redis di Azure tramite il portale di gestione di Azure.

Dopo aver completato la connessione al server di Redis, è possibile ottenere un handle per il database Redis che funge da cache. La connessione Redis fornisce il metodo `GetDatabase` a tale scopo. È quindi possibile recuperare elementi dalla cache e archiviare i dati nella cache usando i metodi `StringGet` e `StringSet`. Questi metodi presuppongano una chiave come parametro e restituiscono l'elemento nella cache con un valore corrispondente (`StringGet`) o aggiungono l'elemento alla cache con questa chiave (`StringSet`).

A seconda della posizione del server Redis, molte operazioni possono essere soggette a una determinata latenza mentre una richiesta viene trasmessa al server e una risposta restituita al client. La libreria StackExchange offre versioni asincrone di molti dei metodi esposti per consentire ai client di rimanere attivi. Questi metodi supportano il [modello asincrono basato su attività](http://msdn.microsoft.com/library/hh873175.aspx) in .NET Framework.

Il frammento di codice riportato di seguito mostra un metodo denominato `RetrieveItem` che illustra un esempio di un'implementazione del modello cache-aside basato su Redis e la libreria StackExchange. Il metodo accetta un valore di chiave di stringa e tenta di recuperare la voce corrispondente dalla cache Redis chiamando il metodo `StringGetAsync` (la versione asincrona di `StringGet`). Se l'elemento non viene trovato, viene recuperato dall'origine dati sottostante tramite il metodo `GetItemFromDataSourceAsync` (che è un metodo locale e non come parte della libreria di StackExchange) e quindi aggiunto alla cache usando il metodo `StringSetAsync`, pertanto può essere recuperato più rapidamente in seguito.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

I metodi `StringGet` e `StringSet` non sono limitati al recupero o all'archiviazione dei valori di stringa; possono assumere qualsiasi elemento serializzato come matrice di byte. Se è necessario salvare un oggetto .NET è possibile serializzarlo come un flusso di byte e usare il metodo StringSet per scrivere nella cache. Analogamente, è possibile leggere un oggetto dalla cache usando il metodo StringGet e deserializzare un oggetto .NET. Il codice seguente viene illustrato un set di metodi di estensione per l'interfaccia IDatabase (il metodo GetDatabase di una connessione Redis restituisce un oggetto `IDatabase`) e il codice di esempio che usa questi metodi per leggere e scrivere un oggetto BlogPost nella cache:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

Nell'esempio di codice riportato di seguito viene illustrato un metodo denominato `RetrieveBlogPost` che usa questi metodi di estensione per leggere e scrivere un oggetto `BlogPost` serializzabile nella cache del modello cache-aside:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis supporta il pipelining di comandi se un'applicazione client invia più richieste asincrone. Redis consente di eseguire il metodo multiplex delle richieste usando la stessa connessione piuttosto che ricevere e rispondere ai comandi in una sequenza rigorosa. Questo approccio consente di ridurre la latenza effettuando un uso più efficiente della rete. Nel frammento di codice riportato di seguito viene illustrato un esempio in cui vengono recuperati i dettagli di due clienti contemporaneamente. Il codice invia due richieste e quindi esegue altre attività di elaborazione (non mostrate) prima di attendere di ricevere i risultati. Il metodo di attesa dell'oggetto della cache è simile al metodo .NET Framework Task.Wait:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

La pagina relativa allo [sviluppo per Cache Redis di Azure](http://msdn.microsoft.com/library/azure/dn690520.aspx) sul sito Web di Microsoft fornisce ulteriori informazioni su come scrivere le applicazioni client che possono usare la Cache Redis di Azure. Informazioni aggiuntive sono disponibili sulla [pagina relativa all'uso di base](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) sul sito Web di Stackexchange e la pagina [Ppipeline e Multiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) sullo stesso sito Web stesso fornisce ulteriori informazioni sulle operazioni asincrone e sul pipelining con Redis e la libreria StackExchange. Nella sezione Casi di uso per la memorizzazione nella cache Redis più avanti in questa guida vengono forniti esempi di alcune delle tecniche più avanzate che è possibile applicare ai dati contenuti in una cache Redis.

## Casi di uso per la memorizzazione nella cache Redis

L'uso più semplice di Redis per i problemi di memorizzazione nella cache per l'archiviazione di coppie chiave/valore dove il valore è una stringa non interpretata di lunghezza arbitraria che può contenere qualsiasi dato binario (è essenzialmente una matrice di byte che può essere considerata come una stringa). Questo scenario è stato illustrato nella sezione Implementazione di applicazioni client della cache Redis in precedenza in questa Guida. Si noti che le chiavi contengano anche dati non interpretati, pertanto è possibile usare qualsiasi informazione binaria come chiave, sebbene più lunga è la chiave più spazio sarà necessario per l'archiviazione e il tempo impiegato per eseguire operazioni di ricerca. Per l'usabilità e la facilità di manutenzione, progettare con attenzione il keyspace e usare chiavi significative (ma non dettagliate). Ad esempio, è possibile usare chiavi strutturate quali "customer:100" per rappresentare la chiave per il cliente con ID 100 anziché semplicemente "100". Questo schema consente di distinguere facilmente tra i valori che consentono di archiviare diversi tipi di dati. Ad esempio, è possibile usare anche la chiave "orders: 100" per rappresentare la chiave per l'ordine con ID 100.

Oltre alle stringhe binarie unidimensionali, un valore nella coppia chiave/valore Redis può contenere anche informazioni più strutturate, inclusi gli elenchi, i set (ordinati e non ordinati) e hash. Redis fornisce un set di comandi completo che consente di modificare questi tipi. Molti di questi comandi sono disponibili per le applicazioni .NET Framework tramite una libreria client, ad esempio StackExchange. La pagina [An introduction to Redis data types and abstractions](http://redis.io/topics/data-types-intro) sul sito Web di Redis fornisce una panoramica più dettagliata su questi tipi e sui comandi che è possibile usare per gestirli.

In questa sezione vengono riepilogati alcuni casi comuni di uso per questi tipi di dati e di comandi.

### Esecuzione atomica e operazioni batch

Redis supporta una serie di operazioni di lettura e scrittura atomiche su valori di stringa. Queste operazioni eliminano i rischi possibili di race condition che potrebbero verificarsi quando si usano i comandi `GET` e `SET` separati. Le operazioni disponibili includono:

- `INCR`, `INCRBY`, `DECR`, e `DECRBY` che eseguono operazioni di incremento e decremento atomiche su valori di dati numerici integer. La libreria StackExchange fornisce versioni di overload dei metodi `IDatabase.StringIncrementAsync` e `IDatabase.StringDecrementAsync` per eseguire queste operazioni e restituiscono il valore risultante memorizzato nella cache. Il frammento di codice riportato di seguito illustra come usare i metodi seguenti:

  ```csharp ConnectionMultiplexer redisHostConnection = ...; IDatabase cache = redisHostConnection.GetDatabase(); ... await cache.StringSetAsync("data:counter", 99); ... long oldValue = await cache.StringIncrementAsync("data:counter"); // Increment by 1 (the default) // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50); // Decrement by 50 // newValue should be 50 ```

- `GETSET` che recupera il valore associato a una singola chiave e lo modifica in un nuovo valore. La libreria StackExchange rende disponibile questa operazione tramite il metodo `IDatabase.StringGetSetAsync`. Nel frammento di codice seguente viene illustrato un esempio di questo metodo. Questo codice restituisce il valore corrente associato alla chiave "data:counter" dell'esempio precedente e reimposta il valore di questa chiave su zero, il tutto nell'ambito della stessa operazione:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- I metodi `MGET` e `MSET`, che possono restituire o modificare un set di valori stringa come un'unica operazione. I metodi `IDatabase.StringGetAsync` e `IDatabase.StringSetAsync` di sovraccarico per supportare questa funzionalità, come illustrato nell'esempio seguente:

  ```csharp ConnectionMultiplexer redisHostConnection = ...; IDatabase cache = redisHostConnection.GetDatabase(); ... // Create a list of key/value pairs var keysAndValues = new List<KeyValuePair<RedisKey  RedisValue>>() { new KeyValuePair<RedisKey  RedisValue>("data:key1", "value1"), new KeyValuePair<RedisKey  RedisValue>("data:key99", "value2"), new KeyValuePair<RedisKey  RedisValue>("data:key322", "value3") };

  // Store the list of key/value pairs in the cache cache.StringSet(keysAndValues.ToArray()); ... // Find all values that match a list of keys RedisKey keys = { "data:key1", "data:key99", "data:key322"}; RedisValue values = null; values = cache.StringGet(keys); // values should contain { "value1", "value2", "value3" } ```

È possibile inoltre combinare più operazioni in una singola transazione Redis in base a quanto descritto nella sezione Transazioni e batch Redis in questa guida. La libreria StackExchange fornisce supporto per le transazioni attraverso l'interfaccia `ITransaction`. È possibile creare un oggetto ITransaction usando il metodo IDatabase.CreateTransaction e richiamare i comandi per la transazione usando i metodi forniti dall'oggetto `ITransaction`. L'interfaccia `ITransaction` fornisce l'accesso a una serie di metodi come l'interfaccia`IDatabase`, ad eccezione del fatto che tutti i metodi sono asincroni, verranno eseguite quando viene richiamato il metodo `ITransaction.Execute`. Il valore restituito dal metodo execute indica se la transazione è stata creata correttamente (true) o non correttamente (false).

Nel frammento di codice riportato di seguito viene illustrato un esempio che incrementa e decrementa due contatori come parte della stessa transazione:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Tenere presente che le transazioni di Redis sono a differenza delle transazioni nei database relazionali. Il metodo Execute semplicemente accoda tutti i comandi che costituiscono la transazione per l'esecuzione e se uno di essi non è corretto la transazione viene interrotta. Se tutti i comandi sono stati accodati correttamente, ogni comando viene eseguito in modo asincrono. Se un comando non viene completato, gli altri continueranno comunque a essere elaborati. Se è necessario verificare che un comando sia stato completato correttamente, è necessario recuperare i risultati del comando usando la proprietà del risultato dell'attività corrispondente, come illustrato nell'esempio precedente. La lettura della proprietà Result verrà bloccata fino al completamento dell'attività.

Per ulteriori informazioni, vedere la pagina relativa alle [transazioni in Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) sul sito Web Stackexchange.Redis.

Per l'esecuzione di operazioni batch, è possibile usare l'interfaccia IBatch della libreria di StackExchange. L'interfaccia fornisce l'accesso a una serie di metodi come l'interfaccia IDatabase, ad eccezione del fatto che tutti i metodi sono asincroni. Creare un oggetto IBatch usando il metodo IDatabase.CreateBatch e quindi eseguire il batch usando il metodo IBatch.Execute, come illustrato nell'esempio seguente. Questo codice semplicemente imposta un valore stringa e incrementa e decrementa gli stessi contatori usati nell'esempio precedente e visualizza i risultati:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

È importante comprendere che a differenza di una transazione, se un comando in un batch non viene completato perché non è corretto altri comandi possono comunque essere eseguiti; il metodo IBatch.Execute non restituisce alcuna indicazione dell'esito positivo o negativo.

### Esecuzione di operazioni cache "fire and forget"

Redis supporta operazioni "fire and forget" usando i flag di comando. In questo caso, il client semplicemente inizia un'operazione ma non ha alcun interesse nel risultato e non attende il comando per il completamento. Nell'esempio seguente viene illustrato come eseguire il comando INCR come operazione "fire and forget":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### Chiavi a scadenza automatica

Quando si archivia un elemento in una cache Redis, è possibile specificare un timeout dopo il quale l'oggetto verrà automaticamente rimosso dalla cache. È inoltre possibile eseguire una query in merito a quanto dispone una chiave prima della scadenza usando il comando `TTL`; questo comando è disponibile per le applicazioni StackExchange mediante il metodo IDatabase.KeyTimeToLive.

Nel frammento di codice riportato di seguito viene illustrato un esempio di impostazione di scadenza pari a 20 secondi su una chiave e l'esecuzione di una query in merito alla durata rimanente della chiave:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

Inoltre, è possibile impostare l'ora di scadenza per una specifica data e ora usando il comando di scadenza, disponibile nella libreria StackExchange come il metodo KeyExpireAsync:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Suggerimento:_ manualmente, è possibile rimuovere un elemento dalla cache usando il comando DEL che è disponibile tramite la libreria StackExchange come il metodo IDatabase.KeyDeleteAsync.

### uso di tag per intercorrelare elementi memorizzati nella cache

Un set di Redis è un insieme di più elementi che condividono una singola chiave. È possibile creare un insieme usando il comando SADD. È possibile recuperare gli elementi di un set usando il comando SMEMBERS. La libreria StackExchange implementa il comando SADD tramite il metodo IDatabase.SetAddAsync e il comando SMEMBERS con il metodo IDatabase.SetMembersAsync. È inoltre possibile combinare set esistenti per creare nuovi set usando i comandi SDIFF (imposta differenzsa), SINTER (imposta intersezione) e SUNION (imposta unione). La libreria StackExchange unifica queste operazioni nel metodo IDatabase.SetCombineAsync; il primo parametro a questo metodo specifica l'operazione di impostazione.

Nei frammenti di codice riportati di seguito viene illustrato come i set possono rivelarsi utili per l'archiviazione e il recupero di raccolte di elementi correlati rapidamente. Questo codice usa il tipo BlogPost descritto nella sezione implementazione Redis Cache applicazioni Client. Un oggetto BlogPost contiene quattro campi, ovvero un ID, un titolo, un punteggio di classificazione e un insieme di tag. Il primo frammento di codice seguente mostra i dati di esempio usati per popolare un elenco di oggetti BlogPost C#:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

È possibile archiviare i tag per ogni oggetto BlogPost come un set in una cache Redis e associare ciascun set all'ID del BlogPost. In questo modo un'applicazione può trovare rapidamente tutti i tag appartenenti a un post di blog specifico. Per attivare la ricerca nella direzione opposta e trovare tutti i post di blog che condividono un tag specifico, è possibile creare un altro set che contiene il post di blog del riferimento all'ID di tag nella chiave:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Queste strutture consentono di eseguire numerose query comuni in modo efficiente. Ad esempio, è possibile trovare e visualizzare tutti i tag per i post di blog 1 simile al seguente:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

È possibile trovare tutti i tag comuni al post di blog 1 e 2 eseguendo un'operazione di intersezione di set, come indicato di seguito:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

Ed è possibile trovare tutti i post di blog che contengono un tag specifico:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### Trovare elementi usati di recente

Un problema comune richiesto da numerose applicazioni consiste nel trovare più elementi usati di recente. Per un sito blog, ad esempio, è possibile visualizzare informazioni sui post di blog letti più di recente. È possibile implementare questa funzionalità usando un elenco di Redis. Un elenco di Redis contiene più elementi che condividono la stessa chiave, ma l'elenco funge da coda doppia. È possibile inserire gli elementi a delle estremità dell'elenco usando i comandi LPUSH (push sinistra) e RPUSH (push destra). È possibile recuperare gli elementi da delle estremità dell'elenco usando i comandi LPOP e RPOP. È inoltre possibile restituire un set di elementi usando i comandi LRANGE e RRANGE. Nei frammenti di codice riportati di seguito viene illustrato come è possibile eseguire queste operazioni tramite la libreria StackExchange. Questo codice usa il tipo di BlogPost degli esempi precedenti. Mentre un post di blog viene letto da un utente, il titolo del post di blog viene inserito in un elenco associato alla chiave "blog:recent_posts" nella cache Redis usando il metodo IDatabase.ListLeftPushAsync:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

Durante la lettura di ulteriori post di blog, i titoli vengono inseriti nello stesso elenco. L'elenco è ordinato dalla sequenza in cui sono stati aggiunti; i post di blog letti più di recente sono posizionati verso l'estremità sinistra dell'elenco (se il post di blog stesso viene letto più di una volta, verranno disposte più voci nell'elenco). È possibile visualizzare i titoli dei post letti più di recente usando il metodo IDatabase.ListRange. Questo metodo accetta la chiave che contiene l'elenco, un punto di partenza e un punto finale. Il codice seguente recupera i titoli dei post di blog 10 (voci da 0 a 9) alla fine dell'elenco più a sinistra:

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Si noti che ListRangeAsync non rimuove gli elementi dall'elenco. A tale scopo è possibile usare i metodi IDatabase.ListLeftPopAsync e IDatabase.ListRightPopAsync.

Per impedire che l'elenco cresca indefinitamente, è possibile selezionare periodicamente elementi Trim nell'elenco. Nel frammento di codice riportato di seguito, vengono rimossi tutti, eccetto i 5 elementi più a sinistra nell'elenco:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### Implementazione di una scheda riempimento

Per impostazione predefinita, gli elementi di un set non vengono mantenuti in un ordine specifico. È possibile creare un set ordinato usando il comando ZADD (il metodo IDatabase.SortedSetAdd nella libreria StackExchange). Gli elementi vengono ordinati usando un valore numerico denominato un punteggio fornito come parametro al comando. Nel frammento di codice seguente viene aggiunto il titolo del post del blog per un elenco ordinato. Nell'esempio, ogni post di blog dispone di un campo di punteggio che contiene la valutazione del post di blog.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

È possibile recuperare i titoli dei post di blog e i punteggi in ordine crescente usando il metodo IDatabase.SortedSetRangeByRankWithScores:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE]La libreria StackExchange fornisce inoltre il metodo IDatabase.SortedSetRangeByRankAsync che restituisce i dati in ordine di punteggio, ma non restituisce i punteggi.

È inoltre possibile recuperare gli elementi in ordine decrescente di punteggi e limitare il numero di elementi restituiti fornendo parametri aggiuntivi per il metodo IDatabase.SortedSetRangeByRankWithScoresAsync. L'esempio seguente consente di visualizzare i titoli e i punteggi dei primi 10 post di blog pertinenti:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

Nell'esempio successivo viene usato il metodo IDatabase.SortedSetRangeByScoreWithScoresAsync che è possibile usare per limitare gli elementi restituiti a quelle che rientrano in un determinato punteggio intervallo:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### Uso dei canali di messaggistica

Oltre che funge da cache di dati, un server Redis fornisce funzionalità di messaggistica attraverso un meccanismo di pubblicazione/sottoscrizione ad alte prestazioni. Le applicazioni client possono sottoscrivere un canale, mentre altre applicazioni o servizi possono pubblicare messaggi nel canale. Applicazioni in sottoscrizione riceveranno i messaggi e ne consentiranno l'elaborazione.

Per iscriversi al canale, Redis fornisce il comando SUBSCRIBE. Questo comando è previsto il nome di uno o più canali su cui l'applicazione accetta i messaggi. La libreria StackExchange include l'interfaccia ISubscription che consente a un'applicazione .NET Framework effettuare la sottoscrizione e pubblicare ai canali. Creare un oggetto ISubscription usando il metodo GetSubscriber della connessione al server di Redis e quindi attendere i messaggi su un canale usando il metodo SubscribeAsync di questo oggetto. Nell'esempio di codice seguente viene illustrato come sottoscrivere un canale denominato "messages:blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Il primo parametro per il metodo Subscribe è il nome del canale. Questo nome segue le stesse convenzioni usate dalle chiavi nella cache e può contenere dati binari, sebbene sia consigliabile usare stringhe relativamente breve e significativi per garantire la gestibilità e prestazioni ottimali. Si noti inoltre che lo spazio dei nomi usato dai canali è separato da quello usato da chiavi, in modo da poter avere canali e chiavi con lo stesso nome, anche se ciò può rendere più difficile gestire il codice dell'applicazione.

Il secondo parametro è un delegato Action. Questo delegato viene eseguito in modo asincrono quando viene visualizzato un nuovo messaggio sul canale. Con questo esempio viene semplicemente visualizzato il messaggio sulla console (il messaggio conterrà il titolo di un post di blog).

Per pubblicare su un canale, un'applicazione può usare il comando PUBLISH di Redis. La libreria StackExchange fornisce il metodo IServer.PublishAsync per eseguire questa operazione. Nel frammento di codice seguente viene illustrato come pubblicare un messaggio per il canale "messages:blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Esistono diversi punti, che è necessario comprendere sul meccanismo di pubblicazione/sottoscrizione:

- Più sottoscrittori possono sottoscrivere lo stesso canale e tutti riceveranno i messaggi pubblicati sul canale.
- I sottoscrittori ricevono solo i messaggi che sono stati pubblicati dopo che hanno effettuato la sottoscrizione. I canali non vengono memorizzati nel buffer e dopo aver pubblicato un messaggio l'infrastruttura di Redis inserisce il messaggio in ogni server di sottoscrizione e successivamente lo rimuove.
- Per impostazione predefinita, i messaggi vengono ricevuti dai sottoscrittori nell'ordine in cui vengono inviati. In un sistema molto attivo con un numero elevato di messaggi e molte sottoscrittori e server di pubblicazione, recapito messaggi garantito sequenza può rallentare le prestazioni del sistema. Se ogni messaggio è indipendente e l'ordine è irrilevante, è possibile abilitare l'elaborazione simultanea dal sistema Redis che può contribuire a migliorare la velocità di risposta. È possibile ottenere questo in un client StackExchange impostando PreserveAsyncOrder della connessione usata dal server di sottoscrizione su false: ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## Modelli correlati e informazioni aggiuntive

Il modello seguente può essere importante per il proprio scenario anche quando si implementa la memorizzazione nella cache nelle applicazioni:

- [Modello cache-Aside](http://msdn.microsoft.com/library/dn589799.aspx): questo modello descrive come caricare i dati su richiesta in una cache da un archivio dati. Questo modello consente inoltre di mantenere la coerenza tra i dati memorizzati nella cache e i dati nell'archivio dati originale.
- Il [modello di partizionamento orizzontale](http://msdn.microsoft.com/library/dn589797.aspx) fornisce informazioni sull'implementazione del partizionamento orizzontale per migliorare la scalabilità per l'archiviazione e l'accesso a grandi volumi di dati.

## Ulteriori informazioni

- Pagina [Classe MemoryCache](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) sul sito Web di Microsoft.
- Pagina [Microsoft Azure Cache](http://msdn.microsoft.com/library/windowsazure/gg278356.aspx) sul sito Web di Microsoft.
- Pagina [Qual è l'offerta di Cache di Azure più adatta alle mie esigenze?](http://msdn.microsoft.com/library/azure/dn766201.aspx) sul sito Web di Microsoft.
- Pagina [Modello di configurazione](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) sul sito Web di Microsoft.
- Pagina [Modello asincrono basato su attività](http://msdn.microsoft.com/library/hh873175.aspx) sul sito Web di Microsoft.
- Pagina [Pipeline e Multiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) nell'archivio GitHub Stackexchange.
- Pagina [Persistenza Redis](http://redis.io/topics/persistence) sul sito Web di Redis.
- Pagina [Replica](http://redis.io/topics/replication) sul sito Web di Redis.
- Pagina [Esercitazione del cluster Redis](http://redis.io/topics/cluster-tutorial) sul sito Web di Redis.
- Pagina [partizionamento: come suddividere i dati tra più istanze di Redis](http://redis.io/topics/partitioning) pagina sul sito Web di Redis.
- Pagina [Uso di Redis come cache LRU](http://redis.io/topics/lru-cache) sul sito Web di Redis.
- Pagina [Transazioni](http://redis.io/topics/transactions) sul sito Web di Redis.
- Pagina [Sicurezza Redis](http://redis.io/topics/security) sul sito Web di Redis.
- Pagina [Introduzione alle Cache Redis di Azure](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sul blog di Azure.
- Pagina [Esecuzione Redis in una macchina virtuale Linux CentOS](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) in Azure sul sito Web di Microsoft.
- Pagina [Provider di stato della sessione ASP.NET per Cache Redis di Azure](http://msdn.microsoft.com/library/azure/dn690522.aspx) sul sito Web di Microsoft.
- Pagina [Provider di cache di output ASP.NET per Cache Redis di Azure](http://msdn.microsoft.com/library/azure/dn798898.aspx) sul sito Web di Microsoft.
- Pagina [Sviluppo per la Cache Redis di Azure](http://msdn.microsoft.com/library/azure/dn690520.aspx) sul sito di Azure.
- Pagina [An Introduction to Redis data types and abstractions](http://redis.io/topics/data-types-intro) sul sito Web di Redis.
- Pagina [Uso di base](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) sul sito Web Stackexchange.Redis.
- Pagina [Transazioni in Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) nell'archivio Stackexchange.Redis.
- [Guida partizionamento dati](http://msdn.microsoft.com/library/dn589795.aspx) sul sito Web di Microsoft.

<!---HONumber=July15_HO3-->