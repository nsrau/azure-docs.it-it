---
title: Risoluzione dei problemi di Cache Redis di Azure | Microsoft Docs
description: Informazioni su come risolvere problemi comuni di Cache Redis di Azure.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527167"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Risoluzione dei problemi di Cache Redis di Azure

Questo articolo contiene di risolvere le diverse categorie di problemi che possono verificare durante la connessione con la Cache di Azure per le istanze di Redis.

- [Risoluzione dei problemi lato client](#client-side-troubleshooting) consente di identificare e risolvere i problemi nell'applicazione di cui ci si connette alla cache.
- [Risoluzione dei problemi lato server](#server-side-troubleshooting) consente di identificare e risolvere i problemi che si verificano nella Cache di Azure per Redis lato.
- [Risoluzione dei problemi di perdita di dati](#data-loss-troubleshooting) consente di identificare e risolvere gli eventi imprevisti in cui le chiavi sono previsti ma non trovate nella cache.
- [Eccezioni di timeout stackexchange. Redis](#stackexchangeredis-timeout-exceptions) fornisce indicazioni specifiche sulla risoluzione dei problemi con la libreria stackexchange. Redis.

> [!NOTE]
> Diversi passaggi per la risoluzione dei problemi illustrati in questa guida includono istruzioni per eseguire comandi di Redis e monitorare svariate metriche delle prestazioni. Per altre informazioni e istruzioni, vedere gli articoli della sezione [Informazioni aggiuntive](#additional-information) .
>
>

## <a name="client-side-troubleshooting"></a>Risoluzione dei problemi lato client

Questa sezione illustra la risoluzione dei problemi che si verificano a causa di una condizione nell'applicazione client.

- [Utilizzo elevato di memoria nel client](#memory-pressure-on-the-client)
- [Burst di traffico](#burst-of-traffic)
- [Utilizzo elevato della CPU client](#high-client-cpu-usage)
- [Larghezza di banda lato client superata](#client-side-bandwidth-exceeded)
- [Dimensioni elevate della richiesta/risposta](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>Utilizzo elevato di memoria nel client

Richieste di memoria sul computer client causa tutti i tipi di problemi di prestazioni che possono ritardare l'elaborazione delle risposte dalla cache. Quando si verifica un utilizzo elevato della memoria, il sistema potrebbe paging dei dati su disco. Questo _errore di pagina_ causa un rallentamento significativo del sistema.

Per rilevare richieste di memoria sul client:

- Monitoraggio dell'utilizzo di memoria nel computer per assicurarsi che non superi la memoria disponibile.
- Monitorare i client `Page Faults/Sec` contatore delle prestazioni. Durante il normale funzionamento, la maggior parte dei sistemi presenta errori di pagina. I picchi di errori di pagina corrispondente con timeout delle richieste possono indicare un utilizzo elevato della memoria.

È possibile ridurre le richieste di memoria elevata sul client diversi modi:

- Esaminare i modelli di utilizzo della memoria per ridurre il consumo di memoria sul client.
- Aggiornare il client della macchina virtuale a dimensioni maggiori con più memoria.

### <a name="burst-of-traffic"></a>Burst di traffico

I burst di traffico combinati con impostazioni `ThreadPool` insufficienti possono causare ritardi nell'elaborazione dei dati già inviati dal server Redis, ma non ancora utilizzati sul lato client.

Monitoraggio come le `ThreadPool` statistiche cambiano nel tempo usando [riportato `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). È possibile usare `TimeoutException` i messaggi da stackexchange. Redis è simile al seguente esaminare ulteriormente questo:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Nel caso di eccezione precedente, esistono diversi problemi interessanti:

- Si noti che nella sezione `IOCP` e nella sezione `WORKER` è presente un valore `Busy` maggiore del valore `Min`. Questa differenza significa il `ThreadPool` impostazioni devono essere modificate.
- È anche possibile osservare `in: 64221` Questo valore indica che 64.211 byte sono state ricevute nel livello di socket kernel del client, ma non sono stati letti dall'applicazione. Questa differenza significa in genere che l'applicazione (ad esempio stackexchange. Redis) non è la lettura dei dati dalla rete stessa velocità con cui il server li invia all'utente.

È possibile [configurare le `ThreadPool` impostazioni](https://gist.github.com/JonCole/e65411214030f0d823cb) per assicurarsi che il pool di thread aumenta rapidamente in scenari di burst.

### <a name="high-client-cpu-usage"></a>Utilizzo elevato della CPU client

Utilizzo CPU elevato client indica che il sistema non riesce a gestire il lavoro che è stato richiesto di eseguire. Anche se la cache ha inviato la risposta velocemente, il client potrebbe non riuscire a elaborare la risposta in modo tempestivo.

Monitorare l'utilizzo di CPU a livello di sistema del client con le metriche disponibili nel portale di Azure o tramite i contatori delle prestazioni nel computer. Prestare attenzione a non monitorare *processo* CPU perché un singolo processo può avere un basso utilizzo della CPU, ma può essere elevata della CPU a livello di sistema. Cercare nell'utilizzo della CPU i picchi corrispondenti ai timeout. Utilizzo elevato della CPU potrebbe causare anche ad alte `in: XXX` i valori `TimeoutException` messaggi di errore, come descritto nel [Burst di traffico](#burst-of-traffic) sezione.

> [!NOTE]
> StackExchange.Redis 1.1.603 e versioni successive includono la metrica `local-cpu` nei messaggi di errore `TimeoutException`. Assicurarsi di usare la versione più recente del [pacchetto NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). È importante avere la versione più recente perché i bug del codice vengono continuamente fissati per renderlo più solido in caso di timeout.
>
>

Per ridurre l'utilizzo elevato della CPU del client:

- Verificare la causa dei picchi della CPU.
- Aggiornare il client a una dimensione di VM più grande con più capacità di CPU.

### <a name="client-side-bandwidth-exceeded"></a>Larghezza di banda lato client superata

A seconda dell'architettura dei computer client, possono esserci limitazioni relative alla quantità di larghezza di banda di rete disponibile. Se il client supera la larghezza di banda disponibile sovraccaricando la capacità della rete, i dati non elaborati sul lato client non appena il server li invia. Questa situazione può causare timeout.

Il monitoraggio come l'utilizzo della larghezza di banda cambiano nel tempo usando [riportato `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Non è possibile eseguire correttamente questo codice in alcuni ambienti con autorizzazioni con restrizioni, come Siti Web di Azure.

Per risolvere, ridurre il consumo di larghezza di banda di rete o aumentare le dimensioni della macchina virtuale a uno con più capacità di rete client.

### <a name="large-requestresponse-size"></a>Dimensioni elevate della richiesta/risposta

Una richiesta/risposta di grandi dimensioni può causare un timeout. Ad esempio, si supponga che il valore di timeout configurato nel client è 1 secondo. L'applicazione richiede due chiavi, ad esempio "A" e "B", nello stesso momento (con la stessa connessione di rete fisica). La maggior parte dei client supportano richiesta "pipelining", dove entrambe "A" e "B" vengono inviate richieste uno dopo l'altro senza tempi di attesa per le relative risposte. Il server invia le risposte nello stesso ordine. Se è grande risposta "A", può consumare la maggior parte del timeout per le richieste successive.

Nell'esempio seguente, richiesta "A" e "B" vengono inviate rapidamente al server. Il server inizia a inviare rapidamente le risposte "A" e "B". A causa di tempi di trasferimento dati, risposta 'B' attesa dietro risposta "A" verifica il timeout anche se il server ha risposto rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Non è semplice misurare questa richiesta/risposta. È possibile instrumentare il codice client per tenere traccia delle risposte e richieste di grandi dimensioni.

Le risoluzioni per le dimensioni della risposta di grandi dimensioni sono diversi, ma includono:

1. Ottimizzare l'applicazione per un numero elevato di valori più bassi, piuttosto che alcuni valori di grandi dimensioni.
    - La soluzione migliore consiste nel dividere i dati in valori più bassi correlati.
    - Vedere il post [qual è l'intervallo di dimensioni valori ideale per redis? È troppo grande 100 KB? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) per informazioni dettagliate sui motivi per cui sono consigliabile usare i valori minori.
1. Aumentare le dimensioni della macchina virtuale per ottenere maggiore capacità di larghezza di banda
    - Maggiore larghezza di banda nel client o server della macchina virtuale può ridurre i tempi di trasferimento dei dati per le risposte più grandi.
    - Confrontare l'utilizzo della rete corrente in entrambi i computer ai limiti della dimensione della macchina virtuale corrente. Larghezza di banda maggiore solo sul server o solo sul client potrebbe non essere sufficiente.
1. Aumentare il numero di oggetti di connessione che viene utilizzata l'applicazione.
    - Usare un approccio round robin per effettuare richieste tramite oggetti di connessione diversa.

## <a name="server-side-troubleshooting"></a>Risoluzione dei problemi lato server

Questa sezione illustra la risoluzione dei problemi che si verificano a causa di una condizione nel server di cache.

- [Utilizzo elevato di memoria nel server](#memory-pressure-on-the-server)
- [Utilizzo della CPU/carico del server elevato](#high-cpu-usage--server-load)
- [Larghezza di banda lato server superata](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Utilizzo elevato di memoria nel server

L'utilizzo elevato di memoria sul lato server causa tutti i tipi di problemi di prestazioni che possono ritardare l'elaborazione delle richieste. Quando si verifica un utilizzo elevato della memoria, il sistema potrebbe paging dei dati su disco. Questo _errore di pagina_ causa un rallentamento significativo del sistema. Le possibili cause di questo utilizzo elevato di memoria sono diverse:

- La cache viene riempita con i dati quasi alla capacità massima.
- Redis sta osservando la frammentazione della memoria elevato. Questa frammentazione viene spesso causata da archiviazione di oggetti di grandi dimensioni poiché Redis è ottimizzato per oggetti di piccole dimensioni.

Redis espone due statistiche tramite le [INFO](https://redis.io/commands/info) comandi che consentono di identificare questo problema: "used_memory" e "used_memory_rss". È possibile [visualizzare queste metriche](cache-how-to-monitor.md#view-metrics-with-azure-monitor) usando il portale.

Esistono diverse modifiche apportate a mantenere integro l'utilizzo della memoria:

- [Configurare un criterio per la memoria](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e impostare scadenze per le chiavi. Questo criterio potrebbe non essere sufficiente in caso di frammentazione.
- [Configurare un valore maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) abbastanza grande da compensare la frammentazione della memoria. Per altre informazioni, vedere le ulteriori [considerazioni per le prenotazioni di memoria](#considerations-for-memory-reservations) sotto.
- Suddividere gli oggetti di grandi dimensioni memorizzati nella cache in oggetti correlati più piccoli.
- [Creare avvisi](cache-how-to-monitor.md#alerts) sulle metriche, ad esempio memoria utilizzata sarà necessario notificare tempestivamente gli impatti potenziali.
- [Scalabilità](cache-how-to-scale.md) per dimensioni di cache maggiori con più capacità di memoria.

#### <a name="considerations-for-memory-reservations"></a>Considerazioni per le prenotazioni di memoria

Aggiornamento dei valori di prenotazione di memoria, come maxmemory-reserved, può influire sulle prestazioni della cache. Si supponga di che avere una cache di 53 GB che viene riempita con 49 GB di dati. La modifica del valore di prenotazione a 8 GB Elimina il massimo della memoria disponibile per 45 GB. Se _used_memory_ oppure _used_memory_rss_ valori sono superiori a 45 GB, il sistema potrebbe rimuovere i dati fino a quando sia _used_memory_ e _used_memory_rss_ sono inferiori a 45 GB. La rimozione può aumentare il carico del server e la frammentazione della memoria.

### <a name="high-cpu-usage--server-load"></a>Utilizzo della CPU/carico del server elevato

Un carico elevato di server o l'utilizzo della CPU indica che il server non è in grado di elaborare le richieste in modo tempestivo. Il server sia lenta a rispondere e non è possibile adeguarsi alla frequenza delle richieste.

[Monitorare le metriche](cache-how-to-monitor.md#view-metrics-with-azure-monitor) , ad esempio il carico della CPU imprevisto o server. Cercare nell'utilizzo della CPU i picchi corrispondenti ai timeout.

Sono disponibili numerose modifiche, che è possibile apportare per ridurre il carico elevato di server:

- Verificare la causa dei picchi della CPU, ad esempio in esecuzione [i comandi dispendiosi](#expensive-commands) o pagina eventi di errore a causa di richieste di memoria elevata.
- [Creare avvisi](cache-how-to-monitor.md#alerts) su metriche quali il carico della CPU o di server sarà necessario notificare tempestivamente gli impatti potenziali.
- [Scalabilità](cache-how-to-scale.md) per dimensioni di cache maggiori con più capacità di CPU.

#### <a name="expensive-commands"></a>Comandi dispendiosi

Non tutti i comandi di Redis vengono creati in modo uniforme: alcuni sono più costosi rispetto ad altri utenti. Il [documentazione di comandi di Redis](https://redis.io/commands) Mostra la complessità di tempo di ogni comando. È consigliabile che esaminare i comandi in esecuzione nella cache per comprendere l'impatto sulle prestazioni di tali comandi. Ad esempio, il [CHIAVI](https://redis.io/commands/keys) comando viene spesso utilizzato senza esserne a conoscenza che è un'operazione o (n). È possibile evitare le CHIAVI usando [SCAN](https://redis.io/commands/scan) per ridurre la CPU i picchi.

Usando il [SLOWLOG](https://redis.io/commands/slowlog) comando, è possibile misurare i comandi dispendiosi in esecuzione sul server.

### <a name="server-side-bandwidth-exceeded"></a>Larghezza di banda lato server superata

Le dimensioni della cache diversi hanno capacità di larghezza di banda di rete diversa. Se il server supera la larghezza di banda disponibile, i dati non verranno inviati al client altrettanto rapidamente. Le richieste dei client è stato possibile raggiungere il timeout perché il server non è il push dei dati al client abbastanza rapidamente.

Le metriche di "Lettura da Cache" e "Scrittura di Cache" possono essere utilizzate per verificare quanta larghezza di banda lato server viene usata. È possibile [visualizzare queste metriche](cache-how-to-monitor.md#view-metrics-with-azure-monitor) nel portale.

Per ridurre i casi in cui utilizzo della larghezza di banda di rete è vicino a capacità massima:

- Modificare il comportamento di chiamata di client per ridurre la richiesta di rete.
- [Creare avvisi](cache-how-to-monitor.md#alerts) sulle metriche, ad esempio lettura da cache o di scrittura nella cache per essere informati tempestivamente gli impatti potenziali.
- [Scalabilità](cache-how-to-scale.md) per dimensioni di cache maggiori con più capacità di larghezza di banda di rete.

## <a name="data-loss-troubleshooting"></a>Risoluzione dei problemi di perdita di dati

Si era previsto di trovare determinati dati nell'istanza della Cache Redis di Azure, ma sembra che non sia così.

Vedere [Che cosa è successo ai dati in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) per le possibili cause e risoluzioni.

## <a name="stackexchangeredis-timeout-exceptions"></a>Eccezioni di timeout di StackExchange.Redis

Stackexchange. Redis Usa un'impostazione di configurazione denominata `synctimeout` per le operazioni sincrone con un valore predefinito di 1000 ms. Se una chiamata sincrona non viene completata in questo momento, il client stackexchange. Redis genera un errore di timeout simile all'esempio seguente:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Questo messaggio di errore contiene metriche che consentono di trovare la causa e la possibile risoluzione del problema. La tabella seguente contiene i dettagli delle metriche del messaggio di errore.

| Metrica del messaggio di errore | Dettagli |
| --- | --- |
| inst |Nell'ultimo periodo di tempo: sono stati rilasciati 0 comandi |
| mgr |Il gestore socket sta eseguendo `socket.select`, vale a dire che sta chiedendo al sistema operativo di indicare un socket con operazioni da eseguire. Il lettore non sta attivamente leggendo dalla rete perché non ritiene che ci siano operazioni da eseguire |
| coda |In totale sono in corso 73 operazioni. |
| qu |6 delle operazioni in corso sono nella coda non inviata e non sono ancora state scritte nella rete in uscita |
| qs |67 delle operazioni in corso sono state inviate al server, ma una risposta non è ancora disponibile. La risposta può essere `Not yet sent by the server` o `sent by the server but not yet processed by the client.` |
| qc |0 delle operazioni in corso hanno avuto risposta, ma non sono ancora state contrassegnate come completate perché cui è in attesa del ciclo di completamento |
| wr |È presente un writer attivo (vale a dire che le 6 richieste non inviate non vengono ignorate) byte/activewriters |
| in |Non ci sono lettori attivi e sono disponibili zero byte da leggere nella scheda di interfaccia di rete. Vengono indicati i byte per lettore attivo. |

### <a name="steps-to-investigate"></a>Passaggi di verifica

1. Come procedura consigliata, assicurarsi che si sta usando il modello seguente per connettersi quando si usa il client stackexchange. Redis.

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

1. Assicurarsi che il server e l'applicazione client siano nella stessa area in Azure. Ad esempio, che si verifichino timeout quando la cache è negli Stati Uniti orientali, ma il client è negli Stati Uniti occidentali e la richiesta non viene completata entro il `synctimeout` intervallo o si verifichino timeout quando esegue il debug dal computer di sviluppo locale. 

    È consigliabile avere la cache e il client nella stessa area di Azure. Nel caso di uno scenario che include chiamate da più aree, è consigliabile impostare l'intervallo `synctimeout` su un valore maggiore dell'intervallo predefinito di 1000 ms includendo una proprietà `synctimeout` nella stringa di connessione. L'esempio seguente illustra un frammento di codice di una stringa di connessione per stackexchange. Redis fornito dalla Cache di Azure per Redis con un `synctimeout` di 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Assicurarsi di usare la versione più recente del [pacchetto NuGet StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). È importante avere la versione più recente perché i bug del codice vengono continuamente fissati per renderlo più solido in caso di timeout.
1. Se le richieste associate di limitazioni della larghezza di banda nel server o nel client, richiede più tempo per poter completare e può causare timeout. Per verificare se il timeout è a causa della larghezza di banda di rete nel server, vedere [larghezza di banda lato Server superata](#server-side-bandwidth-exceeded). Per verificare se il timeout è a causa di larghezza di banda di rete client, vedere [larghezza di banda lato Client superata](#client-side-bandwidth-exceeded).
1. Esistono limiti per la CPU nel server o nel client?

   - Controllare se ha limiti per CPU nel client. Utilizzo elevato della CPU possono causare la richiesta a non essere elaborati all'interno di `synctimeout` intervallo e causa una richiesta di timeout. Per controllare questo problema, può essere utile passare a client di dimensioni maggiori o distribuire il carico.
   - Controllare se si ricevono CPU associata al server monitorando la `CPU` [metrica delle prestazioni della cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Le richieste in arrivo mentre Redis è associata alla CPU possono causare il timeout di tali richieste. Per risolvere il problema, è possibile distribuire il carico tra più partizioni in una cache Premium o eseguire l'aggiornamento a dimensioni maggiori o a un piano tariffario superiore. Per altre informazioni, vedere [Larghezza di banda lato server superata](#server-side-bandwidth-exceeded).
1. Ci sono comandi la cui elaborazione nel server richiede molto tempo? I comandi con esecuzione prolungata che richiedono lunghi tempi di elaborazione nel server redis possono causare timeout. Per altre informazioni sui comandi con esecuzione prolungata, vedere [i comandi dispendiosi](#expensive-commands). È possibile connettersi a Cache di Azure per l'istanza di Redis utilizza il client redis-cli o il [Console di Redis](cache-configure.md#redis-console). Eseguire quindi il [SLOWLOG](https://redis.io/commands/slowlog) comando per verificare se sono richieste più lentamente del previsto. Il server Redis e StackExchange.Redis sono ottimizzati per numerose richieste di piccole dimensioni invece che per meno richieste di grandi dimensioni. Dividere i dati in blocchi più piccoli può facilitare le operazioni.

    Per informazioni sulla connessione all'endpoint SSL della cache con redis-cli e stunnel, vedere il post di blog [Announcing ASP.NET Session State Provider per la versione di anteprima di Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Un carico del server Redis elevato può causare timeout. È possibile monitorare il carico del server monitorando la [metrica delle prestazioni della cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Redis Server Load`. Un carico del server pari a 100 (valore massimo) indica che il server Redis è stato occupato, senza tempo di inattività, a elaborare le richieste. Per verificare se determinate richieste stanno esaurendo la capacità del server, eseguire il comando SlowLog, come descritto nel paragrafo precedente. Per altre informazioni, vedere Utilizzo della CPU/carico del server elevato.
1. Si sono verificati altri eventi sul lato client che potrebbero aver causato un problema di rete? Gli eventi comuni includono: aumentare il numero di istanze client o ridurre le prestazioni, la distribuzione di una nuova versione del client o la scalabilità automatica abilitata. Nel corso dei test, è stato scoperto che il ridimensionamento automatico o la scalabilità su/giù può causare la connettività di rete in uscita andrà persa per alcuni secondi. Il codice StackExchange.Redis è resiliente a tali eventi ed esegue una nuova connessione. Durante la riconnessione, tutte le richieste in coda possono raggiungere il timeout.
1. Era presente una richiesta di grandi dimensioni precedenti diverse richieste di piccole dimensioni per la cache timeout? Il parametro `qs` nel messaggio di errore messaggio è indicato il numero di richieste inviato dal client al server, ma non hanno elaborato una risposta. Questo valore può continuare ad aumentare perché StackExchange.Redis usa una sola connessione TCP e può leggere solo una risposta per volta. Anche se la prima operazione di timeout, non viene interrotto più dati possano essere inviate a o dal server. Le altre richieste verranno bloccato fino a quando la richiesta di grandi dimensioni ha terminata e può causare timeout. Una soluzione consiste nel ridurre al minimo la possibilità di timeout assicurandosi che la cache sia abbastanza grande per il carico di lavoro e dividendo i valori elevati in blocchi più piccoli. Un'altra possibile soluzione consiste nell'usare un pool di oggetti `ConnectionMultiplexer` nel client e nello scegliere il `ConnectionMultiplexer` con il carico minore quando si invia una nuova richiesta. Il caricamento da più oggetti di connessione deve impedire un singolo timeout provochi il timeout anche altre richieste.
1. Se si usa `RedisSessionStateProvider`, assicurarsi di aver impostato correttamente il timeout dei tentativi. `retryTimeoutInMilliseconds` deve essere superiore a `operationTimeoutInMilliseconds`. In caso contrario, non vengono effettuati nuovi tentativi. Nell'esempio seguente `retryTimeoutInMilliseconds` è impostato su 3000. Per altre informazioni, vedere [Provider di stato della sessione ASP.NET per Cache Redis di Azure](cache-aspnet-session-state-provider.md) e [Come usare i parametri di configurazione del provider di stato della sessione e del provider della cache di output](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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

1. Controllare l'utilizzo della memoria nel server Cache Redis di Azure [monitorando](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se è in uso un criterio di rimozione, Redis inizia a rimuovere le chiavi quando `Used_Memory` raggiunge le dimensioni della cache. Idealmente, `Used Memory RSS` deve essere solo di poco più elevato di `Used memory`. Una grande differenza indica la frammentazione della memoria (interne o esterne). Quando `Used Memory RSS` è inferiore a `Used Memory`, significa che il sistema operativo ha effettuato lo swapping di parte della memoria cache. In questo caso, possono verificarsi alcune latenze significative. Poiché Redis non possono controllare come le allocazioni vengono mappate alle pagine in memoria, elevate `Used Memory RSS` è spesso il risultato di un picco nell'utilizzo della memoria. Quando il server Redis libera la memoria, l'allocatore richiede la memoria ma può o potrebbe non restituire la memoria nel sistema. Può esistere una discrepanza tra il valore `Used Memory` e l'utilizzo di memoria segnalato dal sistema operativo, Memoria potrebbe sono stata utilizzata e rilasciata da Redis ma non restituita al sistema. Per ridurre i problemi di memoria, è possibile eseguire i passaggi seguenti:

   - Aggiornare la cache a una dimensione maggiore, in modo che non sono in esecuzione su limiti di memoria nel sistema.
   - Impostare le scadenze per le chiavi in modo che i valori meno recenti vengano rimossi in modo proattivo.
   - Monitorare la metrica della cache `used_memory_rss`. Quando questo valore si avvicina alle dimensioni della cache, è probabile che inizino a notare problemi di prestazioni. Distribuire i dati tra più partizioni se si usa una cache premium, o eseguire l'aggiornamento a una cache di dimensioni superiori.

   Per altre informazioni, vedere [Utilizzo elevato di memoria nel server](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informazioni aggiuntive

- [Quali offerte e dimensioni della Cache Redis è consigliabile usare?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [In che modo è possibile valutare e testare le prestazioni della cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Come si eseguono i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands)
- [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md)
