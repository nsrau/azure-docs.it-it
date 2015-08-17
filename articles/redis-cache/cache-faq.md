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
	ms.date="07/24/2015" 
	ms.author="sdanie"/>

# Domande frequenti sulla Cache Redis di Azure

Risposte alle domande più comuni, modelli e procedure consigliate per la Cache Redis di Azure.

<a name="cache-size"></a>
## Quali offerte e dimensioni della Cache Redis è consigliabile usare?

Ogni offerta della Cache Redis di Azure fornisce livelli diversi per le opzioni relative a **dimensioni**, **larghezza di banda**, **disponibilità elevata** e **Contratto di servizio**.

-	SKU Basic: nodo singolo, nessuna replica, nessun Contratto di servizio, dimensioni di cache da 250 MB a 53 GB.
-	SKU Standard: nodi primari/secondari con replica automatica, Contratto di servizio con disponibilità del 99,9%, dimensioni di cache da 250 MB a 53 GB.

Per ottenere la disponibilità elevata, scegliere l'offerta Standard per la cache, che include un Contratto di servizio con disponibilità del 99,9%. Per lo sviluppo e la creazione di prototipi o per scenari in cui il Contratto di servizio non è necessario, l'offerta Basic potrebbe essere appropriata.

Le dimensioni della cache e la larghezza di banda sono mappate all'incirca alle dimensioni e alla larghezza di banda delle macchine virtuali che ospitano la cache. Le dimensioni pari a 250 MB per le offerte Basic e Standard sono ospitate in macchine virtuali di dimensioni molto piccole (A0), ospitate mediante core condivisi, mentre le altre dimensioni vengono ospitate mediante core dedicati. Le dimensioni di cache pari a 1 GB sono ospitate in macchine virtuali di dimensioni piccole (A1), a cui è associato un core virtuale dedicato, usato per gestire sia il sistema operativo che la Cache Redis. Le dimensioni di cache superiori vengono ospitate in istanze più grandi delle macchine virtuali, con più core virtuali dedicati.

Se la cache ha una velocità effettiva elevata, scegliere dimensioni pari a 1 GB o superiori, in modo che la cache venga eseguita mediante core dedicati. Le dimensioni di cache pari a 1 GB sono ospitate in una macchina virtuale con un core, usato per gestire sia il sistema operativo che la cache. Le cache con dimensioni superiori a 1 GB vengono eseguite in macchine virtuali con più core e la Cache Redis usa un core dedicato non condiviso con il sistema operativo.

Poiché **Redis è a thread singolo**, la disponibilità di più di due core non offre vantaggi ulteriori rispetto alla disponibilità di soli due core, ma in genere **dimensioni di macchina virtuale maggiori hanno una larghezza di banda più elevata rispetto alle dimensioni minori**. Se il server o il client della cache raggiunge i limiti della larghezza di banda, si riceveranno timeout sul lato client.

La tabella seguente mostra i valori massimi per la larghezza di banda osservati durante il test di diverse dimensioni della Cache Redis di Azure usando `redis-benchmark.exe` da una VM IaaS nell'endpoint della Cache Redis di Azure. Si noti che questi valori non sono garantiti e che non è disponibile alcun Contratto di servizio per questi numeri, che dovrebbero essere tuttavia tipici. È consigliabile testare l'applicazione per determinare le dimensioni di cache ottimali per l'applicazione specifica.

Nome cache|Dimensioni cache|Get/sec (semplici chiamate GET con valori di 1 KB)|Larghezza di banda (MBit/sec)
---|---|---|---
C0|250 MB|610|5
C1|1 GB|12\.200|100
C2|2,5 GB|24\.300|200
C3|6 GB|48\.875|400
C4|13 GB|61\.350|500
C5|26 GB|112\.275|1000
C6|53 GB|153\.219|1000+

Per istruzioni sul download degli strumenti Redis quali `redis-benchmark.exe`, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands).

<a name="cache-region"></a>
## In quale area è consigliabile posizionare la cache?

