---
title: Le procedure consigliate per la Cache di Azure per Redis
description: Informazioni su come usare la Cache di Azure per Redis in modo efficace seguendo queste procedure consigliate.
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
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452462"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Le procedure consigliate per la Cache di Azure per Redis 
Seguendo queste procedure consigliate, è possibile aumentarne le prestazioni e la convenienza di Cache di Azure per l'istanza di Redis.

## <a name="configuration-and-concepts"></a>Configurazione e concetti
 * **Usare il livello Standard o Premium per i sistemi di produzione.**  Il livello Basic è un sistema a nodo singolo senza replica dei dati e alcun contratto di servizio. Usare almeno una cache di livello C1.  Cache C0 sono pensate per gli scenari di sviluppo e test semplice, perché è disponibile un core CPU condiviso, poca memoria e sono soggetti a problemi di "noisy neighbor".

 * **Tenere presente che Redis è un archivio dati in memoria.**  [Questo articolo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) descrive alcuni scenari in cui può verificarsi una perdita di dati.

 * **Sviluppare il sistema in modo che possa gestire problemi di connessione** [a causa dell'applicazione di patch e il failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Configurare le [impostazione maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) per migliorare la reattività del sistema** sotto pressione della memoria.  Questa impostazione è particolarmente importante per i carichi di lavoro con intensa attività di scrittura o se si archiviano valori più grandi (100 KB o superiore) in Redis.  Sarebbe consigliabile iniziare con 10% delle dimensioni della cache, quindi aumentare se hai carichi di scrittura pesanti. Visualizzare [alcune considerazioni](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) quando si seleziona un valore.

 * **Redis funziona meglio con i valori minori**, pertanto è consigliabile suddividere i dati più grandi in più chiavi.  Nelle [questa discussione di Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), sono elencate alcune considerazioni che è opportuno considerare con attenzione.  Leggere [in questo articolo](cache-how-to-troubleshoot.md#large-requestresponse-size) per un problema di esempio che può essere causato da valori di grandi dimensioni.

 * **Individuare l'istanza di cache e l'applicazione nella stessa area.**  Connessione a una cache in un'area diversa può significativamente aumentare la latenza e ridurre affidabilità.  Benché sia possibile connettersi dall'esterno di Azure, questa non è consigliabile *soprattutto quando si usa Redis come cache*.  Se si usa Redis come semplicemente un archivio chiave/valore, la latenza potrebbe non essere la principale preoccupazione. 

 * **Riutilizzare le connessioni** -creazione di nuove connessioni è costosa e aumenta la latenza, quindi, riutilizzare quanto più possibile le connessioni. Se si sceglie di creare nuove connessioni, assicurarsi di chiudere le connessioni precedenti prima di rilasciare loro (anche in lingue di memoria gestita, ad esempio .NET o Java).

 * **Configurare la libreria client di usare una *timeout della connessione* di almeno 15 secondi**, fornendo l'ora di sistema per la connessione anche in condizioni di CPU superiore.  Un valore di timeout di connessione di piccole dimensioni non garantisce che la connessione è stabilita entro tale periodo di tempo.  Se qualcosa va errato (client elevato della CPU, della CPU del server elevato e così via), quindi un valore di timeout di connessione di breve causerà il tentativo di connessione avere esito negativo. Questo comportamento rende spesso peggiore una situazione non valido.  Invece, per aiutarti a timeout più breve aggravare il problema, forzando il sistema di riavviare il processo quando si tenta di ristabilire la connessione, che può portare a un *connect -> esito negativo -> di ripetizione dei tentativi* ciclo. In genere, si consiglia di lasciare la connessione di Timeout di almeno 15 secondi. È preferibile lasciare che sia il tentativo di connessione abbia esito positivo dopo 15 o 20 secondi anziché in modo che non riuscire rapidamente solo tentativo. Un ciclo di tentativi può causare l'interruzione del servizio duri più a lungo se si lascia il sistema sufficiente richiedere più inizialmente.  
     > [!NOTE]
     > Queste indicazioni sono specifiche per il *tentativo di connessione* e non correlato al tempo di cui si è disposti ad attendere un' *operazione* , ad esempio GET o SET per il completamento.
 

 * **Evitare i comandi dispendiosi** -alcune redis le operazioni, ad esempio il [comando KEYS](https://redis.io/commands/keys), sono *molto* dispendiosa e deve essere evitata.  Per altre informazioni, vedere [alcune considerazioni per i comandi dispendiosi](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>Gestione della memoria
Esistono diversi aspetti correlati per l'istanza del server Redis che è possibile prendere in considerazione l'utilizzo della memoria.  Ecco alcuni:

 * **Scegliere un' [criteri di rimozione](https://redis.io/topics/lru-cache) che funziona per l'applicazione.**  I criteri predefiniti per Redis di Azure viene *volatile-lru*, il che significa che solo le chiavi con una durata (TTL) valore set saranno idoneo per l'eliminazione.  Se nessuna chiave hanno un valore di durata (TTL), il sistema non rimuove le chiavi.  Se si desidera che il sistema per consentire un tasto qualsiasi per essere eliminata se eccessivo della memoria, quindi è possibile prendere in considerazione la *allkeys-lru* criteri.

 * **Impostare un valore di scadenza per le chiavi.**  Questa operazione rimuoverà le chiavi in modo proattivo anziché attendere fino a quando non è presente un utilizzo elevato della memoria.  Quando rimozione avviare a causa di richieste di memoria, comporta un carico aggiuntivo sul server.  Per altre informazioni, vedere la documentazione per il [Expire](https://redis.io/commands/expire) e [ExpireAt](https://redis.io/commands/expireat) comandi.
 
## <a name="client-library-specific-guidance"></a>Indicazioni specifiche della libreria del client
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - client è consigliabile usare?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lattuga (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Provider di stato della sessione Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando è sicuro provare a?
Non esiste alcuna risposta semplice.  Ogni applicazione deve decidere quali operazioni possono essere ripetute e quali invece non possono.  Ogni operazione presenta diversi requisiti e le dipendenze tra le chiavi.  Ecco alcune operazioni che si consiglia di:

 * È possibile ottenere errori sul lato client anche se Redis è stato eseguito il comando che è richiesto per l'esecuzione.  Ad esempio:
     - I timeout sono un concetto lato client.  Se l'operazione ha raggiunto il server, il server eseguirà il comando anche se il client di rinuncia in attesa.  
     - Quando si verifica un errore nella connessione socket, non è possibile sapere se l'operazione è effettivamente eseguito nel server.  Ad esempio, l'errore di connessione può verificarsi dopo che la richiesta viene elaborata dal server, ma prima che la risposta viene ricevuta dal client.
 *  Come la mia applicazione reagire se viene accidentalmente eseguito due volte la stessa operazione?  Ad esempio, cosa accade se incrementare un valore integer due volte anziché una sola volta?  L'applicazione scrive la stessa chiave da più posizioni?  Cosa accade se la logica di ripetizione dei tentativi sovrascrive il valore impostato da altre parti della mia app?

Se si desidera testare il funzionamento del codice in condizioni di errore, è consigliabile usare la [funzionalità di riavvio](cache-administration.md#reboot). In questo modo è possibile visualizzare l'applicazione di effetti problemi di connessione.

## <a name="performance-testing"></a>Test delle prestazioni
 * **Utilizzare innanzitutto `redis-benchmark.exe`**  per acquisire familiarità con velocità effettiva/latenza possibile prima di scrivere il proprio le prestazioni del test.  Può essere la documentazione di redis-benchmark [disponibili qui](http://redis.io/topics/benchmarks).  Si noti che redis-benchmark non supporta SSL, pertanto è necessario [abilitare la porta Non SSL tramite il portale](cache-configure.md#access-ports) prima di eseguire il test.  [Una versione compatibile di windows di redis-benchmark.exe è disponibili qui](https://github.com/MSOpenTech/redis/releases)
 * Il macchina virtuale client usata per il test deve essere **nella stessa area** dell'istanza di cache Redis.
 * **È consigliabile usare VM serie Dv2** per il client poiché dispongono di hardware migliore e fornirà risultati ottimali.
 * Assicurarsi che il client dispone di macchine Virtuali è usare **almeno della quantità di calcolo e larghezza di banda* come cache sottoposto a test. 
 * **Abilitare VRSS** nel computer client se si usa Windows.  [Per informazioni dettagliate, vedere qui](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Script di powershell di esempio:
     >PowerShell - ExecutionPolicy Unrestricted Enable-NetAdapterRSS-Name (Get-NetAdapter). Nome 
     
 * **È consigliabile usare le istanze di Redis livello Premium**.  Queste dimensioni della cache includerà migliore latenza di rete e velocità effettiva perché vengono eseguiti su hardware migliori per CPU e rete.
 
     > [!NOTE]
     > Sono i risultati di prestazioni osservate [pubblicati qui](cache-faq.md#azure-cache-for-redis-performance) per riferimento.   Inoltre, tenere presente che SSL/TLS aggiunge un certo sovraccarico, in modo che è possibile ottenere latenze diversi e/o velocità effettiva, se si Usa crittografia del trasporto.
 
### <a name="redis-benchmark-examples"></a>Esempi di redis-Benchmark
**Il programma di installazione di pre-test**: Si preparerà l'istanza della cache con i dati necessari per la latenza e velocità effettiva di testing di comandi elencati di seguito.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**Per testare la latenza**: Si testerà le richieste GET usando un 1 payload k.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Per verificare la velocità effettiva:** Usa le richieste GET in pipeline con 1 payload k.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
