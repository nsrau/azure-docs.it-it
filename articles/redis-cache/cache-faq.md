<properties 
	pageTitle="Domande frequenti su Cache Redis di Azure | Microsoft Azure" 
	description="Risposte alle domande più comuni, modelli e procedure consigliate per la Cache Redis di Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/20/2016" 
	ms.author="sdanie"/>

# Domande frequenti sulla Cache Redis di Azure

Risposte alle domande più comuni, modelli e procedure consigliate per la Cache Redis di Azure.


## Cosa fare se non è disponibile una risposta alla domanda?

Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela affinché possiamo fornire il nostro aiuto.

-	È possibile pubblicare una domanda nel [thread Disqus](#comments) alla fine del presente documento di FAQ e interagire con il team di Cache di Azure e altri membri della community in merito a questo articolo.
-	È possibile pubblicare una domanda sul [Forum MSDN di Cache di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e interagire con il team di Cache di Azure e altri membri della community.
-	È possibile inviare un messaggio di posta elettronica all'indirizzo relativo al [feedback esterno su Cache di Azure](mailto:azurecache@microsoft.com).

## Domande frequenti sulla pianificazione

-	[Quali offerte e dimensioni della Cache Redis è consigliabile usare?](#what-redis-cache-offering-and-size-should-i-use)
-	[Prestazioni di Cache Redis di Azure](#azure-redis-cache-performance)
-	[In quale area è consigliabile posizionare la cache?](#in-what-region-should-i-locate-my-cache)
-	[Quali sono i costi addebitati per Cache Redis di Azure?](#how-am-i-billed-for-azure-redis-cache)

<a name="cache-size"></a>
### Quali offerte e dimensioni della Cache Redis è consigliabile usare?
Ogni offerta della Cache Redis di Azure fornisce livelli diversi per le opzioni relative a **dimensioni**, **larghezza di banda**, **disponibilità elevata** e **Contratto di servizio**.

Di seguito alcune considerazioni per la scelta di un'offerta cache.

-	**Memoria**: i livelli Basic e Standard offrono 250 MB – 53 GB. Il livello Premium offre fino a 530 GB con ulteriore disponibilità [su richiesta](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).
-	**Prestazioni della rete**: in presenza di un carico di lavoro che richiede una velocità effettiva elevata, il livello Premium offre maggiore larghezza di banda rispetto a Standard o Basic. Anche all'interno di ogni livello, a dimensioni più grandi della cache corrisponde maggiore larghezza di banda, a causa della macchina virtuale sottostante che ospita la cache. Per altre informazioni, vedere la [tabella seguente](#cache-performance).
-	**Velocità effettiva**: il livello Premium offre la massima velocità effettiva disponibile. Se il server o il client della cache raggiunge i limiti della larghezza di banda, si riceveranno timeout sul lato client. Per altre informazioni, vedere la tabella seguente.
-	**Alta disponibilità/Contratto di servizio**: Cache Redis di Azure garantisce la disponibilità di una cache di livello Standard o Premium per almeno il 99,9% del tempo. Per altre informazioni sul contratto di servizio, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Il contratto di servizio copre solo la connettività agli endpoint della cache. Il contratto di servizio non offre copertura per la protezione dalla perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati.
-	**Persistenza dei dati Redis**: il livello Premium consente la persistenza dei dati della cache in un account di archiviazione di Azure. In una cache Basic/Standard tutti i dati vengono archiviati solo in memoria. In caso di problemi per l'infrastruttura sottostante esiste il rischio di una potenziale perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati. Cache Redis di Azure offre le opzioni RDB e AOF (presto disponibile) per la persistenza di Redis. Per altre informazioni, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).
-	**Cluster Redis**: se si desidera creare cache di dimensioni superiori a 53 GB o si desidera partizionare i dati tra più nodi Redis, è possibile usare la funzionalità di clustering Redis, disponibile nel livello Premium. Ogni nodo è costituito da una coppia di cache primaria/di replica per la disponibilità elevata. Per altre informazioni, vedere [Come configurare il clustering per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).
-	**Protezione avanzata e isolamento della rete**: la distribuzione di Rete virtuale di Azure (VNET) offre maggiore sicurezza e isolamento per Cache Redis di Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso. Per altre informazioni, vedere [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).
-	**Configurare Redis**: nei livelli Standard e Premium, è possibile configurare Redis per le notifiche di Keyspace.
-	**Numero massimo di connessioni client**: il livello Premium offre il numero massimo di client che possono connettersi a Redis, con un numero maggiore di connessioni per cache di dimensioni più grandi. [Per altri dettagli, vedere la pagina relativa a prezzi](https://azure.microsoft.com/pricing/details/cache/).
-	**Core dedicato per il server Redis**: nel livello Premium per tutte le dimensioni della cache è disponibile un core dedicato per Redis. Nei livelli Basic/Standard, per le dimensioni C1 e superiori è disponibile un core dedicato per il server Redis.
-	Poiché **Redis è a thread singolo**, la disponibilità di più di due core non offre vantaggi ulteriori rispetto alla disponibilità di soli due core, ma in genere dimensioni di macchina virtuale maggiori hanno una larghezza di banda più elevata rispetto alle dimensioni minori. Se il server o il client della cache raggiunge i limiti della larghezza di banda, si riceveranno timeout sul lato client.
-	**Miglioramenti delle prestazioni**: le cache nel livello Premium sono distribuite su hardware con processori più veloci e che offrono migliori prestazioni rispetto al livello Basic o Standard. Le cache di livello Premium offrono una velocità effettiva più elevata e minori latenze.

<a name="cache-performance"></a>
### Prestazioni di Cache Redis di Azure

La tabella seguente mostra i valori massimi per la larghezza di banda osservati durante il test di diverse dimensioni di cache Standard e Premium usando `redis-benchmark.exe` da una macchina virtuale IaaS nell'endpoint della Cache Redis di Azure. Si noti che questi valori non sono garantiti e che non è disponibile alcun contratto di servizio per questi numeri, che dovrebbero essere tuttavia tipici. È consigliabile testare l'applicazione per determinare le dimensioni di cache ottimali per l'applicazione specifica.

Da questa tabella è possibile trarre le seguenti conclusioni.

-	La velocità effettiva per la Cache della stessa dimensione è superiore nel Premium rispetto al livello Standard. Ad esempio. Per una Cache di 6 GB, la velocità effettiva di P1 è 140K RPS rispetto a 49 K per C3.
-	Con il clustering di Redis, la velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni (nodi) nel cluster. Ad esempio, se si crea un cluster P4 di 10 partizioni, la velocità effettiva disponibile sarà 250 KB * 10 = 2,5 milioni di RPS.
-	La velocità effettiva per dimensioni maggiori di chiave è superiore nel Premium rispetto al livello Standard.

| Pricing tier | Dimensione | Core CPU | Larghezza di banda disponibile | Dimensioni della chiave 1 KB |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Dimensioni della cache livello Standard** | | | **Megabit al secondo (Mb/s) / Megabyte al secondo (MB/s)** | **Richieste al secondo (RPS)** |
| C0 | 250 MB | Condiviso | 5 / 0,625 | 600 |
| C1 | 1 GB | 1 | 100 / 12,5 | 12200 |
| C2 | 2,5 GB | 2 | 200 / 25 | 24000 |
| C3 | 6 GB | 4 | 400 / 50 | 49000 |
| C4 | 13 GB | 2 | 500 / 62,5 | 61000 |
| C5 | 26 GB | 4 | 1000 / 125 | 115000 |
| C6 | 53 GB | 8 | 2000 / 250 | 150000 |
| **Dimensioni della cache livello Premium** | | **Core CPU per partizione** | | **Richieste al secondo (RPS) per partizione** |
| P1 | 6 GB | 2 | 1000 / 125 | 140000 |
| P2 | 13 GB | 4 | 2000 / 250 | 220000 |
| P3 | 26 GB | 4 | 2000 / 250 | 220000 |
| P4 | 53 GB | 8 | 4000 / 500 | 250000 |


Per istruzioni sul download degli strumenti Redis quali `redis-benchmark.exe`, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands).

<a name="cache-region"></a>
### In quale area è consigliabile posizionare la cache?

Per ottenere le prestazioni migliori e la latenza più bassa, posizionare la Cache Redis di Azure nella stessa area in cui si trova l'applicazione client della cache.

<a name="cache-billing"></a>
### Quali sono i costi addebitati per Cache Redis di Azure?

I prezzi di Cache Redis di Azure sono indicati [in questa pagina](https://azure.microsoft.com/pricing/details/cache/) come tariffa oraria. Le cache vengono fatturate a minuto dal momento in cui la cache viene creata fino a quando viene eliminata. Non è disponibile alcuna opzione per interrompere o sospendere la fatturazione di una cache.

## Domande frequenti sullo sviluppo

-	[Qual è la funzione delle opzioni di configurazione StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
-	[Quali client della cache Redis è possibile usare?](#what-redis-cache-clients-can-i-use)
-	[Esiste un emulatore locale per la cache Redis di Azure?](#is-there-a-local-emulator-for-azure-redis-cache)
-	[Come si eseguono i comandi Redis?](#how-can-i-run-redis-commands)
-	[Perché la Cache Redis di Azure non offre un riferimento alla libreria di classi MSDN, analogamente ad altri servizi di Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)

<a name="cache-configuration"></a>
### Qual è la funzione delle opzioni di configurazione StackExchange.Redis?

StackExchange.Redis include diverse opzioni. Questa sezione illustra alcune impostazioni comuni. Per informazioni più dettagliate sulle opzioni StackExchange.Redis, vedere la pagina relativa alla [configurazione di StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

Opzioni configurazione|Descrizione|Raccomandazione
---|---|---
AbortOnConnectFail|Se impostata su true, la connessione non verrà ristabilita dopo un errore di rete.|Impostare su false, per permettere a StackExchange.Redis di riconnettersi automaticamente.
ConnectRetry|Numero di nuovi tentativi di connessione durante la connessione iniziale.| Per istruzioni, vedere di seguito. |
ConnectTimeout|Timeout in millisecondi per le operazioni di connessione.| Per istruzioni, vedere di seguito. |

Nella maggior parte dei casi sono sufficienti i valori predefiniti del client. È possibile ottimizzare le opzioni in base al carico di lavoro specifico.

-	**Tentativi**
	-	Per ConnectRetry e ConnectTimeout è in genere consigliabile ottenere rapidamente un errore e riprovare, in base al carico di lavoro specifico e al tempo mediamente necessario per il rilascio di un comando Redis da parte del client e la ricezione di una risposta.
	-	Permettere la connessione automatica di StackExchange.Redis invece di controllare lo stato di connessione ed eseguire manualmente la riconnessione. **Evitare di usare la proprietà ConnectionMultiplexer.IsConnected**.
	-	In alcuni casi è possibile che si verifichi un problema che genera nuovi tentativi a catena, senza recupero. In questo caso è consigliabile prendere in considerazione l'uso di un algoritmo di nuovi tentativi con backoff esponenziale, come illustrato in [Materiale sussidiario generale per i nuovi tentativi](best-practices-retry-general.md) pubblicato dal gruppo Microsoft Patterns & Practices.
-	**Valori di timeout**
	-	Esaminare il carico di lavoro e impostare valori adeguati. Se si archiviano valori di grandi dimensioni, impostare il timeout su un valore più elevato.
	-	Impostare `AbortOnConnectFail` su false per permettere a StackExchange.Redis di riconnettersi.
	-	Usare una singola istanza ConnectionMultiplexer per l'applicazione. È possibile usare un valore LazyConnection per creare una singola istanza restituita da una proprietà Connection, come illustrato in [Connettersi alla cache mediante la classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
	-	Impostare la proprietà `ConnectionMultiplexer.ClientName` su un nome univoco dell'istanza dell'app per finalità di diagnostica.
	-	Usare più istanze di `ConnectionMultiplexer` per carichi di lavoro personalizzati.
	-	È possibile seguire questo modello se l'applicazione include carichi variabili. Ad esempio:
	-	È possibile avere un multiplexer per la gestione di chiavi di grandi dimensioni.
	-	È possibile avere un multiplexer per la gestione di chiavi di piccole dimensioni.
	-	È possibile impostare valori diversi per i timeout di connessione e la logica di ripetizione dei tentativi per ogni ConnectionMultiplexer usato.
	-	Impostare la proprietà `ClientName` in ogni multiplexer per semplificare la diagnostica.
	-	Si otterrà una latenza semplificata per ogni `ConnectionMultiplexer`.

### Quali client della cache Redis è possibile usare?

Uno dei principali vantaggi di Redis è la presenza di numerosi client che supportano linguaggi di sviluppo diversi. Per un elenco aggiornato dei client, vedere l'articolo relativo ai [client Redis](http://redis.io/clients). Per esercitazioni su diversi linguaggi e client, vedere [Come usare Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e fare clic sul linguaggio desiderato dallo strumento di selezione del linguaggio all'inizio dell'articolo.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### Esiste un emulatore locale per la cache Redis di Azure?

Non esiste un emulatore locale per Cache Redis di Azure, ma è possibile eseguire la versione MSOpenTech di redis-server.exe dagli [strumenti della riga di comando Redis](https://github.com/MSOpenTech/redis/releases/) sul computer locale e connettersi per ottenere un'esperienza simile a un emulatore cache locale, come illustrato nell'esempio seguente.


	private static Lazy<ConnectionMultiplexer>
  		lazyConnection = new Lazy<ConnectionMultiplexer>
	    (() =>
	    {
		    // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
		    return ConnectionMultiplexer.Connect("127.0.0.1:6379");
	    });
	
	    public static ConnectionMultiplexer Connection
	    {
		    get
		    {
			    return lazyConnection.Value;
		    }
	    }


Facoltativamente, è possibile configurare un file [redis.conf](http://redis.io/topics/config) in modo che si avvicini maggiormente alle [impostazioni della cache predefinite](cache-configure.md#default-redis-server-configuration) per Cache Redis di Azure online.

<a name="cache-commands"></a>
### Come si eseguono i comandi Redis?

È possibile usare i comandi elencati nella pagina sui [comandi di Redis](http://redis.io/commands#) ad eccezione dei comandi elencati nella pagina sui [comandi di Redis non supportati nella Cache Redis di Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Per eseguire i comandi di Redis sono disponibili diverse opzioni.

-	Se si ha una cache Standard o Premium, è possibile eseguire i comandi di Redis usando la [Console Redis](cache-configure.md#redis-console). Ciò consente di eseguire in modo sicuro i comandi di Redis nel portale di Azure.
-	È anche possibile usare gli strumenti da riga di comando di Redis. A questo scopo, eseguire la procedura seguente.
-	Scaricare gli [strumenti da riga di comando Redis](https://github.com/MSOpenTech/redis/releases/).
-	Connettersi alla cache usando `redis-cli.exe`. Passare l'endpoint della cache mediante l'opzione -h e la chiave mediante -a, come illustrato nell'esempio seguente.
-	`redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  -	Si noti che gli strumenti da riga di comando Redis non funzionano con la porta SSL, ma è possibile usare un'utilità come `stunnel` per connettere in modo sicuro gli strumenti alla porta SSL seguendo le istruzioni disponibili nel post di blog [Annuncio del provider di stato della sessione ASP.NET per la versione di anteprima di Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

<a name="cache-reference"></a>
### Perché la Cache Redis di Azure non offre un riferimento alla libreria di classi MSDN, analogamente ad altri servizi di Azure?

Cache Redis di Microsoft Azure si basa sulla nota Cache Redis open source, a cui è possibile accedere da un'ampia gamma di [client Redis](http://redis.io/clients) disponibili per numerosi linguaggi di programmazione. Ogni client ha un'API specifica che effettua chiamate all'istanza della Cache Redis usando i [comandi Redis](http://redis.io/commands).

Poiché ogni client è diverso, non è disponibile alcun riferimento di classe centralizzato su MSDN. Ogni client offre invece documentazione di riferimento specifica. Oltre alla documentazione di riferimento, sono disponibili alcune esercitazioni che illustrano come iniziare a usare la Cache Redis di Azure Redis con linguaggi e client di cache diversi. Per accedere a queste esercitazioni, vedere [Come usare Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e fare clic sul linguaggio desiderato dallo strumento di selezione del linguaggio all'inizio dell'articolo.


## Domande frequenti sulla sicurezza

-	[Quando è consigliabile abilitare la porta non SSL per la connessione a Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

<a name="cache-ssl"></a>
### Quando è consigliabile abilitare la porta non SSL per la connessione a Redis?

Il server Redis non offre il supporto predefinito per SSL, ma tale supporto è disponibile nella Cache Redis di Azure. Se ci si connette alla Cache Redis di Azure e il client supporta SSL, ad esempio StackExchange.Redis, è consigliabile usare SSL.

Si tenga presente che per le nuove istanze della Cache Redis di Azure la porta non SSL è disabilitata per impostazione predefinita. Se il client non supporta SSL, sarà necessario abilitare la porta non SSL seguendo le istruzioni disponibili nella sezione relativa alle [porte di accesso](cache-configure.md#access-ports) dell'articolo [Configurare una cache in Cache Redis di Azure](cache-configure.md).

Gli strumenti Redis quali `redis-cli`non funzionano con la porta SSL, ma è possibile usare un'utilità come `stunnel` per connettere in modo sicuro gli strumenti alla porta SSL seguendo le istruzioni disponibili nel post di blog [Annuncio del provider di stato della sessione ASP.NET per la versione di anteprima di Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Per istruzioni sul download degli strumenti Redis, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands).

## Domande frequenti sulla produzione

-	[Che cosa occorre prendere in considerazione quando si usano i comandi Redis comuni?](#what-are-some-of-the-considerations-whit-ITing-common-redis-commands)
-	[In che modo è possibile valutare e testare le prestazioni della cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-	[Informazioni importanti sulla crescita del pool di thread](#important-details-about-threadpool-growth)
-	[Abilitare il server Garbage Collection in modo da ottenere una velocità effettiva maggiore sul client quando si usa StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-whit-ITing-stackexchangeredis)


<a name="cache-redis-commands"></a>
### Che cosa occorre prendere in considerazione quando si usano i comandi Redis comuni?

-	È consigliabile non eseguire determinati comandi Redis che richiedono molto tempo per il completamento se non si comprende l'impatto di tali comandi.
-	Ad esempio, non eseguire il comando [KEYS](http://redis.io/commands/keys) in produzione, poiché la restituzione di un valore potrebbe richiedere molto tempo, in base al numero delle chiavi. Redis è un server a thread singolo ed elabora un comando alla volta. Eventuali comandi emessi dopo KEYS verranno elaborati solo dopo l'elaborazione del comando KEYS.
-	È consigliabile usare coppie chiave-valore di piccole o di grandi dimensioni? In genere, dipende dallo scenario. Se lo scenario richiede chiavi di dimensioni maggiori, sarà possibile modificare il valore di ConnectionTimeout e dei nuovi tentativi e regolare la logica di ripetizione dei tentativi. Dal punto di vista del server Redis, è stato osservato che valori più piccoli permettono prestazioni migliori.
-	Ciò non significa che non sia possibile archiviare valori di dimensioni maggiori in Redis. Occorre tenere presenti le considerazioni seguenti. Le latenze saranno più elevate. Se sono presenti un set di dati più grande e un set di dati più piccolo, sarà possibile usare più istanze di ConnectionMultiplexer, ognuna delle quali configurata con un diverso set di valori di timeout e di nuovi tentativi, come illustrato nella sezione [Qual è la funzione delle opzioni di configurazione StackExchange.Redis?](#cache-configuration) precedente.



<a name="cache-benchmarking"></a>
### In che modo è possibile valutare e testare le prestazioni della cache?

-	[Abilitare la diagnostica della cache](cache-how-to-monitor.md#enable-cache-diagnostics) per poter [monitorare](cache-how-to-monitor.md) l'integrità della cache. È possibile visualizzare le metriche nel portale di Azure, nonché [scaricarle e analizzarle](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando gli strumenti preferiti.
-	È possibile usare redis-benchmark.exe per eseguire test di carico del server Redis.
-	Assicurarsi che il client del test di carico e la Cache Redis si trovino nella stessa area.
-	Usare redis-cli.exe e monitorare la cache usando il comando INFO.
-	Se il carico provoca la frammentazione elevata della memoria, è consigliabile passare a dimensioni di cache maggiori.
-	Per istruzioni sul download degli strumenti Redis, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands).


<a name="threadpool"></a>
### Informazioni importanti sulla crescita del pool di thread

L'oggetto ThreadPool CLR ha due tipi di thread, i thread di lavoro (WORKER) e i thread IOCP (porta di completamento I/O).

-	I thread di lavoro vengono usati per operazioni come l'elaborazione dei metodi `Task.Run(…)` o `ThreadPool.QueueUserWorkItem(…)`. Questi thread vengono usati anche da vari componenti CLR quando il lavoro deve essere eseguito in un thread in background.
-	I thread IOCP vengono usati quando si verificano I/O asincroni, ad esempio, durante la lettura dalla rete.

Il pool di thread fornisce nuovi thread di lavoro o thread di completamento I/O su richiesta, senza alcuna limitazione, fino a quando non viene raggiunta l'impostazione minima per ogni tipo di thread. Per impostazione predefinita, il numero minimo di thread corrisponde al numero di processori in un sistema.

Quando il numero di thread esistenti, o occupati, raggiunge il minimo, l'oggetto ThreadPool limita la frequenza di inserimento dei nuovi thread a uno ogni 500 millisecondi. Ciò significa che se il sistema riceve un picco di lavoro che necessita di un thread IOCP, il lavoro viene elaborato molto rapidamente. Tuttavia, se il picco di lavoro è superiore all'impostazione minima configurata, ci sarà un ritardo nell'elaborazione di una parte del lavoro mentre l'oggetto ThreadPool attende che si verifichi una delle condizioni seguenti.

1. Un thread esistente diventa disponibile per elaborare il lavoro.
1. Nessun thread esistente diventa disponibile per 500 ms e viene creato un nuovo thread.

In sostanza, ciò significa che quando il numero dei thread occupati è maggiore del numero minimo di thread, è probabile che si verifichi un ritardo di 500 ms prima che il traffico di rete venga elaborato dall'applicazione. È anche importante notare che quando un thread esistente rimane inattivo per più di 15 secondi viene pulito e il ciclo di crescita e riduzione si ripete.

Se si osserva un messaggio di errore di esempio da StackExchange.Redis, build 1.0.450 o versione successiva, si noterà che ora le statistiche dell'oggetto ThreadPool vengono stampate. Di seguito sono riportati i dettagli relativi a IOCP e WORKER.

	System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
	queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
	IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
	WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Nell'esempio precedente sono presenti 6 thread IOCP occupati e il sistema è configurato per consentire un minimo di 4 thread. In questo caso si verificheranno probabilmente due ritardi di 500 ms, perché 6 > 4.

Si noti che StackExchange.Redis può raggiungere il timeout se la crescita dei thread IOCP o WORKER viene limitata.

### Raccomandazione

È consigliabile impostare il numero minimo dei thread IOCP e WORKER su un valore maggiore di quello predefinito. Non è possibile fornire indicazioni universali su questo valore, perché il valore corretto per un'applicazione potrebbe essere troppo alto o troppo basso per un'altra. Questa impostazione può anche influire sulle prestazioni di altre parti di applicazioni complesse. È quindi necessario che venga regolata in base alle esigenze specifiche del cliente. Un buon punto di partenza è 200 o 300, da verificare e modificare a seconda delle necessità.

Come configurare questa impostazione:

-	In ASP.NET usare l'[impostazione di configurazione "minIoThreads"][] nell'elemento di configurazione `<processModel>` in web.config. Se l'esecuzione avviene all'interno di Siti Web di Azure, questa impostazione non viene esposta attraverso le opzioni di configurazione. Dovrebbe tuttavia essere possibile impostarla a livello di codice, come indicato di seguito, dal metodo Application\_Start in global.asax.cs.

> **Nota importante:** il valore specificato in questo elemento di configurazione è un'impostazione *per core*. Ad esempio, se si dispone di un computer a 4 core e si desidera che l'impostazione minIoThreads raggiunga 200 in fase di esecuzione, occorre usare `<processModel minIoThreads="50"/>`.

-	Al di fuori di ASP.NET, usare l'API [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx).

<a name="server-gc"></a>
### Abilitare il server Garbage Collection in modo da ottenere una velocità effettiva maggiore sul client quando si usa StackExchange.Redis

L'abilitazione del server Garbage Collection consente di ottimizzare il client e fornire velocità effettiva e prestazioni migliori quando si usa StackExchange.Redis. Per altre informazioni sul server Garbage Collection e sulla relativa abilitazione, vedere gli articoli seguenti.

-	[Per abilitare il server Garbage Collection](https://msdn.microsoft.com/library/ms229357.aspx)
-	[Nozioni fondamentali di Garbage Collection](https://msdn.microsoft.com/library/ee787088.aspx)
-	[Garbage Collection e prestazioni](https://msdn.microsoft.com/library/ee851764.aspx)





## Domande sul monitoraggio e sulla risoluzione dei problemi

Le domande frequenti riportate in questa sezione riguardano il monitoraggio e la risoluzione dei problemi più comuni. Per altre informazioni sul monitoraggio e la risoluzione dei problemi relativi alle istanze di Cache Redis di Azure, vedere [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md) e [Come risolvere i problemi di Cache Redis di Azure](cache-how-to-troubleshoot.md).

-	[In che modo si esegue il monitoraggio dell'integrità e delle impostazioni della cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-	[Le impostazioni dell'account di archiviazione relativo alla diagnostica della cache sono cambiate. Come mai?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-	[Perché la diagnostica è abilitata per alcune nuove cache ma non per altre?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-	[Perché vengono visualizzati timeout?](#why-am-i-seeing-timeouts)
-	[Perché il client è stato disconnesso dalla cache?](#why-was-my-client-disconnected-from-the-cache)

<a name="cache-monitor"></a>
### In che modo si esegue il monitoraggio dell'integrità e delle impostazioni della cache?

Le istanze della Cache Redis di Microsoft Azure possono essere monitorate nel [portale di Azure](https://portal.azure.com). È possibile visualizzare le metriche, aggiungere i grafici delle metriche alla Schermata iniziale, personalizzare l'intervallo di data e ora per il monitoraggio dei grafici, aggiungere e rimuovere metriche dai grafici e impostare avvisi per le situazioni in cui vengono soddisfatte determinate condizioni. Per altre informazioni, vedere [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md).

La sezione **Supporto e risoluzione dei problemi** del pannello **Impostazioni** di Cache Redis contiene anche numerosi strumenti per il monitoraggio e la risoluzione dei problemi delle cache.

-	**Risoluzione dei problemi** fornisce informazioni sui problemi comuni e sulle strategie per risolverli.
-	**Log di controllo** fornisce informazioni sulle azioni eseguite nella cache. È possibile inoltre utilizzare il filtro per espandere la visualizzazione in modo da includere altre risorse.
-	**Integrità risorsa** esamina la risorsa e indica se viene eseguita nel modo previsto. Per altre informazioni sul servizio Integrità risorse di Azure, vedere l'articolo [Panoramica su Integrità risorse di Azure](../resource-health/resource-health-overview.md).
-	**Nuova richiesta di supporto** fornisce opzioni per aprire una richiesta di supporto per la cache.

Questi strumenti permettono di monitorare l'integrità delle istanze della Cache Redis di Azure e semplificano la gestione delle applicazioni di memorizzazione nella cache. Per altre informazioni, vedere [Supporto e impostazioni di risoluzione dei problemi](cache-configure.md#support-amp-troubleshooting-settings).

### Le impostazioni dell'account di archiviazione relativo alla diagnostica della cache sono cambiate. Come mai?

Le cache nella stessa area e nella stessa sottoscrizione condividono le stesse impostazioni di archiviazione di diagnostica e, quando viene modificata la configurazione (abilitazione/disabilitazione della diagnostica o cambio dell'account di archiviazione), la modifica viene applicata a tutte le cache della sottoscrizione che si trovano in tale area. Se sono state modificate le impostazioni di diagnostica per la cache, verificare se sono state modificate le impostazioni di diagnostica delle altre cache nella stessa sottoscrizione e area. A tale scopo è possibile visualizzare i log di controllo per la cache per un evento `Write DiagnosticSettings`. Per altre informazioni sull'uso dei log di controllo, vedere [Visualizzare eventi e log di controllo](../azure-portal/insights-debugging-with-events.md) e [Operazioni di controllo con Resource Manager](../resource-group-audit.md). Per altre informazioni sul monitoraggio degli eventi di Cache Redis di Azure, vedere [Operazioni e avvisi](cache-how-to-monitor.md#operations-and-alerts).

### Perché la diagnostica è abilitata per alcune nuove cache ma non per altre?

Le cache nella stessa area e sottoscrizione condividono le stesse impostazioni di archiviazione per la diagnostica. Se si crea una nuova cache nella stessa area e sottoscrizione di un'altra cache che con la diagnostica abilitata, essa viene abilitata anche nella nuova cache tramite le stesse impostazioni.


<a name="cache-timeouts"></a>
### Perché vengono visualizzati timeout?

I timeout si verificano nel client usato per comunicare con Redis. Nella maggior parte dei casi, non si verifica il timeout del server Redis. Quando un comando viene inviato al server Redis, il comando viene accodato e il server Redis preleva il comando e lo esegue. È tuttavia possibile che durante questo processo si verifichi il timeout del client e in tale caso viene generata un'eccezione sul lato del chiamante. Per altre informazioni sulla risoluzione dei problemi di timeout, vedere [Risoluzione dei problemi lato client](cache-how-to-troubleshoot.md#client-side-troubleshooting) e [StackExchange.Redis timeout exceptions](Client side troubleshooting](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

'<-- Commento di localizzazione: collegamento interrotto: [Eccezioni timeout StackExchange.Redis](Risoluzione dei problemi lato client](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions). "(Risoluzione dei problemi lato client]" deve essere rimosso. -->'

<a name="cache-disconnect"></a>
### Perché il client è stato disconnesso dalla cache?

Di seguito sono riportati alcuni motivi comuni per la disconnessione di una cache.

-	Cause lato client
	-	L'applicazione client è stata ridistribuita.
	-	L'applicazione client ha eseguito un'operazione di ridimensionamento.
		-	Nel caso dei servizi cloud o delle app Web, ciò potrebbe essere dovuto alla scalabilità automatica.
	-	Il livello di rete sul lato client è stato modificato.
	-	Si sono verificati errori temporanei nel client o nei nodi di rete tra il client e il server.
	-	Sono stati raggiunti i limiti della soglia relativa alla larghezza di banda.
	-	Il completamento di operazioni associate alla CPU ha richiesto troppo tempo.
-	Cause lato server
	-	Nell'offerta Standard per la cache il servizio Cache Redis di Azure ha avviato un failover dal nodo primario al nodo secondario.
	-	Azure stava applicando patch all'istanza in cui è stata distribuita la cache.
		-	Ciò può riguardare aggiornamenti del server Redis o manutenzione generale delle VM.





## Domande frequenti sulle offerte di Cache precedenti

-	[Qual è l'offerta di Cache di Azure più adatta alle mie esigenze?](#which-azure-cache-offering-is-right-for-me)

### Qual è l'offerta di Cache di Azure più adatta alle mie esigenze?

>[AZURE.IMPORTANT]Sulla base dell'[annuncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) dello scorso anno, il Servizio cache gestita di Azure e il servizio Cache nel ruolo di Azure verranno ritirati il 30 novembre 2016. È consigliabile usare [Cache Redis di Azure](https://azure.microsoft.com/services/cache/). Per informazioni sulla migrazione, vedere [Eseguire la migrazione dal Servizio cache gestita alla Cache Redis di Azure](cache-migrate-to-redis.md).

### Cache Redis di Azure
Cache Redis di Azure è in genere disponibile per dimensioni fino a 53 GB e prevede un contratto di servizio con disponibilità del 99,9%. Il nuovo [livello Premium](cache-premium-tier-intro.md) offre dimensioni fino a 530 GB, oltre al supporto per clustering, reti virtuali e persistenza, con Contratti di servizio del 99,9%.

Cache Redis di Azure permette ai clienti di usare una cache Redis protetta, dedicata e gestita da Microsoft. Con questa offerta è possibile sfruttare i vantaggi delle numerose funzionalità avanzate e dell'ecosistema forniti da Redis, insieme all'hosting e al monitoraggio affidabili offerti da Microsoft.

A differenza delle cache tradizionali in grado di gestire solo coppie chiave-valore, Redis è nota per i tipi di dati ad alte prestazioni. Redis supporta inoltre l'esecuzione di operazioni atomiche su questi tipi, ad esempio l'aggiunta a una stringa; l'aumento del valore in un hash; il push in un elenco; il calcolo di intersezione, unione e differenza per un set; oppure il recupero del membro con priorità più elevata in un set ordinato. Altre funzionalità includono il supporto di transazioni, pubblicazione/sottoscrizione, script Lua, chiavi con un ciclo di vita limitato e impostazioni di configurazione per permettere a Redis un comportamento più simile a una cache tradizionale.

Un altro aspetto chiave del successo di Redis è l'ecosistema open source integro e dinamico che si è sviluppato attorno, come dimostrato dal variegato set di client Redis disponibili per più linguaggi. In questo modo può essere usato quasi da qualsiasi carico di lavoro che è possibile creare in Azure.

Per altre informazioni sulle operazioni preliminari con Cache Redis di Azure, vedere [Come usare Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e la [documentazione di Cache Redis di Azure](https://azure.microsoft.com/documentation/services/redis-cache/).

### Servizio cache gestita
[Servizio cache gestita è destinato al ritiro il 30 novembre 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### In-Role Cache
[Cache nel ruolo è destinato al ritiro il 30 novembre 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[impostazione di configurazione "minIoThreads"]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx

<!---HONumber=AcomDC_0720_2016-->