Per ottenere le prestazioni migliori e la latenza più bassa, posizionare la Cache Redis di Azure nella stessa area in cui si trova l'applicazione client della cache.

<a name="cache-billing"></a>
## Quali sono i costi addebitati per Cache Redis di Azure?

I prezzi di Cache Redis di Azure sono indicati [in questa pagina](http://azure.microsoft.com/pricing/details/cache/). come tariffa oraria. Le cache vengono fatturate a minuto dal momento in cui la cache viene creata fino a quando viene eliminata. Non è disponibile alcuna opzione per interrompere o sospendere la fatturazione di una cache.

<a name="cache-timeouts"></a>
## Perché vengono visualizzati timeout?

I timeout si verificano nel client usato per comunicare con Redis. Nella maggior parte dei casi, non si verifica il timeout del server Redis. Quando un comando viene inviato al server Redis, il comando viene accodato e il server Redis preleva il comando e lo esegue. È tuttavia possibile che durante questo processo si verifichi il timeout del client e in tale caso viene generata un'eccezione sul lato del chiamante. Per altre informazioni sulla risoluzione dei problemi di timeout, vedere il post di blog relativo all'[investigazione delle eccezioni di timeout in StackExchange.Redis per la Cache Redis di Azure](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## In che modo si esegue il monitoraggio dell'integrità e delle impostazioni della cache?

Le istanze della Cache Redis di Microsoft Azure possono essere monitorate nel [portale di anteprima di Azure](https://portal.azure.com). È possibile visualizzare le metriche, aggiungere i grafici delle metriche alla Schermata iniziale, personalizzare l'intervallo di data e ora per il monitoraggio dei grafici, aggiungere e rimuovere metriche dai grafici e impostare avvisi per le situazioni in cui vengono soddisfatte determinate condizioni. Questi strumenti permettono di monitorare l'integrità delle istanze della Cache Redis di Azure e semplificano la gestione delle applicazioni di memorizzazione nella cache. Per altre informazioni sul monitoraggio delle cache, vedere [Monitoraggio di Cache Redis di Azure](https://msdn.microsoft.com/library/azure/dn763945.aspx).

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

-	[Abilitare la diagnostica della cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) per poter [monitorare](https://msdn.microsoft.com/library/azure/dn763945.aspx) l'integrità della cache. È possibile visualizzare le metriche nel portale, nonché [scaricarle e analizzarle](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando gli strumenti preferiti.
-	È possibile usare redis-benchmark.exe per eseguire test di carico del server Redis.
	-	Assicurarsi che il client del test di carico e la Cache Redis si trovino nella stessa area.
-	Usare redis-cli.exe e monitorare la cache usando il comando INFO.
	-	Se il carico provoca la frammentazione elevata della memoria, è consigliabile passare a dimensioni di cache maggiori.
-	Per istruzioni sul download degli strumenti Redis, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands).

<a name="cache-commands"></a>
## Come si eseguono i comandi Redis?

È possibile usare i comandi elencati nella pagina sui [comandi di Redis](http://redis.io/commands#) ad eccezione dei comandi elencati nella pagina sui [comandi di Redis non supportati nella Cache Redis di Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Per eseguire i comandi di Redis sono disponibili diverse opzioni.

-	Se si ha una cache Standard, è possibile eseguire i comandi di Redis usando la [Console Redis](cache-configure.md#redis-console). Ciò consente di eseguire in modo sicuro i comandi di Redis nel portale di Azure.
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

Poiché ogni client è diverso, non è disponibile alcun riferimento di classe centralizzato su MSDN. Ogni client offre invece documentazione di riferimento specifica. Oltre alla documentazione di riferimento, nella pagina relativa alla [documentazione sulla Cache Redis](http://azure.microsoft.com/documentatgion/services/redis-cache/) sono disponibili alcune esercitazioni su Azure.com, che illustrano come iniziare a usare la Cache Redis di Azure Redis con linguaggi e client di cache diversi.

<!---HONumber=August15_HO6-->