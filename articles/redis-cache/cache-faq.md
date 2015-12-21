<properties 
	pageTitle="Domande frequenti sulla Cache Redis di Azure" 
	description="Risposte alle domande più comuni, modelli e procedure consigliate per la Cache Redis di Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# Domande frequenti sulla Cache Redis di Azure

Risposte alle domande più comuni, modelli e procedure consigliate per la Cache Redis di Azure.

<a name="cache-size"></a>
## Quali offerte e dimensioni della Cache Redis è consigliabile usare?
Ogni offerta della Cache Redis di Azure fornisce livelli diversi per le opzioni relative a **dimensioni**, **larghezza di banda**, **disponibilità elevata** e **Contratto di servizio**.

Di seguito alcune considerazioni per la scelta di un'offerta cache.

-	**Memoria**: i livelli Basic e Standard offrono 250 MB – 53 GB. Il livello Premium offre fino a 530 GB con ulteriore disponibilità [su richiesta](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).
-	**Prestazioni della rete**: in presenza di un carico di lavoro che richiede una velocità effettiva elevata, il livello Premium offre maggiore larghezza di banda rispetto a Standard o Basic. Anche all'interno di ogni livello, a dimensioni più grandi della cache corrisponde maggiore larghezza di banda, a causa della macchina virtuale sottostante che ospita la cache. Per altre informazioni, vedere la tabella seguente.
-	**Velocità effettiva**: il livello Premium offre la massima velocità effettiva disponibile. Se il server o il client della cache raggiunge i limiti della larghezza di banda, si riceveranno timeout sul lato client. Per altre informazioni, vedere la tabella seguente.
-	**Alta disponibilità/Contratto di servizio**: Cache Redis di Azure garantisce la disponibilità di una cache di livello Standard o Premium per almeno il 99,9% del tempo. Per altre informazioni sul contratto di servizio, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Il contratto di servizio copre solo la connettività agli endpoint della cache. Il contratto di servizio non offre copertura per la protezione dalla perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati.
-	**Persistenza dei dati Redis**: il livello Premium consente la persistenza dei dati della cache in un account di archiviazione di Azure. In una cache Basic/Standard tutti i dati vengono archiviati solo in memoria. In caso di problemi per l'infrastruttura sottostante esiste il rischio di una potenziale perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati. Cache Redis di Azure offre le opzioni RDB e AOF (presto disponibile) per la persistenza di Redis. Per altre informazioni, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).
-	**Cluster Redis**: se si desidera creare cache di dimensioni superiori a 53 GB o si desidera partizionare i dati tra più nodi Redis, è possibile usare la funzionalità di clustering Redis, disponibile nel livello Premium. Ogni nodo è costituito da una coppia di cache primaria/di replica per la disponibilità elevata. Per altre informazioni, vedere [Come configurare il clustering per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).
-	**Protezione avanzata e isolamento della rete**: la distribuzione di Rete virtuale di Azure (VNET) offre maggiori sicurezza e isolamento per Cache Redis di Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso. Per altre informazioni, vedere [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).
-	**Configurare Redis**: nei livelli Standard e Premium, è possibile configurare Redis per le notifiche di Keyspace.
-	**Numero massimo di connessioni client**: il livello Premium offre il numero massimo di client che possono connettersi a Redis, con un numero maggiore di connessioni per cache di dimensioni più grandi. [Per altri dettagli, vedere la pagina relativa a prezzi](https://azure.microsoft.com/pricing/details/cache/).
-	**Core dedicato per il server Redis**: nel livello Premium per tutte le dimensioni della cache è disponibile un core dedicato per Redis. Nei livelli Basic/Standard, per le dimensioni C1 e superiori è disponibile un core dedicato per il server Redis.
-	Poiché **Redis è a thread singolo**, la disponibilità di più di due core non offre vantaggi ulteriori rispetto alla disponibilità di soli due core, ma in genere dimensioni di macchina virtuale maggiori hanno una larghezza di banda più elevata rispetto alle dimensioni minori. Se il server o il client della cache raggiunge i limiti della larghezza di banda, si riceveranno timeout sul lato client.
-	**Miglioramenti delle prestazioni**: le cache nel livello Premium sono distribuite su hardware con processori più veloci e che offre migliori prestazioni rispetto al livello Basic o Standard. Le cache di livello Premium offrono una velocità effettiva più elevata e minori latenze.

La tabella seguente mostra i valori massimi per la larghezza di banda osservati durante il test di diverse dimensioni di cache Standard e Premium usando `redis-benchmark.exe` da una macchina virtuale IaaS nell'endpoint della Cache Redis di Azure. Si noti che questi valori non sono garantiti e che non è disponibile alcun contratto di servizio per questi numeri, che dovrebbero essere tuttavia tipici. È consigliabile testare l'applicazione per determinare le dimensioni di cache ottimali per l'applicazione specifica.

Da questa tabella è possibile trarre le seguenti conclusioni.

-	La velocità effettiva per la Cache della stessa dimensione è superiore nel Premium rispetto al livello Standard. Ad esempio. Per una Cache di 6 GB, la velocità effettiva di P1 è 140K RPS rispetto a 49 K per C3.
-	Con il clustering di Redis, la velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni (nodi) nel cluster. Ad esempio, se si crea un cluster P4 di 10 partizioni, la velocità effettiva disponibile sarà 250 KB * 10 = 2,5 milioni di RPS.
-	La velocità effettiva per dimensioni maggiori di chiave è superiore nel Premium rispetto al livello Standard.

| Pricing tier | Dimensione | Larghezza di banda disponibile | Dimensioni della chiave 1 KB |
|----------------------|--------|----------------------------|--------------------------------|
| **Dimensioni della cache livello Standard** | &nbsp; |**Megabit al secondo (Mbps)** | **Richieste al secondo (RPS)** |
| C0 | 250 MB | 5 | 600 |
| C1 | 1 GB | 100 | 12200 |
| C2 | 2,5 GB | 200 | 24000 |
| C3 | 6 GB | 400 | 49000 |
| C4 | 13 GB | 500 | 61000 |
| C5 | 26 GB | 1000 | 115000 |
| C6 | 53 GB | 2000 | 150000 |
| **Dimensioni della cache livello Premium** | &nbsp; | &nbsp; | **Richieste al secondo (RPS) per partizione** |
| P1 | 6 GB | 1000 | 140000 |
| P2 | 13 GB | 2000 | 220000 |
| P3 | 26 GB | 2000 | 220000 |
| P4 | 53 GB | 4000 | 250000 |


Per istruzioni sul download degli strumenti Redis quali `redis-benchmark.exe`, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands).

