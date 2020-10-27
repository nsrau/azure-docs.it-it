---
title: Domande frequenti sulla gestione di cache di Azure per Redis
description: Informazioni sulle risposte alle domande comuni che consentono di gestire cache di Azure per Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537661"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Domande frequenti sulla gestione di cache di Azure per Redis
Questo articolo fornisce le risposte alle domande comuni su come gestire cache di Azure per Redis.

## <a name="common-questions-and-answers"></a>Domande frequenti e risposte
In questa sezione vengono illustrate le domande frequenti seguenti:

* [Quando è consigliabile abilitare la porta non TLS/SSL per la connessione a Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [Quali sono alcune procedure consigliate per la produzione?](#what-are-some-production-best-practices)
* [Che cosa occorre prendere in considerazione quando si usano i comandi Redis comuni?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [In che modo è possibile valutare e testare le prestazioni della cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Informazioni importanti sulla crescita del pool di thread](#important-details-about-threadpool-growth)
* [Abilitare il server Garbage Collection per poter ottenere una velocità effettiva maggiore sul client quando si usa StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerazioni sulle prestazioni per le connessioni](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Quando è consigliabile abilitare la porta non TLS/SSL per la connessione a Redis?
Il server Redis non offre il supporto nativo per TLS, ma tale supporto è disponibile in Cache Redis di Azure. Se ci si connette a Cache Redis di Azure e il client supporta TLS, ad esempio StackExchange.Redis, è consigliabile usare TLS.

>[!NOTE]
>Per le nuove istanze di Cache Redis di Azure la porta non TLS è disabilitata per impostazione predefinita. Se il client non supporta TLS, sarà necessario abilitare la porta non TLS seguendo le istruzioni disponibili nella sezione [Porte di accesso](cache-configure.md#access-ports) dell'articolo [Configurare una cache in Cache Redis di Azure](cache-configure.md).
>
>

Strumenti di Redis come `redis-cli` non funzionano con la porta TLS, ma è possibile usare un'utilità come `stunnel` per connettere in modo sicuro gli strumenti alla porta TLS seguendo le istruzioni disponibili nel post di blog [Announcing ASP.NET Session State Provider for Redis Preview Release](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/) (Annuncio del provider di stato della sessione ASP.NET per la versione di anteprima di Redis).

Per istruzioni sul download degli strumenti Redis, vedere la sezione [Come si eseguono i comandi Redis?](cache-development-faq.md#how-can-i-run-redis-commands) .

### <a name="what-are-some-production-best-practices"></a>Quali sono alcune procedure consigliate per la produzione?
* [Procedure consigliate di StackExchange.Redis](#stackexchangeredis-best-practices)
* [Configurazione e concetti](#configuration-and-concepts)
* [Test delle prestazioni](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Procedure consigliate di StackExchange.Redis
* Impostare `AbortConnect` su false, quindi consentire a ConnectionMultiplexer di eseguire la riconnessione automatica. [Per informazioni dettagliate, vedere qui](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Riutilizzare ConnectionMultiplexer: non creare una nuova istanza per ogni richiesta. È fortemente consigliato usare il modello `Lazy<ConnectionMultiplexer>`[mostrato qui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
* Redis funziona meglio con valori inferiori, quindi considerare di suddividere i dati più grandi in più chiavi. In [questa discussione di Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb viene considerato grande. Leggere [in questo articolo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) per un problema di esempio che può essere causato da valori di grandi dimensioni.
* Configurare le [impostazioni ThreadPool](#important-details-about-threadpool-growth) per evitare timeout.
* Usare almeno il connectTimeout predefinito di 5 secondi. Questo intervallo fornirebbe a StackExchange.Redis tempo sufficiente per ristabilire la connessione in caso di un problema di rete.
* Tenere presente i costi delle prestazioni associati a diverse operazioni in esecuzione. Ad esempio, il comando `KEYS` è un'operazione O(n) e deve essere evitato. Il [sito redis.io](https://redis.io/commands/) fornisce i dettagli sulla complessità del tempo per ogni operazione supportata. Fare clic su ogni comando per visualizzare la complessità di ogni operazione.

#### <a name="configuration-and-concepts"></a>Configurazione e concetti
* Usare il livello Premium o Standard per i sistemi di produzione. Il livello Basic è un sistema a nodo singolo senza replica dei dati e senza contratto di servizio. Usare almeno una cache di livello C1. Le cache di livello C0 sono usate solitamente in scenari semplici di sviluppo e test.
* Tenere presente che Redis è un archivio dati **in memoria** . Leggere [questo articolo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) per acquisire familiarità con gli scenari in cui può verificarsi una perdita di dati.
* Sviluppare il sistema in modo che possa gestire i problemi di connessione [causati da failover e applicazione di patch](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Test delle prestazioni
* Usare innanzitutto `redis-benchmark.exe` per acquisire familiarità con le velocità effettive possibili prima di scrivere il proprio test delle prestazioni. Il benchmark `redis-benchmark` non supporta TLS, pertanto è necessario [abilitare la porta non TLS tramite il portale di Azure](cache-configure.md#access-ports) prima di eseguire il test. Per esempi, vedere [In che modo è possibile valutare e testare le prestazioni della cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* La macchina virtuale client usata per il test deve trovarsi nella stessa area dell'istanza di Cache Redis di Azure.
* È consigliabile usare macchine virtuali della serie Dv2 per il client poiché dispongono di hardware migliore e dovrebbero quindi dare risultati più precisi.
* Assicurarsi che la macchina virtuale client disponga almeno della capacità di elaborazione e della larghezza di banda della cache che si sta testando.
* Se si usa Windows, abilitare VRSS sul computer client. [Per informazioni dettagliate, vedere qui](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).
* Le istanze del livello Premium di Redis hanno migliore latenza di rete e velocità effettiva, poiché sono in esecuzione su hardware migliori in CPU e rete.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Che cosa occorre prendere in considerazione quando si usano i comandi Redis comuni?

* Evitare di usare determinati comandi Redis che impiegano molto tempo per il completamento se non se ne comprende appieno l'impatto. Ad esempio, non eseguire il comando [KEYS](https://redis.io/commands/keys) in produzione. In base al numero delle chiavi, la restituzione di un valore potrebbe infatti richiedere molto tempo. Redis è un server a thread singolo ed elabora un comando alla volta. Eventuali comandi emessi dopo KEYS verranno elaborati solo dopo l'elaborazione del comando KEYS. Il [sito redis.io](https://redis.io/commands/) fornisce i dettagli sulla complessità del tempo per ogni operazione supportata. Fare clic su ogni comando per visualizzare la complessità di ogni operazione.
* È consigliabile usare coppie chiave-valore di piccole o di grandi dimensioni? Dipende dallo scenario. Se lo scenario richiede chiavi di dimensioni maggiori, si può modificare il valore di ConnectionTimeout e dei nuovi tentativi e regolare la logica di ripetizione dei tentativi. Dal punto di vista del server Redis, valori più piccoli offrono prestazioni migliori.
* Ciò non significa che non sia possibile archiviare valori di dimensioni maggiori in Redis. Occorre tenere presenti le considerazioni seguenti. Le latenze saranno più elevate. Se sono presenti un set di dati più grande e un set di dati più piccolo, sarà possibile usare più istanze di ConnectionMultiplexer, ognuna delle quali configurata con un diverso set di valori di timeout e di nuovi tentativi, come illustrato nella sezione [Qual è la funzione delle opzioni di configurazione StackExchange.Redis?](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) precedente.

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>In che modo è possibile valutare e testare le prestazioni della cache?
* [Abilitare la diagnostica della cache](cache-how-to-monitor.md#enable-cache-diagnostics) per [monitorare](cache-how-to-monitor.md) l'integrità della cache. È possibile visualizzare le metriche nel portale di Azure, nonché [scaricarle e analizzarle](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando gli strumenti preferiti.
* È possibile usare redis-benchmark.exe per eseguire test di carico del server Redis.
* Assicurarsi che il client del test di carico e Cache Redis di Azure si trovino nella stessa area.
* Usare redis-cli.exe e monitorare la cache usando il comando INFO.
* Se il carico provoca la frammentazione elevata della memoria, è consigliabile passare a dimensioni di cache maggiori.
* Per istruzioni sul download degli strumenti Redis, vedere la sezione [Come si eseguono i comandi Redis?](cache-development-faq.md#how-can-i-run-redis-commands) .

I comandi seguenti rappresentano un esempio di uso di redis-benchmark.exe. Per risultati accurati, eseguire questi comandi da una macchina virtuale situata nella stessa area della cache.

* Testare le richieste SET in pipeline usando un payload 1 k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testare le richieste GET in pipeline usando un payload 1 k.

>[!NOTE]
> eseguire il test SET mostrato in alto prima di popolare la cache
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>Informazioni importanti sulla crescita del pool di thread
L'oggetto ThreadPool CLR ha due tipi di thread, i thread di lavoro (WORKER) e i thread IOCP (porta di completamento I/O).

* I thread di lavoro vengono usati per operazioni come l'elaborazione dei metodi `Task.Run(…)` o `ThreadPool.QueueUserWorkItem(…)`. Questi thread vengono usati anche da vari componenti CLR quando il lavoro deve essere eseguito in un thread in background.
* I thread IOCP vengono usati quando si verificano I/O asincroni, ad esempio, durante la lettura dalla rete.

Il pool di thread fornisce nuovi thread di lavoro o thread di completamento I/O su richiesta, senza alcuna limitazione, fino a quando non viene raggiunta l'impostazione minima per ogni tipo di thread. Per impostazione predefinita, il numero minimo di thread corrisponde al numero di processori in un sistema.

Quando il numero di thread esistenti, o occupati, raggiunge il minimo, l'oggetto ThreadPool limita la frequenza di inserimento dei nuovi thread a uno ogni 500 millisecondi. Solitamente, se il sistema riceve un picco di lavoro che necessita di un thread IOCP, il lavoro viene elaborato rapidamente. Tuttavia, se il picco di lavoro è superiore all'impostazione minima configurata, ci sarà un ritardo nell'elaborazione di una parte del lavoro mentre l'oggetto ThreadPool attende che si verifichi una delle condizioni seguenti.

* Un thread esistente diventa disponibile per elaborare il lavoro.
* Nessun thread esistente diventa disponibile per 500 ms e viene creato un nuovo thread.

In sostanza, ciò significa che quando il numero dei thread occupati è maggiore del numero minimo di thread, è probabile che si verifichi un ritardo di 500 ms prima che il traffico di rete venga elaborato dall'applicazione. È anche importante notare che quando un thread esistente rimane inattivo per più di 15 secondi viene pulito e il ciclo di crescita e riduzione si ripete.

Se si osserva un messaggio di errore di esempio da StackExchange.Redis, build 1.0.450 o versione successiva, si noterà che ora le statistiche dell'oggetto ThreadPool vengono stampate. Di seguito sono riportati i dettagli relativi a IOCP e WORKER.

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

Nell'esempio precedente sono presenti sei thread IOCP occupati e il sistema è configurato per consentire un minimo di quattro thread. In questo caso si verificheranno probabilmente due ritardi di 500 ms, perché 6 > 4.

Si noti che StackExchange.Redis può raggiungere il timeout se la crescita dei thread IOCP o WORKER viene limitata.

#### <a name="recommendation"></a>Recommendation

È consigliabile impostare il numero minimo dei thread IOCP e WORKER su un valore maggiore di quello predefinito. Non è possibile fornire indicazioni universali su questo valore, perché il valore corretto per un'applicazione sarebbe probabilmente troppo alto o troppo basso per un'altra. Questa impostazione può anche influire sulle prestazioni di altre parti di applicazioni complesse. È quindi necessario che venga regolata in base alle esigenze specifiche del cliente. Un buon punto di partenza è 200 o 300, da verificare e modificare a seconda delle necessità.

Come configurare questa impostazione:

* Si consiglia di modificare questa impostazione a livello di codice usando il metodo [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) in `global.asax.cs`. Ad esempio:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > Il valore specificato da questo metodo è un'impostazione globale, che interessa l'intero dominio dell'applicazione. Ad esempio, se si ha un computer con 4 core e si vuole impostare *minWorkerThreads* e *minIoThreads* su 50 per ogni CPU durante la fase di esecuzione, si dovrà usare **ThreadPool.SetMinThreads(200, 200)** .

* È anche possibile specificare l'impostazione relativa al numero minimo di thread usando l'impostazione di configurazione [*minIoThreads* o  *minWorkerThreads*](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100)) nell'elemento di configurazione `<processModel>` in `Machine.config`, in genere situato in `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`. **Impostare il numero minimo di thread in questo modo non è in genere consigliato, perché si tratta di un'impostazione a livello di sistema.**

  > [!NOTE]
  > Il valore specificato in questo elemento di configurazione è un'impostazione *per core* . Ad esempio, se si ha un computer con 4 core e si vuole che l'impostazione *minIoThreads* sia 200 in fase di esecuzione, occorre usare `<processModel minIoThreads="50"/>`.
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Abilitare il server Garbage Collection in modo da ottenere una velocità effettiva maggiore sul client quando si usa StackExchange.Redis
L'abilitazione del server Garbage Collection consente di ottimizzare il client e fornire velocità effettiva e prestazioni migliori quando si usa StackExchange.Redis. Per altre informazioni sul server Garbage Collection e sulla relativa abilitazione, vedere gli articoli seguenti:

* [Per abilitare il server Garbage Collection](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Nozioni fondamentali di Garbage Collection](/dotnet/standard/garbage-collection/fundamentals)
* [Garbage Collection e prestazioni](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>Considerazioni sulle prestazioni per le connessioni

Ogni piano tariffario presenta diversi limiti di connessioni client, memoria e larghezza di banda. Mentre ogni dimensione della cache consente *fino a* un determinato numero di connessioni, ogni connessione a Redis dispone di un sovraccarico associato. Un esempio di questo sovraccarico potrebbe essere l'utilizzo della CPU e della memoria a causa della crittografia TLS/SSL. Il limite massimo di connessioni per una dimensione della cache specificata presuppone una cache leggermente caricata. Se il carico del sovraccarico della connessione *sommato* al carico delle operazioni client supera la capacità del sistema, la cache può riscontrare problemi di capacità anche se il limite della connessione non è stato superato in base alla dimensione della cache corrente.

Per altre informazioni sui diversi limiti di connessione per ogni livello, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/). Per ulteriori informazioni sulle connessioni e altre configurazioni predefinite, vedere [Configurazione predefinita del server Redis](cache-configure.md#default-redis-server-configuration).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre [domande frequenti su cache di Azure per Redis](cache-faq.md).