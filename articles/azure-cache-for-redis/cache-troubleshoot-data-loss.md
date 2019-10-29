---
title: Risolvere i problemi di cache di Azure per la perdita di dati Redis | Microsoft Docs
description: Informazioni su come risolvere i problemi di perdita dei dati con cache di Azure per Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 4fee7c84b394e84369b28d2a4191d0e581f3beba
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044356"
---
# <a name="troubleshoot-azure-cache-for-redis-data-loss"></a>Risolvere i problemi di cache di Azure per la perdita di dati Redis

Questa sezione illustra come diagnosticare le perdite di dati effettive o percepite che possono verificarsi in cache di Azure per Redis.

- [Perdita parziale di chiavi](#partial-loss-of-keys)
- [Perdita di chiavi principale o completa](#major-or-complete-loss-of-keys)

> [!NOTE]
> Diversi passaggi per la risoluzione dei problemi illustrati in questa guida includono istruzioni per eseguire comandi di Redis e monitorare svariate metriche delle prestazioni. Per altre informazioni e istruzioni, vedere gli articoli della sezione [Informazioni aggiuntive](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Perdita parziale di chiavi

Redis non elimina in modo casuale le chiavi dopo che sono state archiviate in memoria. Tuttavia, le chiavi vengono rimosse in risposta ai criteri di scadenza o rimozione, oltre ai comandi di eliminazione della chiave espliciti. Inoltre, le chiavi che sono state scritte nel nodo master in una cache di Azure Premium o standard per Redis potrebbero non essere disponibili in una replica immediatamente. I dati vengono replicati dal master alla replica in modo asincrono e non bloccante.

Se si ritiene che le chiavi siano scomparse dalla cache, è possibile controllare quanto segue per vedere quali potrebbero essere le cause:

| Causa | Description |
|---|---|
| [Scadenza chiave](#key-expiration) | Le chiavi vengono rimosse a causa dei timeout impostati |
| [Rimozione della chiave](#key-eviction) | Le chiavi vengono rimosse sotto pressione della memoria |
| [Eliminazione della chiave](#key-deletion) | Le chiavi vengono rimosse dai comandi DELETE espliciti |
| [Replica asincrona](#async-replication) | Le chiavi non sono disponibili in una replica a causa di ritardi di replica dei dati |

### <a name="key-expiration"></a>Scadenza chiave

Redis rimuoverà automaticamente una chiave se viene assegnato un timeout e tale periodo è scaduto. Per altri dettagli sulla scadenza della chiave Redis, vedere la documentazione relativa alla [scadenza](http://redis.io/commands/expire) del comando. I valori di timeout possono anche essere impostati tramite [set](http://redis.io/commands/set), [setex](https://redis.io/commands/setex), [gets](https://redis.io/commands/getset)e altri comandi \*STORE.

È possibile usare il comando [info](http://redis.io/commands/info) per ottenere statistiche sul numero di chiavi scadute. Nella sezione *statistiche* viene visualizzato il numero totale di chiavi scadute. La sezione *spazio* chiavi contiene informazioni aggiuntive sul numero di chiavi con timeout e il valore di timeout medio.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Inoltre, è possibile esaminare le metriche di diagnostica per la cache per verificare se esiste una correlazione tra il momento in cui la chiave è mancante e un picco nelle chiavi scadute. Vedere l' [appendice](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) per informazioni sull'uso delle notifiche o del monitoraggio dello spazio delle comunicazioni per eseguire il debug di questi tipi di problemi.

### <a name="key-eviction"></a>Rimozione della chiave

Redis richiede spazio di memoria per archiviare i dati. Le chiavi vengono ripulite per liberare memoria disponibile quando necessario. Quando i valori **used_memory** o **Used_memory_rss** nel comando [info](http://redis.io/commands/info) si avvicinano all'impostazione **MaxMemory** configurata, Redis avvierà la rimozione delle chiavi dalla memoria in base ai [criteri della cache](http://redis.io/topics/lru-cache).

È possibile monitorare il numero di chiavi eliminate usando il comando [info](http://redis.io/commands/info) .

```
# Stats

evicted_keys:13224
```

Inoltre, è possibile esaminare le metriche di diagnostica per la cache per verificare se esiste una correlazione tra il momento in cui la chiave è mancante e un picco nelle chiavi eliminate. Vedere l' [appendice](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) per informazioni sull'uso delle notifiche o del monitoraggio dello spazio delle comunicazioni per eseguire il debug di questi tipi di problemi.

### <a name="key-deletion"></a>Eliminazione della chiave

I client Redis possono eseguire il comando [del](http://redis.io/commands/del) o [HDEL](http://redis.io/commands/hdel) per rimuovere in modo esplicito le chiavi da Redis. È possibile tenere traccia del numero di operazioni di eliminazione usando il comando [info](http://redis.io/commands/info) . Se i comandi DEL o HDEL sono stati chiamati, verranno elencati nella sezione *Commandstats* .

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replica asincrona

Qualsiasi cache di Azure per Redis nel livello standard o Premium è configurata con un nodo master e almeno una replica. I dati vengono copiati dal database master a una replica in modo asincrono tramite un processo in background. Il sito Web di [Redis.io](http://redis.io/topics/replication) descrive il funzionamento generale della replica dei dati di Redis. Per gli scenari in cui i client scrivono spesso in Redis, è possibile che si verifichi una perdita parziale dei dati a causa del fatto che questa replica è garantita in modo istantaneo. Ad esempio, se il master diventa inattivo _dopo che_ un client scrive una chiave, ma _prima_ che il processo in background abbia la possibilità di inviare questa chiave alla replica, la chiave viene persa quando la replica diventa il nuovo master.

## <a name="major-or-complete-loss-of-keys"></a>Perdita di chiavi principale o completa

Se si ritiene che la maggior parte di o di tutte le chiavi siano scomparse dalla cache, è possibile controllare quanto segue per vedere quali potrebbero essere le cause:

| Causa | Description |
|---|---|
| [Scaricamento della chiave](#key-flushing) | Chiavi eliminate manualmente |
| [Selezione database non corretta](#incorrect-database-selection) | Redis è impostato per l'uso di un database non predefinito |
| [Errore dell'istanza Redis](#redis-instance-failure) | Il server Redis non è disponibile |

### <a name="key-flushing"></a>Scaricamento della chiave

I client possono chiamare il comando [FLUSHDB](http://redis.io/commands/flushdb) per rimuovere tutte le chiavi in un **singolo** database o [FLUSHALL](http://redis.io/commands/flushall) per rimuovere tutte le chiavi da **tutti i** database in una cache Redis. È possibile scoprire se le chiavi sono state scaricate usando il comando [info](http://redis.io/commands/info) . Verrà visualizzato se è stato chiamato uno dei comandi di svuotamento nella sezione *Commandstats* .

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Selezione database non corretta

Per impostazione predefinita, cache di Azure per Redis usa il database **DB0** . Se si passa a un altro database (ad esempio, DB1) e si prova a leggere le chiavi, Redis non le troverà perché ogni database è un'unità logicamente separata e contiene un set di dati diverso. Usare il comando [Select](http://redis.io/commands/select) per usare altri database disponibili e cercare le chiavi in ognuna di esse.

### <a name="redis-instance-failure"></a>Errore dell'istanza Redis

Redis è un archivio dati in memoria. I dati vengono conservati nelle macchine fisiche o virtuali che ospitano Redis. Una cache di Azure per l'istanza di redis nel livello Basic viene eseguita solo su una singola macchina virtuale (VM). Se la macchina virtuale è inattiva, tutti i dati archiviati nella cache andranno perduti. Le cache nei livelli standard e Premium offrono una resilienza molto maggiore rispetto alla perdita di dati usando due macchine virtuali in una configurazione replicata. Quando il nodo master in una cache di questo tipo ha esito negativo, il nodo di replica assumerà il controllo dei dati automaticamente. Queste macchine virtuali si trovano in domini di errore e di aggiornamento separati per ridurre al minimo la possibilità di diventare non disponibili contemporaneamente. In caso di un'interruzione del data center principale, tuttavia, le macchine virtuali possono continuare a rientrare insieme. I dati andranno persi in questi rari casi.

È consigliabile usare la [persistenza dei dati Redis](http://redis.io/topics/persistence) e la [replica geografica](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) per migliorare la protezione dei dati in base a questi errori di infrastruttura.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Risolvere i problemi del lato server di cache di Azure per il server Redis](cache-troubleshoot-server.md)
- [Quali offerte e dimensioni della Cache Redis è consigliabile usare?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md)
- [Come si eseguono i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands)
