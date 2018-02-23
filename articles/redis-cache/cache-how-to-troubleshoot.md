---
title: Come risolvere i problemi di Cache Redis di Azure | Microsoft Docs
description: Informazioni su come risolvere problemi comuni di Cache Redis di Azure.
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: e5f6f423697d90e889ebde2cd203891e34278b3c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Come risolvere i problemi di Cache Redis di Azure
Questo articolo include indicazioni per la risoluzione delle categorie seguenti di problemi di Cache Redis di Azure.

* [Risoluzione dei problemi lato client](#client-side-troubleshooting): questa sezione fornisce linee guida per identificare e risolvere i problemi causati dall'applicazione che si connette a Cache Redis di Azure.
* [Risoluzione dei problemi lato server](#server-side-troubleshooting): questa sezione fornisce linee guida per identificare e risolvere i problemi causati sul lato server di Cache Redis di Azure.
* [Eccezioni di timeout di StackExchange.Redis](#stackexchangeredis-timeout-exceptions) : questa sezione fornisce informazioni sulla risoluzione dei problemi quando si usa il client StackExchange.Redis.

> [!NOTE]
> Diversi passaggi per la risoluzione dei problemi illustrati in questa guida includono istruzioni per eseguire comandi di Redis e monitorare svariate metriche delle prestazioni. Per altre informazioni e istruzioni, vedere gli articoli della sezione [Informazioni aggiuntive](#additional-information) .
> 
> 

## <a name="client-side-troubleshooting"></a>Risoluzione dei problemi lato client
Questa sezione illustra la risoluzione dei problemi che si verificano a causa di una condizione nell'applicazione client.

* [Utilizzo elevato di memoria nel client](#memory-pressure-on-the-client)
* [Burst di traffico](#burst-of-traffic)
* [Utilizzo elevato della CPU client](#high-client-cpu-usage)
* [Larghezza di banda lato client superata](#client-side-bandwidth-exceeded)
* [Dimensioni elevate della richiesta/risposta](#large-requestresponse-size)
* [Che cosa è successo ai dati in Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Utilizzo elevato di memoria nel client
#### <a name="problem"></a>Problema
L'utilizzo elevato di memoria nel computer client causa tutti i tipi di problemi di prestazioni che possono ritardare l'elaborazione dei dati inviati tempestivamente dall'istanza di Redis. Quando si verifica un utilizzo elevato di memoria, il sistema in genere deve eseguire il paging dei dati dalla memoria fisica alla memoria virtuale su disco. Questo *errore di pagina* causa un rallentamento significativo del sistema.

#### <a name="measurement"></a>Misura
1. Monitorare l'utilizzo della memoria nel computer per verificare che non superi la memoria disponibile. 
2. Monitorare il contatore delle prestazioni `Page Faults/Sec` . La maggior parte dei sistemi presenta errori di pagina anche durante il normale funzionamento, quindi è consigliabile controllare i picchi in questo contatore delle prestazioni degli errori di pagina, corrispondenti ai timeout.

#### <a name="resolution"></a>Risoluzione
Aggiornare il client scegliendo dimensioni maggiori della macchina virtuale client con più memoria o esaminare i modelli di utilizzo della memoria per ridurre il consumo di memoria.

### <a name="burst-of-traffic"></a>Burst di traffico
#### <a name="problem"></a>Problema
I burst di traffico combinati con impostazioni `ThreadPool` insufficienti possono causare ritardi nell'elaborazione dei dati già inviati dal server Redis, ma non ancora utilizzati sul lato client.

#### <a name="measurement"></a>Misura
Monitorare il cambiamento delle statistiche `ThreadPool` nel corso del tempo usando un codice [come questo](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). È anche possibile esaminare il messaggio `TimeoutException` da StackExchange.Redis. Di seguito è fornito un esempio: 

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Nel messaggio precedente ci sono diversi problemi interessanti:

1. Si noti che nella sezione `IOCP` e nella sezione `WORKER` è presente un valore `Busy` maggiore del valore `Min`. Questa differenza significa che è necessario modificare le impostazioni di `ThreadPool`.
2. È anche possibile osservare `in: 64221`. Questo valore indica che sono stati ricevuti 64.211 byte a livello di socket kernel, che però non sono ancora stati letti dall'applicazione, ad esempio StackExchange.Redis. Questa differenza significa in genere che l'applicazione non legge i dati dalla rete con la stessa velocità con cui il server li invia.

#### <a name="resolution"></a>Risoluzione
Configurare le [impostazioni di ThreadPool](https://gist.github.com/JonCole/e65411214030f0d823cb) in modo che il pool di thread consenta un rapido aumento delle risorse negli scenari di burst.

### <a name="high-client-cpu-usage"></a>Utilizzo elevato della CPU client
#### <a name="problem"></a>Problema
L'utilizzo elevato della CPU nel client indica che il sistema non riesce a gestire il lavoro che gli viene chiesto di eseguire. Ciò significa che il client potrebbe non riuscire a elaborare tempestivamente una risposta da Redis anche se Redis ha inviato la risposta velocemente.

#### <a name="measurement"></a>Misura
Monitorare l'utilizzo della CPU a livello di sistema tramite il portale di Azure o il contatore delle prestazioni associato. Prestare attenzione a non monitorare la CPU del *processo* perché un singolo processo può avere un utilizzo della CPU basso, ma contemporaneamente l'utilizzo della CPU di sistema complessiva può essere elevato. Cercare nell'utilizzo della CPU i picchi corrispondenti ai timeout. Con un utilizzo elevato della CPU, è anche possibile osservare valori `in: XXX` elevati nei messaggi di errore `TimeoutException`, come illustrato nelle sezione [Burst di traffico](#burst-of-traffic).

> [!NOTE]
> StackExchange.Redis 1.1.603 e versioni successive includono la metrica `local-cpu` nei messaggi di errore `TimeoutException`. Assicurarsi di usare la versione più recente del [pacchetto NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). È importante avere la versione più recente perché i bug del codice vengono continuamente fissati per renderlo più solido in caso di timeout.
> 
> 

#### <a name="resolution"></a>Risoluzione
Eseguire l'aggiornamento a una dimensione maggiore della VM con più capacità di CPU o verificare la causa dei picchi della CPU. 

### <a name="client-side-bandwidth-exceeded"></a>Larghezza di banda lato client superata
#### <a name="problem"></a>Problema
A seconda dell'architettura dei computer client, possono esserci limitazioni relative alla quantità di larghezza di banda di rete disponibile. Se il client supera la larghezza di banda disponibile sovraccaricando la capacità di rete, i dati non vengono elaborati sul lato client con la stessa velocità con cui il server li invia. Questa situazione può causare timeout.

#### <a name="measurement"></a>Misura
Monitorare il cambiamento dell'utilizzo della larghezza di banda nel corso del tempo usando un codice [come questo](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Non è possibile eseguire correttamente questo codice in alcuni ambienti con autorizzazioni con restrizioni, come Siti Web di Azure.

#### <a name="resolution"></a>Risoluzione
Aumentare le dimensioni della VM client o ridurre il consumo di larghezza di banda di rete.

### <a name="large-requestresponse-size"></a>Dimensioni elevate della richiesta/risposta
#### <a name="problem"></a>Problema
Una richiesta/risposta di grandi dimensioni può causare un timeout. Si supponga, ad esempio, che il valore di timeout configurato nel client sia di 1 secondo. L'applicazione richiede due chiavi, ad esempio "A" e "B", nello stesso momento (con la stessa connessione di rete fisica). La maggior parte dei client supporta il "pipelining" delle richieste, in modo che entrambe le richieste "A" e "B" vengano inviate in rete al server una dopo l'altra senza attendere le risposte. Il server invia le risposte nello stesso ordine. Se la risposta "A" è di dimensioni piuttosto grandi, può consumare la maggior parte del timeout per le richieste successive. 

L'esempio seguente illustra questo scenario. In questo scenario le richieste "A" e "B" vengono inviate rapidamente, il server inizia a inviare rapidamente le risposte "A" e "B", ma a causa dei tempi di trasferimento dati, "B" si blocca dietro l'altra richiesta e si verifica il timeout anche se il server ha risposto rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Misura
Non è semplice misurare questa richiesta/risposta. Essenzialmente è necessario instrumentare il codice client per tenere traccia di richieste e risposte di grandi dimensioni. 

#### <a name="resolution"></a>Risoluzione
1. Redis è ottimizzato per molti valori bassi, invece che per pochi valori elevati. La soluzione migliore consiste nel dividere i dati in valori più bassi correlati. Vedere il post in cui viene chiesto [qual è l'intervallo di dimensioni dei valori ideale per Redis e se 100 KB sono troppi](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) per una spiegazione dettagliata del perché siano preferibili valori più bassi.
2. Aumentare le dimensioni della VM (per il client e per il server Cache Redis), per ottenere capacità di larghezza di banda maggiori, riducendo i tempi di trasferimento dati per le risposte più grandi. Ottenere una maggiore larghezza di banda solo nel server o solo nel client potrebbe non essere sufficiente. Misurare l'utilizzo della larghezza di banda e confrontarlo con le capacità delle dimensioni della VM attuali.
3. Aumentare il numero di oggetti `ConnectionMultiplexer` usati ed eseguire il round robin delle richieste su connessioni diverse.

### <a name="what-happened-to-my-data-in-redis"></a>Che cosa è successo ai dati in Redis?
#### <a name="problem"></a>Problema
Si era previsto di trovare determinati dati nell'istanza di Cache Redis di Azure, ma sembra che non sia così.

#### <a name="resolution"></a>Risoluzione
Vedere [Che cosa è successo ai dati in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) per le possibili cause e risoluzioni.

## <a name="server-side-troubleshooting"></a>Risoluzione dei problemi lato server
Questa sezione illustra la risoluzione dei problemi che si verificano a causa di una condizione nel server di cache.

* [Utilizzo elevato di memoria nel server](#memory-pressure-on-the-server)
* [Utilizzo della CPU/carico del server elevato](#high-cpu-usage-server-load)
* [Larghezza di banda lato server superata](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Utilizzo elevato di memoria nel server
#### <a name="problem"></a>Problema
L'utilizzo elevato di memoria sul lato server causa tutti i tipi di problemi di prestazioni che possono ritardare l'elaborazione delle richieste. Quando si verifica un utilizzo elevato di memoria, il sistema in genere deve eseguire il paging dei dati dalla memoria fisica alla memoria virtuale su disco. Questo *errore di pagina* causa un rallentamento significativo del sistema. Le possibili cause di questo utilizzo elevato di memoria sono diverse: 

1. Sono stati inseriti dati nella cache fino a esaurirne la capacità. 
2. Redis sta osservando un'elevata frammentazione della memoria che molto spesso è causata dall'archiviazione di oggetti di grandi dimensioni, mentre Redis è ottimizzato per oggetti di piccole dimensioni. Vedere il post in cui viene chiesto [qual è l'intervallo di dimensioni dei valori ideale per Redis e se 100 KB sono troppi](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) per i dettagli. 

#### <a name="measurement"></a>Misura
Redis espone due metriche che consentono di identificare questo problema. La prima è `used_memory` e l'altra è `used_memory_rss`. [Queste metriche](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) sono disponibili nel portale di Azure o tramite il comando [Redis INFO](http://redis.io/commands/info).

#### <a name="resolution"></a>Risoluzione
È possibile apportare diverse modifiche che consentono di mantenere integro l'utilizzo della memoria:

1. [Configurare un criterio per la memoria](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e impostare scadenze per le chiavi. Questa risoluzione potrebbe non essere sufficiente in caso di frammentazione.
2. [Configurare un valore maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) abbastanza grande da compensare la frammentazione della memoria.
3. Suddividere gli oggetti di grandi dimensioni memorizzati nella cache in oggetti correlati più piccoli.
4. [Passare](cache-how-to-scale.md) a una dimensione della cache maggiore.
5. Se si usa una [cache Premium con il cluster Redis abilitato](cache-how-to-premium-clustering.md), è possibile [aumentare il numero di partizioni](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Utilizzo della CPU/carico del server elevato
#### <a name="problem"></a>Problema
Un utilizzo elevato della CPU può significare che il lato client può non riuscire a elaborare tempestivamente una risposta da Redis anche se Redis ha inviato la risposta velocemente.

#### <a name="measurement"></a>Misura
Monitorare l'utilizzo della CPU a livello di sistema tramite il portale di Azure o il contatore delle prestazioni associato. Prestare attenzione a non monitorare la CPU del *processo* perché un singolo processo può avere un utilizzo della CPU basso, ma contemporaneamente l'utilizzo della CPU di sistema complessiva può essere elevato. Cercare nell'utilizzo della CPU i picchi corrispondenti ai timeout.

#### <a name="resolution"></a>Risoluzione
* Esaminare eventuali suggerimenti e avvisi indicati in [Redis Cache Advisor](cache-configure.md#redis-cache-advisor).
* Esaminare anche gli altri suggerimenti in questo argomento e in [Best Practices for Azure Redis](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) (Procedure consigliate per Redis di Azure) per verificare se sono state usate tutte le opzioni per ottimizzare ulteriormente la cache e il client. 
* Esaminare i grafici delle [prestazioni di Cache Redis di Azure](cache-faq.md#azure-redis-cache-performance) per verificare se si stanno per raggiungere le soglie superiori del livello corrente. Se necessario, [ridimensionare](cache-how-to-scale.md) il livello della cache passando a un piano superiore con capacità della CPU maggiore. Se si usa già il livello Premium, potrebbe essere necessario ricorrere alla [scalabilità orizzontale con il clustering](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Larghezza di banda lato server superata
#### <a name="problem"></a>Problema
A seconda delle dimensioni delle istanze della cache, possono esserci limitazioni relative alla quantità di larghezza di banda di rete disponibile. Se il server supera la larghezza di banda disponibile, i dati non vengono inviati al client altrettanto rapidamente. Questa situazione può causare timeout.

#### <a name="measurement"></a>Misura
È possibile monitorare la metrica `Cache Read` , ovvero la quantità di dati letti dalla cache in megabyte al secondo (MB/s) durante l'intervallo di report specificato. Questo valore corrisponde alla larghezza di banda della rete usata da questa cache. Per configurare avvisi per i limiti della larghezza di banda di rete lato server, è possibile usare il contatore `Cache Read`. Confrontare le letture con i valori in [questa tabella](cache-faq.md#cache-performance) per i limiti della larghezza di banda osservati nei diversi piani tariffari e nelle varie dimensioni della cache.

#### <a name="resolution"></a>Risoluzione
Se si è quasi sempre vicini alla larghezza di banda massima osservata per il piano tariffario e le dimensioni della cache, considerare la possibilità di [passare](cache-how-to-scale.md) a un piano tariffario o a dimensioni con un piano tariffario di rete maggiore, usando i valori di [questa tabella](cache-faq.md#cache-performance) come riferimento.

## <a name="stackexchangeredis-timeout-exceptions"></a>Eccezioni di timeout di StackExchange.Redis
StackExchange.Redis usa un'impostazione di configurazione denominata `synctimeout` per le operazioni sincrone, che ha un valore predefinito di 1000 ms. Se una chiamata sincrona non viene completata nel tempo stabilito, il client StackExchange.Redis genera un errore di timeout simile all'esempio seguente:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Questo messaggio di errore contiene metriche che consentono di trovare la causa e la possibile risoluzione del problema. La tabella seguente contiene i dettagli delle metriche del messaggio di errore.

| Metrica del messaggio di errore | Dettagli |
| --- | --- |
| inst |Nell'ultimo periodo di tempo sono stati eseguiti 0 comandi. |
| mgr |Il gestore socket sta eseguendo `socket.select`, ovvero sta chiedendo al sistema operativo di indicare un socket con operazioni da eseguire. In sostanza, il lettore non sta attivamente leggendo dalla rete perché non ritiene che ci siano operazioni da eseguire |
| coda |In totale sono in corso 73 operazioni. |
| qu |6 delle operazioni in corso sono nella coda degli elementi non inviati e non sono ancora state scritte nella rete in uscita. |
| qs |67 delle operazioni in corso sono state inviate al server, ma non è ancora disponibile una risposta. La risposta può essere `Not yet sent by the server` o `sent by the server but not yet processed by the client.` |
| qc |0 delle operazioni in corso hanno avuto risposta, ma non sono ancora state contrassegnate come completate perché sono in attesa del ciclo di completamento. |
| wr |C'è un writer attivo (che significa che le 6 richieste non inviate non verranno ignorate). Vengono indicati i byte per writer attivo. |
| in |Non ci sono lettori attivi e sono disponibili zero byte da leggere nella scheda di interfaccia di rete. Vengono indicati i byte per lettore attivo. |

### <a name="steps-to-investigate"></a>Passaggi di verifica
1. Come procedura consigliata, verificare di usare il modello seguente per connettersi quando si usa il client StackExchange.Redis.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ````

    Per altre informazioni, vedere [Connettersi alla cache usando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Assicurarsi che Cache Redis di Azure e l'applicazione client siano nella stessa area di Azure. È possibile ad esempio, che si verifichino timeout quando la cache è negli Stati Uniti orientali, ma il client è negli Stati Uniti occidentali e la richiesta non viene completata entro l'intervallo `synctimeout` oppure è possibile che si verifichino timeout quando si esegue il debug dal computer di sviluppo locale. 
   
    È consigliabile avere la cache e il client nella stessa area di Azure. Nel caso di uno scenario che include chiamate da più aree, è consigliabile impostare l'intervallo `synctimeout` su un valore maggiore dell'intervallo predefinito di 1000 ms includendo una proprietà `synctimeout` nella stringa di connessione. L'esempio seguente illustra un frammento della stringa di connessione alla cache StackExchange.Redis con un valore `synctimeout` di 2000 ms.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Assicurarsi di usare la versione più recente del [pacchetto NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). È importante avere la versione più recente perché i bug del codice vengono continuamente fissati per renderlo più solido in caso di timeout.
3. Se nel server o nel client sono presenti richieste soggette a limitazioni di larghezza di banda, il completamento di queste richiede più tempo e di conseguenza possono verificarsi timeout. Per verificare se il timeout è causato dalla larghezza di banda di rete nel server, vedere [Larghezza di banda lato server superata](#server-side-bandwidth-exceeded). Per verificare se il timeout è causato dalla larghezza di banda di rete nel client, vedere [Larghezza di banda lato client superata](#client-side-bandwidth-exceeded).
4. Esistono limiti per la CPU nel server o nel client?
   
   * Controllare se ci sono limiti per la CPU nel client che possono causare la mancata elaborazione della richiesta entro l'intervallo `synctimeout`, provocando così un timeout. Per controllare questo problema, può essere utile passare a client di dimensioni maggiori o distribuire il carico. 
   * Controllare se esistono limiti per la CPU nel server monitorando la [metrica delle prestazioni della cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `CPU`. Le richieste in arrivo mentre Redis è limitato dalla CPU possono causare il timeout di tali richieste. Per risolvere il problema, è possibile distribuire il carico tra più partizioni in una cache Premium o eseguire l'aggiornamento a dimensioni maggiori o a un piano tariffario superiore. Per altre informazioni, vedere [Larghezza di banda lato server superata](#server-side-bandwidth-exceeded).
5. Ci sono comandi la cui elaborazione nel server richiede molto tempo? I comandi con esecuzione di lunga durata la cui elaborazione richiede molto tempo nel server Redis possono causare timeout. Alcuni esempi di comandi con esecuzione di lunga durata sono `mget` con numeri di chiave elevati, `keys *` o script lua scritti in modo inadeguato. È possibile connettersi all'istanza di Cache Redis di Azure usando il client redis-cli o usare la [console Redis](cache-configure.md#redis-console) ed eseguire il comando [SlowLog](http://redis.io/commands/slowlog) per verificare se sono presenti richieste che richiedono più tempo del previsto. Il server Redis e StackExchange.Redis sono ottimizzati per numerose richieste di piccole dimensioni invece che per meno richieste di grandi dimensioni. Dividere i dati in blocchi più piccoli può facilitare le operazioni. 
   
    Per informazioni sulla connessione all'endpoint SSL di Cache Redis di Azure con redis-cli e stunnel, vedere il post di blog contenente l' [Annuncio della versione di anteprima del provider di stato della sessione ASP.NET per Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) . Per altre informazioni, vedere [SlowLog](http://redis.io/commands/slowlog).
6. Un carico del server Redis elevato può causare timeout. È possibile monitorare il carico del server monitorando la [metrica delle prestazioni della cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Redis Server Load`. Un carico del server pari a 100 (valore massimo) indica che il server Redis è stato occupato, senza tempo di inattività, a elaborare le richieste. Per verificare se determinate richieste stanno esaurendo la capacità del server, eseguire il comando SlowLog, come descritto nel paragrafo precedente. Per altre informazioni, vedere [Utilizzo della CPU/carico del server elevato](#high-cpu-usage-server-load).
7. Si sono verificati altri eventi sul lato client che potrebbero aver causato un problema di rete? Controllare nel client (Web, ruolo di lavoro o macchina virtuale Iaas) se si è verificato un evento, ad esempio l'aumento o la riduzione del numero di istanze client o la distribuzione di una nuova versione del client o se è abilitato il ridimensionamento automatico. Nel test è stato scoperto che il ridimensionamento automatico o il passaggio a un piano superiore/inferiore può causare la perdita della connettività di rete per diversi secondi. Il codice StackExchange.Redis è resiliente a tali eventi ed esegue una nuova connessione. Durante la riconnessione può verificarsi il timeout delle richieste nella coda.
8. Si è verificato il timeout di una richiesta di grandi dimensioni che precedeva diverse richieste di piccole dimensioni a Cache Redis? Il parametro `qs` nel messaggio di errore indica quante richieste sono state inviate dal client al server, ma non hanno ancora elaborato una risposta. Questo valore può continuare ad aumentare perché StackExchange.Redis usa una sola connessione TCP e può leggere solo una risposta per volta. Anche se si è verificato il timeout della prima operazione, l'invio dei dati al/dal server non viene arrestato e le altre richieste vengono bloccate finché la richiesta di grandi dimensioni non viene completata, causando i timeout. Una soluzione consiste nel ridurre al minimo la possibilità di timeout assicurandosi che la cache sia abbastanza grande per il carico di lavoro e dividendo i valori elevati in blocchi più piccoli. Un'altra possibile soluzione consiste nell'usare un pool di oggetti `ConnectionMultiplexer` nel client e nello scegliere il `ConnectionMultiplexer` con il carico minore quando si invia una nuova richiesta. In questo modo si eviterà che un singolo timeout provochi il timeout anche delle altre richieste.
9. Se si usa `RedisSessionStateprovider`, verificare di avere impostato correttamente il timeout per i tentativi. `retrytimeoutInMilliseconds` deve essere superiore a `operationTimeoutinMilliseonds`. In caso contrario, non vengono effettuati nuovi tentativi. Nell'esempio seguente `retrytimeoutInMilliseconds` è impostato su 3000. Per altre informazioni, vedere [Provider di stato della sessione ASP.NET per Cache Redis di Azure](cache-aspnet-session-state-provider.md) e [How to use the configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) (Come usare i parametri di configurazione del provider di stato della sessione e del provider di cache di output).

    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


1. Controllare l'utilizzo della memoria nel server Cache Redis di Azure [monitorando](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se è in uso un criterio di rimozione, Redis inizia a rimuovere le chiavi quando `Used_Memory` raggiunge le dimensioni della cache. Idealmente, `Used Memory RSS` deve essere solo di poco più elevato di `Used memory`. Una grande differenza indica la frammentazione della memoria (interna o esterna). Quando `Used Memory RSS` è inferiore a `Used Memory`, significa che il sistema operativo ha effettuato lo swapping di parte della memoria cache. In questo caso, possono verificarsi alcune latenze significative. Poiché Redis non ha il controllo sull'esecuzione del mapping delle allocazioni alle pagine di memoria, un valore `Used Memory RSS` elevato è spesso il risultato di un picco nell'utilizzo della memoria. Quando Redis libera la memoria, la memoria viene restituita all'allocatore che può restituire o meno la memoria al sistema. Può esistere una discrepanza tra il valore `Used Memory` e l'utilizzo di memoria segnalato dal sistema operativo, che può essere dovuta al fatto che la memoria è stata usata e rilasciata da Redis, ma non restituita al sistema Per ridurre i problemi di memoria, è possibile eseguire queste operazioni:
   
   * Aggiornare la cache a una dimensione maggiore per evitare di imbattersi in limitazioni della memoria nel sistema.
   * Impostare le scadenze per le chiavi in modo che i valori meno recenti vengano rimossi in modo proattivo.
   * Monitorare la metrica della cache `used_memory_rss`. Quando questo valore si avvicina alle dimensioni della cache, è probabile che si inizino a notare problemi di prestazioni. Distribuire i dati in più partizioni se si usa una cache Premium o eseguire l'aggiornamento a una cache di dimensioni superiori.
   
   Per altre informazioni, vedere [Utilizzo elevato di memoria nel server](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informazioni aggiuntive
* [Quali offerte e dimensioni della Cache Redis è consigliabile usare?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [In che modo è possibile valutare e testare le prestazioni della cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Come si eseguono i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands)
* [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md)

