---
title: Risolvere i problemi relativi ai timeout di cache di Azure per Redis
description: Informazioni su come risolvere i problemi di timeout comuni con la cache di Azure per Redis, ad esempio l'applicazione di patch al server Redis e le eccezioni di timeout StackExchange.Redis.Learn how to resolve common timeout issues with Azure Cache for Redis, such as Redis server patching and StackExchange.Redis timeout exceptions.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4301a55e3f5ea5b445ef1540ee59d1b5c28ca0ed
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010818"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Risolvere i problemi relativi ai timeout di cache di Azure per Redis

In questa sezione viene illustrata la risoluzione dei problemi di timeout che si verificano durante la connessione alla cache di Azure per Redis.This section discusses troubleshooting timeout issues that occur when connecting to Azure Cache for Redis.

- [Applicazione di patch al server Redis](#redis-server-patching)
- [Eccezioni di timeout di StackExchange.Redis](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Diversi passaggi per la risoluzione dei problemi illustrati in questa guida includono istruzioni per eseguire comandi di Redis e monitorare svariate metriche delle prestazioni. Per altre informazioni e istruzioni, vedere gli articoli della sezione [Informazioni aggiuntive](#additional-information) .
>

## <a name="redis-server-patching"></a>Applicazione di patch al server Redis

La cache di Azure per Redis aggiorna regolarmente il software server come parte della funzionalità del servizio gestito fornita. Questa attività di [patching](cache-failover.md) si svolge in gran parte dietro la scena. Durante i failover durante i nodi del server Redis sottoposti a patch, i client Redis connessi a questi nodi potrebbero verificarsi timeout temporanei quando le connessioni vengono spostate tra questi nodi. Vedere [Come influisce un failover dell'applicazione client](cache-failover.md#how-does-a-failover-affect-my-client-application) per ulteriori informazioni sulle funzionalità di applicazione a effetti collaterali dell'applicazione e su come migliorarne la gestione degli eventi di applicazione delle patch.

## <a name="stackexchangeredis-timeout-exceptions"></a>Eccezioni di timeout di StackExchange.Redis

StackExchange.Redis utilizza un'impostazione di configurazione denominata `synctimeout` per le operazioni sincrone con un valore predefinito di 1000 ms. Se una chiamata sincrona non viene completata in questo intervallo di tempo, il client StackExchange.Redis genera un errore di timeout simile all'esempio seguente:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Questo messaggio di errore contiene metriche che consentono di trovare la causa e la possibile risoluzione del problema. La tabella seguente contiene i dettagli delle metriche del messaggio di errore.

| Metrica del messaggio di errore | Dettagli |
| --- | --- |
| inst |Nell'ultimo periodo di tempo sono stati eseguiti 0 comandi. |
| mgr |Il gestore `socket.select`di socket sta facendo , il che significa che sta chiedendo al sistema operativo di indicare un socket che ha qualcosa da fare. Il lettore non sta leggendo attivamente dalla rete perché non pensa che ci sia nulla da fare |
| coda |In totale sono in corso 73 operazioni. |
| qu |6 delle operazioni in corso sono nella coda non inviati e non sono ancora state scritte nella rete in uscita |
| qs |67 delle operazioni in corso sono state inviate al server, ma una risposta non è ancora disponibile. La risposta può essere `Not yet sent by the server` o `sent by the server but not yet processed by the client.` |
| qc |0 delle operazioni in corso hanno visto le risposte ma non sono ancora state contrassegnate come complete perché sono in attesa del ciclo di completamento |
| wr |C'è un writer attivo (il che significa che le 6 richieste non inviate non vengono ignorate) byte/activewriters |
| in |Non ci sono lettori attivi e sono disponibili zero byte da leggere nella scheda di interfaccia di rete. Vengono indicati i byte per lettore attivo. |

È possibile utilizzare la procedura seguente per analizzare le possibili cause principali.

1. Come procedura consigliata, assicurarsi di utilizzare il modello seguente per connettersi quando si utilizza il client StackExchange.Redis.

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
    ```

    Per altre informazioni, vedere [Connettersi alla cache usando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Verificare che il server e l'applicazione client si trovino nella stessa area in Azure.Ensure that your server and the client application are are in the same region in Azure. Ad esempio, è possibile che si verifichino timeout quando la cache si trova negli Stati `synctimeout` Uniti orientali, ma il client si trova negli Stati Uniti occidentali e la richiesta non viene completata entro l'intervallo oppure potrebbero verificarsi timeout durante il debug dal computer di sviluppo locale. 

    È consigliabile avere la cache e il client nella stessa area di Azure. Nel caso di uno scenario che include chiamate da più aree, è consigliabile impostare l'intervallo `synctimeout` su un valore maggiore dell'intervallo predefinito di 1000 ms includendo una proprietà `synctimeout` nella stringa di connessione. Nell'esempio seguente viene illustrato un frammento di una stringa di connessione per `synctimeout` StackExchange.Redis fornito dalla cache di Azure per Redis con un valore di 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Assicurarsi di usare la versione più recente del [pacchetto NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). È importante avere la versione più recente perché i bug del codice vengono continuamente fissati per renderlo più solido in caso di timeout.
1. Se le richieste sono vincolate da limitazioni della larghezza di banda sul server o sul client, il completamento delle richieste richiede più tempo e può causare timeout. Per verificare se il timeout è dovuto alla larghezza di banda della rete sul server, vedere [Limitazione della larghezza di banda lato server](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Per verificare se il timeout è dovuto alla larghezza di banda della rete client, vedere [Limitazione della larghezza di banda lato client](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Esistono limiti per la CPU nel server o nel client?

   - Controllare se si sta ottenendo vincolato dalla CPU sul client. Una CPU elevata potrebbe causare l'elaborazione della richiesta entro l'intervallo `synctimeout` e il timeout di una richiesta. Il passaggio a un client di dimensioni maggiori o la distribuzione del carico può aiutare a controllare questo problema.
   - Verificare se la CPU viene associata al server monitorando la [metrica delle prestazioni](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)della cache della CPU. Le richieste in arrivo mentre Redis è associato alla CPU possono causare il timeout di tali richieste. Per risolvere questa condizione, è possibile distribuire il carico tra più partizioni in una cache Premium o eseguire l'aggiornamento a una dimensione o a un piano tariffario di dimensioni maggiori. Per ulteriori informazioni, vedere Limitazione della [larghezza di banda lato server](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Ci sono comandi la cui elaborazione nel server richiede molto tempo? I comandi a esecuzione prolungata che richiedono molto tempo per l'elaborazione nel redis-server possono causare timeout. Per ulteriori informazioni sui comandi a esecuzione prolungata, vedere [Comandi a esecuzione prolungata](cache-troubleshoot-server.md#long-running-commands). È possibile connettersi all'istanza della cache di Azure per Redis usando il client redis-cli o la [console Redis](cache-configure.md#redis-console). Quindi, eseguire il comando [SLOWLOG](https://redis.io/commands/slowlog) per verificare se sono presenti richieste più lente del previsto. Il server Redis e StackExchange.Redis sono ottimizzati per numerose richieste di piccole dimensioni invece che per meno richieste di grandi dimensioni. Dividere i dati in blocchi più piccoli può facilitare le operazioni.

    Per informazioni sulla connessione all'endpoint TLS/SSL della cache tramite redis-cli e stunnel, vedere il post di blog [Annunciato ASP.NET provider dello stato sessione per Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Un carico del server Redis elevato può causare timeout. È possibile monitorare il carico del server monitorando la  [metrica delle prestazioni della cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)`Redis Server Load`. Un carico del server pari a 100 (valore massimo) indica che il server Redis è stato occupato, senza tempo di inattività, a elaborare le richieste. Per verificare se determinate richieste stanno esaurendo la capacità del server, eseguire il comando SlowLog, come descritto nel paragrafo precedente. Per altre informazioni, vedere Utilizzo della CPU/carico del server elevato.
1. Si sono verificati altri eventi sul lato client che potrebbero aver causato un problema di rete? Gli eventi comuni includono: scalabilità del numero di istanze client verso l'alto o verso il basso, distribuzione di una nuova versione del client o scalabilità automatica abilitata. Nel nostro test, abbiamo scoperto che la scalabilità automatica o la scalabilità verticale/giù può causare la perdita di connettività di rete in uscita per alcuni secondi. Il codice StackExchange.Redis è resiliente a tali eventi ed esegue una nuova connessione. Durante la riconnessione, tutte le richieste nella coda possono sfumare.
1. C'era una richiesta di grandi dimensioni che precede diverse piccole richieste alla cache che si è verificato il timeout? Il `qs` parametro nel messaggio di errore indica quante richieste sono state inviate dal client al server, ma non hanno elaborato una risposta. Questo valore può continuare ad aumentare perché StackExchange.Redis usa una sola connessione TCP e può leggere solo una risposta per volta. Anche se la prima operazione è sriuscita, non impedisce l'invio di più dati da o verso il server. Altre richieste verranno bloccate fino al termine della richiesta di grandi dimensioni e possono causare timeout. Una soluzione consiste nel ridurre al minimo la possibilità di timeout assicurandosi che la cache sia abbastanza grande per il carico di lavoro e dividendo i valori elevati in blocchi più piccoli. Un'altra possibile soluzione consiste nell'usare un pool di oggetti `ConnectionMultiplexer` nel client e nello scegliere il `ConnectionMultiplexer` con il carico minore quando si invia una nuova richiesta. Il caricamento su più oggetti connessione dovrebbe impedire che un singolo timeout provochi anche il timeout di altre richieste.
1. Se si utilizza `RedisSessionStateProvider`, assicurarsi di aver impostato correttamente il timeout tra tentativi. `retryTimeoutInMilliseconds` deve essere superiore a `operationTimeoutInMilliseconds`. In caso contrario, non vengono effettuati nuovi tentativi. Nell'esempio seguente `retryTimeoutInMilliseconds` è impostato su 3000. Per altre informazioni, vedere [Provider di stato della sessione ASP.NET per Cache Redis di Azure](cache-aspnet-session-state-provider.md) e [Come usare i parametri di configurazione del provider di stato della sessione e del provider della cache di output](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
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
    ```

1. Controllare l'utilizzo della memoria nel server Cache Redis di Azure [monitorando](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se è in uso un criterio di rimozione, Redis inizia a rimuovere le chiavi quando `Used_Memory` raggiunge le dimensioni della cache. Idealmente, `Used Memory RSS` deve essere solo di poco più elevato di `Used memory`. Una grande differenza significa che c'è la frammentazione della memoria (interna o esterna). Quando `Used Memory RSS` è inferiore a `Used Memory`, significa che il sistema operativo ha effettuato lo swapping di parte della memoria cache. In questo caso, possono verificarsi alcune latenze significative. Poiché Redis non ha il controllo sul modo in cui `Used Memory RSS` le allocazioni vengono mappate alle pagine di memoria, alto è spesso il risultato di un picco nell'utilizzo della memoria. Quando il server Redis libera memoria, l'allocatore prende la memoria, ma può o non può restituire la memoria al sistema. Può esistere una discrepanza tra il valore `Used Memory` e l'utilizzo di memoria segnalato dal sistema operativo, La memoria potrebbe essere stata utilizzata e rilasciata da Redis ma non rifornita al sistema. Per ridurre i problemi di memoria, è possibile effettuare le seguenti operazioni:

   - Aggiornare la cache a una dimensione maggiore in modo da non essere in esecuzione contro le limitazioni di memoria del sistema.
   - Impostare le scadenze per le chiavi in modo che i valori meno recenti vengano rimossi in modo proattivo.
   - Monitorare la metrica della cache `used_memory_rss`. Quando questo valore si avvicina alla dimensione della cache, è probabile che si inizino a riscontrare problemi di prestazioni. Distribuire i dati tra più partizioni se si utilizza una cache Premium o eseguire l'aggiornamento a una dimensione della cache maggiore.

   Per ulteriori informazioni, vedere [Pressione della memoria sul server Redis](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Informazioni aggiuntive

- [Risolvere i problemi relativi al lato client di cache di Azure per Redis](cache-troubleshoot-client.md)
- [Risolvere i problemi del lato server di cache di Azure per Redis](cache-troubleshoot-server.md)
- [In che modo è possibile valutare e testare le prestazioni della cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md)
