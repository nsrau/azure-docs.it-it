---
title: Procedure consigliate per cache di Azure per Redis
description: Informazioni su come usare la cache di Azure per Redis in modo efficace seguendo queste procedure consigliate.
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: 6ac4722c1253f97bfb8c232202e24a923c027edf
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018833"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Procedure consigliate per cache di Azure per Redis 
Seguendo queste procedure consigliate, è possibile ottimizzare le prestazioni e l'uso conveniente della cache di Azure per l'istanza di Redis.

## <a name="configuration-and-concepts"></a>Configurazione e concetti
 * **Usare il livello standard o Premium per i sistemi di produzione.**  Il livello Basic è un sistema a nodo singolo senza replica dei dati e nessun contratto di contratto. Usare almeno una cache di livello C1.  Le cache C0 sono destinate a scenari di sviluppo/test semplici poiché hanno un core CPU condiviso, poca memoria e sono soggette a problemi di "vicinanze rumorose".

 * **Tenere presente che Redis è un archivio dati in memoria.**  In [questo articolo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) vengono illustrati alcuni scenari in cui può verificarsi la perdita di dati.

 * **Sviluppare il sistema in modo da poter gestire i segnali di connessione** a [causa dell'applicazione di patch e del failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Configurare l' [impostazione MaxMemory-](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) reserved per migliorare la velocità di risposta del sistema** in condizioni di utilizzo elevato della memoria.  Questa impostazione è particolarmente importante per i carichi di lavoro con intensa attività di scrittura o se si archiviano valori più grandi (100 KB o più) in Redis.  Si consiglia di iniziare con il 10% delle dimensioni della cache, quindi aumentare se si dispone di carichi di lavoro pesanti. Vedere [alcune considerazioni](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) per la selezione di un valore.

 * **Redis funziona meglio con valori più piccoli**, quindi è consigliabile tagliare i dati più grandi in più chiavi.  In [questa discussione](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)di redis sono elencate alcune considerazioni che è opportuno considerare con attenzione.  Leggere [in questo articolo](cache-how-to-troubleshoot.md#large-requestresponse-size) per un problema di esempio che può essere causato da valori di grandi dimensioni.

 * **Individuare l'istanza della cache e l'applicazione nella stessa area.**  La connessione a una cache in un'area diversa può aumentare significativamente la latenza e ridurre l'affidabilità.  Sebbene sia possibile connettersi dall'esterno di Azure, non è consigliabile, *soprattutto quando si usa Redis come cache*.  Se si usa Redis solo come archivio chiave/valore, la latenza potrebbe non essere la preoccupazione principale. 

 * **Riutilizza connessioni** : la creazione di nuove connessioni è costosa e aumenta la latenza, quindi è possibile riutilizzare le connessioni il più possibile. Se si sceglie di creare nuove connessioni, assicurarsi di chiudere le connessioni precedenti prima di rilasciarle (anche in managed memory linguaggi come .NET o Java).

 * **Configurare la libreria client per l'uso di un *timeout di connessione* di almeno 15 secondi**, in modo da consentirne la connessione anche in condizioni di CPU più elevate.  Un valore di timeout di connessione ridotto non garantisce che la connessione venga stabilita in tale intervallo di tempo.  Se si verificano problemi (CPU client elevata, CPU server elevata e così via), un breve valore di timeout della connessione provocherà un tentativo di connessione non riuscito. Questo comportamento spesso peggiora negativamente.  Anziché aiutare, i timeout più brevi aggravano il problema forzando il sistema a riavviare il processo di tentativo di riconnessione, che può causare un ciclo di *tentativi di > di connessione->* . È in genere consigliabile lasciare il timeout della connessione a 15 secondi o superiore. È preferibile che il tentativo di connessione abbia esito positivo dopo 15 o 20 secondi rispetto alla mancata esecuzione rapida solo per tentativi. Un ciclo di ripetizione dei tentativi può causare un periodo di tempo più lungo rispetto a quando si lascia che il sistema riprenda inizialmente più a lungo.  
     > [!NOTE]
     > Queste linee guida sono specifiche del *tentativo di connessione* e non sono correlate al tempo di attesa per il completamento di un' *operazione* , ad esempio Get o set.
 

 * **Evitare costosi comandi** : alcune operazioni di redis, ad esempio il [comando Keys](https://redis.io/commands/keys), sono *molto* costose e dovrebbero essere evitate.  Per ulteriori informazioni, vedere [alcune considerazioni sui comandi costosi](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>Gestione della memoria
Ci sono diversi aspetti relativi all'utilizzo della memoria all'interno dell'istanza del server Redis che può essere opportuno prendere in considerazione.  Di seguito sono riportate alcune:

 * **Scegliere un [criterio di rimozione](https://redis.io/topics/lru-cache) adatto per l'applicazione.**  Il criterio predefinito per Redis di Azure è *volatile-LRU*, il che significa che solo le chiavi che hanno un valore TTL impostato saranno idonee per la rimozione.  Se nessuna chiave ha un valore TTL, il sistema non rimuoverà alcuna chiave.  Se si desidera che il sistema consenta la rimozione di qualsiasi chiave in caso di utilizzo eccessivo della memoria, è opportuno considerare il criterio *AllKeys-LRU* .

 * **Impostare un valore di scadenza sulle chiavi.**  In questo modo, le chiavi verranno rimosse in modo proattivo invece che in attesa fino a quando non si verificano  Quando l'eliminazione viene avviata a causa di un numero eccessivo di richieste di memoria, può causare un carico aggiuntivo sul server.  Per ulteriori informazioni, vedere la documentazione relativa ai comandi [Expire](https://redis.io/commands/expire) e [ExpireAt](https://redis.io/commands/expireat).
 
## <a name="client-library-specific-guidance"></a>Indicazioni specifiche per la libreria client
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java: quale client è necessario usare?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lattuga (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [JEDIS (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Provider di stato della sessione Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando è sicuro riprovare?
Sfortunatamente, non esiste una semplice risposta.  Ogni applicazione deve decidere quali operazioni possono essere ritentate e quali non possono.  Ogni operazione presenta requisiti diversi e dipendenze tra chiavi.  Ecco alcune considerazioni che è possibile considerare:

 * È possibile ottenere gli errori sul lato client anche se Redis ha eseguito correttamente il comando richiesto per l'esecuzione.  Ad esempio:
     - I timeout sono un concetto sul lato client.  Se l'operazione ha raggiunto il server, il server eseguirà il comando anche se il client lascia in attesa.  
     - Quando si verifica un errore nella connessione socket, non è possibile sapere se l'operazione è stata effettivamente eseguita nel server.  Ad esempio, l'errore di connessione può verificarsi dopo l'elaborazione della richiesta da parte del server, ma prima della ricezione della risposta da parte del client.
 *  In che modo l'applicazione risponde se viene eseguita accidentalmente la stessa operazione due volte?  Ad esempio, cosa accade se si incrementa un numero intero due volte anziché una sola volta?  L'applicazione scrive nella stessa chiave da più posizioni?  Cosa accade se la logica di ripetizione dei tentativi sovrascrive un valore impostato da un'altra parte dell'app?

Se si desidera testare il funzionamento del codice in condizioni di errore, è consigliabile utilizzare la [funzionalità di riavvio](cache-administration.md#reboot). In questo modo è possibile verificare il modo in cui i blip della connessione influiscono sull'applicazione.

## <a name="performance-testing"></a>Test delle prestazioni
 * **Iniziare usando `redis-benchmark.exe`**  per ottenere un'idea della velocità effettiva/latenza prima di scrivere i test delle prestazioni.  La documentazione di redis-benchmark è [disponibile qui](https://redis.io/topics/benchmarks).  Si noti che Redis-benchmark non supporta SSL, pertanto è necessario [abilitare la porta non SSL tramite il portale](cache-configure.md#access-ports) prima di eseguire il test.  [È possibile trovare una versione compatibile di Windows di redis-benchmark. exe qui](https://github.com/MSOpenTech/redis/releases)
 * La macchina virtuale client usata per il test deve trovarsi **nella stessa area** dell'istanza di cache Redis.
 * **È consigliabile usare la serie di macchine virtuali dv2** per il client poiché dispongono di hardware migliore e offriranno risultati ottimali.
 * Assicurarsi che la macchina virtuale client utilizzata includa **almeno la quantità di calcolo e larghezza di banda* della cache sottoposta a test. 
 * **Abilitare VRSS** nel computer client se si è in Windows.  [Per informazioni dettagliate, vedere qui](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Esempio di script di PowerShell:
     >PowerShell-ExecutionPolicy unstricted Enable-NetAdapterRSS-Name (Get-NetAdapter). Nome 
     
 * **Prendere in considerazione l'uso delle istanze di redis del livello Premium**.  Queste dimensioni della cache avranno una migliore latenza di rete e velocità effettiva, perché sono in esecuzione su hardware migliore per CPU e rete.
 
     > [!NOTE]
     > I risultati delle prestazioni osservati sono [pubblicati qui](cache-faq.md#azure-cache-for-redis-performance) per informazioni di riferimento.   Tenere inoltre presente che SSL/TLS aggiunge un overhead, pertanto è possibile ottenere latenze e/o velocità effettiva diverse se si utilizza la crittografia del trasporto.
 
### <a name="redis-benchmark-examples"></a>Redis-esempi di benchmark
**Installazione pre-test**: Verrà preparata l'istanza della cache con i dati necessari per i comandi di latenza e di test della velocità effettiva elencati di seguito.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**Per testare**la latenza: Questa operazione proverà le richieste GET usando un payload 1K.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Per testare la velocità effettiva:** Questo usa le richieste GET inviate tramite pipeline con il payload 1K.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
