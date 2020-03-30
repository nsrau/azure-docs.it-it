---
title: Procedure consigliate per Cache di Azure per Redis
description: Informazioni su come usare la cache di Azure per Redis in modo efficace seguendo queste procedure consigliate.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 71056fd04069b861b37a595b1a4f2a8bba4a01ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75689967"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Procedure consigliate per Cache di Azure per Redis 
Seguendo queste procedure consigliate, è possibile ottimizzare le prestazioni e l'uso economico dell'istanza della cache di Azure per Redis.By following these best practices, you can help maximize the performance and cost-effective use of your Azure Cache for Redis instance.

## <a name="configuration-and-concepts"></a>Configurazione e concetti
 * **Utilizzare il livello Standard o Premium per i sistemi di produzione.**  Il livello Basic è un sistema a nodo singolo senza replica dei dati e senza SLA. Usare almeno una cache di livello C1.  Le cache C0 sono destinate a scenari di sviluppo/test semplici poiché hanno un core della CPU condiviso, poca memoria e sono soggette a problemi di "noisy neighbor".

 * **Tenere presente che Redis è un archivio dati in memoria.**  [In questo articolo](cache-troubleshoot-data-loss.md) vengono descritti alcuni scenari in cui può verificarsi la perdita di dati.

 * **Sviluppare il sistema in modo che sia in grado** di gestire i blips di connessione a causa [dell'applicazione di patch e del failover.](cache-failover.md)

 * **Configurare [l'impostazione riservata maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) per migliorare la velocità di risposta** del sistema in condizioni di pressione della memoria.  Un'impostazione di prenotazione sufficiente è particolarmente importante per i carichi di lavoro con un elevato carico di scrittura o se si archiviano valori maggiori (100 KB o più) in Redis. Si dovrebbe iniziare con il 10% delle dimensioni della cache e aumentare questa percentuale se si dispone di carichi in scrittura pesante.

 * **Redis funziona meglio con valori più piccoli,** quindi prendi in considerazione la possibilità di tagliare i dati più grandi in più chiavi.  In [questa discussione Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)sono elencate alcune considerazioni che è necessario considerare attentamente.  Leggere [in questo articolo](cache-troubleshoot-client.md#large-request-or-response-size) per un problema di esempio che può essere causato da valori di grandi dimensioni.

 * **Individuare l'istanza della cache e l'applicazione nella stessa area.**  La connessione a una cache in un'area diversa può aumentare in modo significativo la latenza e ridurre l'affidabilità.  Sebbene sia possibile connettersi dall'esterno di Azure, non è consigliabile *soprattutto quando si usa Redis come cache.*  Se si usa Redis come solo archivio chiave/valore, la latenza potrebbe non essere il problema principale. 

 * **Riutilizzare le connessioni.**  La creazione di nuove connessioni è costosa e aumenta la latenza, quindi riutilizzare le connessioni il più possibile. Se si sceglie di creare nuove connessioni, assicurarsi di chiudere le connessioni precedenti prima di rilasciarle (anche nei linguaggi di memoria gestita come .NET o Java).

 * **Configurare la libreria client per l'utilizzo di un timeout di *connessione* di almeno 15 secondi,** offrendo al sistema l'ora di connettersi anche in condizioni di CPU più elevate.  Un valore di timeout della connessione di piccole dimensioni non garantisce che la connessione venga stabilita in tale intervallo di tempo.  Se qualcosa va storto (ALTA CPU client, CPU del server elevata e così via), un breve valore di timeout di connessione causerà l'esito negativo del tentativo di connessione. Questo comportamento spesso peggiora una brutta situazione.  Invece di aiutare, timeout più brevi aggravano il problema forzando il sistema a riavviare il processo di tentativo di riconnessione, che può portare a un ciclo di tentativi di *connessione > >.* In genere si consiglia di lasciare il timeout di connessione a 15 secondi o superiore. È preferibile lasciare che il tentativo di connessione abbia esito positivo dopo 15 o 20 secondi piuttosto che non riuscire rapidamente solo per riprovare. Tale ciclo di tentativi può causare l'interruzione di un'interruzione di più tempo rispetto a quando si lascia che il sistema richiede più tempo inizialmente.  
     > [!NOTE]
     > Queste indicazioni sono specifiche per il tentativo di *connessione* e non sono correlate al tempo che si è disposti ad attendere il completamento di *un'operazione* come GET o SET.
 
 * **Evitare operazioni costose:** alcune operazioni Redis, come il comando [KEYS,](https://redis.io/commands/keys) sono *molto* costose e devono essere evitate.  Per altre informazioni, vedere alcune considerazioni sui comandi a esecuzione prolungataFor more information, see some considerations around [long-running commands](cache-troubleshoot-server.md#long-running-commands)

 * **Usa crittografia TLS:** la cache di Azure per Redis richiede comunicazioni crittografate TLS per impostazione predefinita.  TLS versioni 1.0, 1.1 e 1.2 sono attualmente supportate.  Tuttavia, TLS 1.0 e 1.1 si trovano su un percorso a livello di settore deprecazione, quindi usa TLS 1.2 se possibile.  Se la libreria o lo strumento client non supporta TLS, è possibile abilitare le connessioni non crittografate [tramite il portale](cache-configure.md#access-ports) di Azure o le API di [gestione.](https://docs.microsoft.com/rest/api/redis/redis/update)  In questi casi in cui le connessioni crittografate non sono possibili, è consigliabile inserire la cache e l'applicazione client in una rete virtuale.  Per ulteriori informazioni sulle porte utilizzate nello scenario della cache di rete virtuale, vedere questa [tabella](cache-how-to-premium-vnet.md#outbound-port-requirements).
 
## <a name="memory-management"></a>Gestione della memoria
Esistono diversi aspetti correlati all'utilizzo della memoria all'interno dell'istanza del server Redis che è possibile prendere in considerazione.  tra cui:

 * **Scegliere un criterio di [rimozione](https://redis.io/topics/lru-cache) adatto all'applicazione.**  I criteri predefiniti per Azure Redis sono *volatile-lru*, il che significa che solo le chiavi con un valore TTL impostato saranno idonee per lo sfratto.  Se nessuna chiave ha un valore TTL, il sistema non rimuovere alcuna chiave.  Se si desidera che il sistema permetta a qualsiasi chiave di essere rimossa se sotto pressione della memoria, allora si consiglia di prendere in considerazione il criterio *allkeys-lru.*

 * **Impostare un valore di scadenza per le chiavi.**  Una scadenza rimuoverà le chiavi in modo proattivo invece di attendere la pressione della memoria.  Quando lo sfratto non entra in gioco a causa della pressione della memoria, può causare un carico aggiuntivo sul server.  Per ulteriori informazioni, vedere la documentazione relativa ai comandi [EXPIRE](https://redis.io/commands/expire) ed [EXPIREAT.](https://redis.io/commands/expireat)
 
## <a name="client-library-specific-guidance"></a>Indicazioni specifiche della libreria client
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - Quale client devo usare?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lattuga (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [Php](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net provider dello stato sessione](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando è sicuro riprovare?
Sfortunatamente, non c'è una risposta facile.  Ogni applicazione deve decidere quali operazioni possono essere ritentate e quali no.  Ogni operazione ha requisiti diversi e dipendenze tra chiavi.  Ecco alcune cose che potresti prendere in considerazione:

 * È possibile ottenere errori sul lato client anche se Redis ha eseguito correttamente il comando richiesto per l'esecuzione.  Ad esempio:
     - I timeout sono un concetto lato client.  Se l'operazione ha raggiunto il server, il server eseguirà il comando anche se il client rinuncia all'attesa.  
     - Quando si verifica un errore nella connessione socket, non è possibile sapere se l'operazione è effettivamente eseguita sul server.  Ad esempio, l'errore di connessione può verificarsi dopo che il server ha elaborato la richiesta ma prima che il client riceva la risposta.
 *  Come reagisce la mia applicazione se eseguo accidentalmente la stessa operazione due volte?  Ad esempio, cosa succede se si incrementa un numero intero due volte anziché una volta?  La mia applicazione sta scrivendo nella stessa chiave da più posizioni?  Cosa succede se la logica dei tentativi sovrascrive un valore impostato da un'altra parte dell'app?

Se si desidera testare il funzionamento del codice in condizioni di errore, prendere in considerazione l'utilizzo della [funzionalità di riavvio](cache-administration.md#reboot). Il riavvio consente di vedere in che modo i problemi di connessione influiscono sull'applicazione.

## <a name="performance-testing"></a>Test delle prestazioni
 * **Iniziare utilizzando `redis-benchmark.exe` ** per ottenere un'idea di possibile velocità effettiva/latenza prima di scrivere i propri test perf.  La documentazione Redis-benchmark è [disponibile qui](https://redis.io/topics/benchmarks).  Si noti che redis-benchmark non supporta SSL, pertanto sarà necessario [abilitare la porta non SSL tramite il portale](cache-configure.md#access-ports) prima di eseguire il test.  [Una versione compatibile con Windows di redis-benchmark.exe è disponibile qui](https://github.com/MSOpenTech/redis/releases)
 * La macchina virtuale client usata per il test deve trovarsi **nella stessa area dell'istanza** della cache Redis.The client VM used for testing should be in the same region as your Redis cache instance.
 * Si consiglia di **utilizzare Dv2 VM Series** per il client in quanto dispongono di hardware migliore e daranno i migliori risultati.
 * Assicurarsi che la macchina virtuale client usata disponga di*almeno la quantità di calcolo e larghezza di banda* della cache da testare. 
 * **Abilitare VRSS** nel computer client se si utilizza Windows.  [Per informazioni dettagliate, vedere qui](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Script di PowerShell di esempio:Example powershell script:
     >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdapterRSS -Name ( Get-NetAdapter). Nome 
     
 * **Prendere in considerazione l'utilizzo di istanze Redis livello Premium**.  Queste dimensioni della cache avranno una migliore latenza di rete e velocità effettiva perché sono in esecuzione su hardware migliore sia per la CPU che per la rete.
 
     > [!NOTE]
     > I risultati delle prestazioni osservati sono [pubblicati qui](cache-faq.md#azure-cache-for-redis-performance) per il vostro riferimento.   Inoltre, tenere presente che SSL/TLS aggiunge un certo sovraccarico, in modo che si possono ottenere diverse latenze e/o velocità effettiva se si utilizza la crittografia di trasporto.
 
### <a name="redis-benchmark-examples"></a>Esempi di Redis-Benchmark
**Pre-test setup**: Preparare l'istanza della cache con i dati necessari per i comandi di test di latenza e velocità effettiva elencati di seguito.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**Per testare la latenza**: Testare le richieste GET utilizzando un payload di 1k.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Per testare la velocità effettiva:To test throughput:** Richieste GET pipeline con payload 1k.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50