<a name="cache-region"></a>
## In quale area è consigliabile posizionare la cache?

Per ottenere le prestazioni migliori e la latenza più bassa, posizionare la Cache Redis di Azure nella stessa area in cui si trova l'applicazione client della cache.

<a name="cache-billing"></a>
## Quali sono i costi addebitati per Cache Redis di Azure?

I prezzi di Cache Redis di Azure sono indicati [in questa pagina](http://azure.microsoft.com/pricing/details/cache/) come tariffa oraria. Le cache vengono fatturate a minuto dal momento in cui la cache viene creata fino a quando viene eliminata. Non è disponibile alcuna opzione per interrompere o sospendere la fatturazione di una cache.

<a name="cache-timeouts"></a>
## Perché vengono visualizzati timeout?

I timeout si verificano nel client usato per comunicare con Redis. Nella maggior parte dei casi, non si verifica il timeout del server Redis. Quando un comando viene inviato al server Redis, il comando viene accodato e il server Redis preleva il comando e lo esegue. È tuttavia possibile che durante questo processo si verifichi il timeout del client e in tale caso viene generata un'eccezione sul lato del chiamante. Per altre informazioni sulla risoluzione dei problemi di timeout, vedere il post di blog relativo all'[investigazione delle eccezioni di timeout in StackExchange.Redis per la Cache Redis di Azure](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## In che modo si esegue il monitoraggio dell'integrità e delle impostazioni della cache?

Le istanze della Cache Redis di Microsoft Azure possono essere monitorate nel [portale di Azure](https://portal.azure.com). È possibile visualizzare le metriche, aggiungere i grafici delle metriche alla Schermata iniziale, personalizzare l'intervallo di data e ora per il monitoraggio dei grafici, aggiungere e rimuovere metriche dai grafici e impostare avvisi per le situazioni in cui vengono soddisfatte determinate condizioni. Questi strumenti permettono di monitorare l'integrità delle istanze della Cache Redis di Azure e semplificano la gestione delle applicazioni di memorizzazione nella cache. Per altre informazioni sul monitoraggio delle cache, vedere [Monitoraggio di Cache Redis di Azure](https://msdn.microsoft.com/library/azure/dn763945.aspx).

<a name="cache-disconnect"></a>
## Perché il client è stato disconnesso dalla cache?

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

<a name="cache-configuration"></a>
## Qual è la funzione delle opzioni di configurazione StackExchange.Redis?

StackExchange.Redis include diverse opzioni. Questa sezione illustra alcune impostazioni comuni. Per informazioni più dettagliate sulle opzioni StackExchange.Redis, vedere la pagina relativa alla [configurazione di StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

Opzioni configurazione|Descrizione|Raccomandazione
---|---|---
AbortOnConnectFail|Se impostata su true, la connessione non verrà ristabilita dopo un errore di rete.|Impostare su false, per permettere a StackExchange.Redis di riconnettersi automaticamente.
ConnectRetry|Numero di nuovi tentativi di connessione durante la connessione iniziale.||
ConnectTimeout|Timeout in millisecondi per le operazioni di connessione.|

Nella maggior parte dei casi sono sufficienti i valori predefiniti del client. È possibile ottimizzare le opzioni in base al carico di lavoro specifico.

-	Tentativi
	-	Per ConnectRetry e ConnectTimeout è in genere consigliabile ottenere rapidamente un errore e riprovare, in base al carico di lavoro specifico e al tempo mediamente necessario per il rilascio di un comando Redis da parte del client e la ricezione di una risposta.
	-	Permettere la connessione automatica di StackExchange.Redis invece di controllare lo stato di connessione ed eseguire manualmente la riconnessione. **Evitare di usare la proprietà ConnectionMultiplexer.IsConnected**.
	-	In alcuni casi è possibile che si verifichi un problema che genera nuovi tentativi a catena, senza recupero. In questo caso è consigliabile prendere in considerazione l'uso di un algoritmo di nuovi tentativi con backoff esponenziale, come illustrato in [Materiale sussidiario generale per i nuovi tentativi](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md) pubblicato dal gruppo Microsoft Patterns & Practices.
-	Valori di timeout
	-	Esaminare il carico di lavoro e impostare valori adeguati. Se si archiviano valori di grandi dimensioni, impostare il timeout su un valore più elevato.
		-	Impostare ABortOnConnectFail su false e permettere la riconnessione automatica da parte di StackExchange.Redis.
-	Usare una singola istanza ConnectionMultiplexer per l'applicazione. È possibile usare un valore LazyConnection per creare una singola istanza restituita da una proprietà Connection, come illustrato in [Connettersi alla cache mediante la classe ConnectionMultiplexer](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect).
-	Impostare la proprietà `ConnectionMultiplexer.ClientName` su un nome univoco dell'istanza dell'app per finalità di diagnostica.
-	Usare più istanze di `ConnectionMultiplexer` per carichi di lavoro personalizzati.
	-	È possibile seguire questo modello se l'applicazione include carichi variabili. Ad esempio:
		-	È possibile avere un multiplexer per la gestione di chiavi di grandi dimensioni. 
		-	È possibile avere un multiplexer per la gestione di chiavi di piccole dimensioni. 
		-	È possibile impostare valori diversi per i timeout di connessione e la logica di ripetizione dei tentativi per ogni ConnectionMultiplexer usato.
		-	Impostare la proprietà `ClientName` in ogni multiplexer per semplificare la diagnostica. 
		-	Si otterrà una latenza semplificata per ogni `ConnectionMultiplexer`.

<a name="cache-redis-commands"></a>
## Che cosa occorre prendere in considerazione quando si usano i comandi Redis comuni?

-	È consigliabile non eseguire determinati comandi Redis che richiedono molto tempo per il completamento se non si comprende l'impatto di tali comandi.
	-	Ad esempio, non eseguire il comando [KEYS](http://redis.io/commands/keys) in produzione, poiché la restituzione di un valore potrebbe richiedere molto tempo, in base al numero delle chiavi. Redis è un server a thread singolo ed elabora un comando alla volta. Eventuali comandi emessi dopo KEYS verranno elaborati solo dopo l'elaborazione del comando KEYS.
-	È consigliabile usare coppie chiave-valore di piccole o di grandi dimensioni? In genere, dipende dallo scenario. Se lo scenario richiede chiavi di dimensioni maggiori, sarà possibile modificare il valore di ConnectionTimeout e dei nuovi tentativi e regolare la logica di ripetizione dei tentativi. Dal punto di vista del server Redis, è stato osservato che valori più piccoli permettono prestazioni migliori.
	-	Ciò non significa che non sia possibile archiviare valori di dimensioni maggiori in Redis. Occorre tenere presenti le considerazioni seguenti. Le latenze saranno più elevate. Se sono presenti un set di dati più grande e un set di dati più piccolo, sarà possibile usare più istanze di ConnectionMultiplexer, ognuna delle quali configurata con un diverso set di valori di timeout e di nuovi tentativi, come illustrato nella sezione [Qual è la funzione delle opzioni di configurazione StackExchange.Redis?](#cache-configuration) precedente.


<a name="cache-ssl"></a>
## Quando è consigliabile abilitare la porta non SSL per la connessione a Redis?

Il server Redis non offre il supporto predefinito per SSL, ma tale supporto è disponibile nella Cache Redis di Azure. Se ci si connette alla Cache Redis di Azure e il client supporta SSL, ad esempio StackExchange.Redis, è consigliabile usare SSL.

Si tenga presente che per le nuove istanze della Cache Redis di Azure la porta non SSL è disabilitata per impostazione predefinita. Se il client non supporta SSL, sarà necessario abilitare la porta non SSL seguendo le istruzioni disponibili nella sezione relativa alle [porte di accesso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) dell'articolo [Configurare una cache in Cache Redis di Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx).

Gli strumenti Redis quali `redis-cli`non funzionano con la porta SSL, ma è possibile usare un'utilità come `stunnel` per connettere in modo sicuro gli strumenti alla porta SSL seguendo le istruzioni disponibili nel post di blog [Annuncio del provider di stato della sessione ASP.NET per la versione di anteprima di Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Per istruzioni sul download degli strumenti Redis, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands).

<a name="cache-benchmarking"></a>
## In che modo è possibile valutare e testare le prestazioni della cache?

-	[Abilitare la diagnostica della cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) per poter [monitorare](https://msdn.microsoft.com/library/azure/dn763945.aspx) l'integrità della cache. È possibile visualizzare le metriche nel portale di Azure, nonché [scaricarle e analizzarle](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando gli strumenti preferiti.
-	È possibile usare redis-benchmark.exe per eseguire test di carico del server Redis.
	-	Assicurarsi che il client del test di carico e la Cache Redis si trovino nella stessa area.
-	Usare redis-cli.exe e monitorare la cache usando il comando INFO.
	-	Se il carico provoca la frammentazione elevata della memoria, è consigliabile passare a dimensioni di cache maggiori.
-	Per istruzioni sul download degli strumenti Redis, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands).

<a name="cache-commands"></a>
## Come si eseguono i comandi Redis?

È possibile usare i comandi elencati nella pagina sui [comandi di Redis](http://redis.io/commands#), ad eccezione dei comandi elencati nella pagina sui [comandi di Redis non supportati nella Cache Redis di Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Per eseguire i comandi di Redis sono disponibili diverse opzioni.

-	Se si ha una cache Standard o Premium, è possibile eseguire i comandi di Redis usando la [Console Redis](cache-configure.md#redis-console). Ciò consente di eseguire in modo sicuro i comandi di Redis nel portale di Azure.
-	È anche possibile usare gli strumenti da riga di comando di Redis. A questo scopo, eseguire la procedura seguente.
	-	Scaricare gli [strumenti da riga di comando Redis](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip).
	-	Connettersi alla cache usando `redis-cli.exe`. Passare l'endpoint della cache mediante l'opzione -h e la chiave mediante -a, come illustrato nell'esempio seguente.
		-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
	-	Si noti che gli strumenti da riga di comando Redis non funzionano con la porta SSL, ma è possibile usare un'utilità come `stunnel` per connettere in modo sicuro gli strumenti alla porta SSL seguendo le istruzioni disponibili nel post di blog [Annuncio del provider di stato della sessione ASP.NET per la versione di anteprima di Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

<a name="cache-common-patterns"></a>
## Quali sono i modelli e le considerazioni comuni sulle cache?

-	Microsoft Patterns & Practices offre il materiale sussidiario seguente.
	-	[Materiale sussidiario sulla memorizzazione nella cache](https://github.com/mspnp/azure-guidance/blob/master/Caching.md).
	-	[Materiale sussidiario sulla progettazione e l'implementazione delle applicazioni cloud di Azure](https://github.com/mspnp/azure-guidance)
-	[Criteri comuni di memorizzazione nella cache con Cache Redis di Azure](cache-howto-common-cache-patterns.md)

<a name="cache-reference"></a>
## Perché la Cache Redis di Azure non offre un riferimento alla libreria di classi MSDN, analogamente ad altri servizi di Azure?

La Cache Redis di Microsoft Azure è basata sulla popolare cache Redis open source e offre l'accesso a una cache Redis sicura e dedicata, gestita da Microsoft. Sono disponibili alcuni [client Redis](http://redis.io/clients) per molti linguaggi di programmazione. Ogni client ha un'API specifica che effettua chiamate all'istanza della Cache Redis usando i [comandi Redis](http://redis.io/commands).

Poiché ogni client è diverso, non è disponibile alcun riferimento di classe centralizzato su MSDN. Ogni client offre invece documentazione di riferimento specifica. Oltre alla documentazione di riferimento, nella pagina relativa alla [documentazione sulla Cache Redis](http://azure.microsoft.com/documentation/services/redis-cache/) sono disponibili alcune esercitazioni su Azure.com, che illustrano come iniziare a usare la Cache Redis di Azure Redis con linguaggi e client di cache diversi.


## Qual è l'offerta di Cache di Azure più adatta alle mie esigenze?

>[AZURE.IMPORTANT]Microsoft consiglia l'uso di Cache Redis di Azure per tutte le nuove soluzioni sviluppate.

Cache di Azure presenta attualmente tre offerte:

-	Cache Redis di Azure
-	Servizio cache gestito di Azure
-	Cache nel ruolo di Azure

>[AZURE.IMPORTANT]Microsoft annuncia per il 30 novembre 2016 il ritiro del Servizio cache gestita di Azure e di Cache nel ruolo di Azure. Si consiglia di eseguire la migrazione a Cache Redis di Azure in preparazione a tale ritiro.
>
>Cache Redis di Azure è stata la soluzione Azure consigliata per la memorizzazione nella cache da quando il servizio è diventato disponibile a livello generale ed è ora disponibile in tutte le aree di Azure, tra cui Cina e Stati Uniti. A causa di tale disponibilità, Microsoft annuncia l'imminente ritiro del Servizio cache gestita e del servizio Cache nel ruolo.
>
>Servizio cache gestita e il servizio Cache nel ruolo rimarranno disponibili per gli attuali clienti per un massimo di 12 mesi dalla data di questo annuncio, 30 novembre 2015, la data di scadenza dei servizi è il 30 novembre 2016. Dopo questa data, Servizio cache gestita e il servizio Cache nel ruolo non saranno più supportati.
>
>Microsoft rimuoverà il supporto per la creazione di nuove cache nel ruolo nella prima versione di Azure SDK che sarà rilasciata dopo il 1° febbraio 2016. I clienti potranno aprire i progetti esistenti in cui sono presenti cache nel ruolo.
>
>Durante questo periodo, si consiglia a tutti gli attuali clienti di Servizio cache gestita e del servizio Cache nel ruolo di eseguire la migrazione a Cache Redis di Azure. Cache Redis di Azure fornisce più funzionalità e un maggiore valore complessivo. Per altre informazioni sulla migrazione, visitare la pagina Web della documentazione [Eseguire la migrazione dal Servizio cache gestita alla Cache Redis di Azure](cache-migrate-to-redis.md).
>
>Per eventuali domande, [contattare Microsoft](https://azure.microsoft.com/support/options/?WT.mc_id=azurebg_email_Trans_933).

### Cache Redis di Azure
Cache Redis di Azure è in genere disponibile per dimensioni fino a 53 GB e prevede un contratto di servizio con disponibilità del 99,9%. Il nuovo [livello Premium](cache-premium-tier.md) offre dimensioni fino a 530 GB, oltre al supporto per clustering, reti virtuali e persistenza, con Contratti di servizio del 99,9%.

Cache Redis di Azure permette ai clienti di usare una cache Redis protetta, dedicata e gestita da Microsoft. Con questa offerta è possibile sfruttare i vantaggi delle numerose funzionalità avanzate e dell'ecosistema forniti da Redis, insieme all'hosting e al monitoraggio affidabili offerti da Microsoft.

A differenza delle cache tradizionali in grado di gestire solo coppie chiave-valore, Redis è nota per i tipi di dati ad alte prestazioni. Redis supporta inoltre l'esecuzione di operazioni atomiche su questi tipi, ad esempio l'aggiunta a una stringa; l'aumento del valore in un hash; il push in un elenco; il calcolo di intersezione, unione e differenza per un set; oppure il recupero del membro con priorità più elevata in un set ordinato. Altre funzionalità includono il supporto di transazioni, pubblicazione/sottoscrizione, script Lua, chiavi con un ciclo di vita limitato e impostazioni di configurazione per permettere a Redis un comportamento più simile a una cache tradizionale.

Un altro aspetto chiave del successo di Redis è l'ecosistema open source integro e dinamico che si è sviluppato attorno, come dimostrato dal variegato set di client Redis disponibili per più linguaggi. In questo modo può essere usato quasi da qualsiasi carico di lavoro che è possibile creare in Azure.

Per altre informazioni sulle operazioni preliminari con Cache Redis di Azure, vedere [Come usare Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e la [documentazione di Cache Redis di Azure](https://azure.microsoft.com/documentation/services/redis-cache/).

### Servizio cache gestita
I clienti di Servizio cache gestita di Azure possono continuare a usare il servizio esistente o eseguire la migrazione a Cache Redis di Azure per sfruttarne le numerose funzionalità avanzate. Servizio cache gestita di Azure è anche disponibile a livello generale, con un contratto di servizio che garantisce il 99,9% di disponibilità.

### In-Role Cache
Se è stato scelto l'hosting indipendente con Cache nel ruolo, è possibile continuare a usare questa soluzione. Poiché Cache nel ruolo è un componente software self-hosted e non un servizio ospitato da Microsoft, non offre un contratto di servizio. Gli utenti di Cache nel ruolo possono scegliere di eseguire la migrazione a Cache Redis di Azure per sfruttare l'ampia gamma di funzionalità e ottenere un contratto di servizio.

<!---HONumber=AcomDC_1210_2015-->