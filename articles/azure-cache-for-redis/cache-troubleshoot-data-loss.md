---
title: Risolvere i problemi di perdita di dati in Cache Redis di Azure
description: Informazioni su come risolvere i problemi di perdita di dati in Cache Redis di Azure, ad esempio la perdita parziale di chiavi, la scadenza delle chiavi o la perdita completa di chiavi.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 29492ee6b7bce50c4807a36d0c252e18e6aadf87
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008951"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Risolvere i problemi di perdita di dati in Cache Redis di Azure

Questo articolo illustra come diagnosticare le perdite di dati effettive o percepite che potrebbero verificarsi in Cache Redis di Azure.

> [!NOTE]
> Diversi passaggi per la risoluzione dei problemi illustrati in questa guida includono istruzioni per eseguire comandi di Redis e monitorare svariate metriche delle prestazioni. Per altre informazioni e istruzioni, vedere gli articoli della sezione [Informazioni aggiuntive](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Perdita parziale di chiavi

Cache Redis di Azure non elimina in modo casuale le chiavi una volta archiviate in memoria. Tuttavia, rimuove le chiavi in risposta ai criteri di scadenza o rimozione e ai comandi espliciti di eliminazione della chiave. Anche le chiavi che sono state scritte nel nodo primario in una cache di Azure Premium o standard per l'istanza di redis potrebbero non essere disponibili in una replica immediatamente. I dati vengono replicati dal database primario alla replica in modo asincrono e non bloccante.

Se si ritiene che delle chiavi siano scomparse dalla cache, verificare le possibili cause seguenti:

| Causa | Descrizione |
|---|---|
| [Scadenza della chiave](#key-expiration) | Le chiavi vengono rimosse a causa dei timeout impostati. |
| [Rimozione della chiave](#key-eviction) | Le chiavi vengono rimosse in condizioni di utilizzo elevato della memoria. |
| [Eliminazione della chiave](#key-deletion) | Le chiavi vengono rimosse con comandi di eliminazione espliciti. |
| [Replica asincrona](#async-replication) | Le chiavi non sono disponibili in una replica a causa di ritardi nella replica dei dati. |

### <a name="key-expiration"></a>Scadenza della chiave

Cache Redis di Azure rimuove automaticamente una chiave se le è stato assegnato un timeout e il periodo è scaduto. Per altre informazioni sulla scadenza della chiave Redis, vedere la documentazione del comando [EXPIRE](https://redis.io/commands/expire). I valori di timeout possono essere impostati anche usando [SET](https://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset) e altri comandi **\*STORE**.

Per ottenere statistiche sul numero di chiavi scadute, usare il comando [INFO](https://redis.io/commands/info). La sezione `Stats` mostra il numero totale di chiavi scadute. La sezione `Keyspace` fornisce ulteriori informazioni sul numero di chiavi con timeout e il valore di timeout medio.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

È anche possibile esaminare le metriche di diagnostica per la cache, per verificare se esista una correlazione tra il momento in cui la chiave è scomparsa e un picco di scadenza delle chiavi. Per informazioni sull'uso delle notifiche dello spazio delle chiavi o di **MONITOR** per il debug dei problemi di questo tipo, vedere l'appendice di [Debugging Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) (Debug dei mancati riscontri nello spazio delle chiavi Redis).

### <a name="key-eviction"></a>Rimozione della chiave

Cache Redis di Azure per Redis richiede spazio di memoria per archiviare i dati. Quando necessario, elimina le chiavi per liberare memoria disponibile. Quando i valori **used_memory** o **used_memory_rss** nel comando [INFO](https://redis.io/commands/info) si avvicinano all'impostazione **maxmemory** configurata, Cache Redis di Azure avvia la rimozione delle chiavi dalla memoria in base ai [criteri della cache](https://redis.io/topics/lru-cache).

È possibile monitorare il numero di chiavi rimosse usando il comando [INFO](https://redis.io/commands/info):

```
# Stats

evicted_keys:13224
```

È anche possibile esaminare le metriche di diagnostica per la cache, per verificare se esista una correlazione tra il momento in cui la chiave è scomparsa e un picco di rimozione delle chiavi. Per informazioni sull'uso delle notifiche dello spazio delle chiavi o di **MONITOR** per il debug dei problemi di questo tipo, vedere l'appendice di [Debugging Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) (Debug dei mancati riscontri nello spazio delle chiavi Redis).

### <a name="key-deletion"></a>Eliminazione della chiave

I client Redis possono eseguire il comando [DEL](https://redis.io/commands/del) o [HDEL](https://redis.io/commands/hdel) per rimuovere in modo esplicito le chiavi dalla cache di Azure per Redis. Si può tenere traccia del numero di operazioni di eliminazione usando il comando [INFO](https://redis.io/commands/info). Se sono stati chiamati comandi **DEL** o **HDEL**, verranno elencati nella sezione `Commandstats`.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replica asincrona

Qualsiasi istanza di cache di Azure per Redis nel livello standard o Premium è configurata con un nodo primario e almeno una replica. I dati vengono copiati dal database primario a una replica in modo asincrono tramite un processo in background. Il sito Web [redis.io](https://redis.io/topics/replication) descrive il funzionamento generale della replica dei dati di Redis. Per gli scenari in cui i client scrivono spesso in Redis, è possibile che si verifichi una perdita parziale dei dati perché non è garantito che la replica sia istantanea. Se, ad esempio, il database primario si arresta *dopo* che un client scrive una chiave, ma *prima* che il processo in background abbia la possibilità di inviare tale chiave alla replica, la chiave viene persa quando la replica diventa il nuovo database primario.

## <a name="major-or-complete-loss-of-keys"></a>Perdita importante o totale delle chiavi

Se la maggior parte o tutte le chiavi sono scomparse dalla cache, verificare le possibili cause seguenti:

| Causa | Descrizione |
|---|---|
| [Scaricamento delle chiavi](#key-flushing) | Le chiavi sono state eliminate manualmente. |
| [Selezione del database errato](#incorrect-database-selection) | Cache Redis di Azure per Redis è impostato per l'uso di un database non predefinito. |
| [Errore dell'istanza di Redis](#redis-instance-failure) | Il server Redis non è disponibile. |

### <a name="key-flushing"></a>Scaricamento delle chiavi

I client possono chiamare il comando [FLUSHDB](https://redis.io/commands/flushdb) per rimuovere tutte le chiavi in un *unico* database o [FLUSHALL](https://redis.io/commands/flushall) per rimuovere tutte le chiavi da *tutti* i database in una cache Redis. Per verificare se le chiavi sono state scaricate, usare il comando [INFO](https://redis.io/commands/info). La sezione `Commandstats` indica se è stato chiamato il comando **FLUSH**:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Selezione del database errato

Cache Redis di Azure usa per impostazione predefinita il database **db0**. Se si passa a un altro database, ad esempio **db1**, e si prova a leggere le chiavi da esso, Cache Redis di Azure non le troverà. Ogni database è un'unità separata logicamente e include un set di dati diverso. Usare il comando [SELECT](https://redis.io/commands/select) comando per usare altri database disponibili e cercare le chiavi in ognuno di essi.

### <a name="redis-instance-failure"></a>Errore dell'istanza di Redis

Redis è un archivio dati in memoria. I dati vengono conservati nelle macchine fisiche o virtuali che ospitano la cache Redis. Un'istanza di Cache Redis di Azure per l'istanza di Redis nel livello Basic viene eseguita su un'unica macchina virtuale. Se la macchina virtuale è inattiva, tutti i dati archiviati nella cache vanno persi. 

Le cache nei livelli Standard e Premium offrono una resilienza molto superiore rispetto alla perdita di dati, usando due macchine virtuali in una configurazione replicata. Quando il nodo primario in una cache di questo tipo ha esito negativo, il nodo di replica acquisisce automaticamente i dati. Queste macchine virtuali si trovano in domini distinti per gli errori e gli aggiornamenti, per ridurre al minimo le possibilità che entrambi diventino non disponibili contemporaneamente. Se si verifica un'interruzione grave nel data center, tuttavia, è comunque possibile che le macchine virtuali diventino inattive contemporaneamente. In questi rari casi, i dati andranno persi.

Prendere in considerazione l'uso della [persistenza dei dati Redis](https://redis.io/topics/persistence) e la [replica geografica](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) per migliorare la protezione dei dati in caso di errori dell'infrastruttura.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Risolvere i problemi del lato server di cache di Azure per Redis](cache-troubleshoot-server.md)
- [Scelta del livello appropriato](cache-overview.md#choosing-the-right-tier)
- [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md)
- [Come si eseguono i comandi Redis?](cache-development-faq.md#how-can-i-run-redis-commands)
