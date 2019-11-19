---
title: Risolvere i problemi di perdita di dati in cache di Azure per Redis
description: Informazioni su come risolvere i problemi di perdita dei dati con cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 77493675de0a654d3bb510f7cda22a2abbca0aa2
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121503"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Risolvere i problemi di perdita di dati in cache di Azure per Redis

Questo articolo illustra come diagnosticare le perdite di dati effettive o percepite che potrebbero verificarsi in cache di Azure per Redis.

> [!NOTE]
> Diversi passaggi per la risoluzione dei problemi illustrati in questa guida includono istruzioni per eseguire comandi di Redis e monitorare svariate metriche delle prestazioni. Per altre informazioni e istruzioni, vedere gli articoli della sezione [Informazioni aggiuntive](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Perdita parziale di chiavi

Cache di Azure per Redis non elimina in modo casuale le chiavi dopo che sono state archiviate in memoria. Tuttavia, rimuove le chiavi in risposta ai criteri di scadenza o rimozione e ai comandi espliciti di eliminazione della chiave. Anche le chiavi che sono state scritte nel nodo master in una cache di Azure Premium o standard per l'istanza di redis potrebbero non essere disponibili in una replica immediatamente. I dati vengono replicati dal master alla replica in modo asincrono e non bloccante.

Se si ritiene che le chiavi siano scomparse dalla cache, verificare le possibili cause seguenti:

| Causa | DESCRIZIONE |
|---|---|
| [Scadenza chiave](#key-expiration) | Le chiavi vengono rimosse a causa dei timeout impostati su di essi. |
| [Rimozione della chiave](#key-eviction) | Le chiavi vengono rimosse in condizioni di memoria insufficiente. |
| [Eliminazione della chiave](#key-deletion) | Le chiavi vengono rimosse dai comandi DELETE espliciti. |
| [Replica asincrona](#async-replication) | Le chiavi non sono disponibili in una replica a causa di ritardi di replica dei dati. |

### <a name="key-expiration"></a>Scadenza chiave

Cache di Azure per Redis rimuove automaticamente una chiave se alla chiave viene assegnato un timeout e tale periodo è scaduto. Per ulteriori informazioni sulla scadenza della chiave Redis, vedere la documentazione relativa alla [scadenza](http://redis.io/commands/expire) del comando. È anche possibile impostare valori di timeout usando i comandi [set](http://redis.io/commands/set), [setex](https://redis.io/commands/setex), [gets](https://redis.io/commands/getset)e other **\*Store** .

Per ottenere statistiche sul numero di chiavi scadute, usare il comando [info](http://redis.io/commands/info) . Nella sezione `Stats` viene visualizzato il numero totale di chiavi scadute. Nella sezione `Keyspace` vengono fornite ulteriori informazioni sul numero di chiavi con timeout e il valore di timeout medio.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

È anche possibile esaminare le metriche di diagnostica per la cache, per verificare se esiste una correlazione tra il momento in cui la chiave è mancante e un picco nelle chiavi scadute. Per informazioni sull'uso delle notifiche dello spazio dei dati o del **monitoraggio** per eseguire il debug di questi tipi di problemi, vedere l'appendice relativa al [debug dei mancati riscontri di spazio](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) .

### <a name="key-eviction"></a>Rimozione della chiave

Cache di Azure per Redis richiede spazio di memoria per archiviare i dati. Elimina le chiavi per liberare memoria disponibile quando necessario. Quando i valori **used_memory** o **Used_memory_rss** nel comando [info](http://redis.io/commands/info) si avvicinano all'impostazione **MaxMemory** configurata, cache di Azure per Redis inizia a rimuovere le chiavi dalla memoria in base ai [criteri della cache](http://redis.io/topics/lru-cache).

È possibile monitorare il numero di chiavi eliminate usando il comando [info](http://redis.io/commands/info) :

```
# Stats

evicted_keys:13224
```

È anche possibile esaminare le metriche di diagnostica per la cache, per verificare se esiste una correlazione tra il momento in cui la chiave è mancante e un picco nelle chiavi rimosse. Per informazioni sull'uso delle notifiche dello spazio dei dati o del **monitoraggio** per eseguire il debug di questi tipi di problemi, vedere l'appendice relativa al [debug dei mancati riscontri di spazio](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) .

### <a name="key-deletion"></a>Eliminazione della chiave

I client Redis possono eseguire il comando [del](http://redis.io/commands/del) o [HDEL](http://redis.io/commands/hdel) per rimuovere in modo esplicito le chiavi dalla cache di Azure per Redis. È possibile tenere traccia del numero di operazioni di eliminazione usando il comando [info](http://redis.io/commands/info) . Se sono stati chiamati i comandi **del** o **HDEL** , verranno elencati nella sezione `Commandstats`.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replica asincrona

Qualsiasi istanza di cache di Azure per Redis nel livello standard o Premium è configurata con un nodo master e almeno una replica. I dati vengono copiati dal database master a una replica in modo asincrono tramite un processo in background. Il sito Web di [Redis.io](http://redis.io/topics/replication) descrive il funzionamento generale della replica dei dati di Redis. Per gli scenari in cui i client scrivono spesso in Redis, è possibile che si verifichi una perdita parziale dei dati perché questa replica è sicuramente immediata. Se, ad esempio, il master diventa inattivo *dopo* che un client scrive una chiave, ma *prima* che il processo in background abbia la possibilità di inviare tale chiave alla replica, la chiave viene persa quando la replica diventa il nuovo master.

## <a name="major-or-complete-loss-of-keys"></a>Perdita di chiavi principale o completa

Se la maggior parte o tutte le chiavi sono scomparse dalla cache, verificare le possibili cause seguenti:

| Causa | DESCRIZIONE |
|---|---|
| [Scaricamento della chiave](#key-flushing) | Le chiavi sono state eliminate manualmente. |
| [Selezione database non corretta](#incorrect-database-selection) | Cache di Azure per Redis è impostato per l'uso di un database non predefinito. |
| [Errore dell'istanza Redis](#redis-instance-failure) | Il server Redis non è disponibile. |

### <a name="key-flushing"></a>Scaricamento della chiave

I client possono chiamare il comando [FLUSHDB](http://redis.io/commands/flushdb) per rimuovere tutte le chiavi in un *singolo* database o [FLUSHALL](http://redis.io/commands/flushall) per rimuovere tutte le chiavi da *tutti i* database in una cache Redis. Per determinare se le chiavi sono state scaricate, usare il comando [info](http://redis.io/commands/info) . La sezione `Commandstats` indica se è stato chiamato il comando di **scaricamento** :

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Selezione database non corretta

Per impostazione predefinita, cache di Azure per Redis usa il database **DB0** . Se si passa a un altro database, ad esempio **DB1**, e si prova a leggere le chiavi, cache di Azure per Redis non le troverà. Ogni database è un'unità separata logicamente e include un set di dati diverso. Usare il comando [Select](http://redis.io/commands/select) per usare altri database disponibili e cercare le chiavi in ognuna di esse.

### <a name="redis-instance-failure"></a>Errore dell'istanza Redis

Redis è un archivio dati in memoria. I dati vengono conservati nelle macchine fisiche o virtuali che ospitano la cache Redis. Una cache di Azure per l'istanza di redis nel livello Basic viene eseguita solo su una singola macchina virtuale (VM). Se la macchina virtuale è inattiva, tutti i dati archiviati nella cache andranno perduti. 

Le cache nei livelli standard e Premium offrono una resilienza molto maggiore rispetto alla perdita di dati usando due macchine virtuali in una configurazione replicata. Quando il nodo master in una cache di questo tipo ha esito negativo, il nodo di replica acquisisce automaticamente i dati. Queste macchine virtuali si trovano in domini distinti per gli errori e gli aggiornamenti, per ridurre al minimo le possibilità di diventare non disponibili contemporaneamente. Se si verifica un'interruzione del data center principale, tuttavia, è possibile che le macchine virtuali si trovino ancora. I dati andranno persi in questi rari casi.

Si consiglia di usare la [persistenza dei dati Redis](http://redis.io/topics/persistence) e la [replica geografica](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) per migliorare la protezione dei dati in base a questi errori di infrastruttura.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Risolvere i problemi del lato server di cache di Azure per il server Redis](cache-troubleshoot-server.md)
- [Quali offerte e dimensioni della Cache Redis è consigliabile usare?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md)
- [Come si eseguono i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands)
