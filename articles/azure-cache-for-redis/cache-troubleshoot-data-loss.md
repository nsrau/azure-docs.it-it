---
title: Risolvere i problemi di perdita di dati in cache di Azure per Redis
description: Informazioni su come risolvere i problemi di perdita di dati con la cache di Azure per Redis, ad esempio perdita parziale di chiavi, scadenza delle chiavi o perdita completa delle chiavi.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530902"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Risolvere i problemi di perdita di dati in cache di Azure per Redis

Questo articolo illustra come diagnosticare le perdite di dati effettive o percepite che potrebbero verificarsi nella cache di Azure per Redis.This article discusses how to diagnose actual or perceived data loss that might occur in Azure Cache for Redis.

> [!NOTE]
> Diversi passaggi per la risoluzione dei problemi illustrati in questa guida includono istruzioni per eseguire comandi di Redis e monitorare svariate metriche delle prestazioni. Per altre informazioni e istruzioni, vedere gli articoli della sezione [Informazioni aggiuntive](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Perdita parziale delle chiavi

La cache di Azure per Redis non elimina in modo casuale le chiavi dopo che sono state archiviate in memoria. Tuttavia, rimuove le chiavi in risposta ai criteri di scadenza o rimozione e ai comandi di eliminazione esplicita delle chiavi. Anche le chiavi scritte nel nodo master in un'istanza Premium o Standard di Azure per Redis potrebbero non essere immediatamente disponibili in una replica. I dati vengono replicati dal master alla replica in modo asincrono e non bloccante.

Se le chiavi sono scomparse dalla cache, verificare le seguenti possibili cause:

| Causa | Descrizione |
|---|---|
| [Scadenza della chiave](#key-expiration) | Le chiavi vengono rimosse a causa di timeout impostati su di essi. |
| [Sfratto chiave](#key-eviction) | Le chiavi vengono rimosse sotto pressione della memoria. |
| [Cancellazione della chiave](#key-deletion) | Le chiavi vengono rimosse da comandi di eliminazione espliciti. |
| [Replica asincrona](#async-replication) | Le chiavi non sono disponibili in una replica a causa di ritardi nella replica dei dati. |

### <a name="key-expiration"></a>Scadenza della chiave

La cache di Azure per Redis rimuove automaticamente una chiave se alla chiave viene assegnato un timeout e tale periodo è passato. Per ulteriori informazioni sulla scadenza della chiave Redis, vedere la documentazione del comando [EXPIRE.](https://redis.io/commands/expire) I valori di timeout possono essere impostati anche utilizzando i comandi [SET](https://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset)e altri ** \*** comandi STORE.

Per ottenere statistiche sul numero di chiavi scadute, utilizzare il comando [INFO.](https://redis.io/commands/info) La `Stats` sezione mostra il numero totale di chiavi scadute. La `Keyspace` sezione fornisce ulteriori informazioni sul numero di chiavi con timeout e il valore medio di timeout.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

È anche possibile esaminare le metriche di diagnostica per la cache, per verificare se esiste una correlazione tra il momento in cui la chiave è scaduta e un picco nelle chiavi scadute. Vedere l'Appendice di [Debug Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) per informazioni sull'utilizzo delle notifiche dello spazio dei tasti o **MONITOR** per eseguire il debug di questi tipi di problemi.

### <a name="key-eviction"></a>Sfratto chiave

La cache di Azure per Redis richiede spazio di memoria per archiviare i dati. Elimina le chiavi per liberare memoria disponibile quando necessario. Quando i valori **di used_memory** o **used_memory_rss** nel comando [INFO](https://redis.io/commands/info) si avvicinano all'impostazione **maxmemory** configurata, la cache di Azure per Redis inizia a rimuovere le chiavi dalla memoria in base ai criteri di [cache.](https://redis.io/topics/lru-cache)

È possibile monitorare il numero di chiavi rimosse utilizzando il comando [INFO:](https://redis.io/commands/info)

```
# Stats

evicted_keys:13224
```

È anche possibile esaminare le metriche di diagnostica per la cache, per verificare se esiste una correlazione tra il momento in cui la chiave è stata persa e un picco nelle chiavi rimosse. Vedere l'Appendice di [Debug Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) per informazioni sull'utilizzo delle notifiche dello spazio dei tasti o **MONITOR** per eseguire il debug di questi tipi di problemi.

### <a name="key-deletion"></a>Cancellazione della chiave

I client Redis possono eseguire il comando [DEL](https://redis.io/commands/del) o [HDEL](https://redis.io/commands/hdel) per rimuovere in modo esplicito le chiavi dalla cache di Azure per Redis. È possibile tenere traccia del numero di operazioni di eliminazione utilizzando il comando [INFO.](https://redis.io/commands/info) Se i comandi **DEL** o **HDEL** sono stati `Commandstats` chiamati, verranno elencati nella sezione.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replica asincrona

Qualsiasi cache di Azure per l'istanza Redis nel livello Standard o Premium è configurata con un nodo master e almeno una replica. I dati vengono copiati dal master in modo asincrono a una replica utilizzando un processo in background. Il [sito Web redis.io](https://redis.io/topics/replication) descrive il funzionamento generale della replica dei dati Redis. Per gli scenari in cui i client scrivono frequentemente in Redis, può verificarsi una perdita parziale di dati perché questa replica è garantita per essere istantanea. Ad esempio, se il master si *arresta dopo* che un client scrive una chiave, ma *prima* che il processo in background abbia la possibilità di inviare tale chiave alla replica, la chiave viene persa quando la replica assume il controllo come nuovo master.

## <a name="major-or-complete-loss-of-keys"></a>Perdita maggiore o completa delle chiavi

Se la maggior parte o tutte le chiavi sono scomparse dalla cache, verificare le seguenti possibili cause:

| Causa | Descrizione |
|---|---|
| [Consuntivazione delle chiavi](#key-flushing) | Le chiavi sono state eliminate manualmente. |
| [Selezione del database non corretta](#incorrect-database-selection) | La cache di Azure per Redis è impostata per l'uso di un database non predefinito. |
| [Errore dell'istanza RedisRedis instance failure](#redis-instance-failure) | Il server Redis non è disponibile. |

### <a name="key-flushing"></a>Consuntivazione delle chiavi

I client possono chiamare il comando [FLUSHDB](https://redis.io/commands/flushdb) per rimuovere tutte le chiavi in un *singolo* database o [FLUSHALL](https://redis.io/commands/flushall) per rimuovere tutte le chiavi da *tutti i* database in una cache Redis. Per scoprire se le chiavi sono state scaricate, utilizzare il comando [INFO.](https://redis.io/commands/info) La `Commandstats` sezione indica se uno dei due comandi **FLUSH** è stato chiamato:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Selezione del database non corretta

La cache di Azure per Redis usa il database **db0** per impostazione predefinita. Se si passa a un altro database (ad esempio, **db1**) e si tenta di leggere le chiavi da esso, la cache di Azure per Redis non le troverà. Ogni database è un'unità logicamente separata e contiene un set di dati diverso. Utilizzare il comando [SELECT](https://redis.io/commands/select) per utilizzare altri database disponibili e cercare le chiavi in ognuno di essi.

### <a name="redis-instance-failure"></a>Errore dell'istanza RedisRedis instance failure

Redis è un archivio dati in memoria. I dati vengono mantenuti nelle macchine fisiche o virtuali che ospitano la cache Redis. Un'istanza cache di Azure per Redis nel livello Basic viene eseguita in una sola macchina virtuale (VM). Se la macchina virtuale è inattivo, tutti i dati archiviati nella cache vengono persi. 

Le cache nei livelli Standard e Premium offrono una resilienza molto maggiore contro la perdita di dati usando due macchine virtuali in una configurazione replicata. Quando il nodo master in tale cache ha esito negativo, il nodo di replica assume il controllo per fornire automaticamente i dati. Queste macchine virtuali si trovano in domini separati per gli errori e gli aggiornamenti, per ridurre al minimo la possibilità che entrambe diventino non disponibili contemporaneamente. Se si verifica un'interruzione principale del data center, tuttavia, le macchine virtuali potrebbero comunque andare giù insieme. I tuoi dati andranno persi in questi rari casi.

Valutare la possibilità di usare la persistenza dei [dati Redis](https://redis.io/topics/persistence) e la [replica geografica](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) per migliorare la protezione dei dati da questi errori dell'infrastruttura.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Risolvere i problemi del lato server di cache di Azure per Redis](cache-troubleshoot-server.md)
- [Quali offerte e dimensioni di Cache Redis di Azure è consigliabile usare?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md)
- [Come si eseguono i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands)
